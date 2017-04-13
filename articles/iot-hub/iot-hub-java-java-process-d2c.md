<properties
    pageTitle="Elaborazione dei messaggi dispositivo nel cloud IoT Hub (Java) | Microsoft Azure"
    description="Seguire questa esercitazione Java per informazioni su modelli utili per elaborare i messaggi IoT Hub dispositivo nel cloud."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>Esercitazione: Come elaborare messaggi di dispositivo nel cloud IoT Hub utilizzando Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduzione

Azure IoT Hub è un servizio completamente gestito consente affidabile e protette bidirezionale tra milioni di dispositivi IoT e un'applicazione di back-end. Altre esercitazioni ([Guida introduttiva a IoT Hub] e [inviare messaggi cloud per dispositivo con IoT Hub][lnk-c2d]) viene illustrato come utilizzare funzionalità di base dispositivo nel cloud e cloud per dispositivo messaggistica di IoT Hub.

In questa esercitazione si basa sul codice riportato nell'esercitazione [Introduzione IoT Hub] e che mostra due motivi scalable che è possibile utilizzare per elaborare i messaggi di dispositivo nel cloud:

- Archiviazione affidabile dei messaggi di dispositivo nel cloud in [archiviazione blob Azure]. Scenari comuni sono analitica *percorso fredda* , in cui memorizzare i dati di telemetria nei BLOB da usare come input per i processi analitica. Questi processi possono essere guidati con strumenti come [Factory di dati di Azure] o stack [HDInsight (Hadoop)] .

- Elaborazione affidabile dei messaggi di dispositivo nel cloud *interattivi* . Dispositivo nel cloud messaggi sono interattivi quando sono immediato trigger per un insieme di azioni nel applicazione back-end. Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM. *Punto dati* messaggi invece inserito semplicemente da un motore analitica. Telemetria temperatura da un dispositivo che deve essere archiviato per analisi successive, ad esempio è un messaggio di punto dati.

Perché IoT Hub espone un [Evento Hub][lnk-event-hubs]-endpoint compatibile a ricevere messaggi di dispositivo nel cloud, questa esercitazione utilizza un'istanza di [EventProcessorHost] . Questa istanza:

* In modo affidabile archivia i messaggi di *punto dati* in archiviazione blob Azure.
* *Interattiva* dispositivo nel cloud messaggi inoltrati a una [coda di servizio Bus] Azure per l'elaborazione immediata.

Servizio Bus garantisce affidabile elaborazione dei messaggi interattivi, poiché fornisce punti di controllo per ogni messaggio e la de-duplicazione basate su finestra ora.

> [AZURE.NOTE] Un'istanza di **EventProcessorHost** è l'unico modo per elaborare i messaggi interattivi. Altre opzioni includono [Azure servizio tessuti] [ lnk-service-fabric] e [Azure flusso Analitica][lnk-stream-analytics].

Al termine di questa esercitazione, eseguire le applicazioni Java tre:

* **dispositivo simulata**, una versione modificata dell'app creata in esercitazione [Introduzione IoT Hub] invia i messaggi di dispositivo nel cloud punto dati ogni messaggi dispositivo nel cloud secondo e interattivi ogni 10 secondi. Questa applicazione utilizza il protocollo AMQP per comunicare con IoT Hub.
* **i messaggi di d2c processo** utilizza la classe [EventProcessorHost] per recuperare i messaggi da endpoint compatibile con Hub evento. Quindi in modo affidabile archivia i messaggi punto dati in archiviazione blob Azure e inoltro dei messaggi interattivi a una coda Bus di servizio.
* **grafici interattivi messaggi** coda messaggi interattivi dalla coda Bus di servizio.

> [AZURE.NOTE] IoT Hub supporta SDK molte piattaforme di dispositivi e lingue, inclusi C, Java e JavaScript. Per istruzioni su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico e su come connettere dispositivi a un IoT Hub, vedere il [Centro per sviluppatori di Azure IoT].

In questa esercitazione è direttamente aggiuntivi applicabile ad altri modi per utilizzare Hub evento compatibile con messaggi, ad esempio progetti [HDInsight (Hadoop)] . Per ulteriori informazioni, vedere [Guida gli sviluppatori di Azure IoT Hub - dispositivo nel cloud].

Per completare questa esercitazione, è necessario quanto segue:

+ Una versione completa di lavoro dell'esercitazione [Introduzione IoT Hub] .

+ SE Java 8. <br/> [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare Java per questa esercitazione su Windows o Linux.

+ Maven 3.  <br/> [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare Maven per questa esercitazione su Windows o Linux.

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

È necessario disporre conoscenza di base di [Archiviazione Azure] e [Azure servizio Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Inviare messaggi interattivi da un dispositivo simulato

In questa sezione modificare l'applicazione dispositivo simulato creata in esercitazione [Introduzione IoT Hub] per inviare messaggi di dispositivo nel cloud interattivi a hub IoT.

1. Utilizzare un editor di testo per aprire il file simulated-device\src\main\java\com\mycompany\app\App.java. Il file contiene il codice per l'app **dispositivo simulato** creata in esercitazione [Introduzione IoT Hub] .

2. Aggiungere la classe annidata seguente per la classe **App** :

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Questa classe è simile alla classe **MessageSender** nel progetto **dispositivo simulata** . Solo le differenze sono che è ora impostare la proprietà di sistema **ID messaggio** e una proprietà personalizzata denominata **messageType**.
    Il codice assegna un identificatore univoco universale (UUID) per la proprietà **ID messaggio** . Bus del servizio è possibile utilizzare l'identificatore per deselezionare duplicare i messaggi ricevuti. L'esempio utilizza la proprietà **messageType** per distinguere interattivi dai messaggi punto dati. L'applicazione passa queste informazioni nelle proprietà messaggio, anziché nel corpo del messaggio, in modo che non è necessario che il processore evento deserializzare il messaggio per eseguire il routing dei messaggi.

    > [AZURE.NOTE] È importante creare l' **ID messaggio** utilizzato per deselezionare duplicare interattivi messaggi nel codice di dispositivo. Comunicazioni di rete intermittente o altri errori, potrebbero causare ritrasmissioni più dello stesso messaggio da tale dispositivo. È anche possibile usare un ID messaggio semantic, ad esempio hashing del campi di dati del messaggio pertinenti, al posto di un UUID.

3. Modificare il metodo **principale** per l'invio di entrambi i messaggi interattivi e coordinata messaggi come illustrato nel frammento di codice seguenti:

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Salvare e chiudere il file simulated-device\src\main\java\com\mycompany\app\App.java.

    > [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare un criterio di tentativi, ad esempio backoff esponenziale, come indicato nell'articolo MSDN [Gestione guasto temporaneo].

5. Per creare l'applicazione **dispositivo simulato** utilizzando Maven, eseguire il comando seguente al prompt dei comandi nella cartella simulata dispositivo:

    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>Elabora i messaggi di dispositivo nel cloud

In questa sezione, si crea un'app di console di linguaggio che elabora i messaggi dall'IoT Hub dispositivo nel cloud. IOT Hub espone [evento Hub]-endpoint compatibile per abilitare un'applicazione leggere i messaggi di dispositivo nel cloud. In questa esercitazione utilizza la classe [EventProcessorHost] per elaborare i messaggi in un'app console. Per ulteriori informazioni su come per elaborare i messaggi da hub di evento, vedere l'esercitazione [Introduzione hub evento] .

La richiesta principale quando implementa affidabile dello spazio di archiviazione dei messaggi punto dati o inoltro di messaggi interattivi, che elaborazione dell'evento si basa su consumer di messaggi per fornire punti di controllo per lo stato di avanzamento. Inoltre, per ottenere un alta velocità quando si leggono evento hub è necessario fornire i punti di arresto in batch di grandi dimensioni. Questo approccio crea la possibilità di elaborazione duplicati per un numero elevato di messaggi, se si verifica un errore e tornare al punto di arresto precedente. In questa esercitazione, viene visualizzato come sincronizzare windows de-duplicazione Bus di servizio e lo spazio di archiviazione di Azure scrittura con punti di controllo **EventProcessorHost** .

Per scrivere in modo affidabile messaggi allo spazio di archiviazione di Azure, nell'esempio Usa la funzione di commit singolo blocco di [blocco BLOB][Azure Block Blobs]. Processore di eventi sono indicati i messaggi in memoria fino a quando non è possibile fornire un punto di controllo. Ad esempio, dopo il buffer accumulato dei messaggi raggiunge le dimensioni massime blocco di 4 MB o dopo la de-duplicazione Service Bus intervallo di tempo trascorso. Quindi, prima di verifica, il codice di un nuovo blocco si impegna al blob.

Processore di eventi utilizza hub evento messaggio offset come blocco ID. Questo meccanismo consente al processore evento eseguire la verifica prima che esegue il commit nuovo blocco allo spazio di archiviazione, verificando di un arresto anomalo possibili tra il commit di un blocco e il punto di controllo.

> [AZURE.NOTE] In questa esercitazione utilizza un singolo account Azure lo spazio di archiviazione per scrivere tutti i messaggi recuperati IoT Hub. Per decidere se è necessario utilizzare più account di archiviazione Azure della soluzione, vedere [lo spazio di archiviazione di Azure scalabilità alle linee guida].

L'applicazione utilizza la funzionalità di annullamento duplicazione Bus di servizio per evitare i duplicati durante l'elaborazione di messaggi interattivi. Il dispositivo simulato contrassegna ogni messaggio interattivo con un **ID messaggio**univoco. Questo id Bus di servizio per garantire che, in intervallo di tempo specificato la duplicazione non esistono due messaggi con la stessa **ID messaggio** vengono inviati per i destinatari. La duplicazione cessazione, insieme alla semantica di completamento di ogni messaggio fornita da code Bus di servizio, è facile implementare l'elaborazione affidabile dei messaggi interattivi.

Per assicurarsi che nessun messaggio viene inviato nuovamente all'esterno della finestra la duplicazione, il codice Sincronizza meccanismo di verifica **EventProcessorHost** con la finestra di deduplicazione coda Bus di servizio. La sincronizzazione è possibile forzare una verifica almeno una volta ogni volta che la finestra de-duplicazione trascorre (in questa esercitazione, la finestra è un'ora).

> [AZURE.NOTE] In questa esercitazione viene usata una singola coda Bus di servizio partizionata elaborare tutti i messaggi interattivi recuperati dall'IoT Hub. Per ulteriori informazioni sull'utilizzo di code Bus di servizio per soddisfare i requisiti di scalabilità della soluzione, vedere la documentazione [Bus di servizio Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Effettuare il provisioning di un account di archiviazione Azure e una coda Bus di servizio

Per utilizzare la classe [EventProcessorHost] , devono avere un account di archiviazione Azure abilitare **EventProcessorHost** registrare le informazioni di verifica. È possibile usare un account di archiviazione Azure esistente o seguire le istruzioni [Sull'archiviazione Azure] per crearne uno nuovo. Prendere nota della stringa di connessione account Azure lo spazio di archiviazione.

> [AZURE.NOTE] Quando si copiare e Incolla la stringa di connessione account Azure lo spazio di archiviazione, assicurarsi che non sono presenti spazi inclusi.

Occorre inoltre una coda Bus di servizio per consentire l'elaborazione di affidabilità dei messaggi interattivi. È possibile creare una coda a livello di programmazione, con una finestra la duplicazione di un'ora, come spiegato [come utilizzare servizio Bus code][coda Bus di servizio]. In alternativa, è possibile utilizzare il [portale classica Azure][lnk-classic-portal], eseguendo la procedura seguente:

1. Nell'angolo in basso a sinistra, fare clic su **Nuovo** . Fare clic su **App servizi** > **Servizio Bus** > **coda** > **Creare personalizzata**. Immettere il nome **d2ctutorial**, selezionare un'area, usare uno spazio dei nomi o crearne uno nuovo. Prendere nota del nome dello spazio dei nomi, è necessario più avanti in questa esercitazione. Nella pagina successiva, selezionare **Attiva il rilevamento duplicati**e impostare **Duplica rilevamento cronologia ora finestra** a un'ora. Fare clic sul segno di spunta nell'angolo in basso a destra per salvare la configurazione di coda.

    ![Creare una coda nel portale di Azure][30]

2. Nell'elenco di code Bus di servizio, fare clic su **d2ctutorial**e quindi fare clic su **Configura**. Creare due criteri di accesso condiviso, una chiamata **Invia** con autorizzazioni di **inviare** e una chiamata **ascoltare** con autorizzazioni di **attesa** . Prendere nota dei valori di **chiave primaria** per entrambi i criteri, è necessario più avanti in questa esercitazione. Al termine, fare clic su **Salva** nella parte inferiore.

    ![Configurare una coda nel portale di Azure][31]

### <a name="create-the-event-processor"></a>Creare processore di eventi

In questa sezione creare un'applicazione di linguaggio per elaborare i messaggi da endpoint compatibile con Hub evento.

La prima attività consiste nell'aggiungere un progetto Maven chiamato **i messaggi di d2c processo** riceve messaggi di dispositivo nel cloud da endpoint Hub IoT Hub evento compatibile con, che instrada i messaggi ad altri servizi di back-end.

1. Nella cartella iot java-get avviato creata in esercitazione [Introduzione IoT Hub] , creare un progetto Maven denominato **messaggi di d2c processo** utilizzando il comando seguente al prompt dei comandi. Si che tratta di un comando singolo, lungo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi, passare alla nuova cartella di messaggi di d2c processo.

3. Con un editor di testo, aprire il file pom.xml nella cartella messaggi di d2c processo e aggiungere le dipendenze seguenti al livello di nodo **dipendenze** . Le relazioni consentono di utilizzare azure eventhubs, eph di eventhubs azure e azure servicebus pacchetti nell'applicazione di interagire con l'hub IoT e coda Bus di servizio:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

L'attività successiva consiste nell'aggiungere una classe **ErrorNotificationHandler** al progetto.

1. Utilizzare un editor di testo per creare un file process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java. Aggiungere il codice seguente al file per visualizzare i messaggi di errore dall'istanza **EventProcesssorHost** :

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Salvare e chiudere il file ErrorNotificationHandler.java.

A questo punto è possibile aggiungere una classe implementata l'interfaccia **IEventProcessor** . La classe **EventProcessorHost** chiama questa classe per elaborare i messaggi di dispositivo nel cloud ricevuti dall'IoT Hub. Il codice di questa classe implementata la logica per archiviare i messaggi in modo affidabile in un contenitore di blob e inoltrare messaggi interattivi alla coda Bus di servizio.

Il metodo **onEvents** imposta la variabile **latestEventData** che tiene traccia del numero di offset e sequenza del messaggio più recente letto da questo processore evento. Tenere presente che ogni processore è responsabile di una singola partizione. Il metodo **onEvents** riceve un batch di messaggi dall'IoT Hub e li elabora come indicato di seguito: invia messaggi interattivi alla coda Bus di servizio e aggiunge il buffer di memoria **toAppend** messaggi punto dati. Se il buffer di memoria raggiunge il limite di blocco di 4 MB o le finestre di ora la de-duplicazione trascorre (un'ora dopo l'ultimo punto di controllo in questa esercitazione), il metodo avvia una verifica.

Il metodo **AppendAndCheckPoint** genera prima di tutto una **blockId** per il blocco aggiungere il blob. Spazio di archiviazione Azure richiede che tutti bloccare ID per avere la stessa lunghezza, in modo che il metodo aggiunge riempimenti offset con zeri iniziali. Quindi, se un blocco con l'ID è già nel blob, il metodo sovrascrive con il contenuto di buffer corrente.

> [AZURE.NOTE] Per semplificare il codice, questa esercitazione utilizza un blob singolo per partizione per archiviare i messaggi. Una soluzione reale implementare file in sequenza mediante la creazione di file aggiuntivi dopo un certo periodo di tempo o quando si raggiunge una certa dimensione. Tenere presente che un blob Azure blocco può contenere al massimo 195 GB di dati.

L'attività successiva è necessario implementare l'interfaccia **IEventProcessor** :

1. Utilizzare un editor di testo per creare un file process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java.

2. Aggiungere la seguente importazioni e la definizione di classe al file EventProcessor.java. La classe **EventProcessor** consente l'interfaccia **IEventProcessor** che definisce il comportamento del client hub evento:

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Aggiungere i metodi seguenti per la classe **EventProcessor** per implementare l'interfaccia **IEventProcessor** :

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Aggiungere le seguenti variabili a livello di classe alla classe **EventProcessor** :

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Aggiungere un metodo **AppendAndCheckPoint** con la firma seguente per la classe **EventProcessor** :

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Aggiungere il metodo **AppendAndCheckPoint** per recuperare il messaggio offset e sequenza numero corrente nella partizione di codice riportato di seguito:

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. Nel metodo **AppendAndCheckPoint** , usare il valore di offset corrente per creare un'istanza di **BlockEntry** per il blocco successivo salvare il blob:

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. Metodo **AppendAndCheckPoint** , caricare il set più recente di messaggi in blob blocco e recuperare l'elenco corrente di blocchi predefiniti:

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. Metodo **AppendAndCheckPoint** , creare il blocco iniziale in un nuovo blob o aggiungere il blocco a quello esistente:

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Infine nel metodo **AppendAndCheckPoint** creato un punto sulla partizione e prepararsi per salvare il blocco di messaggi successivo:

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. Metodo **onEvents** , aggiungere il codice seguente per ricevere i messaggi dalla endpoint IoT Hub e inoltrare i messaggi interattivi coda Bus di servizio. Chiamare il metodo **AppendAndCheckPoint** quando si riempie l'intervallo o il timeout scade:

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Infine nel metodo **onEvents** , aggiungere una clausola 'else if' per chiamare **AppendAndCheckPoint** se il timeout scade mentre non sono presenti messaggi provenienti da IoT Hub:

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Salvare le modifiche al file EventProcessor.java.

L'ultima attività del progetto di **messaggi di d2c processo** consiste nell'aggiungere codice al metodo **principale** che crea un'istanza di **EventProcessorHost** .

1. Utilizzare un editor di testo per aprire il file process-d2c-messages\src\main\java\com\mycompany\app\App.java.

2. Aggiungere le seguenti istruzioni **importare** il file:

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Aggiungere la seguente variabile a livello di classe alla classe **App** . Sostituire **{yourstorageaccountconnectionstring}** con la stringa di connessione account Azure archiviazione eseguite in precedenza nota della sezione [effettuare il provisioning di un account di archiviazione Azure e una coda Bus di servizio](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Aggiungere le seguenti variabili a livello di classe alla classe **App** e sostituire **{yourservicebusnamespace}** con il servizio Bus dello spazio dei nomi e **{yourservicebussendkey}** con tasto **INVIO** della coda. Apportate in precedenza una nota di spazio dei nomi e **ascoltare** valori chiave nella sezione [effettuare il provisioning di un account di archiviazione Azure e una coda Bus di servizio](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Aggiungere le seguenti variabili a livello di classe alla classe **App** . Sostituire **{youreventhubcompatibleendpoint}** con il valore di endpoint compatibile con Hub evento. Il valore di endpoint è simile alla **il suo … dello spazio dei nomi** in modo che è necessario rimuovere il **sb: / /** prefisso e un suffisso **.servicebus.windows.net/** . Sostituire **{youreventhubcompatiblename}** con il nome di Hub evento compatibile con. Sostituire **{youriothubkey}** con il tasto **iothubowner** . Apportate una nota di questi valori in [Crea un IoT Hub] [ lnk-create-an-iot-hub] sezione nell'esercitazione *Introduzione Azure IoT Hub per Java* :

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modificare la firma del metodo **main** come segue:

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Aggiungere il codice seguente al metodo **principale** per ottenere un riferimento al contenitore di blob che archivia i messaggi:

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Aggiungere il codice seguente al metodo **principale** per ottenere un riferimento al servizio Bus di servizio:

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. Nel metodo **principale** , configurare e creare un'istanza di **EventProcessorHost** . L'opzione **setInvokeProcessorAfterReceiveTimeout** garantisce che l'istanza **EventProcessorHost** richiama il metodo **onEvents** nell'interfaccia di **IEventProcessor** anche quando non sono presenti messaggi per l'elaborazione. Il metodo **onEvents** quindi richiama sempre il metodo **AppendAndCheckPoint** quando scade il timeout.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. Nel metodo **principale** , registrare l'implementazione di **IEventProcessor** con l'istanza di **EventProcessorHost** :

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Infine, aggiungere logica al metodo **principale** per arrestare l'istanza di **EventProcessorHost** :

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Salvare e chiudere il file process-d2c-messages\src\main\java\com\mycompany\app\App.java.

13. Per creare l'applicazione di **messaggi di d2c processo** utilizzando Maven, eseguire il comando seguente al prompt dei comandi nella cartella messaggi di d2c processo:

    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>Ricevere messaggi interattivi

In questa sezione, scrivere un'applicazione di console di linguaggio che riceve i messaggi interattivi dalla coda Bus di servizio.

La prima attività consiste nell'aggiungere un progetto Maven denominato **grafici interattivi messaggi** che riceve i messaggi inviati nella coda Bus di servizio da istanze **EventProcessor** .

1. Nella cartella iot java-get avviato creata in esercitazione [Introduzione IoT Hub] , creare un progetto Maven denominato **grafici interattivi messaggi** tramite il comando seguente al prompt dei comandi. Si che tratta di un comando singolo, lungo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi passare alla nuova cartella di grafici interattivi messaggi.

3. Con un editor di testo, aprire il file pom.xml nella cartella grafici interattivi messaggi e aggiungere il nodo **dipendenze** dipendenza seguente. Questa dipendenza consente di usare il pacchetto di azure servicebus nell'applicazione per interagire con la coda Bus di servizio:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

L'attività successiva consiste nell'aggiungere codice per recuperare i messaggi dalla coda Bus di servizio.

1. Utilizzare un editor di testo per aprire il file process-interactive-messages\src\main\java\com\mycompany\app\App.java.

2. Aggiungere quanto segue `import` istruzioni per il file:

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Aggiungere le seguenti variabili a livello di classe alla classe **App** e sostituire **{yourservicebusnamespace}** con spazio dei nomi Bus di servizio e **{yourservicebuslistenkey}** con chiave **ascolto** della coda. Apportate in precedenza una nota di spazio dei nomi e **ascoltare** valori chiave nella sezione [effettuare il provisioning di un account di archiviazione Azure e una coda Bus di servizio](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Aggiungere la classe annidata seguente alla classe **App** per ricevere i messaggi dalla coda:

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modificare la firma del metodo **main** come segue:

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Nel metodo **principale** , aggiungere il codice seguente per avviare l'ascolto per i nuovi messaggi:

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Salvare e chiudere il file process-interactive-messages\src\main\java\com\mycompany\app\App.java.

8. Per creare l'applicazione di **grafici interattivi messaggi** utilizzando Maven, eseguire il comando seguente al prompt dei comandi nella cartella grafici interattivi messaggi:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le tre applicazioni.

1.  Per eseguire l'applicazione di **grafici interattivi messaggi** , in un prompt dei comandi di shell passare alla cartella grafici interattivi messaggi ed eseguire il comando seguente:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire grafici interattivi messaggi][processinteractive]

2.  Per eseguire l'applicazione di **messaggi di d2c processo** , in un prompt dei comandi di shell passare alla cartella messaggi di d2c processo ed eseguire il comando seguente:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire i messaggi di d2c processo][processd2c]

3.  Per eseguire l'applicazione di **dispositivo simulato** , in un prompt dei comandi di shell passare alla cartella dispositivo simulato ed eseguire il comando seguente:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire dispositivo simulato][simulateddevice]

> [AZURE.NOTE] Per visualizzare gli aggiornamenti nel blob, potrebbe essere necessario ridurre la costante **MAX_BLOCK_SIZE** nella classe **StoreEventProcessor** su un valore inferiore, ad esempio **1024**. Questa modifica è utile perché è necessario del tempo per raggiungere il limite di dimensioni blocco con i dati inviati dal dispositivo simulato. Con una dimensione inferiore blocco, non è necessario attendere così tanto tempo per visualizzare il blob viene creato e aggiornato. Tuttavia, utilizzando una dimensione maggiore per il blocco diventa l'applicazione maggiore scalabilità.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si appreso elaborare in modo affidabile punto dati e messaggi dispositivo nel cloud interattivi utilizzando la classe [EventProcessorHost] .

[Come inviare messaggi cloud per dispositivo con IoT Hub] [ lnk-c2d] viene illustrato come inviare messaggi per i dispositivi dal back-end.

Per esempi di soluzioni-to-end complete che utilizzano IoT Hub, vedere [Azure IoT Suite][lnk-suite].

Per ulteriori informazioni sullo sviluppo di soluzioni con IoT Hub, vedere la [Guida per gli sviluppatori di IoT Hub].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Archiviazione blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Dati di Azure Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Coda Bus di servizio]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guida per sviluppatori di IoT Hub Azure - dispositivo nel cloud]: iot-hub-devguide-messaging.md

[Spazio di archiviazione Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus di servizio Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guida per gli sviluppatori IoT Hub]: iot-hub-devguide.md
[Guida introduttiva a IoT Hub]: iot-hub-java-java-getstarted.md
[Centro per sviluppatori di Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Informazioni sull'archiviazione Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Guida introduttiva a hub di evento]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Scalabilità lo spazio di archiviazione Azure linee guida]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub