<properties
    pageTitle="Gestione di registrazione"
    description="In questo argomento viene illustrato come registrare dispositivi con gli hub di notifica per ricevere notifiche push."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>Gestione di registrazione

##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come registrare dispositivi con gli hub di notifica per ricevere notifiche push. L'argomento descrive le registrazioni di alto livello e quindi introduce due modelli principali per i dispositivi di registrazione: la registrazione dal dispositivo direttamente all'hub di notifica e la registrazione tramite un back-end dell'applicazione. 


##<a name="what-is-device-registration"></a>Che cos'è la registrazione di dispositivo

Registrazione di dispositivo con un Hub di notifica viene utilizzata una **registrazione** o **l'installazione**.

#### <a name="registrations"></a>Registrazioni
Una registrazione associa il quadratino di piattaforma Notification Service (PNS) per un dispositivo con contrassegni ed eventualmente un modello. Il quadratino di PNS potrebbe essere un ChannelURI, token di dispositivo o identificativo GCM. Tag vengono utilizzati per instradare le notifiche per il set di punti di manipolazione di dispositivo corretto. Per ulteriori informazioni, vedere [Routing ed espressioni Tag](notification-hubs-tags-segment-push-message.md). I modelli vengono utilizzati per implementare trasformazione della registrazione. Per ulteriori informazioni, vedere [modelli](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Installazioni
Un'installazione è una migliore registrazione che include un elenco della push le proprietà correlate. È l'approccio più recente ed efficaci alla registrazione dei dispositivi. Tuttavia, non è supportato da parte di client .NET SDK ([SDK Hub di notifica per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) in ancora.  Di conseguenza, che se si desidera registrare dal dispositivo client stesso, è necessario utilizzare l'approccio [API REST hub di notifica](https://msdn.microsoft.com/library/mt621153.aspx) per supportare le installazioni. Se si utilizza un servizio di back-end, dovrebbe essere possibile utilizzare [SDK Hub di notifica per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Alcuni vantaggi principali di installazioni sono i seguenti:

* Creazione o aggiornamento di un'installazione è completamente idempotenti. Così è possibile ritentare senza dubbi sulle registrazioni duplicate.
* Il modello di installazione è facile eseguire singoli push - l'assegnazione del dispositivo specifico. Un tag di sistema **"$InstallationId: [ID di installazione]"** viene aggiunto automaticamente a ogni registrazione installazione in base a. Così è possibile chiamare una porta in questo tag a un dispositivo specifico senza la necessità di eseguire qualsiasi codifica supplementare.
* Utilizzo di installazioni è inoltre possibile eseguire gli aggiornamenti di registrazione parziale. Aggiornamento parziale di un'installazione è richiesto un metodo di PATCH utilizzando [JSON Patch standard](https://tools.ietf.org/html/rfc6902). Questa operazione è particolarmente utile quando si desidera aggiornare tag per la registrazione. Non è necessario estrarre verso il basso la registrazione intera e quindi di inviare nuovamente tutti i tag precedenti.

Un'installazione può contenere le proprietà seguenti. Per un elenco di completamento dell'installazione di visualizzare le proprietà, [Crea o Sovrascrivi un'installazione con API REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) o [Proprietà di installazione](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) per il.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

È importante tenere presente che le registrazioni e installazioni per impostazione predefinita non è più scadano.

Le registrazioni e installazioni devono contenere un valido punto di PNS per ogni dispositivo/canale. Perché solo è possibile ottenere punti di manipolazione di PNS in un'applicazione client nel dispositivo, un modello è necessario registrare direttamente sul dispositivo con l'applicazione client. Mano, considerazioni sulla sicurezza e regole di business correlati a tag potrebbe essere necessario gestire dispositivi di registrazione in app back-end. 

#### <a name="templates"></a>Modelli

Se si desidera utilizzare i [modelli](notification-hubs-templates-cross-platform-push-messages.md), l'installazione di dispositivo tenere anche tutti i modelli associati al dispositivo in un JSON formattare (vedere l'esempio precedente). I nomi dei modelli sono utili per diversi modelli di destinazione per lo stesso dispositivo.

Si noti che ogni nome del modello associato a un corpo del modello e un insieme facoltativo di tag. Inoltre, ogni piattaforma può avere proprietà aggiuntive modello. Per Windows Store (con WNS) e Windows Phone 8 (con MPNS), un ulteriore set di intestazioni possono far parte del modello. Nel caso dei APN, è possibile impostare una proprietà di scadenza a uno dei due una costante o un'espressione modello. Per un elenco di completamento dell'installazione di proprietà, vedere argomento [Crea o Sovrascrivi un'installazione con altri](https://msdn.microsoft.com/library/azure/mt621153.aspx) .

#### <a name="secondary-tiles-for-windows-store-apps"></a>Riquadri secondari per le applicazioni di Windows Store

Per le applicazioni client di Windows Store, invio di notifiche riquadri secondari corrisponde inviarli a quello principale. È supportata anche in installazioni. Si noti che le sezioni secondarie una ChannelUri diversi, SDK sull'applicazione client gestisce in modo trasparente.

Il dizionario SecondaryTiles utilizza TileId stessa che viene utilizzato per creare l'oggetto SecondaryTiles nell'app di Windows Store.
Come con ChannelUri principale, ChannelUris dei riquadri secondari può modificare in qualsiasi momento. Per mantenere le installazioni nell'hub notifica aggiornato, è necessario aggiornarli con ChannelUris corrente dei riquadri secondari il dispositivo.


##<a name="registration-management-from-the-device"></a>Gestione di registrazione dal dispositivo

Quando si gestisce la registrazione di dispositivo da applicazioni client, back-end è solo responsabile per l'invio di notifiche. Applicazioni client mantenere aggiornati i punti di manipolazione di PNS e registrare tag. L'immagine seguente viene illustrato questo modello.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Il dispositivo prima recupera il quadratino di PNS dalla PNS, quindi Registra direttamente con l'hub di notifica. Al termine della registrazione, back-end app possibile inviare una notifica che la registrazione di destinazione. Per ulteriori informazioni su come inviare notifiche, vedere [Routing ed espressioni Tag](notification-hubs-tags-segment-push-message.md).
Si noti che in questo caso, utilizzeranno ascoltare solo dei diritti di accesso l'hub di notifica dal dispositivo. Per ulteriori informazioni, vedere [protezione](notification-hubs-push-notification-security.md).

La registrazione dal dispositivo è il metodo più semplice, ma presenta alcuni svantaggi.
Il primo svantaggio è che un'applicazione client possibile aggiornare solo i tag quando l'app è attiva. Ad esempio, se un utente ha due dispositivi che registrare tag relativi ai team di sport, quando il dispositivo primo registra per un tag aggiuntivo (ad esempio Seahawks), il secondo dispositivo non riceverà le notifiche sul Seahawks fino a quando l'app nel secondo dispositivo viene eseguita una seconda volta. Più in generale, quando tag sono interessati da più dispositivi, la gestione di contrassegni da back-end è opportuno.
Il secondo svantaggio di gestione di registrazione dall'app client è che, poiché possono attacco al App, per proteggere la registrazione a tag specifici è necessario prestare particolare attenzione, come illustrato nella sezione "protezione a livello di Tag".



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Esempio di codice per registrare con un hub di notifica da un dispositivo che utilizza un'installazione 

Al momento supportato solo tramite l' [API REST hub di notifica](https://msdn.microsoft.com/library/mt621153.aspx).

È anche possibile utilizzare il metodo di PATCH mediante lo [standard JSON Patch](https://tools.ietf.org/html/rfc6902) per aggiornare l'installazione.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Esempio di codice per registrare con un hub di notifica da un dispositivo che usa una registrazione


Questi metodi creare o aggiornare una registrazione del dispositivo in cui vengono chiamati. Questo errore indica che per aggiornare il quadratino o i tag, sarà necessario sovrascrivere la registrazione intera. Tenere presente che le registrazioni sono temporanee, pertanto è necessario disporre di un archivio affidabile con il tag corrente che deve essere un dispositivo specifico.


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Gestione di registrazione da un back-end

Gestire le registrazioni da back-end richiede la scrittura di codice aggiuntivo. L'app dal dispositivo è necessario specificare PNS aggiornato gestire in modo da back-end ogni volta che l'avvio dell'applicazione (insieme tag e modelli) e back-end necessario aggiornare il punto di controllo sulla hub notifica. L'immagine seguente illustra la struttura.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

I vantaggi della gestione registrazioni di back-end sono la possibilità di modificare i tag alle registrazioni anche quando l'applicazione corrispondente nel dispositivo è inattivo e per eseguire l'autenticazione dell'applicazione client prima di aggiungere un tag per la registrazione.


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Esempio di codice per registrare con un hub di notifica da un back-end tramite un'installazione

Il dispositivo client ancora ottenuto il quadratino di PNS e le proprietà di installazione pertinenti come prima e chiama un'API personalizzata in back-end che consentono di eseguire la registrazione e autorizzare tag e così via. Back-end possibile sfruttare le [SDK Hub di notifica per le operazioni di back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

È anche possibile utilizzare il metodo di PATCH mediante lo [standard JSON Patch](https://tools.ietf.org/html/rfc6902) per aggiornare l'installazione.
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Esempio di codice per registrare con un hub di notifica da un dispositivo con un id di registrazione

Da back-end l'app, è possibile eseguire operazioni di base CRUDS nelle registrazioni. Per esempio:

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


Back-end necessario gestire concorrenza tra gli aggiornamenti di registrazione. Servizio Bus offre ottimistica controllo per la gestione di registrazione. Al livello HTTP, questo viene implementato con l'utilizzo di ETag su operazioni di gestione di registrazione. Questa funzionalità trasparente è utilizzata da Microsoft SDKs, che generano un'eccezione in caso di rifiuto un aggiornamento per motivi di concorrenza. Back-end app è responsabile per la gestione delle eccezioni e ritentare l'aggiornamento, se necessario.