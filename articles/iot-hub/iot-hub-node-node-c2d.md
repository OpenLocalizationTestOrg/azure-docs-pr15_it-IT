<properties
    pageTitle="Inviare messaggi cloud per dispositivo con IoT Hub | Microsoft Azure"
    description="Seguire questa esercitazione per imparare a inviare messaggi cloud per dispositivo con Azure IoT Hub linguaggio."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Esercitazione: Come inviare messaggi cloud per dispositivo con IoT Hub e Node

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduzione

Azure IoT Hub è un servizio completamente gestito che consente di attivare comunicazioni bidirezionale sicura e affidabile tra milioni di dispositivi IoT e la fine di un'applicazione di nuovo. Esercitazioni [introduttive IoT Hub] viene illustrato come creare un hub IoT, effettuare il provisioning di identità di un dispositivo al suo interno e codice un dispositivo simulato che invia messaggi dispositivo nel cloud.

In questa esercitazione si basa su [Introduzione IoT Hub]. Verrà illustrata la procedura per:

- Dall'applicazione cloud back-end, inviare messaggi cloud per dispositivo a una singola periferica tramite IoT Hub.
- Ricevere messaggi cloud al dispositivo in un dispositivo.
- Dal cloud applicazione back-end, richiedere la conferma di recapito (*commenti e suggerimenti*) per i messaggi inviati a un dispositivo dall'IoT Hub.

Sono disponibili ulteriori informazioni sui messaggi cloud al dispositivo la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

Al termine di questa esercitazione, eseguire due applicazioni console Node:

* **SimulatedDevice**, una versione modificata dell'app create in [Guida introduttiva a IoT Hub], che si connette a un centro di raccolta IoT e riceve messaggi cloud al dispositivo.
* **SendCloudToDeviceMessage**, viene inviato un messaggio cloud al dispositivo al dispositivo simulato tramite IoT Hub e quindi riceve la conferma di recapito.

> [AZURE.NOTE] IoT Hub supporta SDK molte piattaforme di dispositivi e lingue (inclusi C, Java e Javascript) tramite Azure IoT dispositivo SDK. Per istruzioni dettagliate su come connettere il dispositivo di codice dell'esercitazione e in genere di Azure IoT Hub, vedere il [Centro per sviluppatori di Azure IoT].

Per completare questa esercitazione, è necessario quanto segue:

+ Versione Node 0.10.x o versione successiva.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

## <a name="receive-messages-on-the-simulated-device"></a>Ricevere messaggi sul dispositivo simulato

In questa sezione è modificare l'applicazione dispositivo simulato è stato creato in [Guida introduttiva a IoT Hub] a ricevere messaggi cloud per dispositivo dall'hub IoT.

1. Con un editor di testo, aprire il file SimulatedDevice.js.

2. Modificare la funzione **connectCallback** per gestire i messaggi inviati dall'IoT Hub. In questo esempio, il dispositivo richiama sempre la funzione di **completamento** per la notifica IoT Hub che il messaggio è stato elaborato. La nuova versione della funzione **connectCallback** è simile alla seguente:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Se si utilizza HTTP invece che tramite MQTT o AMQP come trasporto, l'istanza di **DeviceClient** verifica la presenza di messaggi provenienti da raramente IoT Hub (inferiore a 25 minuti). Per ulteriori informazioni sulle differenze tra il supporto MQTT, AMQP e HTTP e Hub IoT limitazione, vedere la [Guida per gli sviluppatori Hub IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Inviare un messaggio cloud al dispositivo

In questa sezione, si crea un'app console Node che invia messaggi cloud al dispositivo all'app dispositivo simulato. Il dispositivo Id del dispositivo che aggiunti nell'esercitazione [Introduzione IoT Hub] è necessario. È necessario anche la stringa di connessione per l'hub IoT che è possibile trovare nel [portale di Azure].

1. Creare una cartella vuota denominata **sendcloudtodevicemessage**. Nella cartella **sendcloudtodevicemessage** creare un file di package.json usando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **sendcloudtodevicemessage** , eseguire il comando seguente per installare il pacchetto di **azure iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Con un editor di testo, creare un nuovo file **SendCloudToDeviceMessage.js** nella cartella **sendcloudtodevicemessage** .

4. Aggiungere quanto segue `require` istruzioni all'inizio del file **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Aggiungere il codice seguente al file **SendCloudToDeviceMessage.js** . Sostituire il valore di segnaposto stringa di connessione con la stringa di connessione per l'hub IoT creata in esercitazione [Introduzione IoT Hub] . Sostituire il segnaposto di dispositivo di destinazione con l'id di dispositivo del dispositivo che aggiunti nell'esercitazione [Introduzione IoT Hub] :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Aggiungere la funzione seguente per stampare i risultati di un'operazione alla console:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Aggiungere la funzione seguente per stampare i messaggi di feedback recapito alla console:

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Aggiungere il codice seguente per inviare un messaggio al dispositivo e gestire il messaggio di commenti e suggerimenti quando il dispositivo riconosce il messaggio cloud al dispositivo:

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Salvare e chiudere il file **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. Al prompt nella cartella **simulateddevice** , eseguire il seguente comando per inviare telemetria a IoT Hub e per ascoltare i messaggi di cloud al dispositivo:

    ```
    node SimulatedDevice.js 
    ```

    ![Eseguire l'app dispositivo simulato][img-simulated-device]

2. In un prompt dei comandi nella cartella **sendcloudtodevicemessage** , eseguire il seguente comando per inviare un messaggio cloud al dispositivo e attendere che il feedback conferma:

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Eseguire l'app per inviare il comando c2d][img-send-command]

    > [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo].

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si appreso inviare e ricevere messaggi cloud al dispositivo. 

Per esempi di soluzioni-to-end complete che utilizzano IoT Hub, vedere [Azure IoT famiglia di prodotti].

Per ulteriori informazioni sullo sviluppo di soluzioni con IoT Hub, vedere la [Guida per gli sviluppatori di IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Guida introduttiva a IoT Hub]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guida per gli sviluppatori IoT Hub]: iot-hub-devguide.md
[Centro per sviluppatori di Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Gestione degli errori temporaneo]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portale di Azure]: https://portal.azure.com
[Famiglia di prodotti IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/