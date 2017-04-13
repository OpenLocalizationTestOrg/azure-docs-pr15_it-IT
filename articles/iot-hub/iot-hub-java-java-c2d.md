<properties
    pageTitle="Inviare messaggi cloud per dispositivo con IoT Hub | Microsoft Azure"
    description="Seguire questa esercitazione per imparare a inviare messaggi cloud per dispositivo con Azure IoT Hub linguaggio."
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
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>Esercitazione: Come inviare messaggi cloud per dispositivo con IoT Hub e Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione

Azure IoT Hub è un servizio completamente gestito che consente di attivare comunicazioni bidirezionale sicura e affidabile tra milioni di dispositivi IoT e la fine di un'applicazione di nuovo. Esercitazioni [introduttive IoT Hub] viene illustrato come creare un hub IoT, effettuare il provisioning di identità di un dispositivo al suo interno e codice un dispositivo simulato che invia messaggi dispositivo nel cloud.

In questa esercitazione si basa su [Introduzione IoT Hub]. Verrà illustrata la procedura per:

- Dall'applicazione cloud back-end, inviare messaggi cloud per dispositivo a una singola periferica tramite IoT Hub.
- Ricevere messaggi cloud al dispositivo in un dispositivo.
- Dal cloud applicazione back-end, richiedere la conferma di recapito (*commenti e suggerimenti*) per i messaggi inviati a un dispositivo dall'IoT Hub.

Sono disponibili ulteriori informazioni sui messaggi cloud al dispositivo la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

Al termine di questa esercitazione, eseguire due applicazioni console Java:

* **dispositivo simulata**, una versione modificata dell'app create in [Guida introduttiva a IoT Hub], che si connette a un centro di raccolta IoT e riceve messaggi cloud al dispositivo.
* **inviare messaggi di c2d**, viene inviato un messaggio cloud al dispositivo al dispositivo simulato tramite IoT Hub e quindi riceve la conferma di recapito.

> [AZURE.NOTE] IoT Hub supporta SDK molte piattaforme di dispositivi e lingue (inclusi C, Java e Javascript) tramite Azure IoT dispositivo SDK. Per istruzioni dettagliate su come connettere il dispositivo di codice dell'esercitazione e in genere di Azure IoT Hub, vedere il [Centro per sviluppatori di Azure IoT].

Per completare questa esercitazione, è necessario quanto segue:

+ SE Java 8. <br/> [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare Java per questa esercitazione su Windows o Linux.

+ Maven 3.  <br/> [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare Maven per questa esercitazione su Windows o Linux.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

## <a name="receive-messages-on-the-simulated-device"></a>Ricevere messaggi sul dispositivo simulato

In questa sezione è modificare l'applicazione dispositivo simulato è stato creato in [Guida introduttiva a IoT Hub] a ricevere messaggi cloud per dispositivo dall'hub IoT.

1. Con un editor di testo, aprire il file simulated-device\src\main\java\com\mycompany\app\App.java.

2. Aggiungere la classe **MessageCallback** seguente come classe nidificata all'interno della classe **App** . Il metodo **execute** viene richiamato quando il dispositivo riceve un messaggio da IoT Hub. In questo esempio, il dispositivo sempre notifica hub di aver completato il messaggio:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modificare il metodo **principale** per creare un'istanza di **MessageCallback** e il metodo **setMessageCallback** prima di aprire il client come indicato di seguito:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Se si utilizza HTTP invece che tramite MQTT o AMQP come trasporto, l'istanza di **DeviceClient** verifica la presenza di messaggi provenienti da raramente IoT Hub (inferiore a 25 minuti). Per ulteriori informazioni sulle differenze tra il supporto MQTT, AMQP e HTTP e Hub IoT limitazione, vedere la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio cloud al dispositivo

In questa sezione, si crea un'app di console di linguaggio che invia messaggi cloud al dispositivo all'app dispositivo simulato. Il dispositivo Id del dispositivo che aggiunti nell'esercitazione [Introduzione IoT Hub] è necessario. È necessario anche la stringa di connessione per l'hub IoT che è possibile trovare nel [portale di Azure].

1. Creare un progetto Maven denominato **inviare messaggi di c2d** utilizzando il comando seguente al prompt dei comandi. Si che tratta di un comando singolo, lungo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Al prompt dei comandi, passare alla nuova cartella di inviare messaggi di c2d.

3. Con un editor di testo, aprire il file pom.xml nella cartella di inviare messaggi di c2d e aggiungere il nodo **dipendenze** dipendenza seguente. Aggiunta della dipendenza consente di utilizzare il pacchetto di **client del servizio di linguaggio iothub** nell'applicazione di comunicare con il servizio di hub IoT:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Salvare e chiudere il file pom.xml.

5. Con un editor di testo, aprire il file send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Aggiungere le seguenti istruzioni **importare** il file:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Aggiungere le seguenti variabili a livello di classe per la classe **App** sostituendo **{yourhubconnectionstring}** e **{yourdeviceid}** con i valori del indicato in precedenza:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. Sostituire il metodo **principale** con il codice seguente che collega il tuo fulcro IoT, invia un messaggio al dispositivo e attendere il completamento per un messaggio di conferma che il dispositivo ricevuto ed elaborato il messaggio:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo].

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. In un prompt dei comandi nella cartella dispositivo simulato, eseguire il seguente comando per avviare l'invio di telemetria per il tuo fulcro IoT e ascoltare le cloud per dispositivo i messaggi inviati da un centro di raccolta:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Eseguire l'app dispositivo simulato][img-simulated-device]

2. In un prompt dei comandi nella cartella di inviare messaggi di c2d, eseguire il seguente comando per inviare un messaggio cloud al dispositivo e attendere che una conferma di commenti e suggerimenti:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Eseguire il comando per inviare il messaggio cloud al dispositivo][img-send-command]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si appreso inviare e ricevere messaggi cloud al dispositivo. 

Per esempi di soluzioni-to-end complete che utilizzano IoT Hub, vedere [Azure IoT famiglia di prodotti].

Per ulteriori informazioni sullo sviluppo di soluzioni con IoT Hub, vedere la [Guida per gli sviluppatori di IoT Hub].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Guida introduttiva a IoT Hub]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guida per gli sviluppatori IoT Hub]: iot-hub-devguide.md
[Centro per sviluppatori di Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portale di Azure]: https://portal.azure.com
[Famiglia di prodotti IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/