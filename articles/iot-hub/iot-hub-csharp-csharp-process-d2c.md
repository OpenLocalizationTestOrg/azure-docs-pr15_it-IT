<properties
    pageTitle="Elaborazione dei messaggi dispositivo nel cloud IoT Hub (.Net) | Microsoft Azure"
    description="Seguire questa esercitazione per informazioni su modelli utili per elaborare i messaggi IoT Hub dispositivo nel cloud."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>Esercitazione: Come elaborare i messaggi di dispositivo nel cloud IoT Hub utilizzando .net

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduzione

Azure IoT Hub è un servizio completamente gestito consente affidabile e protette bidirezionale tra milioni di dispositivi IoT e un'applicazione di back-end. Altre esercitazioni ([Guida introduttiva a IoT Hub] e [inviare messaggi cloud per dispositivo con IoT Hub][lnk-c2d]) viene illustrato come utilizzare funzionalità di base dispositivo nel cloud e cloud per dispositivo messaggistica di IoT Hub.

In questa esercitazione si basa sul codice riportato nell'esercitazione [Introduzione IoT Hub] e che mostra due motivi scalable che è possibile utilizzare per elaborare i messaggi di dispositivo nel cloud:

- Archiviazione affidabile dei messaggi di dispositivo nel cloud in [archiviazione blob Azure]. Scenari comuni sono analitica *percorso fredda* , in cui memorizzare i dati di telemetria nei BLOB da usare come input per i processi analitica. Questi processi possono essere guidati con strumenti come [Factory di dati di Azure] o stack [HDInsight (Hadoop)] .

- Elaborazione affidabile dei messaggi di dispositivo nel cloud *interattivi* . Dispositivo nel cloud messaggi sono interattivi quando sono immediato trigger per un insieme di azioni nel applicazione back-end. Ad esempio, un dispositivo potrebbe inviare un messaggio di avviso che attiva l'inserimento di un ticket in un sistema CRM. Invece *del punto dati* messaggi feed semplicemente in un motore analitica. Telemetria temperatura da un dispositivo che deve essere archiviato per analisi successive, ad esempio è un messaggio di punto dati.

Perché IoT Hub espone un [Evento Hub][lnk-event-hubs]-endpoint compatibile a ricevere messaggi di dispositivo nel cloud, questa esercitazione utilizza un'istanza di [EventProcessorHost] . Questa istanza:

* In modo affidabile archivia i messaggi *del punto dati* in archiviazione blob Azure.
* *Interattiva* dispositivo nel cloud messaggi inoltrati a una [coda di servizio Bus] Azure per l'elaborazione immediata.

Servizio Bus garantisce affidabile elaborazione dei messaggi interattivi, poiché fornisce punti di controllo per ogni messaggio e la de-duplicazione basate su finestra ora.

> [AZURE.NOTE] Un'istanza di **EventProcessorHost** è l'unico modo per elaborare i messaggi interattivi. Altre opzioni includono [Azure servizio tessuti] [ lnk-service-fabric] e [Azure flusso Analitica][lnk-stream-analytics].

Al termine di questa esercitazione, eseguire tre App console di Windows:

* **SimulatedDevice**, una versione modificata dell'app creati nell'esercitazione [Introduzione IoT Hub] , invia dati punto dispositivo nel cloud messaggi ogni secondo e interattivi dispositivo nel cloud messaggi ogni 10 secondi. Questa applicazione utilizza il protocollo AMQP per comunicare con IoT Hub.
* **ProcessDeviceToCloudMessages** utilizza la classe [EventProcessorHost] per recuperare i messaggi da endpoint compatibile con Hub evento. Quindi in modo affidabile archivia i messaggi di punto dati in archiviazione blob Azure e inoltro dei messaggi interattivi a una coda Bus di servizio.
* **ProcessD2CInteractiveMessages** coda messaggi interattivi dalla coda Bus di servizio.

> [AZURE.NOTE] IoT Hub supporta SDK molte piattaforme di dispositivi e lingue, inclusi C, Java e JavaScript. Per informazioni su come sostituire il dispositivo simulato in questa esercitazione con un dispositivo fisico e su come connettere dispositivi a un IoT Hub, vedere il [Centro per sviluppatori di Azure IoT].

In questa esercitazione è direttamente aggiuntivi applicabile ad altri modi per utilizzare Hub evento compatibile con messaggi, ad esempio progetti [HDInsight (Hadoop)] . Per ulteriori informazioni, vedere [Guida gli sviluppatori di Azure IoT Hub - dispositivo nel cloud].

Per completare questa esercitazione, è necessario quanto segue:

+ Microsoft Visual Studio 2015.

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

È necessario disporre conoscenza di base di [Archiviazione Azure] e [Azure servizio Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Inviare messaggi interattivi da un dispositivo simulato

In questa sezione modificare l'applicazione dispositivo simulato creata in esercitazione [Introduzione IoT Hub] per inviare messaggi di dispositivo nel cloud interattivi a hub IoT.

1. Nel progetto **SimulatedDevice** Visual Studio, aggiungere il metodo seguente alla classe **Program** .

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Questo metodo è simile al metodo **SendDeviceToCloudMessagesAsync** nel progetto **SimulatedDevice** . Solo le differenze sono che è ora impostare la proprietà di sistema **ID messaggio** e una proprietà dell'utente chiamato **messageType**.
    Il codice assegna un identificatore univoco globale (GUID) per la proprietà **ID messaggio** . Bus del servizio è possibile utilizzare l'identificatore per deselezionare duplicare i messaggi ricevuti. L'esempio utilizza la proprietà **messageType** per distinguere interattivi dai messaggi punto dati. L'applicazione passa queste informazioni nelle proprietà messaggio, anziché nel corpo del messaggio, in modo che non è necessario che il processore evento deserializzare il messaggio per eseguire il routing dei messaggi.

    > [AZURE.NOTE] È importante creare l' **ID messaggio** utilizzato per deselezionare duplicare interattivi messaggi nel codice di dispositivo. Comunicazioni di rete intermittente o altri errori, potrebbero causare ritrasmissioni più dello stesso messaggio da tale dispositivo. È anche possibile usare un ID messaggio semantic, ad esempio hashing del campi di dati del messaggio pertinenti, al posto di un GUID.

2. Aggiungere il metodo seguente nel metodo **Main** appena prima di `Console.ReadLine()` riga:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare un criterio di tentativi, ad esempio backoff esponenziale, come indicato nell'articolo MSDN [Gestione guasto temporaneo].

## <a name="process-device-to-cloud-messages"></a>Elabora i messaggi di dispositivo nel cloud

In questa sezione, si crea un'app di console di Windows che elabora i messaggi dall'IoT Hub dispositivo nel cloud. IOT Hub espone [evento Hub]-endpoint compatibile per abilitare un'applicazione leggere i messaggi di dispositivo nel cloud. In questa esercitazione utilizza la classe [EventProcessorHost] per elaborare i messaggi in un'app console. Per ulteriori informazioni su come per elaborare i messaggi da hub di evento, vedere l'esercitazione [Introduzione hub evento] .

La difficoltà quando si implementa affidabile dello spazio di archiviazione dei messaggi punto dati o inoltro di messaggi interattivi l'elaborazione di eventi si basa su consumer di messaggi per fornire punti di controllo per lo stato di avanzamento. Inoltre, per ottenere un alta velocità quando si leggono evento hub è necessario fornire i punti di arresto in batch di grandi dimensioni. Questo approccio crea la possibilità di elaborazione duplicati per un numero elevato di messaggi, se si verifica un errore e tornare al punto di arresto precedente. In questa esercitazione, viene visualizzato come sincronizzare windows de-duplicazione Bus di servizio e lo spazio di archiviazione di Azure scrittura con punti di controllo **EventProcessorHost** .

Per scrivere in modo affidabile messaggi allo spazio di archiviazione di Azure, nell'esempio Usa la funzione di commit singolo blocco di [blocco BLOB][Azure Block Blobs]. Processore di eventi sono indicati i messaggi in memoria fino a quando non è possibile fornire un punto di controllo. Ad esempio, dopo il buffer accumulato dei messaggi raggiunge le dimensioni massime blocco di 4 MB o dopo la de-duplicazione Service Bus intervallo di tempo trascorso. Quindi, prima di verifica, il codice di un nuovo blocco si impegna al blob.

Processore di eventi utilizza hub evento messaggio offset come blocco ID. Questo meccanismo consente al processore evento eseguire la verifica prima che esegue il commit nuovo blocco allo spazio di archiviazione, verificando di un arresto anomalo possibili tra il commit di un blocco e il punto di controllo.

> [AZURE.NOTE] In questa esercitazione utilizza un singolo account Azure lo spazio di archiviazione per scrivere tutti i messaggi recuperati IoT Hub. Per decidere se è necessario utilizzare più account di archiviazione Azure della soluzione, vedere [lo spazio di archiviazione di Azure scalabilità alle linee guida].

L'applicazione utilizza la funzionalità di annullamento duplicazione Bus di servizio per evitare i duplicati durante l'elaborazione di messaggi interattivi. Il dispositivo simulato contrassegna ogni messaggio interattivo con un **ID messaggio**univoco. Questi ID abilitare Bus di servizio garantire che, in intervallo di tempo specificato la duplicazione non esistono due messaggi con la stessa **ID messaggio** vengono inviati per i destinatari. La duplicazione cessazione, insieme alla semantica di completamento di ogni messaggio fornita da code Bus di servizio, è facile implementare l'elaborazione affidabile dei messaggi interattivi.

Per assicurarsi che nessun messaggio viene inviato nuovamente all'esterno della finestra la duplicazione, il codice Sincronizza meccanismo di verifica **EventProcessorHost** con la finestra di deduplicazione coda Bus di servizio. La sincronizzazione è possibile forzare una verifica almeno una volta ogni volta che la finestra de-duplicazione trascorre (in questa esercitazione, la finestra è un'ora).

> [AZURE.NOTE] In questa esercitazione viene usata una singola coda Bus di servizio partizionata elaborare tutti i messaggi interattivi recuperati dall'IoT Hub. Per ulteriori informazioni sull'utilizzo di code Bus di servizio per soddisfare i requisiti di scalabilità della soluzione, vedere la documentazione [Bus di servizio Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Effettuare il provisioning di un account di archiviazione Azure e una coda Bus di servizio
Per utilizzare la classe [EventProcessorHost] , devono avere un account di archiviazione Azure abilitare **EventProcessorHost** registrare le informazioni di verifica. È possibile usare un account di archiviazione Azure esistente o seguire le istruzioni [Sull'archiviazione Azure] per crearne uno nuovo. Prendere nota della stringa di connessione account Azure lo spazio di archiviazione.

> [AZURE.NOTE] Quando si copiare e Incolla la stringa di connessione account Azure lo spazio di archiviazione, assicurarsi che non sono presenti spazi inclusi.

Occorre inoltre una coda Bus di servizio per consentire l'elaborazione di affidabilità dei messaggi interattivi. È possibile creare una coda a livello di programmazione, con una finestra la duplicazione di un'ora, come spiegato [come utilizzare servizio Bus code][coda Bus di servizio]. In alternativa, è possibile utilizzare il [portale classica Azure][lnk-classic-portal], eseguendo la procedura seguente:

1. Nell'angolo in basso a sinistra, fare clic su **Nuovo** . Fare clic su **App servizi** > **Servizio Bus** > **coda** > **Creare personalizzata**. Immettere il nome **d2ctutorial**, selezionare un'area, usare uno spazio dei nomi o crearne uno nuovo. Nella pagina successiva, selezionare **Attiva il rilevamento duplicati**e impostare **Duplica rilevamento cronologia ora finestra** a un'ora. Fare clic sul segno di spunta nell'angolo in basso a destra per salvare la configurazione di coda.

    ![Creare una coda nel portale di Azure][30]

2. Nell'elenco di code Bus di servizio, fare clic su **d2ctutorial**e quindi fare clic su **Configura**. Creare due criteri di accesso condiviso, una chiamata **Invia** con autorizzazioni di **inviare** e una chiamata **ascoltare** con autorizzazioni di **attesa** . Al termine, fare clic su **Salva** nella parte inferiore.

    ![Configurare una coda nel portale di Azure][31]

3. Fare clic su **Dashboard** sopra e **informazioni di connessione** nella parte inferiore. Prendere nota delle due stringhe di connessione.

    ![Dashboard di coda nel portale di Azure][32]

### <a name="create-the-event-processor"></a>Creare processore di eventi

1. Soluzione Visual Studio corrente, fare clic su **File**per creare un progetto Visual c# Windows utilizzando il modello di progetto **Applicazione Console** , > **Aggiungi** > **Nuovo progetto**. Assicurarsi che la versione di .NET Framework sia 4.5.1 o versioni successive. Nome del progetto **ProcessDeviceToCloudMessages**e fare clic su **OK**.

    ![Nuovo progetto di Visual Studio][10]

2. In Esplora soluzioni fare clic sul progetto **ProcessDeviceToCloudMessages** e quindi fare clic su **Gestisci pacchetti NuGet**. Viene visualizzata la finestra di dialogo **Gestione pacchetti NuGet** .

3. Cercare **WindowsAzure.ServiceBus**, fare clic su **installazione**e accettare le condizioni di utilizzo. Questa operazione Scarica, installa e aggiunge un riferimento il [pacchetto di Azure servizio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus), con tutte le relative dipendenze.

4. Cercare **Microsoft.Azure.ServiceBus.EventProcessorHost**, fare clic su **installazione**e accettare le condizioni di utilizzo. Questa operazione Scarica, installa e aggiunge un riferimento a [Azure servizio Bus evento Hub - pacchetto EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con tutte le relative dipendenze.

5. Fare clic sul progetto **ProcessDeviceToCloudMessages** , fare clic su **Aggiungi**e quindi fare clic su **classe**. Denominare la nuova classe **StoreEventProcessor**e quindi fare clic su **OK** per creare la classe.

6. Nella parte superiore del file StoreEventProcessor.cs, aggiungere le istruzioni seguenti:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Sostituire il codice riportato di seguito per il corpo del corso:

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    La classe **EventProcessorHost** chiama questa classe per elaborare i messaggi di dispositivo nel cloud ricevuti dall'IoT Hub. Il codice di questa classe implementata la logica per archiviare i messaggi in modo affidabile in un contenitore di blob e inoltrare messaggi interattivi alla coda Bus di servizio.

    Metodo **OpenAsync** consente di inizializzare la variabile **currentBlockInitOffset** , che registra l'offset corrente del primo messaggio letto da questo processore evento. Tenere presente che ogni processore è responsabile di una singola partizione.

    Il metodo **ProcessEventsAsync** riceve un batch di messaggi dall'IoT Hub e li elabora come indicato di seguito: invia messaggi interattivi alla coda Bus di servizio e aggiunge il buffer di memoria denominato **toAppend**messaggi punto dati. Se il buffer di memoria raggiunge il limite di 4 MB o le finestre di ora la de-duplicazione trascorre (un'ora dopo un punto di controllo in questa esercitazione), l'applicazione attiva una verifica.

    Il metodo **AppendAndCheckpoint** genera prima di tutto una blockId per il blocco da aggiungere. Spazio di archiviazione Azure richiede tutti bloccare ID per avere la stessa lunghezza, in modo che il metodo aggiunge riempimenti offset con zeri iniziali - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Quindi, se un blocco con l'ID è già nel blob, il metodo verrà sovrascritta con il contenuto corrente del buffer.

    > [AZURE.NOTE] Per semplificare il codice, questa esercitazione utilizza un blob singolo per partizione per archiviare i messaggi. Una soluzione reale implementare file in sequenza mediante la creazione di file aggiuntivi dopo un certo periodo di tempo o quando si raggiunge una certa dimensione. Tenere presente che un blob Azure blocco può contenere al massimo 195 GB di dati.

8. In classe **programma** aggiungere la seguente istruzione **using** nella parte superiore:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modificare il metodo **principali** della classe **Program** come indicato di seguito. Sostituire **{stringa di connessione hub iot}** con la stringa di connessione **iothubowner** da esercitazione [Introduzione IoT Hub] . Sostituire la stringa di connessione di spazio di archiviazione con la stringa di connessione annotato all'inizio di questa sezione. Sostituire la stringa di connessione Bus di servizio con autorizzazioni **Invia** per coda denominato **d2ctutorial** annotato all'inizio di questa sezione:

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] Per semplicità, questa esercitazione viene utilizzata una singola istanza della classe [EventProcessorHost] . Per ulteriori informazioni, vedere la [Guida alla programmazione di eventi hub].

## <a name="receive-interactive-messages"></a>Ricevere messaggi interattivi
In questa sezione, scrivere un'applicazione di console di Windows che riceve i messaggi interattivi dalla coda Bus di servizio. Per ulteriori informazioni sulla progettazione di una soluzione utilizzando Bus di servizio, vedere [creazione di applicazioni a più livelli con Bus di servizio][].

1. Soluzione Visual Studio corrente, creare un progetto Visual c# Windows utilizzando il modello di progetto **Applicazione Console** . Nome del progetto **ProcessD2CInteractiveMessages**.

2. In Esplora soluzioni fare clic sul progetto **ProcessD2CInteractiveMessages** e quindi fare clic su **Gestisci pacchetti NuGet**. Questa operazione visualizza la finestra di **Gestione di pacchetti NuGet** .

3. Cercare **WindowsAzure.ServiceBus**, fare clic su **installazione**e accettare le condizioni di utilizzo. Questa operazione Scarica, installa e aggiunge un riferimento al [Bus di servizio Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), con tutte le relative dipendenze.

4. Aggiungere le seguenti istruzioni **using** nella parte superiore del file **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Infine, aggiungere le seguenti righe al metodo **Main** . Sostituire la stringa di connessione con **ascoltare** le autorizzazioni per la coda denominato **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  In Visual Studio, in Esplora soluzioni fare doppio clic la soluzione e selezionare **Imposta progetti di avvio**. Selezionare **più progetti di avvio**, quindi selezionare **Avvia** l'azione per i progetti **ProcessDeviceToCloudMessages**, **SimulatedDevice**e **ProcessD2CInteractiveMessages** .

2.  Premere **F5** per avviare le tre applicazioni console. L'applicazione **ProcessD2CInteractiveMessages** deve elaborare ogni interattivo messaggio inviato dall'applicazione **SimulatedDevice** .

  ![Tre applicazioni console][50]

> [AZURE.NOTE] Per visualizzare gli aggiornamenti nel blob, potrebbe essere necessario ridurre la costante **MAX_BLOCK_SIZE** nella classe **StoreEventProcessor** su un valore inferiore, ad esempio **1024**. Questa modifica è utile perché è necessario del tempo per raggiungere il limite di dimensioni blocco con i dati inviati dal dispositivo simulato. Con una dimensione inferiore blocco, non è necessario attendere così tanto tempo per visualizzare il blob viene creato e aggiornato. Tuttavia, utilizzando una dimensione maggiore per il blocco diventa l'applicazione maggiore scalabilità.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si appreso elaborare in modo affidabile coordinata e interattivi dispositivo nel cloud messaggi utilizzando la classe [EventProcessorHost] .

[Come inviare messaggi cloud per dispositivo con IoT Hub] [ lnk-c2d] viene illustrato come inviare messaggi per i dispositivi dal back-end.

Per esempi di soluzioni-to-end complete che utilizzano IoT Hub, vedere [Azure IoT Suite][lnk-suite].

Per ulteriori informazioni sullo sviluppo di soluzioni con IoT Hub, vedere la [Guida per gli sviluppatori di IoT Hub].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Archiviazione blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Dati di Azure Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Coda Bus di servizio]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guida per sviluppatori di IoT Hub Azure - dispositivo nel cloud]: iot-hub-devguide-messaging.md

[Spazio di archiviazione Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus di servizio Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guida per gli sviluppatori IoT Hub]: iot-hub-devguide.md
[Guida introduttiva a IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Centro per sviluppatori di Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Informazioni sull'archiviazione Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Guida introduttiva a hub di evento]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Scalabilità lo spazio di archiviazione Azure linee guida]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Hub evento programmazione manuale]: ../event-hubs/event-hubs-programming-guide.md
[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Creare applicazioni a più livelli con Bus di servizio]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
