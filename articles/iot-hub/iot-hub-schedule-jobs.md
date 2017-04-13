<properties
 pageTitle="Come pianificare processi | Microsoft Azure"
 description="In questa esercitazione viene illustrato come pianificare processi"
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

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>Esercitazione: Pianificare e trasmettere processi (preview)

## <a name="introduction"></a>Introduzione
Azure IoT Hub è un servizio completamente gestito che consente a un back-end dell'applicazione di creare e gestire processi di pianificare e aggiornare milioni di dispositivi.  Processi possono essere usati per le azioni seguenti:

- Aggiornare le proprietà doppi bene accolta del dispositivo
- Aggiornare i tag doppi del dispositivo
- Richiamare metodi cloud al dispositivo

Livello concettuale, un processo include una di queste azioni e consente di tenere traccia dello stato di avanzamento dell'esecuzione rispetto a un set di dispositivi, definito da una query doppi.  Ad esempio, tramite un processo di back-end un'applicazione può richiamare un metodo di riavviare il computer su dispositivi di 10.000, specificato da una query doppi e pianificata in un secondo momento.  L'applicazione quindi tenere traccia dello stato di ciascuno di tali dispositivi ricevere ed eseguire il metodo di riavviare il computer.

Ulteriori informazioni su ognuna di queste funzionalità in questi articoli:

- Doppi dispositivo e proprietà: [Guida introduttiva a doppi] [ lnk-get-started-twin] e [Esercitazione: come usare le proprietà di un doppio][lnk-twin-props]
- Metodi per il cloud per dispositivo: [Guida per sviluppatori - metodi diretti] [ lnk-dev-methods] e [Esercitazione: metodi C2D][lnk-c2d-methods]

In questa esercitazione verrà illustrato a:

- Creare un dispositivo simulato che dispone di un metodo diretto che consente di lockDoor che possono essere chiamati dall'applicazione indietro fine.
- Creare un'applicazione console che chiama il metodo di accesso diretto lockDoor sul dispositivo simulato tramite un processo e aggiorna le proprietà di un doppio bene accolta tramite un processo di dispositivo.

Al termine di questa esercitazione, sono disponibili due applicazioni console Node:

**simDevice.js**, che si connette a un centro di raccolta IoT con l'identità del dispositivo e riceve un metodo diretto lockDoor.

**scheduleJobService.js**, che chiama un metodo diretto sul dispositivo simulato e aggiornare le proprietà contenga del doppi tramite un processo.

Per completare questa esercitazione, è necessario quanto segue:

Versione Node 0.12.x o versioni successive <br/>  [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare node per questa esercitazione su Windows o Linux.

Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app dispositivo simulato

In questa sezione, si crea un'app console Node che risponde a un metodo diretto chiamato dal cloud, che attiva riavviare il dispositivo simulato e Usa doppi dispositivo segnalati le proprietà per abilitare le query doppi dispositivo per identificare i dispositivi e quando vengono riavviato.

1. Creare una nuova cartella vuota denominata **simDevice**.  Nella cartella **simDevice** creare un file di package.json usando il comando seguente al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Al prompt dei comandi-nella cartella **simDevice** , eseguire il comando seguente per installare il dispositivo SDK **azure iot dispositivi** e pacchetto **iot di azure-mqtt di dispositivo** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor di testo, creare un nuovo file **simDevice.js** nella cartella **simDevice** .

4. Aggiungere le operazioni seguenti 'richiedere' istruzioni all'inizio del file **simDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Aggiungere una variabile **connectionString** e usarlo per creare un client di dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Aggiungere la seguente funzione per gestire il metodo lockDoor.

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. Aggiungere il codice seguente per registrare il gestore per il metodo lockDoor.

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. Salvare e chiudere il file **simDevice.js** .

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio un backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo][lnk-transient-faults].

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>Pianificare processi per la chiamata a un metodo diretto e l'aggiornamento delle proprietà del doppi

In questa sezione, si crea un'app console Node che avvia un lockDoor remoto in un dispositivo che utilizza un metodo diretto e aggiornare le proprietà di un doppio.

1. Creare una nuova cartella vuota denominata **scheduleJobService**.  Nella cartella **scheduleJobService** creare un file di package.json usando il comando seguente al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Al prompt dei comandi-nella cartella **scheduleJobService** , eseguire il comando seguente per installare il dispositivo SDK **iothub azure** e pacchetto **iot di azure-mqtt di dispositivo** :

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. Con un editor di testo, creare un nuovo file **scheduleJobService.js** nella cartella **scheduleJobService** .

4. Aggiungere le operazioni seguenti 'richiedere' istruzioni all'inizio del file **dmpatterns_gscheduleJobServiceetstarted_service.js** :

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Aggiungere le seguenti dichiarazioni di variabili e sostituire i valori segnaposto:

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. Aggiungere la funzione seguente che verrà utilizzata per monitorare l'esecuzione del processo:

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Aggiungere il codice riportato di seguito per pianificare il processo che chiama il metodo di dispositivo:

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. Aggiungere il codice riportato di seguito per pianificare il processo di aggiornamento di un doppio:

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. Salvare e chiudere il file **scheduleJobService.js** .

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. Al prompt nella cartella **simDevice** , eseguire il seguente comando per iniziare ad attendere il metodo di accesso diretto riavviare il computer.

    ```
    node simDevice.js
    ```

2. Al prompt nella cartella **scheduleJobService** , eseguire il comando seguente per attivare la query per doppi dispositivo trovare l'ultimo e riavviare il computer remoto riavviare ora.

    ```
    node scheduleJobService.js
    ```

3. Verrà visualizzata l'output dal dispositivo e le applicazioni di back-end.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è utilizzato un processo per pianificare un metodo diretto a un dispositivo e aggiornamento delle proprietà di un doppio dispositivo.

Per continuare introduzione con IoT Hub e modelli di gestione di dispositivi, ad esempio remota tramite l'aggiornamento del firmware aria, vedere:

[Esercitazione: Come eseguire l'aggiornamento del firmware][lnk-fwupdate]

Per continuare a Introduzione a IoT Hub, vedere [Introduzione al SDK di Gateway IoT][lnk-gateway-SDK].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx