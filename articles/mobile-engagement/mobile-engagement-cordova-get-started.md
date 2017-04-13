<properties
    pageTitle="Guida introduttiva di Azure impegno per dispositivi mobili per Cordova/Phonegap"
    description="Informazioni su come usare Azure Mobile coinvolgimento con Analitica e delle notifiche Push per App Cordova/Phonegap."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Guida introduttiva di Azure impegno per dispositivi mobili per Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e inviare le notifiche push agli utenti segmentati per un'applicazione per dispositivi mobili sviluppati con Cordova.

In questa esercitazione verrà creare un'app vuota Cordova usando Mac e quindi integrare Mobile coinvolgimento SDK. Consente di raccogliere dati di base analitica e riceverà le notifiche push utilizzando Apple Push Notification sistema (APN) per iOS e Google Cloud messaggistica (GCM) per Android. Questa operazione verrà installato per iOS o un dispositivo Android per la verifica. 

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).

In questa esercitazione richiede le operazioni seguenti:

+ XCode, si può installare da Mac App Store (per la distribuzione per iOS)
+ [Emulatore e SDK android](http://developer.android.com/sdk/installing/index.html) (per la distribuzione per Android)
+ Certificato di notifica push (p12) che è possibile ottenere dal centro per sviluppatori di Apple per APNS
+ Numero di progetto GCM che è possibile ottenere dalla Console sviluppo Google per GCM
+ [Plug-in Cordova impegno per dispositivi mobili](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] È possibile trovare il codice sorgente e il file Leggimi per il plug-in di Cordova su [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Configurazione di dispositivi mobili impegno per l'app Cordova

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connessione dell'applicazione back-end coinvolgimento Mobile

In questa esercitazione viene presentato un "integrazione di base" che è il minimo impostato necessari per raccogliere dati e inviare una notifica push. 

Verrà creata un'app di base con Cordova per illustrare l'integrazione di:

###<a name="create-a-new-cordova-project"></a>Creare un nuovo progetto Cordova

1. Avviare finestra *terminale* sul computer Mac e digitare quanto segue in modo da creare un nuovo progetto Cordova dal modello predefinito. Assicurarsi che il profilo pubblicazione che infine usare per distribuire l'app iOS utilizza 'com.mycompany.myapp' come ID. App 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Eseguire le operazioni seguenti per configurare il progetto per **iOS** ed eseguire in iOS simulatore:

        $ cordova platform add ios 
        $ cordova run ios

3. Eseguire le operazioni seguenti per configurare il progetto per **Android** ed eseguire in emulatore Android. Assicurarsi che le impostazioni di Android SDK emulatore abbiano la relativa destinazione come Google APIs (Google Inc.) con la CPU / ABI come Google APIs ARM.  

        $ cordova platform add android
        $ cordova run android

4.  Aggiungere il plug-in Cordova Console. 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

1. Installare il plug-in Azure Mobile coinvolgimento Cordova fornendo i valori delle variabili per configurare il plug-in:

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Icona di raggiungere Android* : deve essere il nome della risorsa senza qualsiasi estensione né prefisso drawable (ex: mynotificationicon), e il file dell'icona deve essere copiato nel progetto android (piattaforme/android/risoluzioni/drawable)

*iOS raggiunga icona* : deve essere il nome della risorsa con l'estensione (ex: mynotificationicon.png), e il file dell'icona deve essere aggiunta nel progetto iOS con XCode (utilizzando il Menu file aggiungere)

##<a id="monitor"></a>Abilita il monitoraggio in tempo reale

1. In project Cordova - modificare **www/js/index.js** per aggiungere la chiamata al cellulare impegno per dichiarare una nuova attività una volta l'evento *deviceReady* viene ricevuta.

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Eseguire l'applicazione:
        
    - **Per iOS**
    
        In `Terminal` finestra di avvio dell'applicazione in una nuova istanza di simulatore eseguendo le operazioni seguenti:

            cordova run ios

    - **Per Android**
        
        In `Terminal` finestra di avvio dell'applicazione in una nuova istanza di emulatore eseguendo le operazioni seguenti:

            cordova run android

3. È possibile visualizzare le operazioni seguenti nei log console:

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Attivazione delle notifiche Push e la messaggistica in app

Coinvolgimento mobile consente di interagire con altri utenti con le notifiche Push e messaggistica nel contesto delle campagne di app. In questo modulo è chiamato portata nel portale di impegno Mobile.
Nelle sezioni seguenti verranno configurazione l'app per riceverle.

###<a name="configure-push-credentials-for-mobile-engagement"></a>Configurare le credenziali di Push per coinvolgimento Mobile

Per consentire l'impegno Mobile per l'invio delle notifiche Push per conto dell'utente, è necessario concedere l'accesso al certificato di Apple iOS o chiave dell'API Server GCM. 
    
1. Passare al portale coinvolgimento Mobile. Assicurarsi che sta utilizzando l'app è in uso per il progetto e quindi fare clic sul pulsante **attirare** nella parte inferiore:
    
    ![][1]
    
2. Verrà inserita nella pagina Impostazioni nel portale di impegno. Da presente fare clic sulla sezione **Push nativo** :
    
    ![][2]

3. Configurare iOS chiave dell'API di certificato/GCM Server

    **[iOS]**

    un. Selezionare il p12, caricarlo e digitare la password:
    
    ![][3]

    **[Android]**

    un. Fare clic sull'icona di modifica davanti **Chiave dell'API** nella sezione Impostazioni GCM e popup che mostra verso l'alto, incollare la chiave del Server GCM e fare clic su **OK**. 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>Attivare le notifiche push nell'app Cordova

Modificare **www/js/index.js** per aggiungere la chiamata a coinvolgimento Mobile per richiedere le notifiche push e dichiarare un gestore:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>Eseguire l'app

**[iOS]**

1. XCode verrà usata per creare e distribuire l'app nel dispositivo per verificare le notifiche push poiché iOS consente solo le notifiche push per un dispositivo effettivo. Passare al percorso in cui viene creato il progetto Cordova e passare al percorso **...\platforms\ios** . Aprire il file .xcodeproj nativo XCode. 
    
2. Compilare e distribuire app Cordova al dispositivo iOS utilizzando l'account che dispone di provisioning profilo che contiene il certificato che appena caricato nel portale coinvolgimento Mobile e l'Id di App che corrisponde a quella fornita durante la creazione di app Cordova. È possibile estrarre l' *identificatore di raggruppamento* nel **risorse\*-info.plist** file in XCode in base alla. 

3. Verrà visualizzato il popup di iOS standard nel dispositivo in cui viene indicato che l'app richiede l'autorizzazione per inviare notifiche. Concedere l'autorizzazione. 

**[Android]**

È possibile utilizzare semplicemente l'emulatore per eseguire l'app di Android come sono supportate le notifiche GCM emulatore Android. 

    cordova run android

##<a id="send"></a>Invia una notifica applicazione in uso.

Verrà ora creata una campagna di notifica Push semplice che invierà un comando per l'app in esecuzione nel dispositivo:

1. Passare alla scheda **raggiungere** nel portale coinvolgimento Mobile

2. Fare clic su **Nuovo annuncio** per creare una campagna di push

    ![][6]

3. Fornire gli input per creare una campagna di **[Android]**
    
    - Specificare un **nome** per la campagna. 
    - Selezionare il **Tipo di consegna** come *notifica di sistema* *semplice*
    - Selezionare l' **ora di recapito** come *"qualsiasi ora"*
    - Specificare un **titolo** per la notifica che sarà la prima riga di comando.
    - Fornire un **messaggio** per la notifica che verrà utilizzata come corpo del messaggio. 

    ![][11]

4. Fornire gli input per creare la campagna **[iOS]**

    - Specificare un **nome** per la campagna. 
    - Selezionare l' **ora di recapito** come *"fuori app"*
    - Specificare un **titolo** per la notifica che sarà la prima riga di comando.
    - Fornire un **messaggio** per la notifica che verrà utilizzata come corpo del messaggio. 
 
    ![][12]

5. Scorrimento verso il basso e nella sezione contenuto selezionare **solo notifica**

    ![][8]

6. [Facoltativo] È anche possibile specificare un URL di azione. Assicurarsi che utilizza una combinazione di URL specificata durante la configurazione del plug-in **AZME\_REINDIRIZZARE\_URL** variabile, ad esempio *myapp://test*.  

7. Aver completato l'impostazione della campagna di base possibili. Ora scorrere verso il basso nuovamente e fare clic sul pulsante **Crea** per salvare la campagna.

8. Infine **Attiva** della campagna
    
    ![][10]

9. Viene visualizzato una notifica push del dispositivo o emulatore come parte della campagna. 

##<a id="next-steps"></a>Passaggi successivi
[Panoramica di tutti i metodi disponibili con Cordova Mobile coinvolgimento SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

