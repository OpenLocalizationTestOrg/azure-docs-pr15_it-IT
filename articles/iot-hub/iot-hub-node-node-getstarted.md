<properties
    pageTitle="Azure IoT Hub per Node introduzione | Microsoft Azure"
    description="IoT Hub Azure con Node Guida introduttiva esercitazione. Utilizzare Azure IoT Hub e Node con Microsoft Azure IoT SDK per implementare una soluzione Internet di elementi."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Guida introduttiva a Azure IoT Hub per Node

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al termine di questa esercitazione, sono disponibili tre applicazioni console Node:

* **CreateDeviceIdentity.js**, che consente di creare una chiave di protezione associato per connettere il dispositivo simulato e identità di dispositivo.
* **ReadDeviceToCloudMessages.js**, che consente di visualizzare telemetria inviato dal dispositivo simulato.
* **SimulatedDevice.js**, che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza e inviare un messaggio di telemetria ogni secondo mediante il protocollo AMQP.

> [AZURE.NOTE] L'articolo [IoT Hub SDK] [ lnk-hub-sdks] vengono fornite informazioni SDK diversi che è possibile utilizzare per creare entrambe le applicazioni per eseguire analisi sui dispositivi e le soluzioni di back-end.

Per completare questa esercitazione, è necessario quanto segue:

+ Versione Node 0.10.x o versione successiva.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

È stata creata l'hub IoT. È necessario hostname IoT Hub e la stringa di connessione IoT Hub che è necessario completare il resto di questa esercitazione.

## <a name="create-a-device-identity"></a>Creare un'identità di dispositivo

In questa sezione, si crea un'app console Node che consente di creare un'identità di dispositivo del Registro di sistema di identità l'hub IoT. Un dispositivo non riesce a connettersi a IoT hub a meno che non dispone di una voce del Registro di sistema di identità di dispositivo. Vedere la sezione **Del Registro di sistema di dispositivo identità** della [Guida per gli sviluppatori Hub IoT] [ lnk-devguide-identity] per ulteriori informazioni. Quando si esegue questa applicazione console, genera un ID univoco del dispositivo e una chiave che il dispositivo è possibile utilizzare per identificarsi durante l'invio di messaggi di dispositivo nel cloud a IoT Hub.

1. Creare una nuova cartella vuota denominata **createdeviceidentity**. Nella cartella **createdeviceidentity** creare un file di package.json usando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **createdeviceidentity** , eseguire il comando seguente per installare il pacchetto di servizio SDK **azure iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Con un editor di testo, creare un file **CreateDeviceIdentity.js** nella cartella **createdeviceidentity** .

4. Aggiungere quanto segue `require` istruzione all'inizio del file **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Aggiungere il codice seguente al file **CreateDeviceIdentity.js** e sostituire il valore di segnaposto con la stringa di connessione per l'hub IoT creata nella sezione precedente: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Aggiungere il codice riportato di seguito per creare una definizione di dispositivo nel dispositivo identità Registro di sistema il tuo fulcro IoT. Questo codice viene creato un dispositivo se l'id del dispositivo non esiste nel Registro di sistema, in caso contrario restituisce il tasto del dispositivo esistente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Salvare e chiudere il file **CreateDeviceIdentity.js** .

8. Per eseguire l'applicazione **createdeviceidentity** , eseguire il comando seguente al prompt dei comandi nella cartella createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Prendere nota **dell'id dispositivo** e di **chiave dispositivo**. È necessario questi valori in un secondo momento quando si crea un'applicazione che si connette a IoT Hub come dispositivo.

> [AZURE.NOTE] Registro di sistema di identità IoT Hub archivia solo identità di dispositivo per abilitare l'accesso sicuro a hub. Memorizza ID e i tasti da utilizzare come le credenziali di sicurezza e un contrassegno di attivazione/disattivazione che è possibile utilizzare per disattivare l'accesso per un singolo dispositivo. Se l'applicazione necessita memorizzare altri metadati specifico di dispositivo, deve utilizzare un archivio specifico dell'applicazione. Fare riferimento alla [Guida per gli sviluppatori Hub IoT] [ lnk-devguide-identity] per ulteriori informazioni.

## <a name="receive-device-to-cloud-messages"></a>Ricevere messaggi di dispositivo nel cloud

In questa sezione, si crea un'app console Node che legge i messaggi di dispositivo nel cloud dall'IoT Hub. Un hub IoT espone un [Evento hub][lnk-event-hubs-overview]-endpoint compatibile che consentono di leggere i messaggi di dispositivo nel cloud. Per semplicità, questa esercitazione viene creato un lettore di base che non è adatto per una distribuzione alta velocità. [Elaborare i messaggi di dispositivo nel cloud] [ lnk-process-d2c-tutorial] esercitazione viene illustrato come elaborare i messaggi di dispositivo nel cloud in scala. [Guida introduttiva a evento hub] [ lnk-eventhubs-tutorial] esercitazione vengono fornite ulteriori informazioni per elaborare i messaggi da hub di eventi e alle endpoint compatibile con Hub IoT Hub evento.

> [AZURE.NOTE] Endpoint compatibile con evento Hub per la lettura dei messaggi di dispositivo nel cloud sempre il protocollo AMQP.

1. Creare una nuova cartella vuota denominata **readdevicetocloudmessages**. Nella cartella **readdevicetocloudmessages** creare un file di package.json usando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **readdevicetocloudmessages** , eseguire il comando seguente per installare il pacchetto di **azure hub di evento** :

    ```
    npm install azure-event-hubs --save
    ```

3. Con un editor di testo, creare un file **ReadDeviceToCloudMessages.js** nella cartella **readdevicetocloudmessages** .

4. Aggiungere quanto segue `require` istruzioni all'inizio del file **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Aggiungere la dichiarazione di variabili seguente e sostituire il valore di segnaposto con la stringa di connessione per l'hub IoT:

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Aggiungere le due funzioni che output alla console di stampa:

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Aggiungere il codice seguente per creare **EventHubClient**, aprire la connessione a un centro di raccolta IoT e creare un ricevitore per ogni partizione. L'applicazione utilizza un filtro per la creazione di un ricevitore, in modo che il destinatario legge solo i messaggi inviati a IoT Hub dopo il ricevitore viene avviata l'esecuzione. Questo filtro è utile in un ambiente di testing in modo da mostrare solo la serie corrente di messaggi. In un ambiente di produzione, il codice necessario verificare l'elaborazione di tutti i messaggi - informazioni su [come elaborare i messaggi di dispositivo nel cloud IoT Hub] [ lnk-process-d2c-tutorial] esercitazioni per ulteriori informazioni:

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Salvare e chiudere il file **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Creare un'app dispositivo simulato

In questa sezione, si crea un'app console Node che simula un dispositivo che invia messaggi dispositivo nel cloud a un hub IoT.

1. Creare una nuova cartella vuota denominata **simulateddevice**. Nella cartella **simulateddevice** creare un file di package.json usando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **simulateddevice** , eseguire il comando seguente per installare il dispositivo SDK **azure iot dispositivi** e pacchetto **iot di azure-amqp di dispositivo** :

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Con un editor di testo, creare un nuovo file **SimulatedDevice.js** nella cartella **simulateddevice** .

4. Aggiungere quanto segue `require` istruzioni all'inizio del file **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Aggiungere una variabile **connectionString** e usarlo per creare un client di dispositivo. Sostituire **{youriothostname}** con il nome dell'hub IoT creata la sezione *creare un IoT Hub* . Sostituire **{yourdevicekey}** con il valore di chiave dispositivo generate nella sezione *creare un'identità di dispositivo* :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Aggiungere la funzione seguente per visualizzare l'output dall'applicazione:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Creare un callback e utilizzare la funzione **setInterval** per inviare un nuovo messaggio per il tuo fulcro IoT ogni secondo:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. Aprire la connessione a un centro di raccolta IoT e iniziare a inviare messaggi:

    ```
    client.open(connectCallback);
    ```

9. Salvare e chiudere il file **SimulatedDevice.js** .

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio un backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo][lnk-transient-faults].


## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. In un prompt dei comandi nella cartella **readdevicetocloudmessages** , eseguire il seguente comando per iniziare il monitoraggio il tuo fulcro IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Applicazione client di servizio Node IoT Hub di controllare i messaggi di dispositivo nel cloud][7]

2. In un prompt dei comandi nella cartella **simulateddevice** , eseguire il seguente comando per avviare l'invio di dati di telemetria per il tuo fulcro IoT:

    ```
    node SimulatedDevice.js
    ```

    ![Applicazione client di Node IoT Hub per dispositivi per inviare messaggi di dispositivo nel cloud][8]

3. Il riquadro **l'uso** del [portale di Azure] [ lnk-portal] Mostra il numero di messaggi inviati a hub:

    ![Azure portale utilizzo riquadro mostra il numero di messaggi inviati a IoT Hub][43]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è configurato un hub IoT nuovo nel portale di Azure, creata e identità di un dispositivo nel Registro di sistema identità dell'hub. Questa identità dispositivo usato per abilitare l'app dispositivo simulato l'invio di messaggi di dispositivo nel cloud hub. È inoltre creata un'applicazione che consente di visualizzare i messaggi ricevuti dall'hub. 

Guida introduttiva a IoT Hub e per esplorare altri scenari IoT, vedere:

- [Connessione del dispositivo][lnk-connect-device]
- [Guida introduttiva di gestione di dispositivi][lnk-device-management]
- [Guida introduttiva a SDK di Gateway IoT][lnk-gateway-SDK]

Per informazioni su come estendere la soluzione IoT ed elaborare messaggi dispositivo nel cloud in scala, vedere [elaborare i messaggi di dispositivo nel cloud] [ lnk-process-d2c-tutorial] esercitazione.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
