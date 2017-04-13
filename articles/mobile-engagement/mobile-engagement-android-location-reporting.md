<properties
    pageTitle="Posizione dei report per SDK Android Azure impegno per dispositivi mobili"
    description="Descrive come configurare l'indirizzo dei report per Azure Mobile coinvolgimento Android SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Posizione dei report per SDK Android Azure impegno per dispositivi mobili

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

In questo argomento viene descritto come eseguire posizione segnalazione per un'applicazione Android.

## <a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Posizione dei report

Se si vuole percorsi non sicuri per essere inserite, è necessario aggiungere alcune righe di configurazione (tra il `<application>` e `</application>` tag).

### <a name="lazy-area-location-reporting"></a>Area Prato posizione report

Area Prato posizione report consente di creare report il paese, l'area geografica e località associato con i dispositivi. Questo tipo di posizione reporting utilizza solo percorsi di rete (basati su ID di cella o WIFI). L'area del dispositivo viene segnalato una sola volta per sessione. Non è mai utilizzato il GPS e pertanto questo tipo di report di posizione influisce basso nella pila.

Aree segnalate vengono utilizzate per calcolare geografiche statistiche sugli utenti, sessioni, eventi e gli errori. Possono inoltre essere utilizzati come criterio in campagne di mano.

Attivare posizione dell'area Prato report utilizzando la configurazione descritta in precedenza in questa procedura:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

È anche necessario specificare un percorso di autorizzazione. Questo codice utilizza ``COARSE`` autorizzazione:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Se l'app lo richiede, è possibile utilizzare ``ACCESS_FINE_LOCATION`` invece.

### <a name="real-time-location-reporting"></a>Creazione di report di destinazione in tempo reale

Creazione di report di destinazione in tempo reale consente di creare report latitudine e longitudine associata con i dispositivi. Per impostazione predefinita, questo tipo di posizione reporting utilizza solo percorsi di rete, in base a ID di cella o WIFI. La creazione di report è attivo solo quando l'applicazione è in esecuzione in primo piano (ad esempio durante una sessione).

Posizioni in tempo reale sono *non* utilizzati per calcolare statistiche. Lo scopo solo consiste nel consente l'uso di recinzione di geografico in tempo reale \<geofencing di gruppo di destinatari portata\> criterio nelle campagne di mano.

Per abilitare la posizione in tempo reale dei report, aggiungere una riga di codice in cui si imposta la stringa di connessione impegno nell'attività di avvio. Il risultato è simile alla seguente:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>Creazione di report in base a GPS

Per impostazione predefinita, creazione di report di destinazione in tempo reale utilizza solo percorsi di rete. Per abilitare l'utilizzo delle posizioni in base GPS, sono molto più preciso, utilizzare l'oggetto di configurazione:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Inoltre, è necessario aggiungere l'autorizzazione seguente se mancanti:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Creazione di report di sfondo

Per impostazione predefinita, creazione di report di destinazione in tempo reale è attivo solo quando l'applicazione è in esecuzione in primo piano (ad esempio durante una sessione). Per abilitare la segnalazione anche in background, utilizzare l'oggetto configurazione:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quando l'applicazione viene eseguita in background, vengono segnalati solo percorsi di rete, anche se è abilitata la GPS.

Se l'utente si riavvia il dispositivo, report di posizione sfondo viene interrotta. È possibile mettere riavviare automaticamente al momento dell'avvio, aggiungere il codice.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Inoltre, è necessario aggiungere l'autorizzazione seguente se mancanti:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Autorizzazioni M Android

A partire da M Android, alcune autorizzazioni sono gestite in fase di esecuzione ed è necessario approvazione dell'utente.

Se si imposta il livello di API Android 23, le autorizzazioni di runtime sono disattivate per impostazione predefinita per le nuove installazioni app. In caso contrario sono attivate per impostazione predefinita.

È possibile attivare o disattivare le autorizzazioni dal menu Impostazioni dispositivo. Disattivare le autorizzazioni dal menu di sistema interrompe processi in background della domanda, che è un'operazione di sistema e non influisce sulla possibilità di ricevere push sullo sfondo.

Nel contesto della posizione Mobile coinvolgimento dei report, le autorizzazioni che richiedono l'approvazione in fase di esecuzione sono:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Richiedere le autorizzazioni da parte dell'utente con una finestra di dialogo di sistema standard. Se l'utente approva, informare ``EngagementAgent`` per prendere in considerazione per la modifica in tempo reale. La modifica in caso contrario viene elaborata la volta successiva che si avvia l'applicazione.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
