<properties 
    pageTitle="Mapping delle applicazioni in applicazione approfondimenti | Microsoft Azure" 
    description="Una presentazione visiva delle dipendenze tra i componenti di app, contrassegnata con avvisi e indicatori KPI." 
    services="application-insights" 
    documentationCenter=""
    authors="SoubhagyaDash" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/15/2016" 
    ms.author="awills"/>
 
# <a name="application-map-in-application-insights"></a>Mapping delle applicazioni in applicazione approfondimenti

In [Visual Studio applicazione approfondimenti](app-insights-overview.md), mapping di applicazioni è un layout visivo delle relazioni di dipendenza dei componenti dell'applicazione. Ogni componente mostra gli indicatori KPI, ad esempio carico, le prestazioni, errori e avvisi per scoprire qualsiasi componente causare un errore o un problema di prestazioni. È possibile fare clic su tramite da qualsiasi componente di diagnostica più dettagliata, entrambe le operazioni da applicazione approfondimenti, e - se l'app è installato Servizi Azure - diagnostica Windows Azure, ad esempio i suggerimenti di preparazione di Database SQL.

Ad esempio altri grafici, è possibile aggiungere un mapping delle applicazioni al dashboard di Azure, in cui è completamente funziona. 

## <a name="open-the-application-map"></a>Aprire il mapping di applicazioni

Aprire la mappa da e il panoramica dell'applicazione:

![Aprire app mappa](./media/app-insights-app-map/01.png)

![mappa di App](./media/app-insights-app-map/02.png)

Mostra la mappa:

* Verifica disponibilità
* Componente di lato client (controllato con JavaScript SDK)
* Componente lato server
* Dipendenze dei componenti client e server

È possibile espandere e comprimere i gruppi di collegamento dipendenza:

![comprimere](./media/app-insights-app-map/03.png)
 
Se si dispone di un numero elevato di dipendenze di un tipo (SQL e così via HTTP), compaiono raggruppati. 


![dipendenze raggruppate](./media/app-insights-app-map/03-2.png)
 
 
## <a name="spot-problems"></a>Individuare i problemi

Ogni nodo dispone indicatori di prestazioni rilevanti, ad esempio il caricamento, le prestazioni ed errore le tariffe per il componente. 

Icone di avviso evidenziare possibili problemi. Un avviso arancione indica che sono disponibili gli errori nelle richieste di pagine o dipendenza chiamate. Rosso indica che un tasso di errore sopra 5%.


![icone di errore](./media/app-insights-app-map/04.png)

 
Attiva avvisa inoltre Mostra la: 


![avvisi attivi](./media/app-insights-app-map/05.png)
 
Se si utilizza SQL Azure, è presente un'icona che mostra quando sono disponibili suggerimenti su come è possibile migliorare le prestazioni. 


![Suggerimento Azure](./media/app-insights-app-map/06.png)

Fare clic su una qualsiasi icona per visualizzare ulteriori dettagli:


![Suggerimento Azure](./media/app-insights-app-map/07.png)
 
 
## <a name="diagnostic-click-through"></a>Fare clic su diagnostica tramite

Ognuno dei nodi sulla mappa offre fare clic su Target tramite per diagnostica. Le opzioni variano a seconda del tipo del nodo.

![opzioni del server](./media/app-insights-app-map/09.png)

 
Per i componenti che sono ospitati in Azure, le opzioni includono collegamenti diretti ad essi.


## <a name="filters-and-time-range"></a>Filtri e intervallo di tempo

Per impostazione predefinita, la mappa contiene un riepilogo di tutti i dati disponibili per l'intervallo di tempo selezionato. Ma è possibile filtrare in modo da includere solo i nomi di operazione specifico o dipendenze.

* Nome dell'operazione: sono inclusi visualizzazioni di pagina e i tipi di richieste sul lato server. Con questa opzione, il mapping Mostra l'indicatore KPI sul nodo lato client/server per le operazioni selezionate. Mostra le dipendenze chiamate nel contesto di queste operazioni specifiche.
* Nome di base della dipendenza: sono incluse le dipendenze lato browser AJAX e dipendenze lato server. Se si segnala telemetria dipendenza personalizzata con l'API TrackDependency, essi verranno visualizzati anche qui. È possibile selezionare le dipendenze da visualizzare nella mappa. Si noti che in questa fase, questo non filtrerà le richieste di lato server o visualizzazioni pagina sul lato client.


![Impostare i filtri](./media/app-insights-app-map/11.png)

 
 
## <a name="save-filters"></a>Salvare i filtri

Per salvare i filtri applicati, aggiungere la visualizzazione filtrata in un [dashboard](app-insights-dashboards.md).


![Aggiungere al dashboard](./media/app-insights-app-map/12.png)
 


## <a name="feedback"></a>Commenti e suggerimenti

Informazioni [fornire commenti e suggerimenti tramite l'opzione di commenti e suggerimenti al portale](app-insights-get-dev-support.md).


![Immagine MapLink 1](./media/app-insights-app-map/13.png)


