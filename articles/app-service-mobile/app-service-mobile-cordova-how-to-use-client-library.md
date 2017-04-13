<properties
    pageTitle="Come usare plug-in Cordova Apache per Azure App per dispositivi mobili"
    description="Come usare plug-in Cordova Apache per Azure App per dispositivi mobili"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Come usare Apache Cordova Client raccolta per Azure App per dispositivi mobili

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Questa guida viene descritto come eseguire gli scenari comuni utilizzando il più recente [Apache Cordova plug-in per le App per dispositivi mobili Azure]. Se ha familiarità con App Mobile Azure, prima completo [Avvio rapido di Azure Mobile App] per creare un back-end, creare una tabella e scaricare un progetto Cordova Apache predefinito. In questa guida l'attenzione sul lato client Apache Cordova plug-in.

## <a name="supported-platforms"></a>Piattaforme supportate

Questo SDK supporta Apache Cordova v6.0.0 e versioni successive su iOS, Android e Windows dispositivi.  Il supporto di piattaforma è come indicato di seguito:

* API Android 19-24 (KitKat tramite Nougat)
* iOS 8.0 e versioni successive.
* Windows Phone 8.0
* Windows Phone 8.1
* Piattaforma Windows universale

##<a name="Setup"></a>Il programma di installazione e i prerequisiti

Questa guida presuppone che sia stato creato un back-end con una tabella. Questa guida presuppone che la tabella con lo stesso schema le tabelle in queste esercitazioni. Sul presupposto che sia stato aggiunto il plug-in Cordova Apache al codice.  Se si è già stato, è possibile aggiungere il plug-in Apache Cordova al progetto nella riga di comando:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Per ulteriori informazioni sulla creazione [della prima applicazione Apache Cordova], vedere la relativa documentazione.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>Procedura: autenticazione degli utenti

Azure servizio App supporta l'autenticazione e autorizzazione degli utenti app con diversi provider di identità esterni: Facebook, Google, Account Microsoft e Twitter. È possibile impostare le autorizzazioni nelle tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È anche possibile utilizzare l'identità degli utenti autenticati per implementare le regole di autorizzazione in script server. Per ulteriori informazioni, vedere l'esercitazione [iniziare a utilizzare l'autenticazione] .

Quando si utilizza l'autenticazione in un'app Cordova Apache, i plug-in Cordova seguenti devono essere disponibili:

* [dispositivo di plug-in cordova]
* [inappbrowser di plug-in cordova]

Due flussi di autenticazione supportati: un flusso di server e un flusso di client.  Il flusso relativo al server offre l'esperienza di autenticazione più semplice, come si basa sull'interfaccia di autenticazione web del provider. Il flusso relativo al client consente l'integrazione più approfondita con funzionalità specifiche del dispositivo, ad esempio single sign-on mentre si basa su SDK specifico di dispositivo specifiche del provider.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Procedura: configurare il servizio di App per dispositivi mobili per gli URL di reindirizzamento esterni.

Diversi tipi di applicazioni Apache Cordova utilizzano la funzionalità di loopback per gestire flussi OAuth UI.  Flussi di OAuth UI sull'host locale provocare problemi poiché il servizio di autenticazione solo sia come utilizzare il servizio per impostazione predefinita.  Esempi di flussi di OAuth UI problematici:

- Emulatore increspatura.
- Live Ricarica con.
- Esecuzione locale back-end di dispositivi mobili
- Eseguire il back-end per dispositivi mobili in un servizio di App Azure diverso rispetto a un'autenticazione che fornisca.

Seguire queste istruzioni per aggiungere le impostazioni locali per la configurazione:

1. Accedere al [portale di Azure]
2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome dell'App Mobile.
3. Fare clic su **Strumenti**
4. Fare clic su **Esplora risorse** nel menu RISPETTA, quindi fare clic su **Vai**.  Una nuova finestra o viene visualizzata la scheda.
5. Espandere la **configurazione**, nodi **authsettings** per il sito nella barra di spostamento sinistro.
6. Fare clic su **Modifica**
7. Cercare l'elemento "allowedExternalRedirectUrls".  Potrebbe essere impostata su null o una matrice di valori.  Modificare il valore per il valore seguente:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Sostituire gli URL con l'URL del servizio.  Ad esempio "http://localhost:3000" (per il servizio di esempio Node) o "http://localhost:4400" (per il servizio di increspatura).  Tuttavia, questi URL sono esempi - base alla situazione specifica, tra cui per i servizi citati negli esempi, possono essere diversi.
8. Fare clic sul pulsante di **Lettura/scrittura** nell'angolo superiore destro dello schermo.
9. Fare clic su verde **inserire** pulsante.

A questo punto vengono salvate le impostazioni.  Chiudere la finestra del browser finché non si è stati le impostazioni di salvataggio.
Aggiungere anche questi URL loopback per le impostazioni di CORS per il servizio di App:

1. Accedere al [portale di Azure]
2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome dell'App Mobile.
3. E l'impostazioni viene aperto automaticamente.  In caso contrario, fare clic su **Tutte le impostazioni**.
4. Fare clic su **CORS** nel menu API.
5. Immettere l'URL che si desidera aggiungere nella casella ricevute le autorizzazioni e prema INVIO.
6. Immettere l'URL aggiuntive in base alle esigenze.
7. Fare clic su **Salva** per salvare le impostazioni.

Bastano circa 10-15 secondi per le nuove impostazioni abbiano effetto.

##<a name="register-for-push"></a>Procedura: eseguire la registrazione per le notifiche Push

Installare il [plug-in-phonegap-push] per gestire le notifiche push.  Il plug-in possono essere aggiunti facilmente tramite il `cordova plugin add` comando nella riga di comando oppure tramite il programma di installazione del plug-in fra Visual Studio.  Il codice seguente nell'app Apache Cordova registra il dispositivo per le notifiche push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Utilizzare SDK hub di notifica per inviare le notifiche push dal server.  Non inviare mai le notifiche push direttamente dai client. In questo modo può essere utilizzato per avviare una negazione del servizio attacco hub di notifica o la PNS.  Il PNS Impossibile escludere il traffico di tali attacchi.

<!-- URLs. -->
[Portale di Azure]: https://portal.azure.com
[Azure Mobile App introduttiva]: app-service-mobile-cordova-get-started.md
[Iniziare a utilizzare l'autenticazione]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Plug-in Cordova Apache per Azure App per dispositivi mobili]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[la prima app Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[push di plug-in phonegap]: https://www.npmjs.com/package/phonegap-plugin-push
[dispositivo di plug-in cordova]: https://www.npmjs.com/package/cordova-plugin-device
[inappbrowser di plug-in cordova]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
