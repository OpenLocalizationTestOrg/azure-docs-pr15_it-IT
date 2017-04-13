<properties
    pageTitle="Le notifiche push recintato geografico con gli hub di notifica di Azure e dati spaziale Bing | Microsoft Azure"
    description="In questa esercitazione si imparerà alla ricezione delle notifiche push basata sulla posizione con gli hub di notifica di Azure e dati spaziale Bing."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="invio di notifiche, invio di notifiche"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Notifiche push recintato geografico con gli hub di notifica di Azure e dati spaziale Bing
 
 > [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

In questa esercitazione si imparerà alla ricezione delle notifiche push basata sulla posizione con gli hub di notifica di Azure e dati spaziale Bing, utilizzati da un'applicazione universale piattaforma Windows.

##<a name="prerequisites"></a>Prerequisiti
Prima di tutto, è necessario assicurarsi di avere tutto i software e servizi prerequisiti:

* [Visual Studio 2015 aggiornamento 1](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) o versioni successive ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) produrrà anche). 
* Versione più recente di [Azure SDK](https://azure.microsoft.com/downloads/). 
* [Centro per sviluppatori di Bing Maps account](https://www.bingmapsportal.com/) (è possibile crearne uno gratuitamente e associarla con l'account Microsoft). 

##<a name="getting-started"></a>Guida introduttiva

Iniziamo creando il progetto. In Visual Studio, avviare un nuovo progetto di tipo **App vuota (Universal Windows)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Una volta completata la creazione del progetto, è necessario che il cablaggio per l'app stesso. A questo punto verrà impostata a tutti gli elementi per l'infrastruttura di recinzione geografico. Perché occorre usare i servizi di Bing per questo, esiste un endpoint API REST pubblico che consente di frame posizione specifica della query:

    http://spatial.virtualearth.net/REST/v1/data/
    
È necessario specificare i parametri seguenti per assicurarne il funzionamento:

* **ID dell'origine dati** e il **Nome dell'origine dati** : in API Bing Maps, origini dati contengono diversi metadati bucketed, ad esempio posizioni e orario di ufficio dell'operazione. È possibile leggere informazioni su queste qui. 
* **Nome dell'entità** : l'entità da usare come punto di riferimento per la notifica. 
* **Bing mappe API chiave** : questa è la chiave è stato acquistato in precedenza durante la creazione di account di Bing Dev Center.
 
Di seguito eseguire approfondire alle impostazioni per tutti gli elementi indicati.

##<a name="setting-up-the-data-source"></a>Impostazione dell'origine dati

È possibile eseguire in Bing Maps Dev Center. È sufficiente fare clic su **origini dati** nella barra di spostamento superiore e selezionare **Gestisci origini dati**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Se non si è usato con API Bing Maps prima, è probabile che non è possibile esistere tutte le origini dati presente, è possibile creare una nuova facendo clic su Carica i dati a un'origine dati. Verificare che compilare tutti i campi obbligatori:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

È importante sapere: che cos'è il file di dati e cosa deve si essere caricando? Ai fini di questo test, è possibile utilizzare solo il campione basate su barra verticale che contiene cornici un'area di trasmissione è disponibile la San Francisco:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
Le risposte sopra rappresenta l'entità:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

È sufficiente copiare e incollare la stringa precedente in un nuovo file e salvarlo come **NotificationHubsGeofence.pipe**e caricarlo in Bing Dev Center.

>[AZURE.NOTE]È possibile che venga chiesto di specificare una nuova chiave per la **chiave Master** è la differenza tra la **Chiave di Query**. È sufficiente creare una nuova chiave tramite dashboard e aggiornare la pagina di caricamento di origine dati.

Una volta caricare il file di dati, è necessario ricordare pubblicare l'origine dati. 

Passare a **Gestire le origini dati**, proprio come abbiamo fatto in precedenza, trovare l'origine dati nell'elenco e fare clic su **pubblica** nella colonna **Azioni** . In un po', verrà visualizzata l'origine dati nella scheda **Origini dati pubblicate** :

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Se si fa clic su **Modifica**, sarà possibile visualizzare a colpo d'occhio quali posizioni abbiamo introdotto al suo interno:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

A questo punto, il portale non mostra i limiti per geofence che è stato creato, è sufficiente è una conferma di tale posizione specificata in prossimità destra.

A questo punto si dispongono di tutti i requisiti per l'origine dati. Per ulteriori informazioni sull'URL della richiesta per la chiamata API in Bing Maps Dev Center, fare clic su **origini dati** e selezionare **Informazioni delle origini dati**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

L' **URL di Query** è siamo dopo. Corrisponde all'endpoint rispetto al quale è possibile eseguire query per verificare se il dispositivo è attualmente entro i limiti di un percorso o meno. Per eseguire questo controllo, è sufficiente eseguire una chiamata GET con URL query con parametri seguenti aggiunti:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

In questo modo che si specifica un punto di destinazione che si ottengono dal dispositivo e Bing Maps automaticamente calcoli per verificare se si trova all'interno di geofence. Quando si esegue la richiesta tramite un browser (o latino), riceverai standard una risposta JSON:

![](./media/notification-hubs-geofence/bing-maps-json.png)

La risposta non accade solo quando il punto sia effettivamente entro i limiti designati. In caso contrario, verrà visualizzato un intervallo di vuoti **risultati** :

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>La configurazione dell'applicazione UWP

Ora che è stata creata l'origine dati pronto, è possibile iniziare a lavorare sull'applicazione UWP che viene avviato automaticamente in precedenza.

Prima di tutto, è necessario attivare servizi di posizione per l'applicazione. A tale scopo, fare doppio clic su `Package.appxmanifest` file in **Esplora soluzioni**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

Nella scheda Proprietà pacchetto che appena aperta, fare clic su **funzionalità** e assicurarsi di selezionare **posizione**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Dichiarato la funzionalità di posizione, creare una nuova cartella all'interno della soluzione denominata `Core`e aggiungere un nuovo file all'interno di SharePoint, chiamato `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

Il `LocationHelper` classe è abbastanza base a questo punto, viene solo è consente di ottenere il percorso utente tramite l'API di sistema:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

È possibile leggere informazioni su come ottenere il luogo dell'utente nelle App UWP nel [documento di MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)ufficiale.

Per verificare che l'acquisizione di posizione effettui effettivamente, aprire il lato di codice della pagina principale (`MainPage.xaml.cs`). Creare un nuovo gestore eventi per il `Loaded` evento in calendario di `MainPage` costruttore:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

L'implementazione del gestore eventi è il seguente:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Si noti che è il gestore dichiarato come asincrono perché `GetCurrentLocation` awaitable e pertanto richiede l'esecuzione in un contesto asincrono. Inoltre, poiché in alcuni casi è possibile ottenere rapidamente con un percorso null (ad esempio, il percorso servizi sono disabilitati o dell'applicazione negato autorizzazioni per la posizione di accesso), è necessario assicurarsi che sia gestito correttamente con un segno di spunta null.

Eseguire l'applicazione. Verificare che si consente l'accesso posizione:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Una volta avvia applicazione dovrebbe essere possibile visualizzare le coordinate nella finestra di **Output** :

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

A questo punto è possibile sapere che works acquisizione posizione: è possibile rimuovere il gestore di test per caricato in quanto è non essere utilizzato più.

Il passaggio successivo consiste nel acquisire le modifiche di posizione. Per tale, tornare al `LocationHelper` per la classe e aggiungere il gestore eventi per `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

L'implementazione mostrerà le coordinate di posizione nella finestra di **Output** :

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>La configurazione di back-end

Scaricare l' [Esempio di back-end .NET da GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Una volta completato il download, aprire la `NotifyUsers` cartella e, successivamente, il `NotifyUsers.sln` file.

Impostare il `AppBackend` del **Progetto di avvio** del progetto e avviarlo.

![](./media/notification-hubs-geofence/vs-startup-project.png)

Il progetto è già configurato per l'invio di notifiche push ai dispositivi di destinazione, che sarà necessario eseguire solo due fattori: consente di sostituire del tipo di connessione di stringa per l'hub di notifica e aggiungere identificazione limite per inviare la notifica solo quando l'utente si trova all'interno di geofence.

Per configurare la stringa di connessione, nel `Models` cartella aperta `Notifications.cs`. Il `NotificationHubClient.CreateClientFromConnectionString` funzione deve contenere le informazioni relative all'hub di notifica che è possibile ottenere nel [Portale di Azure](https://portal.azure.com) (aspetto all'interno e il **Criteri di accesso** in **Impostazioni**). Salvare il file di configurazione aggiornato.

A questo punto è necessario creare un modello per il risultato API Bing Maps. Il modo più semplice per eseguire questa operazione è fare clic sul `Models` cartella, **Aggiungi** > **classe**. Assegnare un nome `GeofenceBoundary.cs`. Una volta completata, copiare il JSON dalla risposta dell'API che è stato illustrato nella sezione prima e in uso di Visual Studio **Modifica** > **Incolla speciale** > **JSON Incolla come classi**. 

In questo modo per garantire che l'oggetto verrà deserializzato esattamente nel modo previsto. Set di classe risultante dovrebbe essere simile a questo:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Aprire quindi `Controllers`  >  `NotificationsController.cs`. È necessario modificare la chiamata Post all'account per la longitudine di destinazione e latitudine. Per cui, aggiungere semplicemente due stringhe nella firma della funzione- `latitude` e `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Creare una nuova classe all'interno del progetto denominata `ApiHelper.cs` – si userà per connettersi a Bing per controllare scegliere intersezioni limite. Implementare un `IsPointWithinBounds` funzione come questa:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Assicurarsi di sostituire l'endpoint API con l'URL di query che è stato acquistato in precedenza dall'interfaccia di sviluppo di Bing (Ciò vale anche per la chiave dell'API). 

Se sono presenti risultati alla query, che indica che il punto specificato è all'interno del geofence, in modo che si torna `true`. Se sono non presenti alcun risultato, Bing è informa che il punto si trova all'esterno della cornice di ricerca, in modo che si torna `false`.

Di nuovo in `NotificationsController.cs`, creare un controllo prima istruzione switch:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

In questo modo la notifica viene inviata solo quando il punto entro i limiti.

##<a name="testing-push-notifications-in-the-uwp-app"></a>Verifica delle notifiche push nell'app UWP

Tornando all'app UWP, abbiamo bisogna in grado di verificare le notifiche. All'interno di `LocationHelper` per la classe, creare una nuova funzione, `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Scambio di `POST_URL` nella posizione dell'applicazione web distribuita creata nella sezione precedente. Per ora, è possibile eseguire in locale, ma mentre si lavora sulla distribuzione di una versione pubblica, sarà necessario ospitare presso un provider di servizi esterno.

Di seguito assicurarsi che si registra l'app UWP per le notifiche push. In Visual Studio, fare clic sul **progetto** > **archiviare** > **associare app con l'archivio**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Dopo l'accesso al proprio account di sviluppo, verificare che si seleziona un'app esistente o crearne uno nuovo e associa il pacchetto. 

Accedere all'interfaccia di sviluppo e aprire l'app appena creata. Fare clic su **servizi** > **Delle notifiche Push** > **sito Live Services**.

![](./media/notification-hubs-geofence/ms-live-services.png)

Nel sito, prendere nota del **Segreto applicazione** e il **Pacchetto SID**. Sarà necessario sia nel portale di Azure, aprire l'hub di notifica, fare clic su **Impostazioni** > **Notification Services** > **Windows (WNS)** e immettere le informazioni nei campi obbligatori.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Fare clic su **Salva**.

Fare clic con il pulsante destro nella scheda **riferimenti** in **Esplora soluzioni** e selezionare **Gestisci pacchetti NuGet**. Sarà necessario aggiungere un riferimento a **Microsoft Azure servizio Bus gestite raccolta** , è sufficiente cercare `WindowsAzure.Messaging.Managed` e aggiungerlo al progetto.

![](./media/notification-hubs-geofence/vs-nuget.png)

Scopo di verifica, è possibile creare il `MainPage_Loaded` nuovo gestore eventi e aggiungervi il frammento di codice:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Le risposte sopra registra l'app con l'hub di notifica. Si è pronti a passare! 

In `LocationHelper`, all'interno di `Geolocator_PositionChanged` gestore, è possibile aggiungere una parte del codice di test che inserirà forzata la posizione all'interno di geofence:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Perché è non passa le coordinate reale (che potrebbero non essere entro i limiti al momento) e i valori di test predefinite, vedremo una notifica vengono visualizzate in aggiornamento:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>Che cos'è successiva?

Esistono un paio di passaggi che potrebbe essere necessario eseguire oltre le risposte sopra per assicurarsi che la soluzione è pronto per la produzione.

Prima di tutto, potrebbe essere necessario fare in modo che siano dinamico geofences. Sarà necessario alcune operazioni aggiuntive con l'API Bing per essere in grado di caricare nuovi limiti all'interno dell'origine dati esistente. Per ulteriori informazioni sull'argomento, consultare la [documentazione di Bing spaziale API ODS](https://msdn.microsoft.com/library/ff701734.aspx) .

Secondo, mentre si lavora per assicurarsi che il recapito è stato completato ai partecipanti alla destro, è consigliabile usarli come destinazione tramite [tagging](notification-hubs-tags-segment-push-message.md).

La soluzione sopra descrive uno scenario in cui una vasta gamma di piattaforme di destinazione, potrebbe essere in modo che non è limitare geofencing alle funzionalità specifiche del sistema. Nonostante ciò, la piattaforma Windows universale offre funzionalità per [rilevare geofences destra - di-pronte](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Per ulteriori informazioni relative alle funzionalità di notifica hub, estrarre il [portale documentazione](https://azure.microsoft.com/documentation/services/notification-hubs/).
