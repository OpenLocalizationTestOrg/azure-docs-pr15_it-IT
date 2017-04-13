<properties
    pageTitle="Hub notifica le ultime notizie Tutorial - Android"
    description="Informazioni su come usare gli hub di notifica di Azure servizio Bus inviare ultime notizie notifiche ai dispositivi Android."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizzare gli hub di notifica per inviare ultime notizie

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come utilizzare Azure notifica hub per trasmettere le notifiche di news importanti per un'app di Android. Al termine, si sarà in grado di eseguire la registrazione per l'interruzione delle categorie di news che si è interessati e ricevere solo le notifiche push per le categorie. Questo scenario è un modello comune per molte applicazioni in cui le notifiche devono essere inviate a gruppi di utenti che hanno precedentemente dichiarati interesse essi, ad esempio lettore RSS, App per fan e così via.

Trasmissione sono abilitati facilmente individuabili perché includono uno o più _tag_ durante la creazione di una registrazione nell'hub notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno registrato per il tag riceverà la notifica. Poiché i contrassegni sono semplicemente stringhe, non è necessario eseguire il provisioning in anticipo. Per ulteriori informazioni sui tag, fare riferimento al [Routing hub di notifica e le espressioni di Tag](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Prerequisiti

In questo argomento si basa sull'app è stato creato in [Guida introduttiva a hub di notifica][get-started]. Prima di iniziare questa esercitazione, è necessario avere già completato [iniziare con gli hub di notifica][get-started].

##<a name="add-category-selection-to-the-app"></a>Aggiungere una selezione di una categoria all'app

Il primo passaggio consiste nell'aggiungere gli elementi dell'interfaccia utente per le attività principali esistente che consentono all'utente di selezionare le categorie per registrare. Le categorie selezionate da un utente sono archiviate nel dispositivo. Quando si avvia l'app, una registrazione di dispositivo viene creata l'hub di notifica con le categorie selezionate come tag.

1. Aprire il file res/layout/activity_main.xml e sostituire il contenuto con le operazioni seguenti:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. Aprire il file res/values/strings.xml e aggiungere le righe seguenti:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    Il layout di grafico main_activity.xml dovrebbe ora simile al seguente:

    ![][A1]

3. Creare una classe **notifiche** nello stesso pacchetto la classe **MainActivity** .

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Questa classe utilizza l'archiviazione locale per archiviare le varie categorie di news del dispositivo per la ricezione. Sono inoltre disponibili metodi per eseguire la registrazione per queste categorie.


4. In una classe **MainActivity** rimuovere i campi privati per **NotificationHub** e **GoogleCloudMessaging**e aggiungere un campo per **le notifiche**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. Il metodo **onCreate** , rimuovere l'inizializzazione del campo **hub** e il metodo **registerWithNotificationHubs** . Quindi aggiungere le seguenti righe che inizializzare un'istanza della classe **notifiche** . 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`e `HubListenConnectionString` deve essere già impostata con la `<hub name>` e `<connection string with listen access>` segnaposto con il nome di hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuto in precedenza.

    > [AZURE.NOTE] Poiché credenziali distribuiti con un'applicazione client non sono in genere sicura, è necessario distribuire solo chiave per ascoltare l'accesso con l'applicazione client. Ascoltare consente l'accesso non è possibile modificare l'app per eseguire la registrazione per le notifiche, ma le registrazioni esistente e non è possibile inviare notifiche. Accesso completo usata in un servizio di back-end protetta per l'invio di notifiche e modificare le registrazioni esistenti.


6. Aggiungere quindi seguenti importazioni e `subscribe` metodo per gestire il pulsante Sottoscrivi fare clic su evento:
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Questo metodo consente di creare un elenco delle categorie e viene utilizzata la classe **le notifiche** per archiviare l'elenco nell'archivio locale e registrare il tag corrispondente con l'hub di notifica. Quando si modificano le categorie, la registrazione verrà ricreata con le nuove categorie.

L'app sarà in grado di archiviare un insieme di categorie in un archivio locale nel dispositivo e registrare con l'hub di notifica ogni volta che l'utente modifica la selezione delle categorie.

##<a name="register-for-notifications"></a>Eseguire la registrazione per le notifiche

Questa procedura registra con l'hub di notifica all'avvio utilizzando le categorie che sono state memorizzate nell'archivio locale.

> [AZURE.NOTE] Poiché registrationId assegnato da Google Cloud messaggistica (GCM) può cambiare in qualsiasi momento, è necessario registrare per le notifiche frequentemente evitare errori di notifica. In questo esempio Registra per la notifica ogni volta che l'avvio dell'applicazione. Per le app che vengono eseguite di frequente, più di una volta al giorno, è possibile probabilmente ignorare la registrazione per mantenere la larghezza di banda se meno di un giorno è trascorse registrazione precedente.


1. Aggiungere il codice seguente alla fine del metodo **onCreate** nella classe **MainActivity** :

        notifications.subscribeToCategories(notifications.retrieveCategories());

    In questo modo che ogni volta che avvia l'app recupera le categorie dall'archivio locale e le richieste di registeration per queste categorie. 

2. Quindi aggiornare il `onStart()` metodo del `MainActivity` per la classe come indicato di seguito:

    @Overrideprotetto void onStart() {super.onStart();      isVisible = vero;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    Aggiorna attività principale in base allo stato delle categorie salvate in precedenza.

L'app è stata completata e memorizzazione un gruppo di categorie nell'archivio locale dispositivo usato per eseguire la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione delle categorie. Si definisce quindi un back-end che possono inviare le notifiche di categoria per questa app.

##<a name="sending-tagged-notifications"></a>Invio di notifiche con tag

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Eseguire l'app e generare notifiche

1. Android Studio, creare l'app e avviare in un dispositivo o emulatore.

    Nota che l'app dell'interfaccia utente fornisce un set di interruttori che consente di scegliere le categorie per la sottoscrizione.

2. Attivare gli interruttori uno o più categorie, quindi fare clic su **Sottoscrivi**.

    L'app converte categorie selezionate in tag e le richieste di una nuova registrazione di dispositivo per i tag selezionati dall'hub notifica. Le categorie registrate vengono restituite e visualizzate in una notifica di tipo avviso popup.

4. Inviare una nuova notifica eseguendo l'app Console .NET.  In alternativa, è possibile inviare le notifiche di modello con tag tramite la scheda debug dell'hub di notifica nel [Portale classica Azure].

    Notifiche relative alle categorie selezionate vengono visualizzati come le notifiche di tipo avviso popup.

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione abbiamo appreso come trasmettere aggiornate in base alla categoria. Valutare la possibilità di completare una delle seguenti esercitazioni che illustrano altri scenari avanzati hub di notifica:

+ [Utilizzare gli hub di notifica trasmettere notizie localizzati]

    Informazioni su come espandere l'app di news importanti per abilitare l'invio di notifiche localizzate.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Utilizzare gli hub di notifica trasmettere notizie localizzati]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Portale classica Azure]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
