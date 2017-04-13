<properties
    pageTitle="Aggiungere le notifiche push Xamarin.iOS all'applicazione in uso con il servizio di App Azure"
    description="Informazioni su come utilizzare il servizio di App Azure per inviare le notifiche push per l'app Xamarin.iOS"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinios-app"></a>Aggiungere le notifiche push Xamarin.iOS all'applicazione in uso

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione, aggiungere le notifiche push [Xamarin.iOS rapido avvia](app-service-mobile-xamarin-ios-get-started.md) progetto in modo che una notifica push viene inviata al dispositivo ogni volta che viene inserito un record.

Se non si utilizza il progetto di server introduttive scaricato, sarà necessario il pacchetto di estensione di notifica push. Per ulteriori informazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Prerequisiti

* Completare l'esercitazione [Guida introduttiva Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .

* Un dispositivo iOS fisica. Notifiche push non sono supportate dal simulatore iOS.

##<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrare l'app per le notifiche push nel portale per sviluppatori di Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##<a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurare l'App Mobile per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Aggiornare project server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="configure-your-xamarinios-project"></a>Configurare il progetto Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##<a name="add-push-notifications-to-your-app"></a>Aggiungere le notifiche push applicazione in uso.

1. In **QSTodoService**, aggiungere la seguente proprietà affinché **AppDelegate** acquisire client mobile:

            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Aggiungere quanto segue `using` istruzione nella parte superiore del file **AppDelegate.cs** .

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. In **AppDelegate**, ignorare l'evento **FinishedLaunching** :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. Nello stesso file, ignorare l'evento **RegisteredForRemoteNotifications** . In questo codice che si sta registrando per una notifica di modello semplice che verrà inviata tutte le piattaforme supportate dal server.

    Per ulteriori informazioni sui modelli con gli hub di notifica, vedere [modelli](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. Ignorare l'evento **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

L'app viene aggiornato per supportare le notifiche push.

## <a name="test"></a>Notifiche push di test nell'app

1. Fare clic sul pulsante **Esegui** per compilare il progetto e avviare l'app in un dispositivo in grado di iOS, quindi fare clic su **OK** per accettare le notifiche push.

    > [AZURE.NOTE] È necessario accettare in modo esplicito notifiche push dell'app. Questa richiesta si verifica solo la prima volta che viene eseguita l'app.

2. Nell'app, digitare un'attività e quindi fare clic sul segno più (**+**) sull'icona.

3. Verificare che si riceve una notifica, quindi fare clic su **OK** per chiudere la notifica.

4. Ripetere il passaggio 2 e immediatamente chiudere l'applicazione, quindi verificare che sia visualizzata una notifica.

In questa esercitazione completata.

<!-- Images. -->

<!-- URLs. -->



