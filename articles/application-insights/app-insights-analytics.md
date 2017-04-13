<properties 
    pageTitle="Analitica - il potente strumento di ricerca di informazioni dettagliate sui applicazione | Microsoft Azure" 
    description="Panoramica delle Analitica, lo strumento di ricerca di diagnostica potente strumento di applicazione approfondimenti. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="awills"/>


# <a name="analytics-in-application-insights"></a>Analitica nell'applicazione approfondimenti


[Analitica](app-insights-analytics.md) è la funzionalità di ricerca potenti di [Informazioni approfondite dell'applicazione](app-insights-overview.md). Queste pagine descrivono lanquage di query Analitica. 

* **[Guardare il video introduttivo](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Unità di test Analitica il nostro simulata dati](https://analytics.applicationinsights.io/demo)** se l'app non invia dati a informazioni dettagliate sui applicazione ancora.

## <a name="queries-in-analytics"></a>Query in Analitica
 
Una tipica query è seguita da una serie di *operatori* separati da una tabella di *origine* `|`. 

Ad esempio, di seguito scoprire quali ora del giorno cittadini dell'Hyderabad provare il nostro web app. Mentre si è presente, vedere di seguito vengono restituiti i codici dei risultati per le proprie richieste HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
  	| where timestamp > ago(7d) and client_City == "Hyderabad"
  	| summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
  	| extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Abbiamo contare indirizzi IP client distinti raggruppamento in base all'ora del giorno negli ultimi 7 giorni. 

Verranno visualizzati i risultati con la presentazione di grafico a barre, se si sceglie di impilare i risultati di codici di risposta diversi:

![Scegliere grafico a barre, x e y assi e quindi segmentazione](./media/app-insights-analytics/020.png)

Simile al lunchtime tempo e cuscino-Hyderabad è più popolari l'app. (E si bisogna provare a utilizzare i 500 codici).


Sono disponibili anche potenti statistiche operazioni:

![](./media/app-insights-analytics/025.png)


La lingua è disponibili numerose funzionalità interessanti:

* [Filtrare](app-insights-analytics-reference.md#where-operator) il telemetria app elaborati da tutti i campi, tra cui le proprietà personalizzate e metriche.
* [Partecipare a](app-insights-analytics-reference.md#join-operator) più tabelle, le richieste di sono correlate con visualizzazioni pagine, le chiamate dipendenza, eccezioni e registro traccia.
* Statistiche potenti [aggregazioni](app-insights-analytics-reference.md#aggregations).
* Potenti come SQL, ma molto più facile per query complesse: invece di istruzioni di annidamento, è inviare i dati da un'operazione scuola elementare alla successiva.
* Visualizzazioni immediate ed efficace.







## <a name="connect-to-your-application-insights-data"></a>Connettersi ai dati di applicazione approfondimenti


Aprire Analitica da dell'app [blade Panoramica](app-insights-dashboards.md) in informazioni dettagliate sui applicazione: 

![Aprire portal.azure.com, aprire la risorsa approfondimenti applicazione e fare clic su Analitica.](./media/app-insights-analytics/001.png)


## <a name="limits"></a>Limiti

Attualmente, i risultati della query sono limitate alla semplicemente più di una settimana di dati precedente.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>Passaggi successivi


* È consigliabile che iniziare con l' [Introduzione al linguaggio](app-insights-analytics-tour.md).