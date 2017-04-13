<properties
    pageTitle="Eseguire l'aggiornamento da servizi mobili a servizio App Azure"
    description="Informazioni su come aggiornare facilmente l'applicazione Servizi mobili a un'App di App servizio Mobile"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Aggiornamento del servizio di dispositivi mobili di Azure .NET esistente al servizio di App

Servizio di App Mobile è una nuova modalità di compilazione di applicazioni per dispositivi mobili utilizzando Microsoft Azure. Per ulteriori informazioni, vedere [quali sono App Mobile?].

In questo argomento viene descritto come aggiornare un'applicazione di back-end .NET esistente da servizi di dispositivi mobili Windows Azure a una nuova App Mobile servizio di App. Mentre si esegue l'aggiornamento, l'applicazione Servizi mobili esistente può continuare a funzionare.   Se è necessario eseguire l'aggiornamento di un'applicazione di back-end Node, consultare [l'aggiornamento dei servizi Mobile Node](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quando si aggiorna un back-end per dispositivi mobili al servizio App Azure, ha accesso a tutte le funzionalità del servizio di App ed effettuata in base alle [App servizio prezzi], non i servizi mobili prezzi.

##<a name="migrate-vs-upgrade"></a>Eseguire la migrazione e aggiornamento

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] È consigliabile che è possibile [eseguire una migrazione](app-service-mobile-migrating-from-mobile-services.md) prima passano attraverso un aggiornamento. In questo modo, è possibile mettere entrambe le versioni dell'applicazione nel piano di servizio stesso App e non sostenere costi aggiuntivi.

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>Miglioramenti Mobile app .NET Server SDK

L'aggiornamento a nuovo [SDK App Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) offre i seguenti vantaggi:

- Maggiore flessibilità sulle dipendenze NuGet. Ambiente di hosting non fornisce più le versioni dei pacchetti NuGet, in modo che è possibile utilizzare le versioni compatibili alternative. Tuttavia, se sono presenti nuovi bugfixes critici o aggiornamenti della sicurezza al Mobile Server SDK o dipendenze, è necessario aggiornare il servizio manualmente.

- Maggiore flessibilità nella SDK per dispositivi mobili. È possibile controllare in modo esplicito le caratteristiche e indirizza sta configurati, ad esempio l'autenticazione, tabella API e l'endpoint di registrazione push. Per ulteriori informazioni, vedere [come utilizzare il server di .NET SDK per App Mobile Azure](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Supporto per gli altri tipi di progetto ASP.NET e indirizza. A questo punto è possibile ospitare controller MVC e API Web nello stesso progetto del progetto per dispositivi mobili back-end.

- Supporto per nuove caratteristiche di autenticazione servizio App, che consentono di utilizzare una configurazione di autenticazione comune tra il web e App per dispositivi mobili.

##<a name="overview"></a>Panoramica di aggiornamento di base

In molti casi l'aggiornamento sarà sufficiente passare a nuove app Mobile server SDK e ripubblicare il codice in una nuova istanza di App Mobile. Esistono tuttavia alcuni scenari che richiederanno un'ulteriore configurazione, ad esempio scenari di autenticazione avanzate e utilizzo dei processi pianificati. Ognuna di queste è coperto nelle sezioni successive.

>[AZURE.TIP] Si consiglia di leggere e comprendere il resto di questo argomento completamente prima di avviare un aggiornamento. Prendere nota delle funzionalità di utilizzare evidenziati sotto.

Il client di servizi mobili SDK sono **non** compatibile con il nuovo server App Mobile SDK. Per garantire la continuità del servizio per l'app, non è possibile pubblicare le modifiche apportate a un sito che servono attualmente client pubblicato. Se, tuttavia, è necessario creare una nuova app per dispositivi mobili che funge da un duplicato. È possibile posizionare l'applicazione nello stesso piano di servizio di App per evitare incorrere costo aggiuntivo.

Sarà quindi possibile due versioni dell'applicazione: uno che rimarrà invariato e serve pubblicate App in caratteri e l'altra che sarà possibile aggiornare e di destinazione con una nuova versione di client. È possibile spostare e testare il codice le esigenze, ma è necessario assicurarsi che le correzioni apportate venga applicate a entrambi. Quando si ritiene che un numero di client App nell'ambiente naturale sono aggiornati alla versione più recente desiderato, è possibile eliminare l'app di migrazione originale se lo si desidera.

La struttura completa per il processo di aggiornamento è il seguente:

1. Creare una nuova App Mobile
2. Aggiornare il progetto per utilizzare i nuovi SDK Server
3. Rilasciare una nuova versione dell'applicazione client
4. (Facoltativo) Eliminare l'istanza migrato originale

##<a name="mobile-app-version"></a>Creazione di una seconda istanza dell'applicazione
Aggiornare il primo passaggio consiste nel creare la risorsa App Mobile che conterrà la nuova versione dell'applicazione. Se già stata eseguita la migrazione un servizio per dispositivi mobili esistente, desiderata creare questa versione sullo stesso piano di hosting. Aprire il [portale di Azure] e passare all'applicazione migrati. Prendere nota del piano App servizio in cui viene eseguito.

Creare la seconda istanza dell'applicazione seguendo le [istruzioni di creazione di .NET back-end](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quando viene richiesto di selezionare il piano di servizio App o "piano hosting" scegliere il piano dell'applicazione migrati.

Si probabile che desidera utilizzare il database stesso e notifica Hub seguendo servizi mobili. È possibile copiare questi valori, aprire [il portale di Azure] e accedere all'applicazione originale e quindi fare clic su **Impostazioni** > **le impostazioni dell'applicazione**. Nella sezione **Stringhe di connessione**, copiare `MS_NotificationHubConnectionString` e `MS_TableConnectionString`. Passare al sito di nuovo a eseguire l'aggiornamento e incollarle in sovrascrivere i valori esistenti. Ripetere questa procedura per tutte le altre impostazioni di applicazione delle esigenze di app. Se non si utilizza un servizio di migrazione, è possibile leggere stringhe di connessione e le impostazioni dell'app nella scheda **Configura** della sezione dei servizi mobili del [portale classica Azure].

Creare una copia del progetto ASP.NET dell'applicazione e pubblicarla al nuovo sito. Utilizzando una copia dell'applicazione client aggiornato con il nuovo URL, verificare che tutto funzioni come previsto.

## <a name="updating-the-server-project"></a>Aggiornamento di project server

App Mobile offre un nuovo [Mobile App Server SDK] che fornisce gran parte della stessa funzionalità runtime Servizi mobili. Prima di tutto, è necessario rimuovere tutti i riferimenti per i pacchetti di servizi mobili. In Gestione pacchetto NuGet, cercare `WindowsAzure.MobileServices.Backend`. La maggior parte delle App vedranno diversi pacchetti qui, tra cui `WindowsAzure.MobileServices.Backend.Tables` e `WindowsAzure.MobileServices.Backend.Entity`. In questo caso, iniziare con il pacchetto più basso nella struttura della dipendenza, ad esempio `Entity`e rimuoverla. Quando richiesto, non vengono rimossi tutti i pacchetti di dipendenti. Ripetere questa procedura fino a quando non è stato rimosso `WindowsAzure.MobileServices.Backend` stesso.

A questo punto è un progetto che non fa più riferimento SDK servizi Mobile.

Aggiungerà riferimenti Mobile App SDK. Per questo aggiornamento maggior parte dei casi sarà necessario scaricare e installare il `Microsoft.Azure.Mobile.Server.Quickstart` creare un pacchetto, come questo verrà estrarre l'intero set necessario.

Vi sono alcune errori del compilatore risultante dalla differenze tra gli SDK, ma questi semplici all'indirizzo e rientrano il resto di questa sezione.

### <a name="base-configuration"></a>Configurazione di base

Quindi, in WebApiConfig.cs, è possibile sostituire:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

con

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Se si desidera ottenere ulteriori informazioni sul nuovo .NET server SDK e come Aggiungi/Rimuovi caratteristiche dell'App, vedere l'argomento [come utilizzare il server di .NET SDK] .

Se l'app rende uso delle funzionalità di autenticazione, è necessario anche registrare un middleware OWIN. In questo caso, spostare il codice di configurazione precedente in una nuova classe OWIN avvio.

1. Aggiungere il pacchetto NuGet `Microsoft.Owin.Host.SystemWeb` se non è già incluso nel progetto.
2. In Visual Studio, fare clic con il pulsante destro del progetto e selezionare **Aggiungi** -> **Nuovo elemento**. Selezionare **Web** -> **generali** -> **OWIN avvio classe**.
3. Spostare il codice sopra di MobileAppConfiguration da `WebApiConfig.Register()` per il `Configuration()` metodo della nuova classe avvio.

Assicurarsi che la `Configuration()` metodo termina con:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Sono disponibili ulteriori modifiche alla relativi all'autenticazione che rientrano nella sezione autenticazione completa.

### <a name="working-with-data"></a>Utilizzo dei dati

Servizi Mobile, il nome dell'app per dispositivi mobili served come nome schema predefinito nel programma di installazione Framework entità.

Per assicurarsi di avere lo stesso schema cui viene fatto riferimento come al solito, utilizzare i seguenti per impostare lo schema in DbContext per l'applicazione:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Verificare di avere MS_MobileServiceName impostare se si le risposte sopra. Se l'applicazione lo personalizzato in precedenza, è anche possibile fornire un altro nome di schema.

### <a name="system-properties"></a>Proprietà-sistema

#### <a name="naming"></a>Assegnare un nome

Nel server di servizi di dispositivi mobili Windows Azure SDK proprietà-sistema sempre contenere un carattere di sottolineatura doppia (`__`) prefisso per le proprietà:

- __createdAt
- __updatedAt
- __deleted
- __version

Il client di servizi mobili SDK hanno logica speciale per l'analisi delle proprietà-sistema in questo formato.

Nell'App Mobile Azure, proprietà-sistema non è più hanno un formato speciale e disporre i nomi seguenti:

- createdAt
- updatedAt
- eliminata
- Versione

Il client App Mobile SDK utilizzare i nuovi nomi di proprietà di sistema, in modo che non sono necessarie al codice client modifiche. Tuttavia, se direttamente chiamate vengano resto del servizio quindi è necessario modificare le query di conseguenza.

#### <a name="local-store"></a>Archivio locale

Modifiche ai nomi di proprietà-sistema indicano che un database locale sincronizzazione offline per i servizi Mobile non è compatibile con App Mobile. Se possibile, è consigliabile evitare l'aggiornamento di client App da servizi mobili alle App Mobile fino a dopo le modifiche in sospeso sono stati inviati al server. App aggiornata quindi necessario utilizzare un nuovo nome di database.

Se un'applicazione client viene aggiornata da servizi mobili a App Mobile sono presenti modifiche non in linea in sospeso nella coda di operazione, il database di sistema deve aggiornato per usare i nuovi nomi di colonna. In iOS, ciò può essere ottenuto utilizzando le migrazioni da leggere e modificare i nomi delle colonne. Android e il client gestito .NET, scrivere codice SQL personalizzato per rinominare le colonne per le tabelle di oggetto dati.

In iOS, è necessario modificare lo schema dei dati di base per l'entità di dati in modo che corrispondano le operazioni seguenti. Si noti che le proprietà `createdAt`, `updatedAt` e `version` non è un `ms_` prefisso:

| Attributo |  Tipo   | Nota                                                 |
|---------- |  ------ | -----------------------------------------------------|
| ID        | Stringa obbligatoria  | chiave primaria nell'archivio remoto         |
| createdAt | Data    | (facoltativo) esegue il mapping a proprietà di sistema createdAt         |
| updatedAt | Data    | (facoltativo) esegue il mapping a proprietà di sistema updatedAt         |
| Versione   | Stringa  | (facoltativo) utilizzati per rilevare conflitti, mappe alla versione |

#### <a name="querying-system-properties"></a>La ricerca delle proprietà di sistema

Servizi di dispositivi mobili Windows Azure, proprietà-sistema non vengono inviate per impostazione predefinita, ma solo quando vengono richiesti utilizzando la stringa di query `__systemProperties`. Invece nel sistema di App Mobile Azure proprietà sono **sempre selezionata** dal momento che fanno parte del modello a oggetti SDK server.

Questa modifica influisce principalmente implementazioni personalizzate di responsabili di dominio, ad esempio le estensioni di `MappedEntityDomainManager`. Servizi di dispositivi mobili, se un client non richiede mai le proprietà di sistema, è possibile utilizzare una `MappedEntityDomainManager` che non viene effettivamente mappato tutte le proprietà. Tuttavia, nell'App Mobile Azure, queste proprietà non mappate si verificherà un errore nella query GET.

Il modo più semplice per risolvere il problema consiste nel modificare il DTOs in modo che ereditano da `ITableData` anziché `EntityData`. Aggiungere quindi il `[NotMapped]` dell'attributo ai campi che devono essere omessi.

Ad esempio, le seguenti definizioni `TodoItem` senza proprietà di sistema:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Nota: se si riceve errori `NotMapped`, aggiungere un riferimento all'assembly `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS

Servizi mobili incluso il supporto per CORS racchiudendo la soluzione CORS ASP.NET. Questo livello di ritorno a capo è stata rimossa per assegnare lo sviluppatore per un maggiore controllo, in modo che è possibile sfruttare direttamente [CORS ASP.NET supportano](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Le principali aree di interesse Se Usa CORS sono che la `eTag` e `Location` intestazioni devono disporre dell'autorizzazione per poter client SDK funzionare correttamente.

### <a name="push-notifications"></a>Notifiche push
Per push, l'elemento principale che potrebbero risultare mancanti messaggi dal Server SDK è la classe PushRegistrationHandler. Le registrazioni sono gestite in modo leggermente diverso in App Mobile e le registrazioni privi abilitate per impostazione predefinita. Gestione dei contrassegni può essere eseguita tramite API personalizzate. Vedere le istruzioni di [registrazione per i tag](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) per altre informazioni.

### <a name="scheduled-jobs"></a>Programmate
Programmate non vengono incorporati in App Mobile, in modo che tutti i processi esistenti con il back-end .NET dovrà essere aggiornato singolarmente. Un'opzione consiste nel creare una pianificato [Processo Web] nel sito di codice App Mobile. È anche Impossibile impostare un controller che contiene il codice di processo e configurare l' [Utilità di pianificazione di Azure] per raggiunto tale endpoint in base alla pianificazione previsto.

### <a name="miscellaneous-changes"></a>Modifiche varie
A questo punto è necessario disporre tutti ApiControllers che verrà utilizzato da un client mobile di `[MobileAppController]` attributo. Non è più necessario inclusi per impostazione predefinita in modo che gli altri ApiControllers per passare invariati dai formattatori per dispositivi mobili.

Il `ApiServices` oggetto non è più parte di SDK. Per accedere alle impostazioni di App Mobile, è possibile utilizzare le operazioni seguenti:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Analogamente, registrazione a questo punto viene utilizzata la scrittura di traccia ASP.NET standard:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considerazioni di autenticazione

I componenti di autenticazione di servizi di dispositivi mobili sono stati spostati nella funzionalità di autenticazione/autorizzazione del servizio di App. Per informazioni sull'attivazione di questa per il sito, leggere l'argomento [aggiungere autenticazione per l'app per dispositivi mobili](app-service-mobile-ios-get-started-users.md) .

Per alcuni provider, ad esempio AAD, Facebook e Google, dovrebbe essere possibile sfruttare la registrazione dall'applicazione di copia. È sufficiente passare al portale del provider di identità e aggiungere un nuovo URL di reindirizzamento per la registrazione. Configurare l'autenticazione/autorizzazione del servizio di App con l'ID client e segreto.

### <a name="controller-action-authorization"></a>Autorizzazione di azione controller
Tutte le istanze della `[AuthorizeLevel(AuthorizationLevel.User)]` attributo ora deve essere modificato per utilizzare ASP.NET standard `[Authorize]` attributo. Inoltre, controller sono ora anonimo per impostazione predefinita, ad esempio in altre applicazioni di ASP.NET.
Se si sta utilizzando una delle altre opzioni AuthorizeLevel, ad esempio amministratore o applicazione, tenere presente che queste siano più disponibile. È invece possibile configurare AuthorizationFilters per informazioni riservate condivisi o configurare un capitale di servizio AAD per attivare le chiamate al servizio in modo sicuro.

### <a name="getting-additional-user-information"></a>Recupero di informazioni utente aggiuntive

È possibile ottenere informazioni utente aggiuntive, inclusi i token di accesso attraverso il `GetAppServiceIdentityAsync()` metodo:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Inoltre, se l'applicazione esegue le dipendenze in ID utente, ad esempio archiviarli in un database, è importante tenere presente che l'ID utente tra servizi mobili e App Mobile servizio App sono diversi. È ancora possibile ottenere l'ID utente di servizi Mobile, attraverso. Tutte le sottoclassi ProviderCredentials hanno una proprietà ID utente. Continuare così dell'esempio prima di:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Se l'app impostare dipendenze ID utente, è importante che si sfrutta la stessa registrazione con un provider di identità se possibile. ID utente sono limitati alla registrazione dell'applicazione che è stata utilizzata in genere in modo da introduzione a una nuova registrazione Impossibile creare i problemi di utenti ai propri dati corrispondenti.

### <a name="custom-authentication"></a>Autenticazione personalizzata

Se l'app Usa una soluzione di autenticazione personalizzata, desiderata per assicurarsi che il sito aggiornato abbia accesso al sistema. Seguire le istruzioni per l'autenticazione personalizzata in [Panoramica di .NET server SDK] nuove per integrare la soluzione. Si noti che i componenti di autenticazione personalizzati sono ancora in anteprima.

##<a name="updating-clients"></a>Aggiornamento di client
Dopo avere inserito un operativi back-end App Mobile, è possibile lavorare su una nuova versione dell'applicazione client che la utilizzi. App mobile include anche una nuova versione del client SDK e come l'aggiornamento del server in precedenza, sarà necessario rimuovere tutti i riferimenti a SDK di servizi Mobile prima di installare le versioni di App Mobile.

Una delle principali modifiche tra le versioni è che i costruttori non richiedono più un tasto applicazione. È ora è sufficiente passare l'URL dell'App Mobile. Ad esempio, nel client .NET il `MobileServiceClient` costruttore è:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Per ulteriori informazioni installazione nuovi SDK e l'utilizzo della nuova struttura tramite i collegamenti seguenti:

- [iOS versione 3.0.0 o versioni successive](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin), versione 2.0.0 o versioni successive](app-service-mobile-dotnet-how-to-use-client-library.md)

Se l'applicazione consente di utilizzare le notifiche push, prendere nota delle istruzioni di registrazione specifico per ogni piattaforma come sono state apportate alcune modifiche sono anche.

Dopo aver la nuova versione del client pronta, provare per il progetto server aggiornato in. Dopo la convalida il corretto funzionamento, è possibile rilasciare una nuova versione dell'applicazione ai clienti. Infine, dopo i clienti hanno avuto la possibilità di ricevere gli aggiornamenti, è possibile eliminare la versione di servizi mobili dell'app. A questo punto è completamente aggiornata a un'App di Mobile servizio App utilizzando il server di App Mobile SDK più recente.

<!-- URLs. -->

[Portale di Azure]: https://portal.azure.com/
[Portale classica Azure]: https://manage.windowsazure.com/
[Quali sono App Mobile?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[SDK Server App per dispositivi mobili]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Utilità di pianificazione Azure]: /en-us/documentation/services/scheduler/
[Processo Web]: ../app-service-web/websites-webjobs-resources.md
[Come utilizzare il server di .NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Servizio di App prezzi]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Panoramica di .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
