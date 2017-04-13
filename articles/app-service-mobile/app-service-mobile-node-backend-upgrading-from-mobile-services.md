<properties
    pageTitle="Eseguire l'aggiornamento da servizi mobili a Azure App servizio - Node"
    description="Informazioni su come aggiornare facilmente l'applicazione Servizi mobili a un'App di App servizio Mobile"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Aggiornamento del servizio di dispositivi mobili di Azure Node esistente al servizio di App

Servizio di App Mobile è una nuova modalità di compilazione di applicazioni per dispositivi mobili utilizzando Microsoft Azure. Per ulteriori informazioni, vedere [quali sono App Mobile?].

In questo argomento viene descritto come aggiornare un'applicazione di back-end Node esistente da servizi di dispositivi mobili Windows Azure a una nuova App Mobile servizio di App. Mentre si esegue l'aggiornamento, l'applicazione Servizi mobili esistente può continuare a funzionare.  Se è necessario eseguire l'aggiornamento di un'applicazione di back-end Node, consultare [l'aggiornamento dei servizi Mobile .NET](./app-service-mobile-net-upgrading-from-mobile-services.md).

Quando si aggiorna un back-end per dispositivi mobili al servizio App Azure, ha accesso a tutte le funzionalità del servizio di App ed effettuata in base alle [App servizio prezzi], non i servizi mobili prezzi.

## <a name="migrate-vs-upgrade"></a>Eseguire la migrazione e aggiornamento

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] È consigliabile che è possibile [eseguire una migrazione](app-service-mobile-migrating-from-mobile-services.md) prima passano attraverso un aggiornamento. In questo modo, è possibile mettere entrambe le versioni dell'applicazione nel piano di servizio stesso App e non sostenere costi aggiuntivi.

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Miglioramenti introdotti in Mobile App Node server SDK

L'aggiornamento a nuovo [Mobile App SDK](https://www.npmjs.com/package/azure-mobile-apps) fornisce numerosi miglioramenti, tra cui:

- Basata su [Express framework](http://expressjs.com/en/index.html), il nuovo SDK nodo è leggera e progettato per mantenersi nuove versioni nodo man mano che arrivano. È possibile personalizzare il comportamento dell'applicazione con middleware Express.

- Miglioramenti significativi delle prestazioni rispetto a Mobile Services SDK.

- A questo punto è possibile ospitare un sito Web con i dispositivi mobili back-end; Analogamente, è facile aggiungere Azure Mobile SDK a qualsiasi applicazione di express.v4 esistente.

- Progettate per lo sviluppo multipiattaforma e locale, Mobile App SDK possibile sviluppati ed eseguire localmente piattaforme OS x, Windows e Linux. A questo punto è facile da usare tecniche di sviluppo nodo comuni ad esempio l'esecuzione di test [Mocha](https://mochajs.org/) prima della distribuzione.

## <a name="overview"></a>Panoramica di aggiornamento di base

Per facilitare l'aggiornamento di un back-end Node, servizio di Azure App ha fornito un pacchetto di compatibilità.  Dopo l'aggiornamento, si verificherà un sito di niew che può essere distribuito in un nuovo sito di servizio di App.

Il client di servizi mobili SDK sono **non** compatibile con il nuovo server App Mobile SDK. Per garantire la continuità del servizio per l'app, non è possibile pubblicare le modifiche apportate a un sito che servono attualmente client pubblicato. Se, tuttavia, è necessario creare una nuova app per dispositivi mobili che funge da un duplicato. È possibile posizionare l'applicazione nello stesso piano di servizio di App per evitare incorrere costo aggiuntivo.

Sarà quindi possibile due versioni dell'applicazione: uno che rimarrà invariato e serve pubblicate App in caratteri e l'altra che sarà possibile aggiornare e di destinazione con una nuova versione di client. È possibile spostare e testare il codice le esigenze, ma è necessario assicurarsi che le correzioni apportate venga applicate a entrambi. Quando si ritiene che un numero di client App nell'ambiente naturale sono aggiornati alla versione più recente desiderato, è possibile eliminare l'app di migrazione originale se lo si desidera. Essa non comportano costi aggiuntivi monetari, se ospitato nello stesso piano di servizio App le App Mobile.

La struttura completa per il processo di aggiornamento è il seguente:

1. Scaricare il servizio di Mobile Azure (migrati) esistente.
2. Convertire il progetto in un'App Mobile Azure utilizzando il pacchetto di compatibilità.
3. Correggere le eventuali differenze (ad esempio le impostazioni di autenticazione).
4. Distribuire il progetto App Mobile Azure convertito in un nuovo servizio di App.
4. Rilasciare una nuova versione dell'applicazione client che utilizzano la nuova App Mobile.
5. (Facoltativo) Eliminare l'applicazione di servizio per dispositivi mobili migrati originale.

Eliminazione può verificarsi quando non è visibile tutto il traffico del servizio di dispositivi mobili migrati originale.

## <a name="install-npm-package"></a>Installare i prerequisiti

È necessario installare [nodo] sul computer locale.  Installare anche il pacchetto di compatibilità.  Dopo l'installazione di nodo, è possibile eseguire il seguente comando da un nuovo cmd o prompt dei comandi PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Ottenere gli script di Azure servizi per dispositivi mobili

- Accedere al [portale di Azure].
- Usa **Tutte le risorse** o **App Services**, individuare il sito di servizi mobili.
- All'interno del sito, fare clic su **Strumenti** -> **Kudu** -> **Vai** per aprire il sito Kudu.
- Fare clic su **Console di Debug** -> **PowerShell** per aprire la console di Debug.
- Passare a `site/wwwroot/App_Data/config` , fare clic su per ogni directory
- Fare clic sull'icona di download accanto al `scripts` directory.

Questa operazione verrà scaricate gli script in formato ZIP.  Creare una nuova directory sul computer locale e decomprimere il `scripts.ZIP` file all'interno della directory.  Verrà creata una `scripts` directory.

## <a name="scaffold-app"></a>Scaffold back-end nuovo App Mobile Azure

Eseguire il comando seguente dalla directory contenente la directory script:

```scaffold-mobile-app scripts out```

Verrà creato un supporto temporaneo App Mobile Azure back-end di `out` directory.  Sebbene non sia obbligatorio, è consigliabile verificare la `out` directory in un repository di codice sorgente preferito.

## <a name="deploy-ama-app"></a>Distribuire il back-end App Mobile Azure

Durante la distribuzione, sarà necessario eseguire le operazioni seguenti:

1. Creare una nuova App Mobile nel [Portale di Azure].
2. Eseguire il `createViews.sql` script sul database connesso.
3. Collegare il database è collegato al servizio di dispositivi mobili per il nuovo servizio di App.
4. Collegare altre risorse (ad esempio hub di notifica) al nuovo servizio di App.
5. Distribuire il codice generato al nuovo sito.

### <a name="create-a-new-mobile-app"></a>Creare una nuova App Mobile

1. Accedere al [portale di Azure].

2. Fare clic su **+ Nuovo** > **Web + Mobile** > **App Mobile**, quindi immettere un nome per il back-end App Mobile.

3. Per il **Gruppo di risorse**, selezionare un gruppo di risorse esistente o crearne uno nuovo (come l'app, con lo stesso nome). 
 
    È possibile selezionare un altro piano di servizio App o crearne uno nuovo. Per ulteriori informazioni sui servizi di App piani e come creare un nuovo piano in un prezzi diversi livelli e nella posizione desiderata, vedere [panoramica approfondita piani servizio App Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Per il **piano di servizio App**, il piano predefinito (nel [livello Standard](https://azure.microsoft.com/pricing/details/app-service/)) sia selezionato. È anche possibile selezionare il piano o [crearne uno nuovo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Impostazioni del piano di servizio di App determinano la [posizione, caratteristiche, costi e calcolare risorse](https://azure.microsoft.com/pricing/details/app-service/) associato all'app. 

    Dopo aver scelto il piano, fare clic su **Crea**. In questo modo si crea back-end App Mobile. 


### <a name="run-createviewssql"></a>Eseguire CreateViews.SQL

L'app supporto temporaneo contiene un file denominato `createViews.sql`.  Questo script deve essere eseguito in database di destinazione.  Il servizio di dispositivi mobili migrati da e **l'Impostazioni** in **Stringhe di connessione**, è possibile ottenere la stringa di connessione per il database di destinazione.  È denominato `MS_TableConnectionString`.

È possibile eseguire questo script dall'interno di SQL Server Management Studio o Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Collegare il Database del servizio di App

Collegare il database esistente per il servizio di App:

- Nel [Portale di Azure], aprire il servizio di App.
- Selezionare **tutte le impostazioni** -> **connessioni dati**.
- Fare clic su **+ Add**.
- Nell'elenco a discesa, selezionare **Database SQL**
- In **Database SQL**, selezionare il database esistente e quindi fare clic su **Seleziona**.
- In **stringa di connessione**, immettere il nome utente e la password per il database e quindi fare clic su **OK**.
- In e **l'Aggiungi le connessioni dati** , fare clic su **OK**.

Il nome utente e password sono disponibili visualizzando la stringa di connessione per il database di destinazione nel servizio di migrazione Mobile.


### <a name="set-up-authentication"></a>Configurare l'autenticazione

App Mobile Azure consente di configurare Azure Active Directory, Facebook, Google, Microsoft e Twitter autenticazione all'interno del servizio.  Autenticazione personalizzata sarà necessario devono essere sviluppati separatamente.  Consultare la documentazione relativa [Concetti di autenticazione] e [Guida introduttiva di autenticazione] per ulteriori informazioni.  

## <a name="updating-clients"></a>Aggiornare i client mobili

Dopo avere inserito un operativi back-end App Mobile, è possibile lavorare su una nuova versione dell'applicazione client che la utilizzi. App mobile include anche una nuova versione del client SDK e come l'aggiornamento del server in precedenza, sarà necessario rimuovere tutti i riferimenti a SDK di servizi Mobile prima di installare le versioni di App Mobile.

Una delle principali modifiche tra le versioni è che i costruttori non richiedono più un tasto applicazione. È ora è sufficiente passare l'URL dell'App Mobile. Ad esempio, nel client .NET il `MobileServiceClient` costruttore è:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Per ulteriori informazioni installazione nuovi SDK e l'utilizzo della nuova struttura tramite i collegamenti seguenti:

- [Android 2.2 o versione successiva](app-service-mobile-android-how-to-use-client-library.md)
- [iOS versione 3.0.0 o versioni successive](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin), versione 2.0.0 o versioni successive](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Apache Cordova versione 2.0 o versioni successiva](app-service-mobile-cordova-how-to-use-client-library.md)

Se l'applicazione consente di utilizzare le notifiche push, prendere nota delle istruzioni di registrazione specifico per ogni piattaforma come sono state apportate alcune modifiche sono anche.

Dopo aver la nuova versione del client pronta, provare per il progetto server aggiornato in. Dopo la convalida il corretto funzionamento, è possibile rilasciare una nuova versione dell'applicazione ai clienti. Infine, dopo i clienti hanno avuto la possibilità di ricevere gli aggiornamenti, è possibile eliminare la versione di servizi mobili dell'app. A questo punto è completamente aggiornata a un'App di Mobile servizio App utilizzando il server di App Mobile SDK più recente.

<!-- URLs. -->

[Portale di Azure]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Quali sono App Mobile?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Servizio di App prezzi]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Concetti di autenticazione]: ../app-service/app-service-authentication-overview.md
[Guida introduttiva di autenticazione]: app-service-mobile-auth.md

[Portale di Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
