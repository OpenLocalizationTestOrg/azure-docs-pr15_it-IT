<properties
    pageTitle="Aggiungere le notifiche push per l'app Xamarin.Forms | Microsoft Azure"
    description="Informazioni su come utilizzare i servizi di Azure per inviare le notifiche push multipiattaforma alle App Xamarin.Forms."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Aggiungere l'app Xamarin.Forms le notifiche push

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione, aggiungere le notifiche per tutti i progetti condotto alla visualizzazione dal menu di [avvio rapido Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) in modo che una notifica push viene inviata a tutti i client multipiattaforma e su ogni volta che viene inserito un record push.

Se non si utilizza il progetto di server introduttive scaricato, sarà necessario il pacchetto di estensione di notifica push. Per ulteriori informazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Prerequisiti

* Per iOS, sarà necessario un' [iscrizione al programma di sviluppo di Apple](https://developer.apple.com/programs/ios/) e un dispositivo iOS fisica perché [simulatore iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="configure-hub"></a>Configurare un Hub di notifica

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Aggiornare project server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>(Facoltativo) Configurare ed eseguire il progetto Android

Completare questa sezione per attivare le notifiche push per il progetto Xamarin.Forms Droid per Android.


###<a name="enable-firebase-cloud-messaging-fcm"></a>Abilitare Cloud Firebase messaggistica (FCM)

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>Configurare il back-end App Mobile per inviare richieste push utilizzando FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>Aggiungere le notifiche push per il progetto Android

Con il back-end configurati con FCM, è possibile aggiungere componenti e i codici per il client per eseguire la registrazione con FCM, eseguire la registrazione per le notifiche push con gli hub di notifica di Azure tramite back-end app per dispositivi mobili e ricevere notifiche.

1. Nel progetto **Droid** pulsante destro del mouse sulla cartella **componenti** , fare clic su **Guida più componenti...**, eseguire una ricerca per il componente **Client di messaggistica di Google Cloud** e aggiungerlo al progetto. Questo componente supporta le notifiche push per un progetto Xamarin Android.


2. Aprire il file di progetto MainActivity.cs e aggiungere la seguente istruzione nella parte superiore del file:

        using Gcm.Client;

3. Aggiungere il codice seguente al metodo **OnCreate** dopo la chiamata a **LoadApplication**:

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. Aggiungere un nuovo metodo di supporto **CreateAndShowDialog** , come indicato di seguito:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. Aggiungere il codice seguente alla classe **MainActivity** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Espone l'istanza corrente di **MainActivity** in modo che è possibile eseguire su thread dell'interfaccia utente principale.

6. Inizializzare il `instance`, variabile all'inizio del metodo **OnCreate** , come descritto di seguito.

        // Set the current instance of MainActivity.
        instance = this;

2. Aggiungere un nuovo file di classe al progetto **Droid** denominato `GcmService.cs`e verificare che siano presente le seguenti istruzioni **using** nella parte superiore del file:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. Aggiungere le richieste di autorizzazione seguenti nella parte superiore del file, dopo le istruzioni **using** e prima della dichiarazione **dello spazio dei nomi** .

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Aggiungere la definizione di classe seguenti per lo spazio dei nomi. 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]Sostituire **< PROJECT_NUMBER >** con il numero di progetto che è indicato in precedenza.   

11. Sostituire la classe **GcmService** vuota con il codice seguente, che viene utilizzato il ricevitore trasmissione nuovo:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. Aggiungere il codice seguente alla classe **GcmService** che sostituisce il gestore eventi **OnRegistered** e implementato un metodo di **eseguire la registrazione** .

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. Aggiungere il codice seguente di implementazione **OnMessage**: 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Si gestisce le notifiche di posta in arrivo e inviarli al responsabile per visualizzare notifica.

14. **GcmServiceBase** anche è necessario implementare i metodi di gestore **OnUnRegistered** e **SuErrore** , che è possibile procedere come segue:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

A questo punto, si è pronti test le notifiche push nell'app in esecuzione su un dispositivo Android o emulatore.

###<a name="test-push-notifications-in-your-android-app"></a>Notifiche push di test nell'app Android

I primi due passaggi sono necessari solo per la verifica in un emulatore.

1. Verificare che si sta per distribuzione o il debug su un dispositivo virtuale che dispone di Google APIs impostare come destinazione, come illustrato di seguito in Gestione dispositivi virtuale Android (AVD).

2. Aggiungere un account di Google in un dispositivo Android facendo clic su **App** > **Impostazioni** > **Aggiungi account**e quindi seguire le istruzioni visualizzate per utilizzare aggiungere un account di Google esistente per il dispositivo per crearne uno nuovo.

1. Fare clic con il pulsante destro del progetto **Droid** Visual Studio o Xamarin Studio, quindi fare clic su **Imposta come progetto di avvio**.

2. Fare clic sul pulsante **Esegui** per compilare il progetto e avviare l'app nel dispositivo Android o emulatore.

3. Nell'app, digitare un'attività e quindi fare clic sul segno più (**+**) sull'icona.

4. Verificare che si riceve una notifica quando viene aggiunto un elemento.


##<a name="optional-configure-and-run-the-ios-project"></a>(Facoltativo) Configurare ed eseguire il progetto di iOS

In questa sezione è per l'esecuzione del progetto di iOS Xamarin per i dispositivi iOS. È possibile ignorare questa sezione se non si lavora con i dispositivi iOS.

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>Configurare l'hub di notifica per APNS

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Punto configurare le impostazioni di progetto iOS in Xamarin Studio o Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>Aggiungere le notifiche push per l'app iOS

1. Nel progetto **iOS** aprire AppDelegate.cs aggiungere l'istruzione **usando** nella parte superiore del file di codice.

        using Newtonsoft.Json.Linq;

4. Aggiungere un override per l'evento **RegisteredForRemoteNotifications** registrare notifiche di classe **AppDelegate** :

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. In **AppDelegate**, aggiungere anche l'override seguente per il gestore eventi **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Questo metodo gestisce le notifiche di posta in arrivo durante l'esecuzione dell'app.

2. In classe **AppDelegate** , aggiungere il codice seguente al metodo **FinishedLaunching** : 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    In questo modo il supporto per le notifiche remote e le richieste di spingere la registrazione.

L'app viene aggiornato per supportare le notifiche push.

####<a name="test-push-notifications-in-your-ios-app"></a>Notifiche push di test nell'app iOS

1. Fare clic con il pulsante destro del progetto di iOS e fare clic su **Imposta come progetto StartPp**.

2. Premere il pulsante **Esegui** o **F5** in Visual Studio per generare il progetto e avviare l'app in un dispositivo iOS, quindi fare clic su **OK** per accettare le notifiche push.

    > [AZURE.NOTE] È necessario accettare in modo esplicito notifiche push dell'app. Questa richiesta si verifica solo la prima volta che viene eseguita l'app.

3. Nell'app, digitare un'attività e quindi fare clic sul segno più (**+**) sull'icona.

4. Verificare che si riceve una notifica, quindi fare clic su **OK** per chiudere la notifica.


##<a name="optional-configure-and-run-the-windows-projects"></a>(Facoltativo) Configurare ed eseguire i progetti di Windows

In questa sezione è per l'esecuzione Xamarin.Forms WinApp e WinPhone81 progetti per dispositivi Windows. Questa procedura supporta anche i progetti universale Windows piattaforma (UWP). Se non si lavora con i dispositivi di Windows, è possibile ignorare questa sezione.


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrare l'app di Windows per le notifiche push con WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>Configurare l'hub di notifica per WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>Aggiungere le notifiche push per l'app di Windows

1. In Visual Studio, aprire **App.xaml.cs** in un progetto di Windows e aggiungere le seguenti istruzioni **using** .

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Sostituire `<your_TodoItemManager_portable_class_namespace>` con spazio dei nomi del progetto portatile che contiene il `TodoItemManager` classe.
 

2. In App.xaml.cs aggiungere il metodo **InitNotificationsAsync** seguente: 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Questo metodo ottiene il canale notifica push e registra un modello per ricevere una notifica modello dall'hub notifica. Una notifica di modello che supporta *messageParam* verrà recapitata a questo client.

3. In App.xaml.cs, aggiornare la definizione del metodo gestore eventi **OnLaunched** aggiungendo il `async` tasto di modifica, quindi aggiungere la riga di codice seguente alla fine del metodo: 

        await InitNotificationsAsync();

    In questo modo che la registrazione di notifica push viene creata o aggiornata ogni volta che avvia l'app. È importante procedere come segue per avere la certezza che il canale di push WNS è sempre attivo.  

4. In Esplora soluzioni per Visual Studio, aprire il file **Package.appxmanifest** e impostare **Dotati di tipo avviso popup** su **Sì** in **notifiche**.

5. Creare l'app e verificare che non siano presenti errori.  Client app dovrebbe ora eseguire la registrazione per le notifiche di modello da back-end App Mobile. Ripetere questa sezione per ogni progetto Windows della soluzione.


####<a name="test-push-notifications-in-your-windows-app"></a>Notifiche push test nelle applicazioni di Windows

1. In Visual Studio, fare clic con il pulsante destro un progetto di Windows e scegliere **Imposta come progetto di avvio**.

2. Fare clic sul pulsante **Esegui** per compilare il progetto e avviare l'app.

3. Nell'app, digitare un nome per un nuovo todoitem e quindi fare clic sul segno più (**+**) sull'icona per aggiungerlo.

4. Verificare che si riceve una notifica quando l'elemento viene aggiunto.

##<a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle notifiche push:

* [Diagnosticare i problemi di notifica push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Esistono diversi motivi perché notifiche scada o non finiscano nei dispositivi. In questo argomento viene illustrato come analizzare e individuare la causa degli errori di notifica push. 

Valutare la possibilità di proseguire con uno dei seguenti esercitazioni:

* [Aggiungere l'autenticazione applicazione in uso.](app-service-mobile-xamarin-forms-get-started-users.md)  
Informazioni su come eseguire l'autenticazione degli utenti dell'app con un provider di identità.

* [Attivare la sincronizzazione offline per l'app](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Informazioni su come aggiungere l'app utilizzando un back-end App Mobile supporto della modalità offline. Sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili&mdash;la visualizzazione, aggiunta o la modifica dei dati&mdash;anche quando non vi è alcuna connessione di rete.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

