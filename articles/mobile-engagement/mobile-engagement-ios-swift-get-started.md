<properties
    pageTitle="Guida introduttiva di Azure Mobile impegno per iOS in Swift | Microsoft Azure"
    description="Informazioni su come usare Azure Mobile coinvolgimento con Analitica e delle notifiche Push per iOS app."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Guida introduttiva a Azure Mobile impegno per iOS App in Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e inviare le notifiche push per segmentato agli utenti di un'applicazione di iOS.
In questa esercitazione si crea un'app per iOS vuota che consente di raccogliere dati di base e riceverà le notifiche push utilizzando Apple Push Notification sistema (APNS).

In questa esercitazione richiede le operazioni seguenti:

+ 8 XCode, che si possono installare dal MAC App Store
+ [coinvolgimento Mobile iOS SDK]
+ Certificato di notifica push (p12) che è possibile ottenere nel centro per sviluppatori Apple

> [AZURE.NOTE] In questa esercitazione utilizza Swift versione 3.0. 

Esercitazione è un prerequisito per tutte le altre esercitazioni Mobile impegno per App iOS.

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Configurazione Mobile impegno per l'app iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app back-end coinvolgimento Mobile

In questa esercitazione viene presentato una "integrazione di base", ovvero il numero minimo necessario per raccogliere dati e inviare una notifica push. La documentazione di integrazione completa, vedere l' [integrazione di impegno Mobile iOS SDK](mobile-engagement-ios-sdk-overview.md)

Verrà creata un'app di base con XCode per illustrare l'integrazione di:

###<a name="create-a-new-ios-project"></a>Creare un nuovo progetto di iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

1. Scaricare la [partecipazione attiva Mobile iOS SDK]
2. Estrarre il. gz file in una cartella nel computer
3. Fare clic con il pulsante destro del progetto e selezionare "Aggiungi file per..."

    ![][1]

4. Passare alla cartella in cui è stato estratto il SDK e selezionare il `EngagementSDK` cartella, quindi scegliere OK.

    ![][2]

5. Aprire la `Build Phases` scheda e nel `Link Binary With Libraries` menu aggiungere il Framework come illustrato di seguito:

    ![][3]

8. Creare un'intestazione di Bridging per poter usare le API C obiettivo del SDK scegliendo File > Nuovo > File > iOS > origine > File di intestazione.

    ![][4]

9. Modificare il file di intestazione provvisorio per esporre il codice Mobile coinvolgimento obiettivo-C al codice rapido, aggiungere importazioni seguenti:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. In impostazioni di compilazione, assicurarsi che la compilazione di intestazione Bridging obiettivo C impostazione in compilatore Swift: generazione del codice abbia un percorso per questa intestazione. Ecco un esempio di percorso: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (a seconda del percorso)**

    ![][6]

11. Tornare al portale di Azure nella pagina relativa alle *Informazioni di connessione* dell'applicazione e copiare la stringa di connessione

    ![][5]

12. A questo punto incollare la stringa di connessione nel `didFinishLaunchingWithOptions` delegato

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Abilita il monitoraggio in tempo reale

Per iniziare subito a inviare dati e assicurare che gli utenti attivi, è necessario inviare almeno una schermata (attività) back-end coinvolgimento Mobile.

1. Aprire il file **ViewController.swift** e sostituire la classe base di **ViewController** da **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Attivazione delle notifiche Push e la messaggistica in app

Coinvolgimento mobile consente di interagire e raggiungere con altri utenti con le notifiche Push e messaggistica all'interno dell'applicazione nel contesto delle campagne. In questo modulo è chiamato portata nel portale di impegno Mobile.
Nelle sezioni seguenti verranno configurazione l'app per riceverle.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Abilitare l'app ricevere notifiche Push invisibile

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Aggiungere la raccolta di accedere al progetto

1. Fare clic con il pulsante destro del progetto
2. Selezionare`Add file to ...`
3. Passare alla cartella in cui è stato estratto SDK
4. Selezionare il `EngagementReach` cartella
5. Fare clic su Aggiungi
6. Modificare il file di intestazione provvisorio per esporre Mobile coinvolgimento obiettivo C raggiunga intestazioni e aggiungere le importazioni seguenti:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modificare il delegato applicazione

1. All'interno di `didFinishLaunchingWithOptions` - creare un modulo portata e passare alla linea inizializzazione impegno esistente:

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Abilitare l'app ricevere notifiche Push APN
1. Aggiungere la riga seguente per il `didFinishLaunchingWithOptions` metodo:

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Aggiungere il `didRegisterForRemoteNotificationsWithDeviceToken` metodo come descritto di seguito:

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Aggiungere il `didReceiveRemoteNotification:fetchCompletionHandler:` metodo come descritto di seguito:

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Coinvolgimento mobile iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
