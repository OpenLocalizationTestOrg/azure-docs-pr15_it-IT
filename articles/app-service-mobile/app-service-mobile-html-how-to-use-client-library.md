<properties
    pageTitle="Come usare SDK JavaScript per Azure App per dispositivi mobili"
    description="Come usare v per le App per dispositivi mobili Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Come usare la libreria JavaScript Client per Azure App per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida viene descritto come eseguire gli scenari comuni utilizzando la versione più recente [SDK JavaScript per App Mobile Azure]. Se ha familiarità con App Mobile Azure, completare [Azure Mobile App introduttive] per creare un back-end e creare una tabella. In questa guida esaminato l'utilizzo di back-end di dispositivi mobili nelle applicazioni Web HTML/JavaScript.

## <a name="supported-platforms"></a>Piattaforme supportate

È necessario limitare browser supportati per le versioni corrente e il cognome dei browser principali: Google Chrome, Microsoft Edge, Microsoft Internet Explorer e Mozilla Firefox.  Si prevede che SDK per funzionare con qualsiasi browser relativamente moderno.

Il pacchetto viene distribuito come modulo JavaScript universale, in modo che supporta globali, AMD, e CommonJS formati.

##<a name="Setup"></a>Il programma di installazione e i prerequisiti

Questa guida presuppone che sia stato creato un back-end con una tabella. Questa guida presuppone che la tabella con lo stesso schema le tabelle in queste esercitazioni.

L'installazione di Azure Mobile App JavaScript SDK possono essere eseguita tramite il `npm` comando:

```
npm install azure-mobile-apps-client --save
```

Dopo aver installato, la libreria si trova `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Copiare il file all'area del proprio web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

La raccolta è utilizzabile anche come modulo ES2015 in ambienti CommonJS, ad esempio Browserify e Webpack e come una raccolta di AMD.  Per esempio:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Procedura: autenticazione degli utenti

Azure servizio App supporta l'autenticazione e autorizzazione degli utenti app con diversi provider di identità esterni: Facebook, Google, Account Microsoft e Twitter. È possibile impostare le autorizzazioni nelle tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È anche possibile utilizzare l'identità degli utenti autenticati per implementare le regole di autorizzazione in script server. Per ulteriori informazioni, vedere l'esercitazione [iniziare a utilizzare l'autenticazione] .

Due flussi di autenticazione supportati: un flusso di server e un flusso di client.  Il flusso relativo al server offre l'esperienza di autenticazione più semplice, come si basa sull'interfaccia di autenticazione web del provider. Il flusso relativo al client consente l'integrazione più approfondita con funzionalità specifiche del dispositivo, ad esempio single sign-on mentre si basa su SDK specifiche del provider.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Procedura: configurare il servizio di App per dispositivi mobili per gli URL di reindirizzamento esterni.

Diversi tipi di applicazioni JavaScript utilizzano la funzionalità di loopback per gestire flussi OAuth UI.  Queste funzionalità includono:

* Servizio localmente
* Utilizzo di Live Ricarica con Framework
* Reindirizzamento al servizio di App per l'autenticazione. 

In esecuzione in locale possono causare problemi perché, per impostazione predefinita, l'autenticazione del servizio di App solo è configurato per consentire l'accesso dal back-end App Mobile. Per modificare le impostazioni del servizio di App per abilitare l'autenticazione durante l'esecuzione del server in locale, procedere come segue:

1. Accedere al [portale di Azure]
2. Passare al back-end App Mobile.
3. Selezionare **Esplora risorse** nel menu **Strumenti di sviluppo** .
4. Fare clic su **Vai** per aprire Esplora risorse per il back-end App Mobile in una nuova scheda o finestra.
5. Espandere **configurazione** > **authsettings** nodo per l'app.
6. Fare clic sul pulsante **Modifica** per consentire la modifica della risorsa.
7. Trovare l'elemento **allowedExternalRedirectUrls** , che deve essere null. Aggiungere gli URL in una matrice:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Sostituire l'URL della matrice con l'URL del servizio, ossia in questo esempio `http://localhost:3000` per il servizio di esempio Node locale. È inoltre possibile utilizzare `http://localhost:4400` per il servizio di increspatura o altri URL, a seconda della configurazione dell'applicazione.

8. Nella parte superiore della pagina, fare clic su **Lettura/scrittura**, quindi fare clic su **mettere** per salvare le modifiche apportate.

Inoltre, è necessario aggiungere gli stessi URL loopback alle impostazioni di proprietà consentite CORS:

1. Passare al [portale di Azure].
2. Passare al back-end App Mobile.
3. Fare clic su **CORS** nel menu **API** .
4. Immettere ogni URL nella casella di testo vuota **Consentito origini diverse** .  Viene creata una nuova casella di testo.
5. Fare clic su **Salva**
    
Dopo l'aggiornamento di back-end, sarà possibile usare i nuovi URL loopback nell'app.

<!-- URLs. -->
[Azure Mobile App introduttiva]: app-service-mobile-cordova-get-started.md
[Iniziare a utilizzare l'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portale di Azure]: https://portal.azure.com/
[SDK JavaScript per Azure App per dispositivi mobili]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

