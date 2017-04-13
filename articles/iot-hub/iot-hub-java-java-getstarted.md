<properties
    pageTitle="Azure IoT Hub per Java introduttiva | Microsoft Azure"
    description="IoT Hub Azure con linguaggio Guida introduttiva esercitazione. Utilizzare Azure IoT Hub e Java con Microsoft Azure IoT SDK per implementare una soluzione Internet di elementi."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>Guida introduttiva di Azure IoT Hub per Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione, sono disponibili tre applicazioni console Java:

* **creare identità di dispositivo**, che consente di creare una chiave di protezione associato per connettere il dispositivo simulato e identità di dispositivo.
* **messaggi di d2c già letti**, che consente di visualizzare telemetria inviato dal dispositivo simulato.
* **dispositivo simulato**, che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza e inviare un messaggio di telemetria ogni secondo mediante il protocollo AMQP.

> [AZURE.NOTE] L'articolo [IoT Hub SDK] [ lnk-hub-sdks] vengono fornite informazioni SDK diversi che è possibile utilizzare per creare entrambe le applicazioni per eseguire analisi sui dispositivi e le soluzioni di back-end.

Per completare questa esercitazione, è necessario quanto segue:

+ SE Java 8. <br/> [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare Java per questa esercitazione su Windows o Linux.

+ Maven 3.  <br/> [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare Maven per questa esercitazione su Windows o Linux.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Come ultimo passaggio, prendere nota del valore della **chiave primaria** e quindi fare clic su **messaggistica**. In e il **messaggistica** , prendere nota **dell'Hub evento compatibile con nome** e l' **endpoint Hub evento compatibile con**. Quando si crea un'applicazione di **messaggi di d2c lettura** , è necessario questi tre valori.

![Azure blade portale IoT Hub messaggistica][6]

È stato creato il tuo fulcro IoT e disporre hostname IoT Hub, la stringa di connessione IoT Hub, chiave primaria Hub IoT, Hub evento compatibile con nome ed endpoint compatibile con evento Hub che è necessario completare questa esercitazione.

## <a name="create-a-device-identity"></a>Creare un'identità di dispositivo

In questa sezione, si crea un'app di console di linguaggio che crea una nuova identità di dispositivo del Registro di sistema di identità l'hub IoT. Un dispositivo non riesce a connettersi a IoT hub a meno che non dispone di una voce del Registro di sistema di identità di dispositivo. Vedere la sezione **Del Registro di sistema di dispositivo identità** della [Guida per gli sviluppatori Hub IoT] [ lnk-devguide-identity] per ulteriori informazioni. Quando si esegue questa applicazione console, genera un ID univoco del dispositivo e una chiave che il dispositivo è possibile utilizzare per identificarsi durante l'invio di messaggi di dispositivo nel cloud a IoT Hub.

1. Creare una nuova cartella vuota denominata iot java-get avviato. Nella cartella iot java-get avviato creare un nuovo progetto Maven denominato **identità di dispositivo creare** utilizzando il comando seguente al prompt dei comandi. Si che tratta di un comando singolo, lungo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi, passare alla nuova cartella di identità di dispositivo creare.

3. Con un editor di testo, aprire il file pom.xml nella cartella identità di dispositivo crea e aggiungere il nodo **dipendenze** dipendenza seguente. In questo modo è possibile utilizzare il pacchetto sdk di servizio iothub nell'applicazione:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Salvare e chiudere il file pom.xml.

5. Con un editor di testo, aprire il file create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Aggiungere le seguenti istruzioni **importare** il file:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Aggiungere le seguenti variabili a livello di classe alla classe **App** , la sostituzione **{yourhubconnectionstring}** con il valore l'indicato in precedenza:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Modificare la firma del metodo **principale** per includere le eccezioni come indicato di seguito:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Aggiungere il codice seguente come corpo del metodo **principale** . Questo codice viene creato un dispositivo denominato *javadevice* nel Registro di sistema identità IoT Hub se non esiste già. Quindi, Visualizza l'id di dispositivo e chiave che è necessario in seguito:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Salvare e chiudere il file App.java.

11. Per creare l'applicazione di **identità di dispositivo crea** utilizzando Maven, eseguire il comando seguente al prompt dei comandi nella cartella identità di dispositivo crea:

    ```
    mvn clean package -DskipTests
    ```

12. Per eseguire l'applicazione di **identità di dispositivo crea** utilizzando Maven, eseguire il comando seguente al prompt dei comandi nella cartella identità di dispositivo crea:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prendere nota **dell'id dispositivo** e di **chiave dispositivo**. Sarà necessario sono disponibili più avanti quando si crea un'applicazione che si connette a IoT Hub come dispositivo.

> [AZURE.NOTE] Registro di sistema di identità IoT Hub archivia solo identità di dispositivo per abilitare l'accesso sicuro a hub. Memorizza ID e i tasti da utilizzare come le credenziali di sicurezza e un contrassegno di attivazione/disattivazione che è possibile utilizzare per disattivare l'accesso per un singolo dispositivo. Se l'applicazione necessita memorizzare altri metadati specifico di dispositivo, deve utilizzare un archivio specifico dell'applicazione. Fare riferimento alla [Guida per gli sviluppatori Hub IoT] [ lnk-devguide-identity] per ulteriori informazioni.

## <a name="receive-device-to-cloud-messages"></a>Ricevere messaggi di dispositivo nel cloud

In questa sezione, si crea un'app di console di linguaggio che legge i messaggi di dispositivo nel cloud dall'IoT Hub. Un hub IoT espone un [Evento Hub][lnk-event-hubs-overview]-endpoint compatibile che consentono di leggere i messaggi di dispositivo nel cloud. Per semplicità, questa esercitazione viene creato un lettore di base che non è adatto per una distribuzione alta velocità. [Elaborare i messaggi di dispositivo nel cloud] [ lnk-process-d2c-tutorial] esercitazione viene illustrato come elaborare i messaggi di dispositivo nel cloud in scala. [Guida introduttiva a evento hub] [ lnk-eventhubs-tutorial] esercitazione vengono fornite ulteriori informazioni per elaborare i messaggi da hub di eventi e alle endpoint compatibile con Hub IoT Hub evento.

> [AZURE.NOTE] Endpoint compatibile con evento Hub per la lettura dei messaggi di dispositivo nel cloud sempre il protocollo AMQP.

1. Nella cartella iot java-get avviato creata nella sezione *creare identità di un dispositivo* , creare un nuovo progetto Maven denominato **messaggi di d2c lettura** utilizzando il comando seguente al prompt dei comandi. Si che tratta di un comando singolo, lungo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi, passare alla nuova cartella di messaggi di d2c lettura.

3. Con un editor di testo, aprire il file pom.xml nella cartella messaggi d2c di lettura e aggiunta della dipendenza seguente al livello di nodo **dipendenze** . In questo modo è possibile utilizzare il pacchetto eventhubs client nell'applicazione di leggere l'endpoint Hub evento compatibile con:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. Con un editor di testo, aprire il file read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Aggiungere le seguenti istruzioni **importare** il file:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Aggiungere le seguenti variabili a livello di classe alla classe **App** . Sostituire **{youriothubkey}**, **{youreventhubcompatibleendpoint}**e **{youreventhubcompatiblename}** con i valori indicati in precedenza:

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Aggiungere il metodo **receiveMessages** seguenti per la classe **App** . Questo metodo crea un'istanza di **EventHubClient** per connettersi all'endpoint Hub evento compatibile con e quindi in modo asincrono crea un'istanza di **PartitionReceiver** per leggere da una partizione di evento Hub. Viene eseguito un ciclo continuo e consente di stampare i dettagli del messaggio finché non termina con l'applicazione.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Questo metodo utilizza un filtro quando viene creato il destinatario in modo che il destinatario legge solo i messaggi inviati a IoT Hub dopo che il ricevitore inizia in esecuzione. Questo è utile in un ambiente di testing in modo da visualizzare la serie corrente di messaggi. In un ambiente di produzione il codice necessario assicurarsi che lo elabora tutti i messaggi, vedere [la modalità di elaborazione dei messaggi di dispositivo nel cloud IoT Hub] [ lnk-process-d2c-tutorial] esercitazioni per altre informazioni.

9. Modificare la firma del metodo **principale** per includere l'eccezione come indicato di seguito:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Aggiungere il codice seguente al metodo **principale** in classe **App** . Questo codice vengono create due istanze **EventHubClient** e **PartitionReceiver** ed è possibile chiudere l'applicazione al termine dell'elaborazione dei messaggi:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Questo codice si presuppone creato il tuo fulcro IoT nel livello (gratuito) F1. Un hub IoT gratuito include due partizioni denominate "0" e "1".

11. Salvare e chiudere il file App.java.

12. Per creare l'applicazione di **messaggi di d2c lettura** utilizzando Maven, eseguire il comando seguente al prompt dei comandi nella cartella messaggi d2c di lettura:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Creare un'app dispositivo simulato

In questa sezione, si crea un'app di console di linguaggio che simula un dispositivo che invia messaggi dispositivo nel cloud a un hub IoT.

1. Nella cartella iot java-get avviato creata nella sezione *creare identità di un dispositivo* , creare un nuovo progetto Maven denominato **dispositivo simulata** con il comando seguente al prompt dei comandi. Si che tratta di un comando singolo, lungo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi, spostarsi la nuova cartella dispositivo simulata.

3. Con un editor di testo, aprire il file pom.xml nella cartella dispositivo simulato e aggiungere le dipendenze seguenti al livello di nodo **dipendenze** . In questo modo è possibile utilizzare il pacchetto client di java iothub nell'applicazione di comunicare con il tuo fulcro IoT e serializzare gli oggetti Java JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. Con un editor di testo, aprire il file simulated-device\src\main\java\com\mycompany\app\App.java.

6. Aggiungere le seguenti istruzioni **importare** il file:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Aggiungere le seguenti variabili a livello di classe alla classe **App** , sostituendo **{youriothubname}** con il nome dell'hub IoT e **{yourdevicekey}** con il valore di chiave dispositivo generate nella sezione *creare un'identità di dispositivo* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Questa applicazione di esempio utilizza la variabile **protocollo** quando si crea un oggetto **DeviceClient** . È possibile usare il protocollo HTTP o AMQP per comunicare con IoT Hub.

8. Aggiunta di che classe di **TelemetryDataPoint** all'interno della classe **App** per specificare i dati di telemetria che dispositivo invia il tuo fulcro IoT nidificata le operazioni seguenti:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Aggiungere la seguente classe **EventCallback** nidificata all'interno della classe **App** per visualizzare lo stato di riconoscimento che hub IoT restituisce durante l'elaborazione di un messaggio da dispositivo simulato. Questo metodo notifica al thread principale dell'applicazione quando il messaggio è stato elaborato:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Aggiungere la seguente classe **MessageSender** nidificata all'interno della classe **App** . Il metodo **run** in questa classe genera dati di telemetria di esempio per inviare il tuo fulcro IoT e che venga di conferma prima di inviare il messaggio successivo:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Questo metodo invia un nuovo messaggio di dispositivo nel cloud un secondo dopo l'hub IoT riconosce il messaggio precedente. Il messaggio contiene un oggetto serializzato JSON con l'ID della periferica e un numero casuale per simulare un sensore di velocità del vento.

11. Sostituire il metodo **principale** con il codice seguente viene creato un thread per inviare messaggi di dispositivo nel cloud per il tuo fulcro IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Salvare e chiudere il file App.java.

13. Per creare l'applicazione **dispositivo simulato** utilizzando Maven, eseguire il comando seguente al prompt dei comandi nella cartella simulata dispositivo:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio un backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo][lnk-transient-faults].

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. In un prompt dei comandi nella cartella d2c di lettura, eseguire il seguente comando per iniziare il monitoraggio prima partizione l'hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Applicazione client di servizio linguaggio IoT Hub di controllare i messaggi di dispositivo nel cloud][7]

2. In un prompt dei comandi nella cartella dispositivo simulato, eseguire il seguente comando per avviare l'invio di dati di telemetria per il tuo fulcro IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Applicazione client di linguaggio IoT Hub per dispositivi per inviare messaggi di dispositivo nel cloud][8]

3. Il riquadro **l'uso** del [portale di Azure] [ lnk-portal] Mostra il numero di messaggi inviati a hub:

    ![Azure portale utilizzo riquadro mostra il numero di messaggi inviati a IoT Hub][43]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è configurato un hub IoT nuovo nel portale di Azure, creata e identità di un dispositivo nel Registro di sistema identità dell'hub. Questa identità dispositivo usato per abilitare l'app dispositivo simulato l'invio di messaggi di dispositivo nel cloud hub. È inoltre creata un'applicazione che consente di visualizzare i messaggi ricevuti dall'hub. 

Guida introduttiva a IoT Hub e per esplorare altri scenari IoT vedere:

- [Connessione del dispositivo][lnk-connect-device]
- [Guida introduttiva di gestione di dispositivi][lnk-device-management]
- [Guida introduttiva a SDK di Gateway IoT][lnk-gateway-SDK]

Per informazioni su come estendere la soluzione IoT ed elaborare messaggi dispositivo nel cloud in scala, vedere [elaborare i messaggi di dispositivo nel cloud] [ lnk-process-d2c-tutorial] esercitazione.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/