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

Al termine di questa esercitazione, sarà necessario .NET e un'applicazione di console Node:

* **AddTagsAndQuery.sln**, un'app .NET progettata per essere eseguiti dal back-end, per aggiungere tag e due facce dispositivo una query.
* **TwinSimulatedDevice.js**, un'app Node che simula un dispositivo che si connette a un centro di raccolta IoT con l'identità di dispositivo creata in precedenza, rapporti e le condizioni di integrazione applicativa.

> [AZURE.NOTE] L'articolo [IoT Hub SDK] [ lnk-hub-sdks] fornisce informazioni sulle varie SDK che è possibile utilizzare la creazione di applicazioni dispositivo e back-end.

Per completare questa esercitazione è necessario quanto segue:

+ Microsoft Visual Studio 2015.

+ Versione Node 0.10.x o versione successiva.

+ Un account Azure attivo. (Se non si dispone di un account, è possibile creare un [account gratuito] [ lnk-free-trial] in pochi minuti.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Creare l'applicazione di servizio

In questa sezione creare un'app di console Node che consente di aggiungere i metadati percorso doppi associato a **myDeviceId**. Viene quindi query due facce archiviati nell'hub la selezione del dispositivo si trovano negli Stati Uniti e quindi quelli che reporting una connessione di rete.

1. In Visual Studio, aggiungere un progetto Visual c# Desktop di Windows alla soluzione corrente utilizzando il modello di progetto **Applicazione Console** . Nome del progetto **AddTagsAndQuery**.

    ![Nuovo progetto di Visual c# Desktop di Windows][img-createapp]

2. In Esplora soluzioni fare clic sul progetto **AddTagsAndQuery** e quindi fare clic su **Gestisci pacchetti Nuget**.

3. Nella finestra di **Gestione di pacchetti Nuget** , verificare che sia selezionata **Includi preliminare** , cercare **microsoft.azure.devices**, selezionare **Installa** per installare la versione *non definitiva* di **Microsoft.Azure.Devices** creare un pacchetto e accettare le condizioni di utilizzo. Questa procedura Scarica, installa e aggiunge un riferimento a [Microsoft Azure IoT Service SDK] [ lnk-nuget-service-sdk] Nuget pacchetto e delle dipendenze.

    ![Finestra di gestione di pacchetti NuGet][img-servicenuget]

4. Aggiungere quanto segue `using` le istruzioni nella parte superiore del file **Program.cs** :

        using Microsoft.Azure.Devices;

5. Aggiungere i campi seguenti per la classe di **programma** . Sostituire il valore di segnaposto con la stringa di connessione per l'hub IoT creata nella sezione precedente.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Aggiungere il metodo seguente alla classe **Program** :

        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }

    La classe **RegistryManager** espone tutti i metodi richiesti per interagire con due facce dispositivo dal servizio. Il codice precedente prima di tutto consente di inizializzare l'oggetto **registryManager** , quindi recupera un doppio per **myDeviceId**e infine Aggiorna i tag con informazioni sulla posizione desiderata.

    Dopo l'aggiornamento, vengono eseguite due query: la prima consente di selezionare solo due facce dispositivo dei dispositivi all'interno di una pianta **Redmond43** e il secondo perfeziona la query per selezionare solo i dispositivi connessi anche tramite rete cellulare.

    Si noti che il codice precedente, quando viene creato l'oggetto **query** specifica un numero massimo di documenti restituiti dalla query. L'oggetto della **query** contiene una proprietà booleana **HasMoreResults** che è possibile utilizzare per richiamare i metodi di **GetNextAsTwinAsync** più volte per recuperare tutti i risultati. Un metodo denominato **GetNextAsJson** è disponibile per i risultati non sono due facce dispositivo, ad esempio, i risultati delle query di aggregazione.

7. Infine, aggiungere le seguenti righe al metodo **principali** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

8. Eseguire questa applicazione e verrà visualizzato un dispositivo nei risultati della query che richiede di tutti i dispositivi presenti in **Redmond43** e Nessuno per la query che limita i risultati ai dispositivi che utilizzano una rete cellulare.

    ![Risultati query nella finestra][img-addtagapp]

Nella sezione seguente si crea un'app per dispositivi che riporta le informazioni di connettività e cambia il risultato della query nella sezione precedente.

## <a name="create-the-device-app"></a>Creare app dispositivo

In questa sezione, si crea un Node console app si connette a un centro di raccolta come **myDeviceId**e quindi aggiorna la doppi segnalati proprietà per contenere le informazioni che è connesso tramite una rete cellulare.

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

6. Ora che il dispositivo segnalati le relative informazioni di integrazione applicativa, dovrebbero essere visualizzate in entrambe le query. Eseguire l'app .NET **AddTagsAndQuery** per eseguire di nuovo la query. Questa volta **myDeviceId** dovrebbero essere visualizzate in entrambi risultati della query.

    ![][img-addtagapp2]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione, è configurato un hub IoT nuovo nel portale di Azure, creata e identità di un dispositivo nel Registro di sistema identità dell'hub. Aggiunto dispositivo metadati come tag da un'applicazione di back-end e scritto un'app dispositivo simulato per registrare le informazioni di connettività di dispositivo in doppi dispositivo. Inoltre appreso come queste informazioni tramite IoT Hub linguaggio di query mi piace SQL della query.

Utilizzare le risorse seguenti per informazioni su come:

- invio di telemetria da dispositivi con la [Guida introduttiva a IoT Hub] [ lnk-iothub-getstarted] esercitazione
- configurare i dispositivi con proprietà desiderate della disponibilità di una copia di [utilizzare le proprietà desiderate per configurare i dispositivi] [ lnk-twin-how-to-configure] esercitazione
- controllare i dispositivi in modo interattivo (ad esempio, attivare una ventola da un'app controllata dall'utente) con i [metodi diretti usare] [ lnk-methods-tutorial] esercitazione.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

