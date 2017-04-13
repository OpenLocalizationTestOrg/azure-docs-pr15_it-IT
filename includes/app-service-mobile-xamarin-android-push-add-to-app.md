4. Creare una nuova classe nel progetto chiamato `ToDoBroadcastReceiver`.

5. Aggiungere le seguenti istruzioni per la classe **ToDoBroadcastReceiver** using:

        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

6. Aggiungere le richieste di autorizzazione seguenti tra le istruzioni di **utilizzo** e la dichiarazione **dello spazio dei nomi** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. Sostituire la definizione di classe **ToDoBroadcastReceiver** esistente con le operazioni seguenti:
 
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
        public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            // Set the Google app ID.
            public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
        }

    Nel codice precedente, è necessario sostituire _`<PROJECT_NUMBER>`_ con il numero di progetto assegnato da Google quando viene completato il provisioning l'app nel portale per sviluppatori di Google. 

8. Nel file di progetto ToDoBroadcastReceiver.cs aggiungere il codice seguente che definisce la classe **PushHandlerService** :
 
        // The ServiceAttribute must be applied to the class.
        [Service] 
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
 
            public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
        }

    Si noti che questa classe deriva da **GcmServiceBase** e che l'attributo **servizio** deve essere applicato a questa classe.

    >[AZURE.NOTE]La classe **GcmServiceBase** implementato dei metodi di **OnUnRegistered()**, **OnMessage()** e **OnError()** , **OnRegistered()**. È necessario eseguire l'override di questi metodi nella classe **PushHandlerService** .

5. Aggiungere il codice seguente alla classe **PushHandlerService** che sostituisce il gestore eventi **OnRegistered** . 

        protected override void OnRegistered(Context context, string registrationId)
        {
            System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");

            // Get the MobileServiceClient from the current activity instance.
            MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;
            var push = client.GetPush();

            // Define a message body for GCM.
            const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

            // Define the template registration as JSON.
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
              {"body", templateBodyGCM }
            };

            try
            {
                // Make sure we run the registration on the same thread as the activity, 
                // to avoid threading errors.
                ToDoActivity.CurrentActivity.RunOnUiThread(

                    // Register the template with Notification Hubs.
                    async () => await push.RegisterAsync(registrationId, templates));
                
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Push Installation Id", push.InstallationId.ToString()));
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Error with Azure push registration: {0}", ex.Message));
            }
        }

    Questo metodo utilizza l'ID di registrazione GCM restituito per eseguire la registrazione con Azure per le notifiche push. Tag possono essere aggiunti solo per la registrazione dopo averlo creato. Per ulteriori informazioni, vedere [come: aggiungere contrassegni a installazione di un dispositivo per abilitare push su tag](../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

10. Override del metodo **OnMessage** in **PushHandlerService** con il codice seguente:

        protected override void OnMessage(Context context, Intent intent)
        {          
            string message = string.Empty;

            // Extract the push notification message from the intent.
            if (intent.Extras.ContainsKey("message"))
            {
                message = intent.Extras.Get("message").ToString();
                var title = "New item added:";

                // Create a notification manager to send the notification.
                var notificationManager = 
                    GetSystemService(Context.NotificationService) as NotificationManager;

                // Create a new intent to show the notification in the UI. 
                PendingIntent contentIntent = 
                    PendingIntent.GetActivity(context, 0, 
                    new Intent(this, typeof(ToDoActivity)), 0);           

                // Create the notification using the builder.
                var builder = new Notification.Builder(context);
                builder.SetAutoCancel(true);
                builder.SetContentTitle(title);
                builder.SetContentText(message);
                builder.SetSmallIcon(Resource.Drawable.ic_launcher);
                builder.SetContentIntent(contentIntent);
                var notification = builder.Build();

                // Display the notification in the Notifications Area.
                notificationManager.Notify(1, notification);

            }
        }

12. Ignorare i metodi **OnUnRegistered()** e **OnError()** con il codice riportato di seguito.

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            throw new NotImplementedException();
        }

        protected override void OnError(Context context, string errorId)
        {
            System.Diagnostics.Debug.WriteLine(
                string.Format("Error occurred in the notification: {0}.", errorId));
        }