1. Aprire il file nel progetto **app** `AndroidManifest.xml`. Nel codice in due passaggi successivi, sostituire _`**my_app_package**`_ con il nome del pacchetto di app per il progetto, che rappresenta il valore della `package` attributo del `manifest` tag.

2. Aggiungere nuove autorizzazioni seguenti alla fine dell'istruzione `uses-permission` elemento:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Aggiungere il codice seguente dopo il `application` tag di apertura:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Aprire il file *ToDoActivity.java*e aggiungere l'istruzione import seguente:

        import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Aggiungere la seguente variabile privata per la classe: sostituire _`<PROJECT_NUMBER>`_ il numero di progetto assegnato da Google all'app nella procedura precedente:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. Modificare la definizione di *MobileServiceClient* da **privato** a **pubblico statico**in modo che a questo punto è simile alla seguente:

        public static MobileServiceClient mClient;

7. Successivamente è necessario aggiungere una nuova classe per gestire le notifiche. In Esplora aree di progetto, aprire **src** => **principale** => **java** nodi e rapida nodo nome pacchetto: fare clic su **Nuovo**e quindi fare clic su **Classe Java**.

8. In **nome** digitare `MyHandler`, quindi fare clic su **OK**.


    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


9. Nel file MyHandler sostituire la dichiarazione di classe con

        public class MyHandler extends NotificationsHandler {


10. Aggiungere le seguenti istruzioni di importazione per il `MyHandler` classe:

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;


11. Aggiungere il membro per il `MyHandler` classe:

        public static final int NOTIFICATION_ID = 1;


12. Nel `MyHandler` per la classe, aggiungere il codice riportato di seguito per ignorare il metodo **onRegistered** , che registra il dispositivo con il servizio mobile Hub di notifica.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            new AsyncTask<Void, Void, Void>() {

                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                        return null;
                    }
                    catch(Exception e) {
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }


13. Nel `MyHandler` per la classe, aggiungere il codice riportato di seguito per ignorare il metodo **onReceive** , che causa la notifica da visualizzare quando si riceve.

        @Override
        public void onReceive(Context context, Bundle bundle) {
                String msg = bundle.getString("message");

                PendingIntent contentIntent = PendingIntent.getActivity(context,
                        0, // requestCode
                        new Intent(context, ToDoActivity.class),
                        0); // flags

                Notification notification = new NotificationCompat.Builder(context)
                        .setSmallIcon(R.drawable.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                        .setContentText(msg)
                        .setContentIntent(contentIntent)
                        .build();

                NotificationManager notificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);
                notificationManager.notify(NOTIFICATION_ID, notification);
        }


14. Nel file TodoActivity.java aggiornare il metodo **onCreate** della classe *ToDoActivity* per registrare la classe di gestore di notifica. Assicurarsi che aggiungere il codice dopo *MobileServiceClient* viene creata un'istanza.


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    L'app è stato aggiornato per le notifiche push di supporto.
