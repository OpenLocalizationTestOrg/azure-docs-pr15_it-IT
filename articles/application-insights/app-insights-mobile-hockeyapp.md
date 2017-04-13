<properties
    pageTitle="Monitoraggio delle prestazioni per applicazioni web per dispositivi mobili con sviluppo Analitica | Microsoft Azure"
    description="Le prestazioni dell'applicazione e l'uso di monitoraggio per gli sviluppatori di app per dispositivi mobili. , desktop, servizio web e App back-end con HockeyApp e approfondimenti applicazione."
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Dispositivi mobili Analitica con HockeyApp e applicazione approfondimenti

Monitorare le prestazioni e l'uso dei test beta e distribuito App per dispositivi mobili e desktop con [HockeyApp](https://hockeyapp.net/). Monitorare i supporto del servizio e back-end online con [Visual Studio applicazione approfondimenti](app-insights-overview.md). Analizzare i dati da applicazioni client e server in Analitica e visualizzare i tipi di grafici accanto a altro in un dashboard di Azure.

Microsoft Developer Analitica offre due soluzioni per il monitoraggio delle prestazioni di applicazione:

* App **HockeyApp per dispositivi mobili** e desktop.
 * Distribuire le versioni di test agli utenti selezionati.
 * Un arresto anomalo di analisi.
 * Telemetria personalizzato per l'analisi di utilizzo.
* Applicazioni di servizi e **Applicazioni approfondimenti per i siti web** e back-end.
 * Metriche di utilizzo e le prestazioni e avvisi.
 * Eccezione per la creazione di report e analisi diagnostica.
 * Diagnostica ricerca con i collegamenti di telemetria di filtro e correlati.

Entrambe le soluzioni offrono:

 * **[Linguaggio di query analitico](app-insights-analytics.md)** potenti per diagnostica e analisi.
 * **[Esportare i dati](app-insights-export-telemetry.md)** in modalità di archiviazione personalizzata.
 * Visualizzazione di **dashboard integrata** di un grafico analitico e tabelle.

## <a name="monitor-your-app-components"></a>Monitorare i componenti di app

Seguire le istruzioni in queste pagine per installare il SDK nel codice e iniziare a registrare l'app.

### <a name="web-apps---application-insights"></a>App Web - applicazione approfondimenti

* [App web ASP.NET](app-insights-asp-net.md) 
* [Linguaggio web app](app-insights-java-get-started.md)
* [Node web app](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Servizi Cloud Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>App per dispositivi mobili - HockeyApp

* [app iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [App di Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [App di Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [App universale di Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [App di Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation app](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Per le applicazioni desktop di Windows, è consigliabile HockeyApp. Ma è anche possibile [inviare telemetria da un'app di Windows a informazioni dettagliate sui applicazione](app-insights-windows-desktop.md). È consigliabile farlo per sperimentare approfondimenti applicazione.


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Analitica ed esportazione di telemetria HockeyApp

È possibile provare a utilizzare HockeyApp personalizzati e accedere usando le caratteristiche Analitica ed esportare continuo di informazioni dettagliate sui applicazione tramite [la configurazione di un bridge di](app-insights-hockeyapp-bridge-app.md)telemetria.




