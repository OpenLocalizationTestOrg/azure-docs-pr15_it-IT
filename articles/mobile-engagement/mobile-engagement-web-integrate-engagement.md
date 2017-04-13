<properties
    pageTitle="Integrazione di Azure Mobile coinvolgimento Web SDK | Microsoft Azure"
    description="Gli ultimi aggiornamenti e procedure di Azure Mobile coinvolgimento Web SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrare Azure Mobile impegno in un'applicazione web

> [AZURE.SELECTOR]
- [Universale di Windows](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Nelle procedure in questo articolo viene descritto il modo più semplice per attivare l'analitica e funzioni di monitoraggio di Azure Mobile coinvolgimento nell'applicazione web.

Seguire la procedura per attivare i report del log necessari per calcolare tutte le statistiche sugli utenti, sessioni, attività, anomalo e technicals. Per le statistiche di dipendenti dall'applicazione, ad esempio gli eventi, gli errori e processi, è necessario attivare manualmente i report del log tramite l'API di impegno Azure Mobile. Per ulteriori informazioni, informazioni [su come utilizzare il coinvolgimento Mobile avanzate tagging API in un'applicazione web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introduzione

[Scaricare Azure coinvolgimento Mobile Web SDK](http://aka.ms/P7b453).
Mobile coinvolgimento Web SDK viene fornito come un singolo file JavaScript azure-engagement.js, è necessario includere in ogni pagina dell'applicazione web o del sito.

> [AZURE.IMPORTANT] Prima di eseguire questo script, è necessario eseguire frammento di codice o script scritto per configurare Mobile coinvolgimento dell'applicazione.

## <a name="browser-compatibility"></a>Compatibilità con i browser

Mobile coinvolgimento Web SDK utilizza nativo JSON codifica e decodifica, oltre alle richieste di AJAX domini (che si basa sulla specifica W3C CORS). È compatibile con browser seguenti:

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 anni
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Configurare impegno per dispositivi mobili

Creare uno script che crea amministratore globale `azureEngagement` oggetto JavaScript, come illustrato nell'esempio seguente. Poiché il sito potrebbe avere pagine multipli, si presuppone che lo script è disponibile in ogni pagina. In questo esempio, l'oggetto JavaScript è denominato `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

Il `connectionString` valore per l'applicazione viene visualizzata nel portale di Azure.

> [AZURE.NOTE] `appVersionName`e `appVersionCode` sono facoltativi. Tuttavia, è consigliabile configurarli in modo che analitica può elaborare le informazioni sulla versione.

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Includere Mobile coinvolgimento script nelle pagine
Aggiungere gli script di impegno Mobile alle pagine in uno dei modi seguenti:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

O questo:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias

Dopo il caricamento di script Mobile coinvolgimento Web SDK creato l'alias di **impegno** per accedere a API SDK. È possibile utilizzare l'alias per definire la configurazione di SDK. L'alias viene utilizzato come riferimento in questa documentazione.

Tenere presente che se l'alias predefinito in conflitto con un'altra variabile globale dalla pagina, è possibile ridefinire nella configurazione come indicato di seguito prima di caricare Mobile coinvolgimento Web SDK:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Report di base

Base reporting in Mobile coinvolgimento riguarda le statistiche di livello sessione, ad esempio statistiche sugli utenti, sessioni, attività e anomalo.

### <a name="session-tracking"></a>Tenere traccia della sessione

Una sessione di impegno Mobile è diviso in una sequenza di attività, ciascuna identificata da un nome.

In un sito Web classico, è consigliabile dichiarare un'altra attività in ogni pagina del sito. Per una sito Web o un'applicazione web in cui la pagina corrente non cambia mai, è consigliabile tenere traccia delle attività su scala ridotta, ad esempio all'interno della pagina.

In entrambi i casi, per avviare o modificare l'attività dell'utente corrente, chiamare il `engagement.agent.startActivity` funzione. Per esempio:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Il server Mobile coinvolgimento termina automaticamente una sessione aperta entro tre minuti dopo la chiusura della pagina dell'applicazione.

In alternativa, è possibile terminare una sessione manualmente chiamando `engagement.agent.endActivity`. Consente di impostare l'attività dell'utente corrente su 'Inattivo'.  La sessione terminerà 10 secondi in un secondo momento, a meno che non una nuova chiamata a `engagement.agent.startActivity` riprende la sessione.

È possibile configurare il ritardo di 10 secondi nell'oggetto impegno globale, come indicato di seguito:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] Non è possibile utilizzare `engagement.agent.endActivity` nel `onunload` callback perché non è possibile effettuare chiamate AJAX in questa fase.

## <a name="advanced-reporting"></a>Segnalazione avanzate

Facoltativamente, se si desidera segnalare processi, gli errori ed eventi specifiche di un'applicazione, è necessario utilizzare l'API di impegno Mobile. Accedere all'API di impegno Mobile tramite il `engagement.agent` oggetto.

È possibile accedere a tutte le funzionalità avanzate di impegno Mobile dell'API di impegno Mobile. L'API è illustrata nell'articolo [come usare il coinvolgimento Mobile avanzate tagging API in un'applicazione web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Personalizzare gli URL utilizzati per le chiamate AJAX

È possibile personalizzare gli URL che utilizza Mobile coinvolgimento Web SDK. Ad esempio, per ridefinire l'URL di log (endpoint SDK per l'accesso), è possibile ignorare la configurazione come segue:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Se le funzioni di URL restituiranno una stringa che inizia con `/`, `//`, `http://`, o `https://`, non viene utilizzata la combinazione predefinita. Per impostazione predefinita, la `https://` schema viene utilizzato per tali URL. Se si desidera personalizzare lo schema predefinito, ignorare la configurazione, come segue:

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
