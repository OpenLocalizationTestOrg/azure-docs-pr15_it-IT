<properties
    pageTitle="Guida introduttiva di Azure notifica hub per le applicazioni di dispositivo Kindle | Microsoft Azure"
    description="In questa esercitazione si imparerà a utilizzare Azure notifica hub per inviare le notifiche push per un'applicazione di dispositivo Kindle."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Guida introduttiva a hub di notifica per App dispositivo Kindle

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come utilizzare Azure notifica hub per inviare le notifiche push per un'applicazione di dispositivo Kindle.
Sarà necessario creare un'app dispositivo Kindle vuota che riceve le notifiche push utilizzando messaggistica dispositivo Amazon (ADM).

##<a name="prerequisites"></a>Prerequisiti

In questa esercitazione richiede le operazioni seguenti:

+ È possibile ottenere SDK Android (si presuppone che si utilizzerà Eclisse) dal <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sito Android</a>.
+ Seguire i passaggi descritti in <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Impostazione delle variabili di sviluppo ambiente</a> per configurare l'ambiente di sviluppo per dispositivo Kindle.

##<a name="add-a-new-app-to-the-developer-portal"></a>Aggiungere una nuova app del portale per sviluppatori

1. Prima di tutto, creare un'app nel [portale per sviluppatori di Amazon].

    ![][0]

2. Copiare il **tasto applicazione**.

    ![][1]

3. Nel portale, fare clic sul nome dell'app e quindi fare clic sulla scheda **Dispositivo messaggistica** .

    ![][2]

4. Fare clic su **Crea un nuovo profilo di sicurezza**e quindi si crea un nuovo profilo di sicurezza (ad esempio, **il profilo di sicurezza TestAdm**). Fare clic su **Salva**.

    ![][3]

5. Fare clic su **Profili di sicurezza** per visualizzare il profilo di sicurezza appena creata. Copiare i valori **ID Client** e **Client segreto** per successivi utilizzi.

    ![][4]

## <a name="create-an-api-key"></a>Creare una chiave dell'API

1. Aprire un prompt dei comandi con privilegi di amministratore.
2. Passare alla cartella SDK Android.
3. Immettere il comando seguente:

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  Per la password **keystore** digitare **android**.

5.  Copiare l'ID digitale **MD5** .
6.  Nel portale per sviluppatori, nella scheda **messaggistica** fare clic su **Android/dispositivo Kindle** e immettere il nome del pacchetto per l'applicazione (ad esempio **com.sample.notificationhubtest**) e il valore **MD5** e quindi fare clic su **Genera chiave dell'API**.

## <a name="add-credentials-to-the-hub"></a>Aggiungere le credenziali per l'hub

Nel portale di aggiungere la scheda **Configura** dell'hub notifica il segreto del client e l'ID client.

## <a name="set-up-your-application"></a>Impostare l'applicazione

> [AZURE.NOTE] Quando si crea un'applicazione, utilizzare almeno API livello 17.

Aggiungere librerie ADM al progetto Eclisse:

1. Per ottenere la raccolta ADM, [scaricare il SDK]. Estrarre il file zip SDK.
2. In Eclisse, mouse sul progetto e quindi fare clic su **proprietà**. Selezionare **Il percorso di compilazione di linguaggio** sul lato sinistro e quindi selezionare la scheda **raccolte **nella parte superiore. Fare clic su **Aggiungi Jar esterni**e selezionare il file `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` dalla directory in cui sono stati estratti SDK Amazon.
3. Scaricare il SDK Android NotificationHubs (collegamento).
4. Decomprimere il pacchetto e quindi trascinare il file `notification-hubs-sdk.jar` nel `libs` cartella Eclisse.

Modificare il manifesto app per supportare i ADM:

1. Aggiungere lo spazio di Amazon nell'elemento manifesto radice:


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Aggiungi autorizzazioni come primo elemento nell'elemento manifesto. Sostituire **[Nome pacchetto]** con il pacchetto utilizzati per creare l'applicazione.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Inserire l'elemento seguente come il primo elemento figlio dell'elemento dell'applicazione. Ricordarsi di sostituire **[Nome servizio]** con il nome del gestore messaggio ADM creata nella sezione successiva (incluso il pacchetto) e sostituire **[Nome pacchetto]** con il nome del pacchetto con cui si è creato l'app.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Creare il gestore di messaggi ADM

1. Creare una nuova classe eredita `com.amazon.device.messaging.ADMMessageHandlerBase` e denominarla `MyADMMessageHandler`, come illustrato nella figura seguente:

    ![][6]

2. Aggiungere quanto segue `import` istruzioni:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. Aggiungere il codice seguente nella classe che è stato creato. Ricordarsi di sostituire l'hub nome stringa di connessione e (ascolto):

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. Aggiungere il codice seguente per il `OnMessage()` metodo:

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Aggiungere il codice seguente per il `OnRegistered` metodo:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Aggiungere il codice seguente per il `OnUnregistered` metodo:

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. Nel `MainActivity` metodo, aggiungere l'istruzione import seguente:

        import com.amazon.device.messaging.ADM;

8. Aggiungere il codice seguente alla fine della `OnCreate` metodo:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Aggiungere la chiave dell'API applicazione in uso.

1. In Eclisse, creare un nuovo file denominato **api_key.txt** attività directory del progetto.
2. Aprire il file e copiare la chiave dell'API generati nel portale per sviluppatori di Amazon.

## <a name="run-the-app"></a>Eseguire l'app

1. Avviare l'emulatore.
2. Nell'emulatore, scorrere rapidamente dall'alto e fare clic su **Impostazioni**, quindi fare clic su **account personale** e registrare con un account di Amazon valido.
3. In Eclisse, eseguire l'app.

> [AZURE.NOTE] Se si verifica un problema, verificare l'ora dell'emulatore (o dispositivo). Il valore di ora deve essere accurato. Per modificare l'ora dell'emulatore dispositivo Kindle, è possibile eseguire il comando seguente dalla directory di strumenti per la piattaforma SDK Android:

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Inviare un messaggio

Per inviare un messaggio usando .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Portale per sviluppatori di Amazon]: https://developer.amazon.com/home.html
[scaricare il SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
