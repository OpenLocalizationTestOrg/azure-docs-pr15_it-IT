<properties
 pageTitle="Introduzione alla gestione di dispositivi | Microsoft Azure"
 description="In questa esercitazione viene illustrato come iniziare a utilizzare la gestione dei dispositivi in Azure IoT Hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="tutorial-get-started-with-device-management-preview"></a>Esercitazione: Introduzione alla gestione di dispositivi (preview)

## <a name="introduction"></a>Introduzione
Applicazioni cloud IoT consente primitive hub IoT Azure, vale a dire il doppi dispositivo e i metodi di accesso diretti, in modalità remota avviare e monitorare le operazioni di gestione dispositivi nei dispositivi.  In questo articolo fornisce istruzioni e codice per funzionamento di un'applicazione di cloud IoT e un dispositivo per avviare e monitorare riavviare il dispositivo remoto utilizzando IoT Hub.

Per avviare e monitorare le operazioni di gestione dispositivi nei dispositivi da un'app basata su cloud, back-end in remoto, utilizzare le primitive di Azure IoT Hub, ad esempio [un doppio dispositivo] [ lnk-devtwin] e [i metodi (C2D) cloud per dispositivo][lnk-c2dmethod]. In questa esercitazione Mostra come un'app di back-end e un dispositivo che possono collaborare per consentire di avviare e monitorare riavviare il computer remoto dispositivo dall'IoT Hub.

Un metodo C2D per avviare azioni di gestione di dispositivi (ad esempio, riavviare il computer, factory Reimposta e aggiornamento del firmware) da un'app di back-end nel cloud. Il dispositivo è responsabile per:

- Gestisce la richiesta di metodo inviata dall'IoT Hub.
- Avviare l'azione specifico di dispositivo corrispondente nel dispositivo.
- Fornire gli aggiornamenti dello stato tramite doppi dispositivo segnalati proprietà IoT Hub.

È possibile utilizzare un'app di back-end nel cloud per eseguire query doppi dispositivo per segnalare lo stato di avanzamento delle azioni di gestione del dispositivo.

In questa esercitazione verrà illustrato a:

- Usare il portale di Azure per creare un IoT Hub e identità di un dispositivo l'hub IoT.
- Creare un dispositivo simulato che dispone di un metodo diretto che consente di riavviare il computer che può essere chiamato tramite nel cloud.
- Creare un'applicazione console che chiama il metodo di accesso diretto riavviare il computer sul dispositivo simulato tramite l'hub IoT.

Al termine di questa esercitazione, sono disponibili due applicazioni console Node:

**dmpatterns_getstarted_device.js**, che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza, riceve un metodo diretto riavviare il computer, simula riavviare il computer fisico e indica il tempo per l'ultimo riavvio.

**dmpatterns_getstarted_service.js**, che chiama un metodo diretto sul dispositivo simulato, viene visualizzata la risposta e Visualizza doppi dispositivo aggiornati segnalati proprietà.

Per completare questa esercitazione, è necessario quanto segue:

Versione Node 0.12.x o versioni successive <br/>  [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare node per questa esercitazione su Windows o Linux.

Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app dispositivo simulato

In questa sezione, si crea un'app console Node che risponde a un metodo diretto chiamato dal cloud, che attiva riavviare il dispositivo simulato e Usa doppi dispositivo segnalati le proprietà per abilitare le query doppi dispositivo per identificare i dispositivi e quando vengono riavviato.

1. Creare una nuova cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file di package.json usando il comando seguente al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Al prompt dei comandi-nella cartella **manageddevice** , eseguire il comando seguente per installare il **azure-iot-device@dtpreview** pacchetto SDK dispositivo e **azure-iot-device-mqtt@dtpreview** pacchetto:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor di testo, creare un nuovo file **dmpatterns_getstarted_device.js** nella cartella **manageddevice** .

4. Aggiungere le operazioni seguenti 'richiedere' istruzioni all'inizio del file **dmpatterns_getstarted_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Aggiungere una variabile **connectionString** e usarlo per creare un client di dispositivo.  Sostituire la stringa di connessione con la stringa di connessione di dispositivi.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Aggiungere la funzione seguente per implementare il metodo diretto sul dispositivo

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Aprire la connessione a un centro di raccolta IoT e iniziare a comunicare ascoltatore metodo diretto:

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. Salvare e chiudere il file **dmpatterns_getstarted_device.js** .

 [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio un backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Impostare un trigger riavviare il computer remoto sul dispositivo utilizzando un metodo diretto 

In questa sezione, si crea un'app console Node che avvia riavviare il computer remoto in un dispositivo che utilizza un metodo diretto e utilizza le query doppi dispositivo per trovare l'ultima volta riavviare il computer per il dispositivo.

1. Creare una nuova cartella vuota denominata **triggerrebootondevice**.  Nella cartella **triggerrebootondevice** creare un file di package.json usando il comando seguente al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Al prompt dei comandi-nella cartella **triggerrebootondevice** , eseguire il comando seguente per installare il **azure-iothub@dtpreview** pacchetto SDK dispositivo e **azure-iot-device-mqtt@dtpreview** pacchetto:

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. Con un editor di testo, creare un nuovo file **dmpatterns_getstarted_service.js** nella cartella **triggerrebootondevice** .

4. Aggiungere le operazioni seguenti 'richiedere' istruzioni all'inizio del file **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Aggiungere le seguenti dichiarazioni di variabili e sostituire i valori segnaposto:

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. Aggiungere la funzione seguente per richiamare il metodo di dispositivo per riavviare il dispositivo:

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Aggiungere la funzione seguente per eseguire una query per il dispositivo e ottenere l'ultima volta riavviare il computer:

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. Aggiungere il codice riportato di seguito per chiamare le funzioni che attiveranno la query e il metodo diretto riavviare il computer per l'ultima volta riavviare il computer:

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. Salvare e chiudere il file **dmpatterns_getstarted_service.js** .

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. Al prompt nella cartella **manageddevice** , eseguire il seguente comando per iniziare ad attendere il metodo di accesso diretto riavviare il computer.

    ```
    node dmpatterns_getstarted_device.js
    ```

2. Al prompt nella cartella **triggerrebootondevice** , eseguire il comando seguente per attivare la query per doppi dispositivo trovare l'ultimo e riavviare il computer remoto riavviare ora.

    ```
    node dmpatterns_getstarted_service.js
    ```

3. Per la stampa il messaggio viene visualizzato intraprendere alcuna azione per il metodo di accesso diretto

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizzare ed estendere il dispositivo azioni di gestione

Le soluzioni IoT espandere il set di criteri di gestione dei dispositivi definiti o abilitare modelli personalizzati mediante l'utilizzo doppi dispositivo e primitive metodo C2D. Altre azioni di gestione di dispositivi esempi di reimpostazione factory, aggiornamento del firmware, aggiornamento software, risparmio energia, la gestione della rete e connettività e la crittografia dei dati.

## <a name="device-maintenance-windows"></a>Finestre di manutenzione dispositivo

In genere, configurare i dispositivi per eseguire azioni alla volta che viene ridotto a icona delle interruzioni e il tempo di inattività.  Finestre di manutenzione dispositivo costituiscono un modello usato di frequente per definire l'ora in un dispositivo necessario aggiornare la configurazione. Le soluzioni di back-end è possono utilizzare le proprietà desiderate di doppi dispositivo per definire e attivare un criterio in un dispositivo che consente di una finestra di manutenzione. Quando un dispositivo riceve il criterio Manutenzione di finestra, è possibile utilizzare la proprietà segnalata di doppi dispositivo per segnalare lo stato dei criteri. App back-end possono utilizzare query doppi dispositivo per confermare la conformità dei dispositivi e ogni criterio.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è utilizzato un metodo diretto per impostare un trigger riavviare il computer remoto in un dispositivo usato doppi dispositivo segnalati proprietà per segnalare l'ultima volta riavviare il computer dal dispositivo ed eseguire una query per doppi dispositivo individuare l'ultima volta riavvio del dispositivo dal cloud.

Per continuare introduzione con IoT Hub e modelli di gestione di dispositivi, ad esempio remota tramite l'aggiornamento del firmware aria, vedere:

[Esercitazione: Come eseguire l'aggiornamento del firmware][lnk-fwupdate]

Per informazioni su come estendere il IoT soluzione e pianificazione chiamate in più dispositivi, vedere [pianificazione e processi di trasmissione] [ lnk-tutorial-jobs] esercitazione.

Per continuare a Introduzione a IoT Hub, vedere [Introduzione al SDK di Gateway IoT][lnk-gateway-SDK].


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx