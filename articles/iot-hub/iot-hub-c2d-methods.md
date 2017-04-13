<properties
 pageTitle="Utilizzare metodi diretti | Microsoft Azure"
 description="In questa esercitazione viene illustrato come utilizzare metodi diretti"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>Esercitazione: Utilizzare metodi diretti

## <a name="introduction"></a>Introduzione

Azure IoT Hub è un servizio completamente gestito consente affidabile e protette bidirezionale tra milioni di dispositivi IoT e un'applicazione di back-end. Esercitazioni precedenti ([Guida introduttiva a IoT Hub] e [inviare messaggi Cloud per dispositivo con IoT Hub]) illustrano funzionalità di base dispositivo nel cloud e cloud per dispositivo messaggistica di IoT Hub. IoT Hub offre anche la possibilità di richiamare metodi non permanenti nei dispositivi dal cloud. Metodi rappresentano un'interazione risposta convocazione con un dispositivo simile a una chiamata HTTP che sono esito positivo o negativo (dopo un timeout specificato dall'utente) comunicare all'utente che lo stato della chiamata. [Richiamare un metodo diretto in un dispositivo] [ lnk-devguide-methods] vengono descritti i metodi in modo più dettagliato e vengono fornite indicazioni su quando utilizzare metodi e messaggi cloud al dispositivo.

In questa esercitazione verrà illustrato a:

- Usare il portale di Azure per creare un hub IoT e identità di un dispositivo l'hub IoT.
- Creare un dispositivo simulato che dispone di un metodo diretto che può essere chiamato tramite nel cloud.
- Creare un'applicazione console che chiama un metodo diretto sul dispositivo simulato tramite l'hub IoT.

> [AZURE.NOTE] In questo momento metodi diretti sono accessibili solo da dispositivi che eseguono la connessione a IoT Hub mediante il protocollo MQTT. Consultare per [supportare MQTT] [ lnk-devguide-mqtt] articolo per istruzioni su come convertire app dispositivo esistente da utilizzare MQTT.

Al termine di questa esercitazione, sono disponibili due applicazioni console Node:

* **CallMethodOnDevice.js**, che chiama un metodo sul dispositivo simulato e viene visualizzata la risposta.
* **SimulatedDevice.js**, che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza e risponde al metodo chiamato dal cloud.

> [AZURE.NOTE] L'articolo [IoT Hub SDK] [ lnk-hub-sdks] vengono fornite informazioni SDK diversi che è possibile utilizzare per creare entrambe le applicazioni per eseguire analisi sui dispositivi e le soluzioni di back-end.

Per completare questa esercitazione, è necessario quanto segue:

+ Versione Node 0.10.x o versione successiva.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app dispositivo simulato

In questa sezione, si crea un'app console Node che risponde a un metodo chiamato dal cloud.

1. Creare una nuova cartella vuota denominata **simulateddevice**. Nella cartella **simulateddevice** creare un file di package.json usando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **simulateddevice** , eseguire il comando seguente per installare il dispositivo SDK **azure iot dispositivi** e pacchetto **iot di azure-mqtt di dispositivo** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor di testo, creare un nuovo file **SimulatedDevice.js** nella cartella **simulateddevice** .

4. Aggiungere quanto segue `require` istruzioni all'inizio del file **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Aggiungere una variabile **connectionString** e usarlo per creare un client di dispositivo. Sostituire **{stringa di connessione dispositivo}** con la stringa di connessione generati nella sezione *creare un'identità di dispositivo* :

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. Aggiungere la funzione seguente per implementare il metodo nel dispositivo:

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. Aprire la connessione a IoT hub e start inizializzare comunicare ascoltatore metodo:

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. Salvare e chiudere il file **SimulatedDevice.js** .

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio il tentativo di connessione), come indicato nell'articolo MSDN [Gestione guasto temporaneo][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Chiamare un metodo in un dispositivo

In questa sezione, si crea un'app console Node che chiama un metodo sul dispositivo simulato e quindi viene visualizzata la risposta.

1. Creare una nuova cartella vuota denominata **callmethodondevice**. Nella cartella **callmethodondevice** creare un file di package.json usando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **callmethodondevice** , eseguire il comando seguente per installare il pacchetto di **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Con un editor di testo, creare un file **CallMethodOnDevice.js** nella cartella **callmethodondevice** .

4. Aggiungere quanto segue `require` istruzioni all'inizio del file **CallMethodOnDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Aggiungere la dichiarazione di variabili seguente e sostituire il valore di segnaposto con la stringa di connessione per l'hub IoT:

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Creare il client per aprire la connessione a un centro di raccolta IoT.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Aggiungere la funzione seguente per richiamare il metodo di dispositivo e stampare la risposta di dispositivo alla console di:

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Salvare e chiudere il file **CallMethodOnDevice.js** .

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. In un prompt dei comandi nella cartella **simulateddevice** , eseguire il seguente comando per avviare l'ascolto per chiamate dall'IoT Hub:

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. In un prompt dei comandi nella cartella **callmethodondevice** , eseguire il seguente comando per iniziare il monitoraggio il tuo fulcro IoT:

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Verrà visualizzato il dispositivo rispondere al metodo eseguendo la stampa il messaggio e l'applicazione chiamata visualizzazione metodo la risposta dal dispositivo:

    ![][9]
    
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è configurato un hub IoT nuovo nel portale di Azure, creata e identità di un dispositivo nel Registro di sistema identità dell'hub. Identità dispositivo usato per abilitare l'app dispositivo simulato rispondere a metodi richiamati nel cloud. È inoltre creata un'applicazione che richiama metodi sul dispositivo e viene visualizzata la risposta dal dispositivo. 

Guida introduttiva a IoT Hub e per esplorare altri scenari IoT, vedere:

- [Guida introduttiva a IoT Hub]
- [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per informazioni su come estendere il IoT soluzione e pianificazione chiamate in più dispositivi, vedere [pianificazione e processi di trasmissione] [ lnk-tutorial-jobs] esercitazione.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Inviare messaggi Cloud per dispositivo con IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Guida introduttiva a IoT Hub]: iot-hub-node-node-getstarted.md
