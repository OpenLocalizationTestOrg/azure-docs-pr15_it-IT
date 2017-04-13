<properties
    pageTitle="Configurazione avanzata per Azure Mobile coinvolgimento Android SDK"
    description="Descrive le opzioni di configurazione avanzate incluse manifesto Android con Azure Mobile coinvolgimento Android SDK"
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
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configurazione avanzata per Azure Mobile coinvolgimento Android SDK

> [AZURE.SELECTOR]
- [Windows universale](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Questa procedura viene descritto come configurare le opzioni di configurazione del App Azure Mobile coinvolgimento Android.

## <a name="prerequisites"></a>Prerequisiti

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Requisiti di autorizzazione
Alcune opzioni richiedono autorizzazioni specifiche, ognuno dei quali sono elencati di seguito per riferimento e in linea nella funzionalità specifiche. Aggiungere queste autorizzazioni AndroidManifest.xml del progetto immediatamente prima o dopo il `<application>` tag.

È necessario un aspetto simile al seguente, in cui è compilare le autorizzazioni appropriate della tabella che segue il codice di autorizzazione.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Autorizzazione | Quando utilizzata |
| ---------- | --------- |
| INTERNET | Obbligatorio. Per i report di base |
| ACCESS_NETWORK_STATE | Obbligatorio. Per i report di base |
| RECEIVE_BOOT_COMPLETED | Obbligatorio. Per visualizzare il centro notifiche dopo il riavvio di dispositivo |
| WAKE_LOCK | È consigliabile. Abilita la raccolta dei dati quando tramite WiFi o dello schermo è disattivata |
| VIBRAZIONE | Facoltativo. Abilita vibrazione quando vengono ricevute notifiche |
| DOWNLOAD_WITHOUT_NOTIFICATION | Facoltativo. Attiva la notifica di Android quadro generale |
| WRITE_EXTERNAL_STORAGE | Facoltativo. Attiva la notifica di Android quadro generale |
| ACCESS_COARSE_LOCATION | Facoltativo. Consente di creare report di destinazione in tempo reale |
| ACCESS_FINE_LOCATION | Facoltativo. Consente di creare report basati su GPS posizione |

A partire da Android M, [alcune autorizzazioni sono gestite in fase di esecuzione](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Se si sta già utilizzando ``ACCESS_FINE_LOCATION``, quindi non è necessario utilizzare anche ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Opzioni di configurazione manifesto Android

### <a name="crash-report"></a>Segnalazione di arresto anomalo

Per disattivare un arresto anomalo report, aggiungere il codice tra le `<application>` e `</application>` tag:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Soglia burst

Per impostazione predefinita, i report del servizio coinvolgimento registra in tempo reale. Se i registri di report applicazione variano spesso, è consigliabile buffer i log e inviare una segnalazione visualizzarle tutte contemporaneamente su un orario base (denominato "modalità burst"). A tale scopo, aggiungere il codice tra le `<application>` e `</application>` tag:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Modalità burst leggermente aumenta l'autonomia ma ha un impatto sul Monitor coinvolgimento: durata tutte le sessioni e processi vengono arrotondati per la soglia burst (in questo modo, sessioni e processi inferiore soglia burst potrebbe non essere visibile). Soglia del burst deve contenere non più di 30000 (30s).

### <a name="session-timeout"></a>Timeout di sessione

 È possibile terminare un'attività premendo il tasto **Home** o **all'indietro** , tramite telefono inattivo o del passaggio in un'altra applicazione. Per impostazione predefinita, una sessione dieci secondi dopo la fine della propria attività ultima. In questo modo una divisione sessione ogni volta che l'utente si chiude e restituisce all'applicazione rapidamente, che possono verificarsi quando l'utente sceglie l'immagine, verifica una notifica e così via. Può essere necessario modificare il parametro. A tale scopo, aggiungere il codice tra le `<application>` e `</application>` tag:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Disattivare la segnalazione di log

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
-   Il `engagement:agent:settings:mode` chiave viene utilizzata per definire la modalità di file delle preferenze condiviso. Utilizzare la stessa modalità il `PreferenceActivity`. La modalità deve essere passata come un numero: se si utilizza una combinazione di flag costante nel codice, controllare il valore totale.

Usa sempre coinvolgimento di `engagement:key` booleana chiave all'interno il file delle preferenze per la gestione di questa impostazione.

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
