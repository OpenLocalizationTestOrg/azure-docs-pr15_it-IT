<properties
    pageTitle="iOS delle notifiche Push con gli hub di notifica per le app Xamarin | Microsoft Azure"
    description="In questa esercitazione si imparerà a utilizzare Azure notifica hub per inviare le notifiche push per un'applicazione di iOS Xamarin."
    services="notification-hubs"
    keywords="IOS push notifiche push messaggi, notifiche push, inserire il messaggio"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS delle notifiche Push con gli hub di notifica per le app Xamarin

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Panoramica
> [AZURE.IMPORTANT] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

In questa esercitazione viene illustrato come utilizzare Azure notifica hub per inviare le notifiche push a un'applicazione di iOS.
Sarà necessario creare un'app Xamarin.iOS vuota che riceve le notifiche push utilizzando il [Servizio di notifica Push di Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Al termine, sarà possibile usare l'hub di notifica per trasmettere le notifiche push per tutti i dispositivi che eseguono l'app. Codice completato è disponibile nell' [app NotificationHubs] [ GitHub] campione.

In questa esercitazione viene illustrato lo scenario di trasmissione di messaggio push semplice con gli hub di notifica.

##<a name="prerequisites"></a>Prerequisiti

In questa esercitazione richiede le operazioni seguenti:

+ [Xcode 6.0][Install Xcode]
+ Un iOS 7.0 (o versione successiva) dispositivo compatibile
+ iOS partecipazione al programma per sviluppatori
+ [Studio Xamarin]

   > [AZURE.NOTE] A causa di requisiti di configurazione per iOS push notifiche, è necessario distribuire e testare l'applicazione di esempio in un dispositivo fisico iOS (iPhone o iPad) anziché in simulatore.

Esercitazione è un prerequisito per tutte le altre esercitazioni hub di notifica per le app iOS Xamarin.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>Configurare l'hub di notifica

In questa sezione viene descritto come creare un nuovo hub di notifica e configurare l'autenticazione con APN utilizzando il certificato di push **p12** creato. Se si desidera utilizzare un hub di notifica che è già stato creato, è possibile procedere al passaggio 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Come si desidera configurare la connessione APN nel portale di Azure, aprire le impostazioni di notifica Hub, ande fare clic su <b>Servizi di notifica</b>e quindi fare clic sull'elemento <b>Apple (APN)</b> nell'elenco. Al termine, fare clic su <b>Carica certificato</b> e selezionare il certificato <b>p12</b> esportati in precedenza, e la password per il certificato.</p>
<p>Assicurarsi di selezionare la modalità <b>Sandbox</b> dal momento che si inviano messaggi push in un ambiente di sviluppo. Utilizzare le impostazioni di <b>produzione</b> solo se si desidera inviare le notifiche push agli utenti che hanno già acquistato l'app dall'archivio.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


L'hub di notifica è ora configurato per l'utilizzo con APN e disporre le stringhe di connessione per registrare l'app e inviare le notifiche push.


##<a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica

#### <a name="create-a-new-project"></a>Creare un nuovo progetto

1. In Xamarin Studio, creare un nuovo progetto iOS e selezionare l' **API unificata** > modello**Singola applicazione di visualizzazione** .

    ![Studio Xamarin - tipo di selezione dell'applicazione][31]

2. Aggiungere un riferimento al componente della messaggistica Azure. In visualizzazione soluzioni fare clic sulla cartella **componenti** per il progetto e scegliere **Ottenere più componenti**. Cercare il componente di **Azure messaggistica** e il componente al progetto.

3. In **AppDelegate.cs**, aggiungere la seguente istruzione:

        using WindowsAzure.Messaging;

4. Dichiarare un'istanza di **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Creare una classe **Constants.cs** con le variabili seguenti:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. In **AppDelegate.cs**, aggiornare **FinishedLaunching()** in modo da corrispondere le operazioni seguenti:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Override del metodo **RegisteredForRemoteNotifications()** in **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Override del metodo **ReceivedRemoteNotification()** in **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Creare il metodo seguente **ProcessNotification()** in **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] È possibile scegliere di ignorare **FailedToRegisterForRemoteNotifications()** per gestire le situazioni, ad esempio alcuna connessione di rete. Questa operazione è particolarmente importante nel punto in cui l'utente può avviare l'applicazione in modalità offline (ad esempio aeroplano) e si vuole gestire push messaggistica scenari specifici per l'app.


10. Eseguire l'app nel dispositivo.


## <a name="sending-push-notifications"></a>Invio di notifiche Push


È possibile verificare la ricezione delle notifiche push nell'app tramite l'invio di notifiche nel [Portale di Azure] tramite la funzionalità di **Test invia** nel set di strumenti **risoluzione dei problemi** , a destra nella pagina hub di notifica, come illustrato nell'immagine seguente.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Notifiche push normalmente vengono inviate tramite il servizio di back-end come servizi mobili o ASP.NET utilizzando una raccolta compatibile. Utilizzare l'API REST direttamente a inviare messaggi push se una raccolta non è disponibile nel proprio scenario. 

In questa esercitazione verrà privilegiare la semplicità e appena dimostrare-test dell'applicazione client mediante l'invio di notifiche tramite .NET SDK per hub di notifica in un'applicazione console invece di un servizio di back-end. È consigliabile l'esercitazione di [Utilizzare gli hub di notifica per le notifiche push agli utenti](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) il passaggio successivo per l'invio di notifiche da un back-end ASP.NET. Tuttavia, è possono utilizzare approcci seguenti per l'invio di notifiche:

* **Interfaccia REST**: È possibile supportare notifica push su qualsiasi piattaforma back-end tramite l' [interfaccia REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure notifica hub .NET SDK**: In the Nuget pacchetto Manager per Visual Studio, eseguire [Microsoft.Azure.NotificationHubs pacchetto di installazione](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node** : [come usare gli hub di notifica da Node](notification-hubs-nodejs-push-notification-tutorial.md).

**App mobile**: per informazioni su come inviare notifiche da un back-end Azure App servizio Mobile App che è integrato con gli hub di notifica, vedere [aggiungere le notifiche push per l'app per dispositivi mobili](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Linguaggio / PHP**: per informazioni su come inviare le notifiche push tramite le API REST, vedere "Come usare gli hub di notifica da Java/PHP" ([linguaggio](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>(Facoltativo) Inviare le notifiche Push da un'App di Console .NET

In questa sezione, le notifiche push viene inviato tramite una semplice applicazione console .NET. Ai fini di questo esempio, è necessario spostarsi in un ambiente di sviluppo di Windows che contiene già installato Visual Studio.

1. In Visual Studio, creare una nuova applicazione console c#:

    ![Visual Studio - creare una nuova applicazione console][213]

2. In Visual Studio, fare clic su **Strumenti**, fare clic su **Gestione pacchetti NuGet**e quindi fare clic su **Gestione pacchetti Console**.

    Console di gestione di pacchetto dovrebbero essere visualizzate ancorata nella parte inferiore dell'area di lavoro Visual Studio.

3. Nella finestra Gestione pacchetti Console set **predefinito di project** per il nuovo progetto applicazione console e quindi nella finestra della console, eseguire il comando seguente:

        Install-Package Microsoft.Azure.NotificationHubs

    Aggiungere un riferimento di Azure notifica hub SDK utilizzando il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto Microsoft.Azure.Notification hub NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Aprire la `Program.cs` file e aggiungere le seguenti `using` istruzione, garantendo che è possibile utilizzare le classi Azure e funzioni all'interno della classe principale:

        using Microsoft.Azure.NotificationHubs;

3. Nel `Program` per la classe, aggiungere il metodo seguente (non dimenticare di sostituire la **stringa di connessione** e **nome dell'hub**):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Aggiungere le righe seguenti il `Main` metodo:

         SendNotificationAsync();
         Console.ReadLine();

5. Premere F5 per eseguire l'app. In pochi secondi, verrà visualizzata una notifica push vengono visualizzati nel dispositivo. Se si utilizza una rete dati del cellulare o Wi-Fi, assicurarsi di avere una connessione internet nel dispositivo.

È possibile trovare tutti i possibili payload in Apple [Push Notification Programming Guide e locale].

####<a name="optional-send-notifications-from-a-mobile-service"></a>(Facoltativo) Invio di notifiche da un servizio per dispositivi mobili

In questa sezione, viene inviato un servizio di dispositivi mobili tramite uno script nodo le notifiche push.

Per inviare una notifica tramite un servizio per dispositivi mobili, seguire la [Guida introduttiva a servizi mobili]e quindi:

1. Accedere al [Portale classica Azure]e selezionare il servizio mobile.

2. Selezionare la scheda **utilità di pianificazione** nella parte superiore.

    ![Azure portale classico - utilità di pianificazione][215]

3. Creare un nuovo processo pianificato, inserire un nome e selezionare **su richiesta**.

    ![Portale classica Azure - creare un nuovo processo][216]

4. Quando viene creato il processo, fare clic sul nome di processo. Quindi fare clic sulla scheda **Script** sulla barra superiore.

5. Inserire il seguente script all'interno della funzione di pianificazione. Assicurarsi di sostituire i segnaposto con il nome di hub di notifica e la stringa di connessione per *DefaultFullSharedAccessSignature* ottenuto in precedenza. Fare clic su **Salva**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Fare clic su **Esegui una volta** nella barra inferiore. Verrà visualizzato un avviso nel dispositivo.

##<a name="next-steps"></a>Passaggi successivi

In questo esempio semplice trasmessa le notifiche push per tutti i dispositivi iOS. Per impostare la destinazione utenti specifici, fare riferimento all'esercitazione [Utilizzare gli hub di notifica per le notifiche push per gli utenti]. Se si vuole segmento gli utenti dai gruppi di interesse, è possibile leggere [Utilizzare gli hub di notifica per inviare ultime notizie]. Ulteriori informazioni su come usare gli hub di notifica [Indicazioni hub di notifica] e [Notifica hub procedure per iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Guida introduttiva a servizi mobili]: /develop/mobile/tutorials/get-started-xamarin-ios
[Portale classica Azure]: https://manage.windowsazure.com/
[Notifica hub indicazioni]: http://msdn.microsoft.com/library/jj927170.aspx
[Notifica hub procedure per iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Utilizzare gli hub di notifica per le notifiche push per gli utenti]: /manage/services/notification-hubs/notify-users-aspnet
[Utilizzare gli hub di notifica per inviare ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet

[Locale e Guida di programmazione di notifica Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Studio Xamarin]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Portale di Azure]: https://portal.azure.com
