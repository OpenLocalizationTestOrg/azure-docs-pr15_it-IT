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
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>Note sulla versione

## <a name="423-08102016"></a>4.2.3 (08/10/2016)

- Nessun blocco WIFI altre.
- Risolvere un blocco critico quando si chiama getDeviceId prima inizializzazione (bug introdotti 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)

- Stabilità.

## <a name="421-05102016"></a>4.2.1 (10/05/2016)

- Sicurezza: disattivare l'accesso ai file locali di visualizzazione web.
- Sicurezza: rimozione `EngagementPreferenceActivity` classe che estende obsolete e non protetta `PreferenceActivity` classe.
- Sicurezza: portata attività sono descritte ora per l'utilizzo di `exported="false"`, questo flag può anche essere utilizzato nelle versioni precedenti di SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)

- il SDK a questo punto è concesso in licenza in MIT.
- Consente di specificare l'identificatore dispositivo personalizzati in fase di inizializzazione SDK.

## <a name="415-02012016"></a>4.1.5 (01/02/2016)

- Stabilità.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)

- Stabilità.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)

- Stabilità.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)

- Stabilità.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)

- Stabilità.

## <a name="410-08252015"></a>4.1.0 (08/25/2015)

- Gestire nuovo modello di autorizzazione per Android M.
- Ora è possibile configurare le funzionalità di posizione in fase di esecuzione invece di usare `AndroidManifest.xml`.
- Correggere un errore di autorizzazione: se si usa `ACCESS_FINE_LOCATION`, quindi `ACCESS_COARSE_LOCATION` non è più necessaria.
- Stabilità.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)

-   Modifiche protocollo interno per rendere più affidabile analitica e push.
-   Push nativi (GCM/ADM) viene ora usato anche per le notifiche di app in modo che è necessario configurare le credenziali push nativi per qualsiasi tipo di campagna push.
-   Correggere notifica quadro generale: sono stati visualizzati 10s solo dopo che viene inserito.
-   Correggere un errore nella visualizzazione web: facendo clic sul collegamento durante l'esecuzione anche l'URL di azione predefiniti.
-   Correggere un arresto anomalo raro correlato alla gestione degli archivi locale.
-   Correggere la gestione di stringhe configurazione dinamica.
-   Aggiornare contratto di licenza.

## <a name="300-02172015"></a>3.0.0 (17/02/2015)

-   Versione iniziale di Azure impegno per dispositivi mobili
-   configurazione di appId viene sostituita da una configurazione della stringa di connessione.
-   Rimuovere API per inviare e ricevere messaggi di azienda non autorizzati da entità azienda non autorizzata.
-   Rimuovere API per inviare e ricevere messaggi tra i dispositivi.
-   Miglioramenti della protezione.
-   Verifica di Google Play e SmartAd rimossi.
