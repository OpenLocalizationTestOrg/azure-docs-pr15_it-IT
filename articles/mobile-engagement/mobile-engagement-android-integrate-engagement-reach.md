<properties
    pageTitle="Integrazione di Android SDK Azure impegno per dispositivi mobili"
    description="Ultimi aggiornamenti e le procedure per Android SDK per Azure Mobile coinvolgimento"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-reach-on-android"></a>Come integrare impegno portata in Android

> [AZURE.IMPORTANT] È necessario seguire la procedura di integrazione descritta in come integrare impegno Android documento prima di iniziare questa Guida.

##<a name="standard-integration"></a>Integrazione standard

SDK raggiunga richiede la **libreria di supporto Android (v4)**.

Il modo più rapido per aggiungere la libreria per un progetto in **Eclisse** `Right click on your project -> Android Tools -> Add Support Library...`.

Se non si usa Eclisse, è possibile leggere le istruzioni riportate [di seguito].

Copiare il file di risorse portata SDK del progetto:

-   Copiare i file di `res/layout` cartella forniti con SDK nel `res/layout` cartella dell'applicazione.
-   Copiare i file di `res/drawable` cartella forniti con SDK nel `res/drawable` cartella dell'applicazione.

Modificare il `AndroidManifest.xml` file:

-   Aggiungere la sezione seguente (tra il `<application>` e `</application>` tag):

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
              <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
              </intent-filter>
            </receiver>

-   È necessario questa autorizzazione per riprodurre le notifiche di sistema non sono stato fatto clic all'avvio (in caso contrario verranno mantenuti nel disco, ma non viene visualizzati più, è importante includere questo).

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Specificare un'icona utilizzata per le notifiche (entrambi in app e sistema quelli) copiando e modificando la sezione seguente (tra il `<application>` e `</application>` tag):

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] In questa sezione è **obbligatorio** se si prevede di utilizzare le notifiche di sistema durante la creazione di campagne di mano. Android impedisce che le notifiche di sistema senza icone da visualizzare. Pertanto se si omette in questa sezione, gli utenti finali non sarà possibile riceverle.

-   Se si creano campagne con le notifiche di sistema tramite ampio, è necessario aggiungere le seguenti autorizzazioni (dopo il `</application>` tag) se mancanti:

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   M Android e se l'applicazione è destinato a livello di API Android 23 o versione successiva, ``WRITE_EXTERNAL_STORAGE`` autorizzazione richiede l'approvazione dell'utente. In [questa sezione](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   Per le notifiche di sistema è anche possibile specificare la campagna portata se il dispositivo deve squillare e/o vibrazione. Perché il lavoro, è necessario assicurarsi che sia dichiarato l'autorizzazione seguente (dopo il `</application>` tag):

            <uses-permission android:name="android.permission.VIBRATE" />

    Senza questa autorizzazione Android impedisce notifiche di sistema che viene visualizzato se è selezionata l'opzione vibrate in Gestione raggiunga campagna o inoltrare la chiamata.

-   Se si compila un'applicazione tramite **ProGuard** e gli errori relativi alla libreria di supporto Android o vaso coinvolgimento, aggiungere le seguenti righe per il `proguard.cfg` file:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Push nativo

Ora che la configurazione di mano modulo, è necessario configurare nativo push per essere in grado di ricevere le campagne sul dispositivo.

Sono supportate due servizi in Android:

  - Dispositivi di Google Play: usare [Google Cloud messaggistica] seguendo la Guida [come integrare GCM con Guida coinvolgimento](mobile-engagement-android-gcm-integrate.md) .
  - Dispositivi di Amazon: usare [La messaggistica dispositivo Amazon] seguendo la Guida [come integrare ADM con Guida coinvolgimento](mobile-engagement-android-adm-integrate.md) .

Se si desidera Amazon e Google Play dispositivi, è possibile disporre tutto il contenuto 1 AndroidManifest.xml/APK per lo sviluppo di destinazione. Ma quando si inviano ad Amazon, potrebbe rifiutare l'applicazione se si trova il codice GCM.

In questo caso, è necessario utilizzare più APKs.

**L'applicazione è ora possibile ricevere e visualizzare le campagne di mano!**

##<a name="how-to-handle-data-push"></a>In che modo gestire push dei dati

### <a name="integration"></a>Integrazione

Se si desidera all'applicazione di essere in grado di ricevere portata push di dati, è necessario creare una classe secondari di `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` e farvi riferimento nel `AndroidManifest.xml` file (tra il `<application>` e/o `</application>` tag):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

È possibile eseguire l'override di `onDataPushStringReceived` e `onDataPushBase64Received` callback. Ecco un esempio:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Categoria

Il parametro di categoria è facoltativo quando si crea una campagna Push di dati e consente di filtrare i dati inserisce. Questo è utile se si dispone di più ricevitori trasmissione gestisce diversi tipi di dati inserimenti, o se si desidera inviare diversi tipi di `Base64` dati e si desidera identificare il tipo di prima dell'analisi loro.

### <a name="callbacks-return-parameter"></a>Parametro restituito degli callback

Ecco alcune linee guida per gestire in modo appropriato il parametro restituito `onDataPushStringReceived` e `onDataPushBase64Received`:

-   Deve restituire un ricevitore trasmissione `null` nel callback se che non è in grado di gestire un push di dati. Utilizzare la categoria per determinare se il ricevitore trasmissione dovranno essere gestite push dei dati o meno.
-   Una trasmissione ricevitore deve restituire `true` nel callback se accetta push dei dati.
-   Una trasmissione ricevitore deve restituire `false` nel callback se riconosce push dei dati, ma ignora per qualsiasi motivo. Ad esempio, `false` quando i dati ricevuti non sono validi.
-   Se una trasmissione ricevitore restituisce `true` mentre un altro uno restituisce `false` per l'inserimento di dati stesso, il comportamento è definito, è non necessario farlo mai.

Il tipo restituito verrà usato solo per le statistiche di mano:

-   `Replied`viene incrementato se uno di essi trasmissione restituito uno `true` o `false`.
-   `Actioned`viene incrementato solo se uno di essi trasmissione restituito `true`.

##<a name="how-to-customize-campaigns"></a>Come personalizzare campagne

Per personalizzare le campagne, è possibile modificare i layout disponibili nel SDK di raggiungere.

È necessario mantenere tutti gli identificatori utilizzati nei layout di tipo e organizzare i tipi di visualizzazioni che utilizzano un identificatore, in particolare per visualizzazioni di testo e immagine. Alcune visualizzazioni appena vengono utilizzati per visualizzare o nascondere le aree in modo che il tipo di può essere modificato. Se si desidera modificare il tipo di una visualizzazione in layout disponibili, controllare il codice sorgente.

### <a name="notifications"></a>Notifiche

Esistono due tipi di notifiche: le notifiche di sistema e in app che utilizzano i file di layout diverso.

#### <a name="system-notifications"></a>Notifiche di sistema

Per personalizzare le notifiche di sistema che è necessario utilizzare le **categorie**. È possibile passare alle [categorie](#categories).

#### <a name="in-app-notifications"></a>Notifiche di app

Per impostazione predefinita, una notifica nell'app è una visualizzazione in modo dinamico viene aggiunto all'interfaccia utente di attività corrente ringraziamento metodo Android `addContentView()`. Questo comando si chiama una sovrapposizione di notifica. Sovrapposizioni di notifica sono ideali per una rapida integrazione perché non richiedono è possibile modificare un layout dell'applicazione.

Per modificare l'aspetto delle sovrapposizioni di notifica, è sufficiente modificare il file `engagement_notification_area.xml` alle proprie esigenze.

> [AZURE.NOTE] Il file `engagement_notification_overlay.xml` è quello che viene utilizzato per creare una sovrapposizione di notifica, che include il file `engagement_notification_area.xml`. È anche possibile personalizzarlo in base alle esigenze (ad esempio per l'area di notifica all'interno di sovrapposizione di posizionamento).

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Includere layout notifica come parte di un layout di attività

Sovrapposizioni sono ideali per un'integrazione veloce ma possono risultare poco o lato effetti in casi particolari. È possibile personalizzare il sistema di sovrapposizione a un livello di attività, rendendo più semplice evitare che gli effetti di lato per le attività speciali.

È possibile includere il layout di notifica in un layout esistente ringraziamento istruzione Android **includere** . Nell'esempio è illustrato un esempio di un oggetto modificato `ListActivity` layout contenente solo un `ListView`.

**Prima di integrazione con coinvolgimento:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Dopo l'integrazione di impegno:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

In questo esempio abbiamo aggiunto un contenitore padre poiché il layout originale utilizzata una visualizzazione elenco come elemento di livello principale. Abbiamo aggiunto anche `android:layout_weight="1"` la possibilità di aggiungere una vista sotto una visualizzazione elenco configurata con `android:layout_height="fill_parent"`.

L'impegno raggiunga SDK rileva automaticamente che il layout di notifica inclusi in questa attività e non aggiungerà una sovrapposizione per l'attività.

> [AZURE.TIP] Se si usa un ListActivity nell'applicazione, una sovrapposizione portata visibile sarà possibile reagendo al fatto clic più elementi nella visualizzazione elenco. Si tratta di un problema noto. Per risolvere questo problema si consiglia di per incorporare il layout di notifica nel proprio elenco attività layout come nell'esempio precedente.

##### <a name="disabling-application-notification-per-activity"></a>Disattivazione di notifica di applicazione per attività

Se non si vuole sovrapposizione da aggiungere alla propria attività e se non si specifica il layout di notifica in un layout personalizzato, è possibile disattivare la sovrapposizione per l'attività in di `AndroidManifest.xml` mediante l'aggiunta di un `meta-data` sezione come nell'esempio seguente:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Categorie

Quando si modificano i layout disponibili, modificare l'aspetto di tutte le notifiche. Categorie consentono di definire diversi aspetti target (eventualmente comportamenti) per le notifiche. Quando si crea una campagna di mano, è possibile specificare una categoria. Tenere presente che le categorie consentono inoltre di personalizzare gli annunci e sondaggi, descritto più avanti in questo documento.

Per registrare un gestore di categoria per le notifiche, è necessario aggiungere una chiamata quando l'applicazione viene inizializzato.

> [AZURE.IMPORTANT] Leggere il messaggio di avviso sull'attributo android: processo \<processo coinvolgimento di sdk android\> in come integrare impegno su Android argomento prima di continuare.

Nell'esempio seguente presuppone riconosciuto avviso precedente e utilizzare una classe secondari di `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

Il `MyNotifier` oggetto è l'implementazione del gestore categoria notifica. È l'implementazione del `EngagementNotifier` interfaccia o una classe sub dell'implementazione predefinita: `EngagementDefaultNotifier`.

Si noti che notifica stessa può gestire varie categorie, è possibile registrare come segue:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Per sostituire l'implementazione di categoria predefinita, è possibile registrare l'implementazione come nell'esempio seguente:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

La categoria corrente utilizzata in un gestore viene passata come parametro nella maggior parte dei metodi è possibile ignorare in `EngagementDefaultNotifier`.

Viene passato come un `String` parametro o indirettamente in una `EngagementReachContent` oggetto che ha un `getCategory()` metodo.

È possibile modificare la maggior parte del processo di creazione notifica ridefinendo metodi su `EngagementDefaultNotifier`, per la personalizzazione più avanzata è possibile consultare la documentazione tecnica e il codice sorgente.

##### <a name="in-app-notifications"></a>Notifiche di app

Se si desidera utilizzare layout alternativi per una specifica categoria, è possibile implementare come illustrato nell'esempio seguente:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Esempio di `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Come si può notare, l'identificatore di visualizzazione di sovrapposizione è diverso da quello standard. È importante che ogni layout utilizzare un nome univoco per sovrapposizioni.

**Esempio di `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Come si può notare, identificatore visualizzazione area di notifica è diverso da quello standard. È importante che ogni layout utilizza un identificatore univoco relativi alle aree di notifica.

Questo semplice esempio categoria rende le notifiche di applicazione (o in app) visualizzate nella parte superiore dello schermo. Non è stato identificatori standard usati nell'area di notifica stessa.

Se si desidera modificare questa impostazione, è necessario ridefinire la `EngagementDefaultNotifier.prepareInAppArea` metodo. È consigliabile per esaminare la documentazione tecnica e il codice sorgente della `EngagementNotifier` e `EngagementDefaultNotifier` se si desidera che questo livello di personalizzazione avanzata.

##### <a name="system-notifications"></a>Notifiche di sistema

Aumentando `EngagementDefaultNotifier`, è possibile ignorare `onNotificationPrepared` per modificare la notifica che è stata preparata con l'implementazione predefinita.

Per esempio:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Questo esempio formatta una notifica di sistema per un contenuto viene visualizzato come un evento in corso quando viene utilizzata la categoria "in corso".

Se si desidera creare il `Notification` oggetto da zero, è possibile tornare `false` al metodo e chiamare `notify` personalmente nel `NotificationManager`. In questo caso è importante mantenere un `contentIntent`, `deleteIntent` e l'identificatore di notifica utilizzato da `EngagementReachReceiver`.

Ecco un esempio corretto di tale implementazione:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Annunci solo notifica

La gestione del, fare clic su una notifica solo annuncio può essere personalizzato mediante l'override `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` per modificare il preparato `Intent`. Con questo metodo consente di ottimizzare facilmente i contrassegni.

Ad esempio per aggiungere il `SINGLE_TOP` contrassegno:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Per gli utenti coinvolgimento legacy, tenere presente che le notifiche di sistema senza azione URL ora avvia l'applicazione se è stato in background, pertanto è possibile chiamare questo metodo con un annuncio senza URL di azione. È consigliabile che durante la personalizzazione lo scopo.

È anche possibile implementare `EngagementNotifier.executeNotifAnnouncementAction` da zero.

##### <a name="notification-life-cycle"></a>Ciclo di vita di notifica

Quando si usa la categoria predefinita, alcuni metodi di ciclo di vita sono invitati la `EngagementReachInteractiveContent` oggetto report statistiche e aggiornare lo stato della campagna:

-   Quando la notifica viene visualizzata nell'applicazione o inserire nella barra di stato, il `displayNotification` metodo (che vengono rilevate le statistiche) da `EngagementReachAgent` se `handleNotification` restituisce `true`.
-   Se la notifica viene chiusa, il `exitNotification` chiamato, la segnalazione di statistica e ora è possibile elaborare campagne successive.
-   Se si fa clic sul messaggio di notifica, `actionNotification` è chiamato, viene segnalato statistico e lo scopo associato viene avviato.

Se l'implementazione di `EngagementNotifier` consente di ignorare il comportamento predefinito, è necessario chiamare questi metodi di ciclo di vita per se stessi. Negli esempi seguenti sono disponibili alcuni casi in cui viene ignorato il comportamento predefinito:

-   Non estendere `EngagementDefaultNotifier`, ad esempio è implementata la gestione delle categorie da zero.
-   Per le notifiche di sistema, overrode il `onNotificationPrepared` e modificata `contentIntent` o `deleteIntent` nel `Notification` oggetto.
-   Per le notifiche nell'app, overrode `prepareInAppArea`, assicurarsi di eseguire il mapping almeno `actionNotification` a uno del U.I controlli.

> [AZURE.NOTE] Se `handleNotification` genera un'eccezione, il contenuto viene eliminata e `dropContent` si chiama. Viene riportato nelle statistiche e ora è possibile elaborare campagne successive.

### <a name="announcements-and-polls"></a>Annunci e sondaggi

#### <a name="layouts"></a>Layout

È possibile modificare il `engagement_text_announcement.xml`, `engagement_web_announcement.xml` e `engagement_poll.xml` file per personalizzare gli annunci di tipo testo, annunci web e sondaggi.

Questi file condividono due layout comuni per l'area del titolo e nell'area del pulsante. Layout per il titolo `engagement_content_title.xml` e utilizza il file drawable eponymous per lo sfondo. Layout per i pulsanti di azione e di uscita `engagement_button_bar.xml` e utilizza il file drawable eponymous per lo sfondo.

In un sondaggio, il layout della domanda e le relative scelte sono in modo dinamico ingrandite usando più volte la `engagement_question.xml` file di layout per le domande e le `engagement_choice.xml` file per le opzioni.

#### <a name="categories"></a>Categorie

##### <a name="alternate-layouts"></a>Layout alternativi

Ad esempio le notifiche, categoria della campagna può essere utilizzata per layout alternativi per gli annunci e sondaggi.

Per creare una categoria per un annuncio di testo, ad esempio, è possibile estendere `EngagementTextAnnouncementActivity` e farvi riferimento il `AndroidManifest.xml` file:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Si noti che la categoria nella casella Tipo di filtro consente di creare la differenza tra le attività di annuncio predefinita.

SDK raggiunga utilizza il sistema intento per risolvere l'attività giusto per una specifica categoria e ritorna nella categoria predefinita se non è riuscita la risoluzione.

Quindi è necessario implementare `MyCustomTextAnnouncementActivity`, se si vuole semplicemente cambiare il layout (ma mantenere gli stessi identificatori di visualizzazione), è sufficiente definire la classe come nell'esempio seguente:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Per sostituire la categoria predefinita degli annunci di tipo testo, è sufficiente sostituire `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` dall'implementazione in uso.

Sondaggi e gli annunci web possono essere personalizzati in modo simile.

Per informazioni sugli annunci web è possibile estendere `EngagementWebAnnouncementActivity` e dichiarare le attività di `AndroidManifest.xml` come nell'esempio seguente:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Per sondaggi è possibile estendere `EngagementPollActivity` e dichiarare il nel `AndroidManifest.xml` come nell'esempio seguente:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementazione da zero

È possibile implementare categorie per le attività annuncio (e sondaggio) senza estendere una del `Engagement*Activity` classi fornite da SDK raggiungere. Questo è utile ad esempio se si desidera definire un layout che non utilizza le stesse visualizzazioni come i layout standard.

Ad esempio per la personalizzazione di notifiche avanzate, è consigliabile esaminare il codice sorgente dell'implementazione standard.

Ecco alcuni aspetti da tenere presenti: portata verrà avviato l'attività con un intento specifico (corrispondente al filtro intento) e un parametro aggiuntivo che è l'identificatore di contenuto.

Per recuperare l'oggetto contenuto che contengono i campi specificato quando si crea la campagna sul sito web è possibile procedere come segue:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Per le statistiche, sarà necessario segnalare il contenuto viene visualizzato nel `onResume` evento:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Quindi, non dimenticare di chiamare `actionContent(this)` o `exitContent(this)` sull'oggetto contenuto prima che l'attività entra in background.

Se si non chiama `actionContent` o `exitContent`, statistiche non verrà inviate (ad esempio non analitica della campagna) e più importante, campagne successive utilizzate non si riceveranno fino a quando non si riavvia il processo di applicazione.

Orientamento o altre modifiche di configurazione possono effettuare il codice difficile determinare se l'attività entra in background o meno, l'implementazione standard verifica che il contenuto viene segnalato come usciti se l'utente lascia l'attività (premendo `HOME` o `BACK`), ma non se cambia l'orientamento.

Ecco la parte interessante dell'implementazione:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Come si può notare, se è stato chiamato `actionContent(this)` quindi completata l'attività, `exitContent(this)` può essere chiamato senza alcun effetto.

[qui]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud messaggistica]:http://developer.android.com/guide/google/gcm/index.html
[Messaggistica dispositivo Amazon]:https://developer.amazon.com/sdk/adm.html
