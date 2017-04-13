<properties
    pageTitle="Invio di notifiche push per Android con gli hub di notifica di Azure e messaggistica Cloud Firebase | Microsoft Azure"
    description="In questa esercitazione si imparerà a utilizzare gli hub di notifica di Azure e Firebase messaggistica Cloud per le notifiche push per dispositivi Android."
    services="notification-hubs"
    documentationCenter="android"
    keywords="inserire le notifiche, notifica push, notifica push android, fcm, firebase cloud messaggistica"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Invio di notifiche push per Android con gli hub di notifica di Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Panoramica

> [AZURE.IMPORTANT] In questo argomento illustra le notifiche push con Google Firebase Cloud messaggistica (FCM). Se si sta ancora utilizzando Google Cloud messaggistica (GCM), vedere [invio di notifiche push per Android con gli hub di notifica di Azure e GCM](notification-hubs-android-push-notification-google-gcm-get-started.md).

In questa esercitazione viene illustrato come utilizzare hub di notifica di Azure e messaggistica Cloud Firebase per inviare le notifiche push a un'applicazione Android.
Sarà necessario creare un'app di Android vuota che riceve le notifiche push utilizzando messaggistica Cloud Firebase (FCM).



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione può essere scaricato dal GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).


##<a name="prerequisites"></a>Prerequisiti

> [AZURE.IMPORTANT] Per completare questa esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

- Oltre a un account Azure attivo indicato in precedenza, questa esercitazione richiede la versione più recente di [Studio Android](http://go.microsoft.com/fwlink/?LinkId=389797).
- Android 2.3 o versione successiva per la messaggistica Cloud Firebase.
- Revisione di archivio di Google 27 o versioni successiva è necessaria per la messaggistica Cloud Firebase.
- Google Play Services 9.0.2 dei o versioni successive per la messaggistica Cloud Firebase.
- Esercitazione è un prerequisito per tutte le altre esercitazioni hub di notifica per le app Android.


##<a name="create-a-new-android-studio-project"></a>Creare un nuovo progetto di Studio Android

1. Android Studio, avviare un nuovo progetto di Studio Android.

    ![Studio Android - nuovo progetto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. Scegliere il fattore di forma **telefoni e Tablet** e il **Minimo SDK** che si desidera supportare. Fare clic su **Avanti**.

    ![Studio Android - flusso di lavoro creazione progetto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. Scegliere **Attività vuota** per l'attività principale, fare clic su **Avanti**e quindi fare clic su **Fine**.


##<a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Creare un progetto che supporta messaggistica Cloud Firebase

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>Configurare un nuovo hub di notifica

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. e **l'Impostazioni** dell'hub di notifica, selezionare **I servizi di notifica** quindi **Google (GCM)**. Immettere la chiave del server FCM copiato in precedenza dalla [console di Firebase](https://firebase.google.com/console/) e fare clic su **Salva**.

&emsp;&emsp;![Notifica Azure hub - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

L'hub di notifica è ora configurato per l'utilizzo con Firebase Cloud Messagin e disporre le stringhe di connessione a entrambi registrare l'app per ricevere e inviare le notifiche push.

##<a id="connecting-app"></a>Connettere l'app all'hub di notifica

###<a name="add-google-play-services-to-the-project"></a>Aggiungere servizi Google Play al progetto

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>Aggiunta di raccolte hub di notifica di Azure


1. Nel `Build.Gradle` file per l' **app**, aggiungere le righe seguenti nella sezione **dipendenze** .

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Aggiungere l'archivio seguente dopo la sezione **dipendenze** .

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Aggiornare il AndroidManifest.xml.


1. Per supportare FCM, è necessario implementare un servizio di comunicare ascoltatore ID istanza nel codice che consente di [ottenere i token di registrazione](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) utilizzando [l'API FirebaseInstanceId Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). In questa esercitazione verrà assegnato un nome della classe `MyInstanceIDService`. 
 
    Aggiungere la definizione di servizio seguente al file AndroidManifest.xml all'interno di `<application>` tag. 

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. Dopo il token di registrazione FCM abbiamo ricevuto dall'API FirebaseInstanceId, verrà usata per [registrare con l'Hub di notifica di Azure](notification-hubs-push-notification-registration-management.md). Abbiamo supporterà la registrazione in background utilizzando un `IntentService` denominata `RegistrationIntentService`. Questo servizio sarà anche responsabile per l'aggiornamento il token di registrazione FCM.
 
    Aggiungere la definizione di servizio seguente al file AndroidManifest.xml all'interno di `<application>` tag. 

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. È inoltre possibile definire un ricevitore per ricevere una notifica. Aggiungere la definizione di ricevitore seguente al file AndroidManifest.xml all'interno di `<application>` tag. Sostituire il `<your package>` segnaposto con il proprio nome pacchetto effettivo visualizzato nella parte superiore la `AndroidManifest.xml` file.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. Aggiungere quanto segue FCM necessarie correlate autorizzazioni sotto il `</application>` tag. Assicurarsi di sostituire `<your package>` con il nome del pacchetto visualizzato nella parte superiore di `AndroidManifest.xml` file.

    Per ulteriori informazioni su queste autorizzazioni, vedere [configurazione di un'applicazione Client GCM per Android](https://developers.google.com/cloud-messaging/android/client#manifest) e [spostare un'applicazione Client GCM per Android alla messaggistica Cloud Firebase](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### <a name="adding-code"></a>Aggiunta di codice


1. Nella visualizzazione progetto espandere **app** > **src** > **principale** > **java**. Fare clic sulla cartella del pacchetto in **linguaggio**, fare clic su **Nuovo**e quindi fare clic su **Classe Java**. Aggiungere una nuova classe denominata `NotificationSettings`. 

    ![Studio Android - nuova classe Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

    Assicurarsi di aggiornare questi tre segnaposto nel codice seguente per il `NotificationSettings` classe:
    * **SenderId**: l'Id mittente è stato acquistato in precedenza nella scheda **Messaggistica Cloud** delle impostazioni del progetto nella [console Firebase](https://firebase.google.com/console/).
    * **HubListenConnectionString**: la stringa di connessione **DefaultListenAccessSignature** per il tuo fulcro. È possibile copiare la stringa di connessione, scegliere **I criteri di accesso** e **l'Impostazioni** del tuo fulcro nel [Portale di Azure].
    * **HubName**: usare il nome dell'hub di notifica visualizzato e il hub nel [Portale di Azure].

    `NotificationSettings`codice:

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }

2. Seguendo la procedura precedente, aggiungere un'altra nuova classe denominata `MyInstanceIDService`. Questo sarà l'implementazione di servizio comunicare ascoltatore ID istanza.

    Il codice per la classe chiama il nostro `IntentService` per [aggiornare il token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) in background.

        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;
        
        
        public class MyInstanceIDService extends FirebaseInstanceIdService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.d(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. Aggiungere un'altra classe al progetto denominato, `RegistrationIntentService`. Questo sarà l'implementazione per il nostro `IntentService` che consente di gestire [l'aggiornamento di token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e [la registrazione con l'hub di notifica](notification-hubs-push-notification-registration-management.md).

    Utilizzare il codice seguente per la classe.

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {
        
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
        
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. Nel `MainActivity` per la classe, aggiungere la seguente `import` istruzioni sopra la dichiarazione di classe.

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. Aggiungere i membri privati seguenti nella parte superiore della classe. Verrà usata questi [Controlla la disponibilità di Google Play Services come consigliato da Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. Nel `MainActivity` per la classe, aggiungere il metodo seguente per la disponibilità di Google Play Services. 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. Nel `MainActivity` per la classe, aggiungere il codice seguente controllerà per Google Play Services prima di chiamare il `IntentService` per ottenere la registrazione FCM token e registrare con l'hub di notifica.

        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. Nel `OnCreate` metodo del `MainActivity` per la classe, aggiungere il codice seguente per avviare il processo di registrazione quando si crea attività.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. Aggiungere questi altri metodi per la `MainActivity` per verificare lo stato di stato e rapporto di app nell'app.

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. Il `ToastNotify` metodo utilizza *"Hello World"* `TextView` controllo per segnalare lo stato e notifiche in modo permanente nell'app. Nel layout activity_main.xml, aggiungere il seguente id per il controllo.

        android:id="@+id/text_hello"

11. Successivamente verrà aggiunta una sottoclasse per il ricevitore che è definiti nel AndroidManifest.xml. Aggiungere un'altra classe al progetto denominato `MyHandler`.

12. Aggiungere le seguenti istruzioni di importazione nella parte superiore `MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Aggiungere il codice seguente per il `MyHandler` classe rendendola sottoclasse di `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Questo codice esegue l'override di `OnReceive` metodo, in modo che il gestore segnalerà le notifiche ricevute. Il gestore inoltre invia notifica push per la gestione di notifiche Android utilizzando il `sendNotification()` metodo. Il `sendNotification()` metodo deve essere eseguito quando l'app non è in esecuzione e ricezione di una notifica.

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. Android Studio sulla barra dei menu, fare clic su **compilazione** > **Ricostruire progetto** per assicurarsi che non siano presenti nel codice errori.
15. Eseguire l'app nel dispositivo e verificare che registra correttamente con l'hub di notifica. 

    > [AZURE.NOTE] Registrazione potrebbe non riuscire sulla barra di avvio iniziale finché il `onTokenRefresh()` chiamato dell'istanza servizio Id. L'aggiornamento è necessario avviare una registrazione positiva con l'hub di notifica.

##<a name="sending-push-notifications"></a>Invio di notifiche push

È possibile verificare la ricezione delle notifiche push nell'app inviandole tramite il [Portale di Azure] - aspetto per la sezione **risoluzione dei problemi** e il hub, come illustrato di seguito.

![Inviare notifica Azure hub - Test](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Facoltativo) Inviare le notifiche push direttamente dall'app

>[AZURE.IMPORTANT] In questo esempio di invio di notifiche da app client disponibile per solo a scopo didattico. Dal momento che sarà necessario il `DefaultFullSharedAccessSignature` ad assistere all'applicazione client, vengono esposti l'hub di notifica per il rischio che un utente può accedere per l'invio di notifiche non autorizzate ai client.

In genere, inviare notifiche tramite un server di back-end. In alcuni casi, è consigliabile la possibilità di inviare le notifiche push direttamente dall'applicazione client. In questa sezione viene descritto come inviare notifiche dal client tramite l' [API REST di Azure notifica Hub](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. In visualizzazione di progetto di Studio Android, espandere **App** > **src** > **principale** > **risoluzioni** > **layout**. Aprire la `activity_main.xml` file di layout e fare clic su **testo** della scheda per aggiornare il contenuto di testo del file. Aggiornarla con il codice riportato di seguito, che consente di aggiungere nuove `Button` e `EditText` controlli per l'invio di messaggi di notifica push per l'hub di notifica. Aggiungere il codice nella parte inferiore, prima `</RelativeLayout>`.

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. In visualizzazione di progetto di Studio Android, espandere **App** > **src** > **principale** > **risoluzioni** > **valori**. Aprire la `strings.xml` file e aggiungere i valori stringa che fanno riferimento il nuovo `Button` e `EditText` controlli. Aggiungere questi nella parte inferiore del file prima `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. Nel `NotificationSetting.java` file, aggiungere le seguenti impostazioni per la `NotificationSettings` classe.

    Aggiornamento `HubFullAccess` con la stringa di connessione **DefaultFullSharedAccessSignature** per il tuo fulcro. È possibile copiare la stringa di connessione dal [Portale di Azure] , scegliere **I criteri di accesso** e **l'Impostazioni** per l'hub di notifica.

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. Nel `MainActivity.java` file, aggiungere quanto segue `import` istruzioni sopra la `MainActivity` classe.

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. Nel `MainActivity.java` file, aggiungere i membri seguenti nella parte superiore della `MainActivity` classe.  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. È necessario creare un token di firma di Access Software (SA) per eseguire l'autenticazione di una richiesta POST per inviare messaggi all'hub di notifica. È necessario analisi dei dati chiavi dalla stringa di connessione e quindi creare token sa come indicato nel riferimento all'API REST [Concetti comuni](http://msdn.microsoft.com/library/azure/dn495627.aspx) . Il codice seguente è un esempio di implementazione.

    In `MainActivity.java`, aggiungere il metodo seguente per il `MainActivity` classe per analizzare la stringa di connessione.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. In `MainActivity.java`, aggiungere il metodo seguente per il `MainActivity` classe per creare un token di autenticazione SA.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. In `MainActivity.java`, aggiungere il metodo seguente per il `MainActivity` classe per gestire il pulsante **Invia notifica** e inviare il messaggio di notifica push all'hub tramite l'API REST incorporato.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>Test l'app

####<a name="push-notifications-in-the-emulator"></a>Notifiche push nell'emulatore

Se si desidera verificare le notifiche push all'interno di un emulatore, assicurarsi che l'immagine di emulatore supporta il livello di Google API che si è scelto per l'app. Se l'immagine non supporta APIs Google native, otterranno le **servizio\_non\_disponibile** eccezione.

Oltre a quanto sopra menzionato, verificare di aver ha aggiunto l'account Google per l'emulatore in esecuzione in **Impostazioni** > **account**. In caso contrario, il tentativo di eseguire la registrazione con GCM potrebbe il **autenticazione\_non riuscito** eccezione.

####<a name="running-the-application"></a>Esecuzione dell'applicazione

1. Eseguire l'app e notare che l'ID di registrazione viene segnalato per una registrazione positiva.

    ![Test su Android - la registrazione del canale](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. Immettere un messaggio di notifica da inviare a tutti i dispositivi Android che hanno registrato con l'hub.

    ![Test su Android - invio di un messaggio](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. Premere **invio della notifica**. Eventuali dispositivi che dispone dell'app in esecuzione verranno visualizzato un `AlertDialog` istanza con il messaggio di notifica push. Dispositivi che non dispone dell'app in esecuzione ma precedentemente registrati per le notifiche push riceveranno una notifica in Gestione notifiche Android. Quelli possono essere visualizzati, scorrere verso il basso dall'angolo superiore sinistro.

    ![Test su Android - notifiche](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##<a name="next-steps"></a>Passaggi successivi

È consigliabile l'esercitazione di [Utilizzare gli hub di notifica per le notifiche push agli utenti] il passaggio successivo. Verrà visualizzato si sull'invio di notifiche da un back-end ASP.NET uso di tag a utenti specifici.

Se si vuole segmento gli utenti dai gruppi di interesse, vedere l'esercitazione di [Utilizzare gli hub di notifica per inviare ultime notizie] .

Per ulteriori informazioni generali sull'hub di notifica, vedere le [Indicazioni hub di notifica].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notifica hub indicazioni]: notification-hubs-push-notification-overview.md
[Utilizzare gli hub di notifica per le notifiche push per gli utenti]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Utilizzare gli hub di notifica per inviare ultime notizie]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portale di Azure]: https://portal.azure.com
