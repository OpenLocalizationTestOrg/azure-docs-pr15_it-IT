<properties
    pageTitle="Guida introduttiva a hub di notifica per App Xamarin.Android | Microsoft Azure"
    description="In questa esercitazione si imparerà a utilizzare Azure notifica hub per inviare le notifiche push per un'applicazione Xamarin Android."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Guida introduttiva a hub di notifica con Xamarin per Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come utilizzare Azure notifica hub per inviare le notifiche push per un'applicazione Xamarin.Android.
Sarà necessario creare un'app Xamarin.Android vuota che riceve le notifiche push utilizzando Google Cloud messaggistica (GCM). Al termine, sarà possibile usare l'hub di notifica per trasmettere le notifiche push per tutti i dispositivi che eseguono l'app. Codice completato è disponibile nell' [app NotificationHubs] [ GitHub] campione.

In questa esercitazione viene illustrato lo scenario di trasmissione semplice con gli hub di notifica.


## <a name="before-you-begin"></a>Prima di iniziare

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione sono disponibili nel GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##<a name="prerequisites"></a>Prerequisiti

In questa esercitazione richiede le operazioni seguenti:

+ Visual Studio con Xamarin in Windows o Xamarin Studio in Mac OS x completamento istruzioni relative all'installazione siano presenti [e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Account di Google attivo
+ [Componente di messaggistica di Azure]
+ [Componente Client di messaggistica di Google Cloud]

Esercitazione è un prerequisito per tutte le altre esercitazioni hub di notifica per le app Xamarin.Android.

> [AZURE.IMPORTANT] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="enable-google-cloud-messaging"></a>Abilitare Google Cloud messaggistica

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>Configurare l'hub di notifica

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Fare clic sulla scheda <b>Configura</b> nella parte superiore, immettere il valore di <b>Chiave dell'API</b> ottenuto nella sezione precedente e quindi fare clic su <b>Salva</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


L'hub di notifica è ora configurato per l'utilizzo con GCM e disporre le stringhe di connessione a entrambi registrare l'app per ricevere notifiche e inviare le notifiche push.

##<a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica

###<a name="create-a-new-project"></a>Creare un nuovo progetto

1. In Xamarin Studio, fare clic su **Nuova soluzione**, fare clic su **App Android**e fare clic su **Avanti**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Immettere il **Nome dell'App** e **identificatore**. Fare clic su **Plaforms destinazione** desiderata per il supporto e quindi fare clic su **Crea**e **successiva** .

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    In questo modo si crea un nuovo progetto Android.

2. Aprire le proprietà del progetto, facendo clic su Nuovo progetto in visualizzazione soluzioni e scegliere **Opzioni**. Selezionare l'elemento **Applicazione Android** nella sezione **creare** .

    Assicurarsi che la prima lettera del proprio **Nome pacchetto** sia lettere minuscole.

    > [AZURE.IMPORTANT] La prima lettera del nome del pacchetto deve essere minuscola. In caso contrario, si riceveranno gli errori manifesto dell'applicazione quando si registra il **BroadcastReceiver** e **IntentFilter** per le notifiche push riportata di seguito.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Facoltativamente, è possibile impostare la **versione minima Android** a un altro livello di API.

4. Facoltativamente, è possibile impostare la **destinazione Android versione** a un'altra versione di API che si desidera effettuarla (deve essere livello API 8 o versione successiva).

Fare clic su **OK** e chiudere la finestra di dialogo Opzioni di Project.


###<a name="add-the-required-components-to-your-project"></a>Aggiungere i componenti necessari al progetto

Google Cloud Client di messaggistica di disponibili per l'archivio componente Xamarin semplifica il processo di supportare le notifiche push in Xamarin.Android.

1. Pulsante destro del mouse sulla cartella componenti nell'app Xamarin.Android e scegliere **Ottenere più componenti**.

2. Cercare il componente di **Azure messaggistica** e aggiungerlo al progetto.

3. Cercare il componente **Client di messaggistica di Google Cloud** e aggiungerlo al progetto.


###<a name="set-up-notification-hubs-in-your-project"></a>Configurare gli hub di notifica di un progetto

1. Raccogliere le informazioni seguenti per l'hub di app e la notifica Android:

    - **GoogleProjectNumber**: è possibile ottenere questo valore numero progetto dal panoramica dell'app nel portale per sviluppatori di Google. L'app è stata creata nel portale di apportate una nota di questo valore in precedenza.
    - **Ascoltare stringa di connessione**: nel dashboard nel [Portale classica di Azure], fare clic su **visualizzazione stringhe di connessione**. Copiare la stringa di connessione *DefaultListenSharedAccessSignature* per questo valore.
    - **Nome dell'hub**: si tratta del nome del computer di controllo dal [Portale classica Azure]. Ad esempio *mynotificationhub2*.

    Creare una classe **Constants.cs** per il progetto Xamarin e definire i valori delle costanti seguenti nella classe. Sostituire i segnaposto con i valori.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Aggiungere le seguenti istruzioni **MainActivity.cs**using:

        using Android.Util;
        using Gcm.Client;

3. Aggiungere una variabile di istanza per il `MainActivity` classe che verrà utilizzato per visualizzare una finestra di dialogo di avviso durante l'esecuzione dell'app:

        public static MainActivity instance;


3. Creare il metodo seguente nella classe **MainActivity** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Nel `OnCreate` metodo di **MainActivity.cs**inizializzare il `instance` variabile e aggiungere una chiamata a `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Creare una nuova classe **MyBroadcastReceiver**.

    > [AZURE.NOTE] Esamineremo la creazione di una classe **BroadcastReceiver** da zero riportata di seguito. Tuttavia, un'alternativa rapida per creare manualmente **MyBroadcastReceiver.cs** consiste nel fare riferimento al file **GcmService.cs** trovato nel progetto di Xamarin.Android di esempio incluso con gli [esempi NotificationHubs][GitHub]. Duplicare **GcmService.cs** e modificare i nomi di classe può essere un ottimo punto di partenza anche.

5. Aggiungere le seguenti istruzioni **MyBroadcastReceiver.cs** (riferimento per il componente e assembly aggiunto in precedenza) using:

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. In **MyBroadcastReceiver.cs**, aggiungere le richieste di autorizzazione seguenti tra le istruzioni di **utilizzo** e la dichiarazione **dello spazio dei nomi** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. In **MyBroadcastReceiver.cs**, modificare la classe **MyBroadcastReceiver** in modo da corrispondere le operazioni seguenti:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Aggiungere un'altra classe in **MyBroadcastReceiver.cs** denominata **PushHandlerService**, che deriva da **GcmServiceBase**. Assicurarsi di applicare l'attributo **del servizio** per la classe:

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** implementata metodi **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()**e **OnError()**. La classe di implementazione **PushHandlerService** necessario eseguire l'override di questi metodi e questi metodi verranno generato in risposta a interagire con l'hub di notifica.


9. Ignorare il metodo **OnRegistered()** in **PushHandlerService** utilizzando il codice seguente:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] Nel codice **OnRegistered()** , annotare la possibilità di specificare tag per eseguire la registrazione per canali messaggistica specifici.

10. Ignorare il metodo **OnMessage** in **PushHandlerService** utilizzando il codice seguente:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Aggiungere i seguenti metodi **createNotification** e **dialogNotify** **PushHandlerService** per informare gli utenti quando si riceve una notifica.

    >[AZURE.NOTE] Progettazione di notifica di Android 5.0 e versioni successive rappresenta un allontanamento significativo rispetto a quello delle versioni precedenti. Se si verifica in Android 5.0 o versioni successive, sarà necessario eseguire per ricevere la notifica dell'app. Per ulteriori informazioni, vedere [Notifiche Android](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Override membri astratti **OnUnRegistered()**, **OnRecoverableError()**e **OnError()** in modo che il codice viene compilato:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>Eseguire l'app nell'emulatore

Se si esegue questa app nell'emulatore, assicurarsi che utilizzare un oggetto Android virtuale dispositivo (AVD) che supporta Google APIs.

> [AZURE.IMPORTANT] Per ricevere notifiche push, è necessario impostare un account di Google nel dispositivo Android virtuale. (Nell'emulatore, passare a **Impostazioni** e fare clic su **Aggiungi Account**) Assicurarsi che l'emulatore sia connesso a Internet.

>[AZURE.NOTE] Progettazione di notifica di Android 5.0 e versioni successive rappresenta un allontanamento significativo rispetto a quello delle versioni precedenti. Per ulteriori informazioni, vedere [Notifiche Android](http://go.microsoft.com/fwlink/?LinkId=615880).


1. **Strumenti**, fare clic su **Apri Android emulatore Manager**, selezionare il dispositivo e quindi fare clic su **Modifica**.

    ![][18]

2. Selezionare **Google API** di **destinazione**e quindi fare clic su **OK**.

    ![][19]

3. Sulla barra superiore, fare clic su **Esegui**e quindi selezionare l'app. Verrà avviato l'emulatore e viene eseguita l'app.

  L'app recupera *registrationId* da GCM e registra con l'hub di notifica.

##<a name="send-notifications-from-your-backend"></a>Invio di notifiche dal back-end


È possibile testare ricezione delle notifiche nell'app tramite l'invio di notifiche nel [Portale classica Azure] attraverso la scheda debug dell'hub di notifica, come illustrato nell'immagine seguente.

![][30]


Notifiche push normalmente vengono inviate in un servizio di back-end come servizi mobili o ASP.NET tramite una libreria compatibile. È anche possibile utilizzare API REST direttamente a inviare messaggi di notifica se una raccolta non è disponibile per il back-end.

Di seguito è riportato un elenco di alcune altre esercitazioni che è possibile esaminare per l'invio di notifiche:

- ASP.NET, Vedere [Utilizzare gli hub di notifica per le notifiche push per gli utenti].
- Azure notifica hub linguaggio SDK: informazioni su [come usare gli hub di notifica da Java](notification-hubs-java-push-notification-tutorial.md) per l'invio di notifiche da Java. È stato verificato in Eclisse per lo sviluppo di Android.
- PHP: Informazioni su [come usare gli hub di notifica da PHP](notification-hubs-php-push-notification-tutorial.md).


Nelle sottosezioni successive dell'esercitazione, inviare notifiche usando un'app di console .NET e utilizzando un servizio per dispositivi mobili mediante script del nodo.

####<a name="optional-send-notifications-by-using-a-net-app"></a>(Facoltativo) Inviare le notifiche tramite un'app di .NET

In questa sezione sono le notifiche verranno inviate usando un'app di console .NET

1. Creare una nuova applicazione console c#:

    ![][20]

2. In Visual Studio, fare clic su **Strumenti**, fare clic su **Gestione pacchetti NuGet**e quindi fare clic su **Gestione pacchetti Console**.

    Verrà visualizzata la Console di gestione pacchetti in Visual Studio.

3. Nella finestra Gestione pacchetti Console set **predefinito di project** per il nuovo progetto applicazione console e quindi nella finestra della console, eseguire il comando seguente:

        Install-Package Microsoft.Azure.NotificationHubs

    Aggiungere un riferimento di Azure notifica hub SDK utilizzando il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto Microsoft.Azure.Notification hub NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Aprire il file Program.cs e aggiungere le seguenti `using` istruzione:

        using Microsoft.Azure.NotificationHubs;

5. Nel `Program` per la classe, aggiungere il metodo seguente. Aggiornare il testo segnaposto con il proprio *DefaultFullSharedAccessSignature* connessione stringa e hub nome dal [Portale classica Azure].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Aggiungere le righe seguenti nel metodo **Main** :

         SendNotificationAsync();
         Console.ReadLine();

7. Premere F5 per eseguire l'app. Si riceverà una notifica nell'app.

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>(Facoltativo) Inviare le notifiche tramite un servizio per dispositivi mobili

1. Seguire [Introduzione a servizi mobili].

1. Accedere al [Portale classica Azure]e selezionare il servizio mobile.

2. Selezionare la scheda **utilità di pianificazione** nella parte superiore.

    ![][22]

3. Creare un nuovo processo pianificato, inserire un nome e selezionare **su richiesta**.

    ![][23]

4. Quando viene creato il processo, fare clic sul nome di processo. Quindi fare clic sulla scheda **Script** sulla barra superiore.

5. Inserire il seguente script all'interno della funzione di pianificazione. Assicurarsi di sostituire i segnaposto con il nome di hub di notifica e la stringa di connessione per *DefaultFullSharedAccessSignature* ottenuto in precedenza. Fare clic su **Salva**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Fare clic su **Esegui una volta** nella barra inferiore. Si riceverà una notifica di tipo avviso popup.

##<a name="next-steps"></a>Passaggi successivi

In questo esempio semplice trasmessa notifiche per tutti i dispositivi Android. Per impostare la destinazione utenti specifici, fare riferimento all'esercitazione [Utilizzare gli hub di notifica per le notifiche push per gli utenti]. Se si vuole segmento gli utenti dai gruppi di interesse, è possibile leggere [Utilizzare gli hub di notifica per inviare ultime notizie]. Ulteriori informazioni su come usare gli hub di notifica [Indicazioni hub di notifica] e [Notifica hub procedure per Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Guida introduttiva a servizi mobili]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portale classica Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notifica hub indicazioni]: http://msdn.microsoft.com/library/jj927170.aspx
[Notifica hub procedure per Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Utilizzare gli hub di notifica per le notifiche push per gli utenti]: /manage/services/notification-hubs/notify-users-aspnet
[Utilizzare gli hub di notifica per inviare ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Componente Client di messaggistica di Google Cloud]: http://components.xamarin.com/view/GCMClient/
[Componente di messaggistica di Azure]: http://components.xamarin.com/view/azure-messaging
