<properties
    pageTitle="Guida introduttiva a due facce | Microsoft Azure"
    description="In questa esercitazione viene illustrato come utilizzare due facce"
    services="iot-hub"
    documentationCenter="node"
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

# <a name="tutorial-get-started-with-device-twins-preview"></a>Esercitazione: Introduzione a due facce dispositivo (preview)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al termine di questa esercitazione, saranno disponibili due applicazioni console Node:

* **AddTagsAndQuery.js**, un'app Node progettata per essere eseguiti dal back-end, per aggiungere tag e due facce dispositivo una query.
* **TwinSimulatedDevice.js**, un'app Node che simula un dispositivo che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza, rapporti e le condizioni di integrazione applicativa.

> [AZURE.NOTE] L'articolo [IoT Hub SDK] [ lnk-hub-sdks] fornisce informazioni sulle varie SDK che è possibile utilizzare la creazione di applicazioni dispositivo e back-end.

Per completare questa esercitazione è necessario quanto segue:

+ Versione Node 0.10.x o versione successiva.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Creare l'applicazione di servizio

In questa sezione creare un'app di console Node che consente di aggiungere i metadati percorso doppi associato a **myDeviceId**. Viene quindi query due facce archiviati nell'hub la selezione del dispositivo si trovano negli Stati Uniti e quindi quelli che reporting una connessione di rete.

1. Creare una nuova cartella vuota denominata **addtagsandqueryapp**. Nella cartella **addtagsandqueryapp** creare un nuovo file package.json utilizzando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **addtagsandqueryapp** , eseguire il comando seguente per installare il pacchetto di **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Con un editor di testo, creare un nuovo file **AddTagsAndQuery.js** nella cartella **addtagsandqueryapp** .

4. Aggiungere il codice seguente al file **AddTagsAndQuery.js** e sostituire il segnaposto **{stringa di connessione servizio}** con la stringa di connessione che sono stati copiati quando è stato creato il tuo fulcro:

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    L'oggetto di **Registro di sistema** espone tutti i metodi richiesti per interagire con due facce dispositivo dal servizio. Il codice precedente prima di tutto consente di inizializzare l'oggetto del **Registro di sistema** e quindi recupera un doppio per **myDeviceId**e infine Aggiorna i tag con informazioni sulla posizione desiderata.

    Dopo l'aggiornamento i contrassegni che chiama la funzione **queryTwins** .

7. Aggiungere il codice seguente alla fine di **AddTagsAndQuery.js** per implementare la funzione **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    Il codice precedente vengono eseguite due query: la prima consente di selezionare solo due facce dispositivo dei dispositivi all'interno di una pianta **Redmond43** e il secondo perfeziona la query per selezionare solo i dispositivi connessi anche tramite rete cellulare.

    Si noti che il codice precedente, quando viene creato l'oggetto **query** specifica un numero massimo di documenti restituiti dalla query. L'oggetto della **query** contiene una proprietà booleana **hasMoreResults** che è possibile utilizzare per richiamare i metodi di **nextAsTwin** più volte per recuperare tutti i risultati. Un metodo denominato **successivo** è disponibile per i risultati non sono due facce dispositivo, ad esempio, i risultati delle query di aggregazione.

8. Eseguire l'applicazione con:

        node AddTagsAndQuery.js

    Verrà visualizzato un dispositivo nei risultati della query che richiede di tutti i dispositivi presenti in **Redmond43** e Nessuno per la query che limita i risultati ai dispositivi che utilizzano una rete cellulare.

    ![][1]

Nella sezione seguente si crea un'app per dispositivi che riporta le informazioni di connettività e cambia il risultato della query nella sezione precedente.

## <a name="create-the-device-app"></a>Creare app dispositivo

In questa sezione, si crea un Node console app si connette a un centro di raccolta come **myDeviceId**e quindi aggiorna la doppi segnalati proprietà per contenere le informazioni che è connesso tramite una rete cellulare.

> [AZURE.NOTE] In questa fase, due facce dispositivo sono accessibili solo da dispositivi che eseguono la connessione a IoT Hub mediante il protocollo MQTT. Consultare per [supportare MQTT] [ lnk-devguide-mqtt] articolo per istruzioni su come convertire app dispositivo esistente da utilizzare MQTT.

1. Creare una nuova cartella vuota denominata **reportconnectivity**. Nella cartella **reportconnectivity** creare un nuovo file package.json utilizzando il comando seguente al prompt dei comandi. Accettare tutte le impostazioni predefinite:

    ```
    npm init
    ```

2. Al prompt dei comandi-nella cartella **reportconnectivity** , eseguire il comando seguente per installare il **dispositivo di iot azure**e **azure-iot-dispositivo-mqtt** pacchetto:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor di testo, creare un nuovo file **ReportConnectivity.js** nella cartella **reportconnectivity** .

4. Aggiungere il codice seguente al file **ReportConnectivity.js** e sostituire il segnaposto **{stringa di connessione dispositivo}** con la stringa di connessione che sono stati copiati al momento della creazione dell'identità di dispositivo **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    L'oggetto **Client** espone tutti i metodi che necessari per interagire con due facce dispositivo dal dispositivo. Il codice precedente dopo l'inizializzazione oggetto **Client** recupera doppi per **myDeviceId** e aggiorna la relativa proprietà segnalati con le informazioni di connettività.

5. Eseguire l'app dispositivo

        node ReportConnectivity.js

    Verrà visualizzato il messaggio `twin state reported`.

6. Ora che il dispositivo segnalati le relative informazioni di integrazione applicativa, dovrebbero essere visualizzate in entrambe le query. Tornare nella cartella **addtagsandqueryapp** ed eseguire di nuovo la query:

        node AddTagsAndQuery.js

    Questa volta **myDeviceId** dovrebbero essere visualizzate in entrambi risultati della query.

    ![][3]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione, è configurato un hub IoT nuovo nel portale di Azure, creata e identità di un dispositivo nel Registro di sistema identità dell'hub. Aggiunto dispositivo metadati come tag da un'applicazione di back-end e scritto un'app dispositivo simulato per registrare le informazioni di connettività di dispositivo in doppi dispositivo. Inoltre appreso come queste informazioni tramite IoT Hub linguaggio di query mi piace SQL della query.

Utilizzare le risorse seguenti per informazioni su come:

- invio di telemetria da dispositivi con la [Guida introduttiva a IoT Hub] [ lnk-iothub-getstarted] esercitazione
- configurare i dispositivi con proprietà desiderate della disponibilità di una copia di [utilizzare le proprietà desiderate per configurare i dispositivi] [ lnk-twin-how-to-configure] esercitazione
- controllare i dispositivi in modo interattivo (ad esempio, attivare una ventola da un'app controllata dall'utente), con i [metodi diretti usare] [ lnk-methods-tutorial] esercitazione.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md