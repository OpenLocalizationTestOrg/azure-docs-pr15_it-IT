<properties
    pageTitle="Guida introduttiva di Azure impegno per dispositivi mobili per Xamarin.iOS"
    description="Informazioni su come usare Azure Mobile coinvolgimento con Analitica e delle notifiche Push per App Xamarin.iOS."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Guida introduttiva di Azure impegno per dispositivi mobili per le app Xamarin.iOS

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

In questo argomento viene illustrato come utilizzare Azure Mobile impegno per comprendere l'utilizzo di app e inviare le notifiche push agli utenti segmentati in un'applicazione Xamarin.iOS.
In questa esercitazione si crea un'app Xamarin.iOS vuota che consente di raccogliere dati di base e riceverà le notifiche push utilizzando Apple Push Notification sistema (APNS).

In questa esercitazione richiede le operazioni seguenti:

+ [Studio Xamarin](http://xamarin.com/studio). È anche possibile utilizzare Visual Studio con Xamarin ma in questa esercitazione utilizza Xamarin Studio. Per istruzioni sull'installazione, vedere [installazione e installare per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
+ [SDK Xamarin impegno per dispositivi mobili](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Configurazione Mobile impegno per l'app iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app back-end coinvolgimento Mobile

In questa esercitazione viene presentato un "integrazione di base" che è il minimo impostato necessari per raccogliere dati e inviare una notifica push.

Verrà creata un'app di base con Xamarin per illustrare l'integrazione di:

###<a name="create-a-new-xamarinios-project"></a>Creare un nuovo progetto Xamarin.iOS

1. Avviare Xamarin Studio. Passare a **File** -> **nuova** -> **soluzione** 

    ![][1]

2. Selezionare **Solo visualizzazione App**, assicurarsi che sia la lingua selezionata **c#** e quindi fare clic su **Avanti**.

    ![][2]

3. Immettere il **Nome dell'App** e **Identificativo dell'organizzazione** e quindi fare clic su **Avanti**. 

    ![][3]

    > [AZURE.IMPORTANT] Assicurarsi che il profilo pubblicazione che infine usare per distribuire l'app iOS con un ID App che corrisponde esattamente l'identificatore di raggruppamento sono disponibili. 

4. Aggiornare il **Nome del progetto**, **Nome soluzione** e **luogo** , se richiesto e fare clic su **Crea**.

    ![][4]
 
Xamarin Studio creerà l'app demo in cui si prevede di integrare coinvolgimento Mobile. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app back-end coinvolgimento Mobile

1. Fare clic con il pulsante destro sulla cartella **pacchetti** in Esplora e selezionare **Aggiungi pacchetti**

    ![][5]

2. Cercare **SDK Xamarin coinvolgimento di Microsoft Azure Mobile** e aggiungerlo alla soluzione.  

    ![][6]
   
3. Aprire **AppDelegate.cs** e aggiungere la seguente istruzione:

        using Microsoft.Azure.Engagement.Xamarin;

4. Metodo **FinishedLaunching** , aggiungere le operazioni seguenti per inizializzare la connessione con coinvolgimento Mobile back-end. Assicurarsi di aggiungere il **ConnectionString**. Questo codice utilizza anche un fittizio **NotificationIcon** che viene aggiunto da SDK coinvolgimento Mobile da sostituire. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Abilita il monitoraggio in tempo reale

Per avviare l'invio di dati e gli utenti siano attivi, è necessario inviare almeno una schermata a back-end coinvolgimento Mobile.

1. Aprire **ViewController.cs** e aggiungere la seguente istruzione:

        using Microsoft.Azure.Engagement.Xamarin;

2. Sostituire la classe da cui `ViewController` eredita `UIViewController` a `EngagementViewController`. 

##<a id="monitor"></a>Connettere app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Attivare le notifiche push e la messaggistica in app

Coinvolgimento mobile consente di interagire con altri utenti e accedere con le notifiche push e messaggistica nel contesto delle campagne di app. In questo modulo è chiamato portata nel portale di impegno Mobile.
Nelle sezioni seguenti vengono configurare l'app per riceverle.

### <a name="modify-your-application-delegate"></a>Modificare il delegato applicazione

1. Aprire **AppDelegate.cs** e aggiungere la seguente istruzione:

        using System; 

2. A questo punto all'interno di `FinishedLaunching` metodo, aggiungere le operazioni seguenti per eseguire la registrazione per i messaggi push dopo`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Infine, aggiungere o aggiornare i metodi seguenti:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. Nel file **Info.plist** della soluzione, verificare che l' **Identificatore di raggruppamento** corrisponda con l' **ID di App** avere nel profilo di provisioning in Dev Center di Apple. 

    ![][7]

5. Nello stesso file **Info.plist** , assicurarsi di aver selezionato le **Attiva modalità di sfondo** e **Le notifiche remoto**. 

    ![][8]

6. Eseguire l'app nel dispositivo che è associato a questo profilo di pubblicazione. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
