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

#<a name="how-to-integrate-engagement-on-android"></a>Come integrare impegno in Android

> [AZURE.SELECTOR]
- [Universale di Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Questa procedura descrive il modo più semplice attivare del coinvolgimento Analitica e il monitoraggio delle funzioni nell'applicazione Android.

> [AZURE.IMPORTANT] Il livello di Android API SDK minimo deve essere 10 o versione successiva (Android 2.3.3 o versioni successive).

La procedura seguente è sufficienti per il report del log necessari per calcolare tutte le statistiche relative agli utenti, sessioni, attività, anomalo e Technicals attiva. Report sui registri necessari per calcolare altre statistiche come eventi, gli errori e processi devono essere eseguiti manualmente tramite l'API di impegno (vedere [come utilizzare il coinvolgimento Mobile avanzate tagging API in un telefono Android](mobile-engagement-android-use-engagement-api.md) poiché queste statistiche sono dipende dall'applicazione.

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Incorporare la partecipazione attiva SDK e il servizio nel progetto Android

Scaricare il SDK Android da [qui](https://aka.ms/vq9mfn) Get `mobile-engagement-VERSION.jar` e renderli disponibili nel `libs` cartella del progetto Android (se non esiste ancora, creare la cartella librerie).

> [AZURE.IMPORTANT]
> Se si creazione pacchetto dell'applicazione con ProGuard, è necessario mantenere alcune classi. È possibile utilizzare il frammento di codice di configurazione seguenti:
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

Specificare la stringa di connessione coinvolgimento chiamando il metodo seguente nell'attività di pulsante di visualizzazione:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure.

-   Se manca, aggiungere le seguenti autorizzazioni Android (prima di `<application>` tag):

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Aggiungere la sezione seguente (tra il `<application>` e `</application>` tag):

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   Modifica `<Your application name>` in base al nome dell'applicazione.

> [AZURE.TIP] Il `android:label` attributo consente di scegliere il nome del servizio di impegno come apparirà agli utenti finali nella schermata "Che esegue servizi" del proprio telefono. È consigliabile impostare l'attributo su `"<Your application name>Service"` (ad esempio `"AcmeFunGameService"`).

Specificare la `android:process` attributo assicura che verrà eseguito il servizio di coinvolgimento nel proprio processo (impegno è in esecuzione lo stesso processo come applicazione renderà il thread principali/UI potenzialmente tempi).

> [AZURE.NOTE] Il codice inserire nel `Application.onCreate()` e altri callback applicazione verrà eseguita per i processi tutti dell'applicazione, incluso il servizio di impegno. Potrebbe avere effetti indesiderati (ad esempio le allocazioni di memoria non necessari e processo di impegno, ricevitori di trasmissione duplicati o servizi).

Se esegue l'override `Application.onCreate()`, è consigliabile per aggiungere il frammento di codice seguenti all'inizio del `Application.onCreate()` funzione:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

È possibile eseguire la stessa operazione per `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

È anche possibile estendere `EngagementApplication` invece di estensione `Application`: il callback `Application.onCreate()` esegue il controllo di processo e le chiamate perse `Application.onApplicationProcessCreate()` solo se il processo corrente non è quello che include il servizio di impegno, applicano le stesse regole per le altre richiamate.

##<a name="basic-reporting"></a>Report di base

### <a name="recommended-method-overload-your-activity-classes"></a>Metodo consigliato: overload il `Activity` classi

Per attivare il report di tutti i registri necessari affinché impegno per calcolare gli utenti, sessioni, attività, anomalo e le statistiche tecniche, è sufficiente apportare tutte le `*Activity` classi secondari ereditano corrispondente `Engagement*Activity` classi (ad esempio, se l'attività legacy estende `ListActivity`, effettuare estende `EngagementListActivity`).

**Senza impegno:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Con coinvolgimento:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] Quando si usa `EngagementListActivity` o `EngagementExpandableListActivity`, verificare che tutte le chiamate a `requestWindowFeature(...);` viene effettuato prima la chiamata a `super.onCreate(...);`, in caso contrario si verificherà un arresto anomalo.

È possibile trovare queste classi di `src` cartella e possibile copiarli nel progetto. Le classi sono inoltre **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternare metodo: chiamare `startActivity()` e `endActivity()` manualmente

Se non è possibile o non si desidera overload il `Activity` classi, è invece possibile inizio e fine delle attività chiamando `EngagementAgent`di metodi direttamente.

> [AZURE.IMPORTANT] Android SDK non chiama mai il `endActivity()` metodo, anche quando l'applicazione viene chiusa (Android, non chiudere le applicazioni sono effettivamente mai). Pertanto è *altamente* consigliati per chiamare il `startActivity()` metodo nel `onResume` callback di *tutte* le attività e la `endActivity()` metodo nel `onPause()` callback di *tutte* le attività. Questo è l'unico modo per assicurarsi che le sessioni non vengano perso. Se viene perso a una sessione, il servizio di impegno mai disconnessa dal back-end coinvolgimento (poiché il servizio rimane collegato, purché sia in attesa di una sessione).

Ecco un esempio:

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

In questo esempio molto simili a quelle di `EngagementActivity` classe e le relative varianti, il cui codice sorgente è fornito nel `src` cartella.

##<a name="test"></a>Test

Ora verificare l'integrazione eseguendo l'app per dispositivi mobili in un emulatore o dispositivo e verificare che consente di registrare una sessione nella scheda Monitor.

Nelle sezioni successive sono facoltative.

##<a name="location-reporting"></a>Posizione dei report

Se si vuole percorsi non sicuri per essere inserite, è necessario aggiungere alcune righe di configurazione (tra il `<application>` e `</application>` tag).

### <a name="lazy-area-location-reporting"></a>Area Prato posizione report

Area Prato percorso reporting consente di inviare una segnalazione del paese, l'area geografica e località associati ai dispositivi. Questo tipo di posizione reporting utilizza solo percorsi di rete (basati su ID di cella o WIFI). L'area del dispositivo viene segnalato una sola volta per sessione. Non è mai utilizzato il GPS e pertanto questo tipo di report di posizione è pochi (non di esprimere) relativo impatto sulla pila.

Aree segnalate vengono utilizzate per calcolare geografiche statistiche sugli utenti, sessioni, eventi e gli errori. Possono inoltre essere utilizzati come criterio in campagne di mano.

Per attivare il report percorso area lenta, è possibile eseguire in mediante la configurazione descritta in precedenza in questa procedura:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Inoltre, è necessario aggiungere l'autorizzazione seguente se mancanti:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Oppure è possibile continuare a usare ``ACCESS_FINE_LOCATION`` se è già usare nell'applicazione.

### <a name="real-time-location-reporting"></a>Posizione dei report in tempo reale

In tempo reale posizione reporting consente di inviare una segnalazione latitudine e longitudine associati ai dispositivi. Per impostazione predefinita, questo tipo di posizione reporting utilizza solo percorsi di rete (basati su ID di cella o WIFI) e la segnalazione è attiva solo quando l'applicazione è in esecuzione in primo piano (ad esempio durante una sessione).

Posizioni in tempo reale sono *non* utilizzati per calcolare statistiche. Lo scopo solo consiste nel consente l'uso di in tempo reale geografico-recinzione \<geofencing di gruppo di destinatari portata\> criterio nelle campagne di mano.

Per abilitare la posizione in tempo reale dei report, è possibile eseguire in mediante la configurazione descritta in precedenza in questa procedura:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Inoltre, è necessario aggiungere l'autorizzazione seguente se mancanti:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Oppure è possibile continuare a usare ``ACCESS_FINE_LOCATION`` se è già usare nell'applicazione.

#### <a name="gps-based-reporting"></a>Creazione di report in base a GPS

Per impostazione predefinita, in tempo reale posizione reporting utilizza solo percorsi di rete in base a. Per abilitare l'utilizzo dei percorsi di GPS in base a (che sono molto più preciso), utilizzare l'oggetto di configurazione:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Inoltre, è necessario aggiungere l'autorizzazione seguente se mancanti:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Creazione di report di sfondo

Per impostazione predefinita, in tempo reale posizione reporting è attivo solo quando l'applicazione è in esecuzione in primo piano (ad esempio durante una sessione). Per abilitare la segnalazione anche in background, utilizzare l'oggetto di configurazione:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quando l'applicazione viene eseguita in background, solo in percorsi di rete vengono segnalati, anche se è abilitata la GPS.

Report di posizione sfondo verranno interrotti se l'utente si riavvia il dispositivo, è possibile aggiungere questo è possibile mettere riavviare automaticamente al momento dell'avvio:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Inoltre, è necessario aggiungere l'autorizzazione seguente se mancanti:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Autorizzazioni M Android

A partire da M Android, alcune autorizzazioni sono gestiti in fase di esecuzione e richiede l'approvazione di utente.

Le autorizzazioni di runtime verranno disattivate per impostazione predefinita per le nuove installazioni app se si imposta il livello di API Android 23. In caso contrario verranno essere attivato per impostazione predefinita.

L'utente può abilitare/disabilitare le autorizzazioni dal menu Impostazioni dispositivo. Disattivare le autorizzazioni dal menu di sistema elimina i processi di sfondo dell'applicazione, questa è un'operazione di sistema e non influisce sulla possibilità di ricevere push sullo sfondo.

Nel contesto di impegno Mobile, le autorizzazioni che richiedono l'approvazione in fase di esecuzione sono:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`(solo quando l'assegnazione del livello di API Android 23 per questa occorrenza)

Lo spazio di archiviazione esterno verrà usata solo per funzionalità di grande portata. Se si trovano chiedere agli utenti di questa autorizzazione per essere dannosi, è possibile rimuoverlo se è stato usato solo per la partecipazione attiva Mobile ma comporta disabilitazione della caratteristica quadro generale.

Per le caratteristiche di posizione, è consigliabile richiedere autorizzazioni per l'utente che usa una finestra di dialogo di sistema standard. Se l'utente approva, è necessario specificare ``EngagementAgent`` per scrivere la modifica in considerazione in tempo reale (in caso contrario le modifiche verranno elaborate la volta successiva che si avvia l'applicazione).

Ecco un esempio di codice per utilizzare un'attività dell'applicazione per richiedere le autorizzazioni e inoltrare il risultato se un numero positivo per ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>Segnalazione avanzate

Facoltativamente, se si desidera segnalare eventi specifici dell'applicazione, errori e processi, è necessario utilizzare l'API coinvolgimento mediante i metodi del `EngagementAgent` classe. Un oggetto di questa classe può essere recuperati chiamando il `EngagementAgent.getInstance()` metodo statico.

API coinvolgimento consente di utilizzare tutte le funzionalità avanzate di impegno e dettaglio nelle come usare l'API coinvolgimento Android (nonché come nella documentazione tecnica del `EngagementAgent` classe).

##<a name="advanced-configuration-in-androidmanifestxml"></a>Configurazione avanzata (in AndroidManifest.xml)

### <a name="wake-locks"></a>Riattivare blocchi

Se si desidera assicurarsi che le statistiche vengono inviate in tempo reale quando si usa Wifi o quando lo schermo è disattivata, aggiungere l'autorizzazione facoltativa seguente:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Segnalazione di arresto anomalo

Se si desidera disattivare un arresto anomalo report, aggiungere questo (tra il `<application>` e `</application>` tag):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Soglia burst

Per impostazione predefinita, i report del servizio coinvolgimento registra in tempo reale. Se l'applicazione dei registri molto spesso, è consigliabile per i registri di buffer e per inviare una segnalazione tutte contemporaneamente su un orario base (questo comando si chiama "modalità burst"). A tale scopo, aggiungere (tra il `<application>` e `</application>` tag):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

La modalità burst leggermente prolungare l'autonomia ma ha un impatto sul Monitor coinvolgimento: tutte le sessioni e processi la durata verrà arrotondato alla soglia burst (in questo modo, sessioni e processi inferiore soglia burst potrebbe non essere visibile). È consigliabile usare una soglia burst non è più di 30000 (30s).

### <a name="session-timeout"></a>Timeout di sessione

Per impostazione predefinita, una sessione è terminato 10s dopo la fine della propria attività ultimo (che in genere si verifica premendo il tasto Home o indietro, impostare il telefono inattivo oppure passare in un'altra applicazione). Questa operazione consiste nell'evitare una divisione di sessione ogni ora uscita utente e tornare all'applicazione molto rapidamente (che possa verificarsi quando ha sollevare un'immagine, selezionare una notifica e così via.). Può essere necessario modificare il parametro. A tale scopo, aggiungere (tra il `<application>` e `</application>` tag):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>Disattivare la segnalazione di log

### <a name="using-a-method-call"></a>Eseguendo una chiamata di metodo

Se si vuole impegno per interrompere l'invio dei registri, è possibile chiamare:

            EngagementAgent.getInstance(context).setEnabled(false);

Questa chiamata è persistente: utilizza un file condiviso preferenze.

Se coinvolgimento attivo quando si chiama questa funzione, potrebbe richiedere un minuto interrompere il servizio. Tuttavia, non è possibile avviare il servizio di tutto la volta successiva che si avvia l'applicazione.

È possibile attivare log reporting nuovamente chiamando la stessa funzione con `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integrazione nella propria`PreferenceActivity`

Anziché la chiamata a questa funzione, è possibile integrare questa impostazione direttamente in esistenti `PreferenceActivity`.

È possibile configurare coinvolgimento di utilizzare il file delle preferenze (con la modalità desiderata) la `AndroidManifest.xml` file con `application meta-data`:

-   Il `engagement:agent:settings:name` chiave consente di definire il nome del file delle preferenze condiviso.
-   Il `engagement:agent:settings:mode` chiave viene utilizzata per definire la modalità di file delle preferenze condivisa, è necessario utilizzare la stessa modalità il `PreferenceActivity`. La modalità deve essere passata come un numero: se si utilizza una combinazione di flag costante nel codice, controllare il valore totale.

Coinvolgimento sempre utilizzare la `engagement:key` booleana chiave all'interno il file delle preferenze per la gestione di questa impostazione.

Nell'esempio seguente di `AndroidManifest.xml` Mostra i valori predefiniti:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

È possibile aggiungere un `CheckBoxPreference` nel layout preferenza come quella riportata di seguito:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
