<properties
    pageTitle="Utilizzare le proprietà di un doppio | Microsoft Azure"
    description="In questa esercitazione viene illustrato come utilizzare le proprietà di un doppio"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Esercitazione: Utilizzare le proprietà desiderate per configurare i dispositivi (preview)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al termine di questa esercitazione, saranno disponibili due applicazioni console Node:

* **SimulateDeviceConfiguration.js**, un'app di dispositivo simulato che attesa per un aggiornamento della configurazione desiderata e lo stato di un processo di aggiornamento configurazione simulata.
* **SetDesiredConfigurationAndQuery.js**, un'app Node progettata per essere eseguiti dal back-end, che imposta la configurazione desiderata in un dispositivo e il processo di aggiornamento configurazione una query.

> [AZURE.NOTE] L'articolo [IoT Hub SDK] [ lnk-hub-sdks] fornisce informazioni sulle varie SDK che è possibile utilizzare la creazione di applicazioni dispositivo e back-end.

Per completare questa esercitazione è necessario quanto segue:

+ Versione Node 0.10.x o versione successiva.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

Se è stata seguita la [Guida introduttiva a due facce dispositivo] [ lnk-twin-tutorial] esercitazione, si dispone già di un hub di gestione attivata dispositivo e identità di un dispositivo chiamato **myDeviceId**; ed è possibile ignorare [creare app dispositivo simulato] [ lnk-how-to-configure-createapp] sezione.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Creare app dispositivo simulato

In questa sezione, si crea un'app console Node che si connette a un centro di raccolta come **myDeviceId**, attenderà un aggiornamento della configurazione desiderata e quindi segnala gli aggiornamenti del processo di aggiornamento configurazione simulata.

1. Creare una nuova cartella vuota denominata **simulatedeviceconfiguration**. Nella cartella **simulatedeviceconfiguration** creare un nuovo file package.json utilizzando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **simulatedeviceconfiguration** , eseguire il comando seguente per installare il **dispositivo di iot azure**e **azure-iot-dispositivo-mqtt** pacchetto:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor di testo, creare un nuovo file **SimulateDeviceConfiguration.js** nella cartella **simulatedeviceconfiguration** .

4. Aggiungere il codice seguente al file **SimulateDeviceConfiguration.js** e sostituire il segnaposto **{stringa di connessione dispositivo}** con la stringa di connessione che sono stati copiati al momento della creazione dell'identità di dispositivo **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    L'oggetto **Client** espone tutti i metodi richiesti per interagire con due facce dispositivo dal dispositivo. Codice precedente dopo l'inizializzazione oggetto **Client** recupera doppi per **myDeviceId**e associa un gestore per l'aggiornamento nella proprietà desiderate. Il gestore verifica la presenza è una richiesta di modifica configurazione effettiva confrontando i configIds, quindi richiama un metodo che viene avviata la modifica di configurazione.

    Si noti che per semplicità, il codice precedente utilizza predefinito a livello di codice per la configurazione iniziale. Un'app reale probabilmente carica la configurazione da un archivio locale.
    
    > [AZURE.IMPORTANT] Gli eventi di modifica proprietà desiderata sempre generati una volta alla connessione di dispositivi, assicurarsi di verificare la presenza di una modifica effettiva nelle proprietà desiderate prima di eseguire qualsiasi azione.

5. Aggiungere i metodi seguenti prima di `client.open()` chiamata:

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    Il metodo **initConfigChange** Aggiorna proprietà segnalate sull'oggetto doppi locale con la richiesta di aggiornamento di configurazione e imposta lo stato in **sospeso**, quindi Aggiorna doppi dispositivo il servizio. Dopo aver aggiornato correttamente la disponibilità di una copia, essa consente di simulare un processo esecuzione prolungata che termina l'esecuzione di **completeConfigChange**. Questo metodo Aggiorna proprietà segnalati del doppi locale impostare lo stato di **successo** e la rimozione dell'oggetto **pendingConfig** . Quindi aggiorna la disponibilità di una copia del servizio.

    Si noti che, salvare la larghezza di banda, segnalati proprietà vengono aggiornate specificando solo le proprietà per modificare (denominato **patch** nel codice precedente), invece di sostituzione nell'intero documento.

    > [AZURE.NOTE] In questa esercitazione non consente di simulare qualsiasi comportamento gli aggiornamenti della configurazione simultanee. Alcuni processi di aggiornamento configurazione potrebbero essere possibile supportare le modifiche di configurazione di destinazione durante l'esecuzione dell'aggiornamento, gli altri utenti potrebbe essere necessario messi in coda e gli altri utenti potrebbero rifiutarle con una condizione di errore. Assicurarsi di considerare il comportamento desiderato per il processo di configurazione specifiche e aggiungere la logica appropriata prima di iniziare la modifica di configurazione.

6. Eseguire l'app dispositivo:

        node SimulateDeviceConfiguration.js

    Verrà visualizzato il messaggio `retrieved device twin`. Mantenere l'app in esecuzione.

## <a name="create-the-service-app"></a>Creare l'applicazione di servizio

In questa sezione, è necessario creare un'app console Node che consente di aggiornare le *proprietà desiderate* in doppi associato **myDeviceId** con un nuovo oggetto di configurazione di telemetria. Le due facce dispositivo archiviati nell'hub una query e quindi viene visualizzata la differenza tra le configurazioni desiderate e segnalate del dispositivo.

1. Creare una nuova cartella vuota denominata **setdesiredandqueryapp**. Nella cartella **setdesiredandqueryapp** creare un nuovo file package.json utilizzando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **setdesiredandqueryapp** , eseguire il comando seguente per installare il pacchetto di **azure iothub** :

    ```
    npm install azure-iothub@dtpreview node-uuid --save
    ```

3. Con un editor di testo, creare un nuovo file **SetDesiredAndQuery.js** nella cartella **addtagsandqueryapp** .

4. Aggiungere il codice seguente al file **SetDesiredAndQuery.js** e sostituire il segnaposto **{stringa di connessione servizio}** con la stringa di connessione che sono stati copiati quando è stato creato il tuo fulcro:

        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
         
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });
            

    L'oggetto di **Registro di sistema** espone tutti i metodi richiesti per interagire con due facce dispositivo dal servizio. Il codice precedente dopo l'inizializzazione oggetto **Registry** recupera doppi per **myDeviceId**e aggiorna le proprietà desiderate con un nuovo oggetto di configurazione di telemetria. Successivamente, chiama l'evento di funzione **queryTwins** 10 secondi.

    > [AZURE.IMPORTANT] Questa applicazione query IoT Hub ogni 10 secondi esemplificativo. Utilizzare query per generare i report utente esposto su più dispositivi e non per rilevare le modifiche. Se la soluzione richiede notifiche in tempo reale di eventi dispositivo utilizzano [messaggi dispositivo nel cloud][lnk-d2c].

5. Aggiungere il diritto di codice seguenti prima di `registry.getDeviceTwin()` chiamata per implementare la funzione **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };

    Le query di codice precedente le due facce archiviati nell'hub e stampa le configurazioni di telemetria segnalati e desiderato. Fare riferimento al [linguaggio di query IoT Hub] [ lnk-query] per imparare a generare report dettagliati da tutti i tuoi dispositivi.


8. **SimulateDeviceConfiguration.js** è in esecuzione, eseguire l'applicazione con:

        node SetDesiredAndQuery.js 5m

    Verrà visualizzata la segnalati modifica da **successo** di configurazione **in attesa di** **successo di un** nuovo con la nuova data di attivazione frequenza di invio dei cinque minuti invece di 24 ore.

    > [AZURE.IMPORTANT] Esiste un ritardo di fino a un minuto tra l'operazione di report di dispositivo e il risultato della query. In questo modo viene l'infrastruttura di query di lavorare in scala molto elevata. Per recuperare coerente visualizzazioni di un singolo doppi utilizzano il metodo **getDeviceTwin** in classe **Registro di sistema** .

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, impostare una configurazione desiderata come *proprietà desiderate* da un'applicazione di back-end e scritto un'app di dispositivo simulato per rilevare da modificare e simulare un processo di aggiornamento di più passaggi segnalare lo stato come *segnalati proprietà* per la disponibilità di una copia.

Utilizzare le risorse seguenti per informazioni su come:

- invio di telemetria da dispositivi con la [Guida introduttiva a IoT Hub] [ lnk-iothub-getstarted] esercitazione
- pianificare o eseguire operazioni sui insiemi estesi di dispositivi, vedere i [processi di utilizzo di pianificare e trasmettere operazioni del dispositivo] [ lnk-schedule-jobs] esercitazione.
- controllare i dispositivi in modo interattivo (ad esempio, attivare una ventola da un'app controllata dall'utente), con i [metodi diretti usare] [ lnk-methods-tutorial] esercitazione.


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
