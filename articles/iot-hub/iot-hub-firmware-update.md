<properties
 pageTitle="Come eseguire l'aggiornamento del firmware | Microsoft Azure"
 description="In questa esercitazione viene illustrato come eseguire l'aggiornamento del firmware"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Esercitazione: Come eseguire un firmware update (preview)

## <a name="introduction"></a>Introduzione
In [Guida introduttiva a gestione di dispositivi] [ lnk-dm-getstarted] esercitazione, è stato illustrato come utilizzare [un doppio dispositivo] [ lnk-devtwin] e [i metodi (C2D) cloud per dispositivo] [ lnk-c2dmethod] primitive riavviare in remoto un dispositivo. In questa esercitazione utilizza le stesse primitive IoT Hub e vengono fornite indicazioni e illustra come eseguire un aggiornamento del firmware simulata-to-end.  Questo modello viene usato nell'implementazione di aggiornamento del firmware per il campione di dispositivo Intel Edison.

In questa esercitazione verrà illustrato a:

- Creare un'applicazione console che chiama il metodo di accesso diretto firmwareUpdate sul dispositivo simulato tramite l'hub IoT.
- Creare un dispositivo simulato implementato un metodo di accesso diretto firmwareUpdate che attraversa un processo in più fasi in attesa per scaricare l'immagine del firmware, download immagine del firmware e infine applica esimo firmware immagine.  Durante l'esecuzione di ogni fase ottimali dispositivo la disponibilità di una copia segnalati proprietà per aggiornare l'avanzamento.

Al termine di questa esercitazione, sono disponibili due applicazioni console Node:

**dmpatterns_fwupdate_service.js**, che chiama un metodo diretto per il dispositivo simulato, viene visualizzata la risposta e periodicamente (ogni 500 ms) Visualizza doppi dispositivo aggiornati segnalati proprietà.

**dmpatterns_fwupdate_device.js**, che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza, riceve un metodo diretto firmwareUpdate, viene eseguita tramite un processo più stato per simulare un aggiornamento del firmware inclusi: in attesa per il download di immagini, scaricare la nuova immagine e infine l'applicazione dell'immagine.


Per completare questa esercitazione, è necessario quanto segue:

Versione Node 0.12.x o versioni successive <br/>  [Preparare l'ambiente di sviluppo] [ lnk-dev-setup] viene descritto come installare node per questa esercitazione su Windows o Linux.

Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

Eseguire l'articolo [Introduzione alla gestione di dispositivi](iot-hub-device-management-get-started.md) per creare il tuo fulcro IoT e ottenere la stringa di connessione.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creare un'app dispositivo simulato

In questa sezione, si crea un'app console Node che risponde a un metodo diretto chiamato dal cloud, che attiva l'aggiornamento del firmware dispositivo simulato e Usa doppi dispositivo segnalati le proprietà per abilitare le query doppi dispositivo per identificare i dispositivi e quando vengono riavviato.

1. Creare una nuova cartella vuota denominata **manageddevice**.  Nella cartella **manageddevice** creare un file di package.json usando il comando seguente al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Al prompt dei comandi-nella cartella **manageddevice** , eseguire il comando seguente per installare il **azure-iot-device@dtpreview** pacchetto SDK dispositivo e **azure-iot-device-mqtt@dtpreview** pacchetto:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor di testo, creare un nuovo file **dmpatterns_fwupdate_device.js** nella cartella **manageddevice** .

4. Aggiungere le operazioni seguenti 'richiedere' istruzioni all'inizio del file **dmpatterns_fwupdate_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Aggiungere una variabile **connectionString** e usarlo per creare un client di dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Aggiungere la seguente funzione che verrà utilizzato per aggiornare un doppio segnalati proprietà

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. Aggiungere le funzioni seguenti, che consente di simulare il download e applicare dell'immagine firmware.

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. Aggiungere la seguente funzione che verrà aggiornato lo stato di aggiornamento del firmware tramite il doppi segnalato proprietà sia in attesa di scaricare.  In genere, i dispositivi sono informati di un aggiornamento disponibile e un amministratore definita cause criteri il dispositivo per iniziare a scaricare e installare l'aggiornamento.  Verrà visualizzata nel punto in cui eseguire la logica per abilitare il criterio.  Per semplicità, abbiamo stiamo ritardare per 4 secondi ed effettuare il download dell'immagine firmware. 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. Aggiungere la seguente funzione che verrà aggiornato lo stato di aggiornamento del firmware tramite il doppi segnalati proprietà per il download dell'immagine firmware.  Successivo verso l'alto tramite la simulazione il download del firmware e infine Aggiorna lo stato di aggiornamento del firmware per informare del successo di download o errore.

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. Aggiungere la seguente funzione che verrà aggiornato lo stato di aggiornamento del firmware tramite il doppi segnalati proprietà l'applicazione dell'immagine firmware.  Successivo verso l'alto tramite la simulazione di un'applicazione dell'immagine firmware e infine Aggiorna lo stato di aggiornamento del firmware per informare di sia applica esito positivo o negativo.

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. Aggiungere i seguenti functoin quale gestire il metodo firmwareUpdate e avviare il processo di aggiornamento del firmware più fasi.

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. Infine, aggiungere il codice seguente che si connette a hub IoT come un dispositivo 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] Per semplicità, questa esercitazione non implementare qualsiasi criterio Riprova. Nel codice di produzione, è necessario implementare criteri tentativi (ad esempio un backoff esponenziale), come indicato nell'articolo MSDN [Gestione guasto temporaneo][lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Attivare l'aggiornamento del firmware remoto sul dispositivo utilizzando un metodo diretto 

In questa sezione, si crea un'app console Node che avvia un aggiornamento del firmware remote in un dispositivo che utilizza un metodo diretto e utilizza le query doppi dispositivo per ottenere periodicamente lo stato dell'aggiornamento del firmware attivi sul dispositivo.


1. Creare una nuova cartella vuota denominata **triggerfwupdateondevice**.  Nella cartella **triggerfwupdateondevice** creare un file di package.json usando il comando seguente al prompt dei comandi.  Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```
    
2. Al prompt dei comandi-nella cartella **triggerfwupdateondevice** , eseguire il comando seguente per installare il dispositivo SDK **iothub azure** e pacchetto **iot di azure-mqtt di dispositivo** :

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. Con un editor di testo, creare un nuovo file **dmpatterns_getstarted_service.js** nella cartella **triggerfwupdateondevice** .

4. Aggiungere le operazioni seguenti 'richiedere' istruzioni all'inizio del file **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Aggiungere le seguenti dichiarazioni di variabili e sostituire i valori segnaposto:

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. Aggiungere la funzione seguente per trovare e visualizzare il valore della firmwareUpdate segnalati proprietà.

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. Aggiungere la funzione seguente per richiamare il metodo firmwareUpdate per riavviare il dispositivo:

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. Infine, aggiungere la funzione seguente al codice per avviare il firmware Aggiorna la sequenza e avviare periodicamente con la disponibilità di una copia segnalati proprietà:

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Salvare e chiudere il file **dmpatterns_fwupdate_service.js** .

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1. Al prompt nella cartella **manageddevice** , eseguire il seguente comando per iniziare ad attendere il metodo di accesso diretto riavviare il computer.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. Al prompt nella cartella **triggerfwupdateondevice** , eseguire il comando seguente per attivare la query per doppi dispositivo trovare l'ultimo e riavviare il computer remoto riavviare ora.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Per la stampa il messaggio viene visualizzato intraprendere alcuna azione per il metodo di accesso diretto

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è stato utilizzato un metodo diretto per attivare un aggiornamento del firmware remote in un dispositivo e periodicamente utilizzato segnalati la disponibilità di una copia delle proprietà per comprendere l'avanzamento del firmware processo di aggiornamento.  

Per informazioni su come estendere il IoT soluzione e pianificazione chiamate in più dispositivi, vedere [pianificazione e processi di trasmissione] [ lnk-tutorial-jobs] esercitazione.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
