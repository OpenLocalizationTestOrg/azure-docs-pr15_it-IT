<properties
    pageTitle="Guida introduttiva di Azure Mobile impegno per iOS obiettivo c | Microsoft Azure"
    description="Informazioni su come usare Azure Mobile coinvolgimento con le notifiche push e analitica per App iOS."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Guida introduttiva a Azure Mobile impegno per App iOS obiettivo c

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e inviare le notifiche push per segmentato agli utenti di un'applicazione di iOS.
In questa esercitazione si crea un'app per iOS vuota che consente di raccogliere dati di base e riceverà le notifiche push utilizzando Apple Push Notification sistema (APNS).

In questa esercitazione richiede le operazioni seguenti:

+ 8 XCode, che si possono installare dal MAC App Store
+ [coinvolgimento Mobile iOS SDK]

Esercitazione è un prerequisito per tutte le altre esercitazioni Mobile impegno per App iOS.

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Configurazione Mobile impegno per l'app iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app back-end coinvolgimento Mobile

In questa esercitazione viene presentato una "integrazione di base", ovvero il numero minimo necessario per raccogliere dati e inviare una notifica push. La documentazione di integrazione completa, vedere l' [integrazione di impegno Mobile iOS SDK](mobile-engagement-ios-sdk-overview.md)

Verrà creata un'app di base con XCode per illustrare l'integrazione.

###<a name="create-a-new-ios-project"></a>Creare un nuovo progetto di iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

1. Scaricare la [partecipazione attiva Mobile iOS SDK].
2. Estrarre il. gz file in una cartella nel computer.
3. Fare clic sul progetto e quindi selezionare **Aggiungi al file**.

    ![][1]

4. Passare alla cartella in cui è stato estratto SDK, selezionare la `EngagementSDK` cartella e quindi premere **OK**.

    ![][2]

5. Aprire la scheda **Creare fasi** e nel menu **Collegamento binario con le raccolte** , aggiungere il Framework come illustrato di seguito:

    ![][3]

6. Tornare al portale di Azure nella pagina relativa alle **Informazioni di connessione** dell'applicazione e copiare la stringa di connessione.

    ![][4]

7. Aggiungere la riga di codice seguente nel file **AppDelegate.m** .

        #import "EngagementAgent.h"

8. A questo punto incollare la stringa di connessione nel `didFinishLaunchingWithOptions` delegati.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`è un'istruzione facoltativa che consente di registri SDK dell'utente identificare i problemi. 

##<a id="monitor"></a>Attivare il monitoraggio in tempo reale

Per iniziare subito a inviare dati e assicurare che gli utenti attivi, è necessario inviare almeno una schermata (attività) back-end coinvolgimento Mobile.

1. Aprire il file **ViewController.h** e importare **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Sostituire la classe superiore dell'interfaccia **ViewController** da `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Attivare le notifiche push e la messaggistica in app

Coinvolgimento mobile consente di interagire con altri utenti e accedere con le notifiche push e messaggistica nel contesto delle campagne di app. In questo modulo è chiamato portata nel portale di impegno Mobile.
Nelle sezioni seguenti vengono configurare l'app per riceverle.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Abilitare l'app ricevere notifiche Push invisibile

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>Aggiungere la raccolta di accedere al progetto

1. Pulsante destro del mouse del progetto.
2. Selezionare **il file da aggiungere**.
3. Passare alla cartella in cui è stato estratto SDK.
4. Selezionare il `EngagementReach` cartella.
5. Fare clic su **Aggiungi**.

### <a name="modify-your-application-delegate"></a>Modificare il delegato applicazione

1. Nel file **AppDeletegate.m** , importare il modulo impegno raggiungere.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. All'interno di `application:didFinishLaunchingWithOptions` metodo, creare un modulo portata e passare alla linea inizializzazione impegno esistente:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Abilitare l'app ricevere notifiche Push APN

1. Aggiungere la riga seguente per il `application:didFinishLaunchingWithOptions` metodo:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Aggiungere il `application:didRegisterForRemoteNotificationsWithDeviceToken` metodo come descritto di seguito:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Aggiungere il `didFailToRegisterForRemoteNotificationsWithError` metodo come descritto di seguito:

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Aggiungere il `didReceiveRemoteNotification:fetchCompletionHandler` metodo come descritto di seguito:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Coinvolgimento mobile iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

