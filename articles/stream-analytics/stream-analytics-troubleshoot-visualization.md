<properties
    pageTitle="Visualizzare e risolvere i processi di flusso Analitica | Microsoft Azure"
    description="Informazioni su come visualizzare una pipeline di processo flusso Analitica per modalità self-service risoluzione dei problemi con la funzionalità di diagramma di diagnostica."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualizzare e risolvere i processi di flusso Analitica

In flusso Analitica, come con altre tecnologie basate su cloud, risoluzione dei problemi è utile per esaminare il perché un processo non produce output previsto (o qualsiasi output a tale scopo). Con questo, flusso Analitica offre la possibilità per la visualizzazione di un processo di flusso. È anche utile come strumento di modellazione e il vantaggio lato per tali che richiedono la documentazione del loro lavoro.

Nel riquadro di visualizzazione sono visibili nonché la query eseguita e quindi tutti gli output configurati l'input. Problemi di connettività o configurazione possono diventare più evidenti e può inoltre essere utile visualizzare una rappresentazione visiva della configurazione.

## <a name="using-the-diagnosis-diagram-tool"></a>Tramite lo strumento di diagramma diagnosi

Per accedere a questo visualizzatore, è sufficiente fare clic sul pulsante "Diagramma diagnosi" in e il "Impostazioni" del del processo di flusso Analitica.

![Stream-Analytics-Troubleshoot-Visualization-DIAGNOSIS-Diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Ogni input e output è stato colori diversi per indicare corrente di tale componente, come illustrato di seguito.

![Stream-Analytics-Troubleshoot-Visualization-input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Quando l'utente desidera esaminare i passaggi della query intermedi per ottenere informazioni sui modelli di flusso dei dati all'interno di un processo, lo strumento di visualizzazione fornisce una visualizzazione della suddivisione della query in tali passaggi componente e la sequenza di flusso. Fare clic su ogni passaggio della query illustra la sezione corrispondente in una query modifica riquadro come illustrato. 

![Stream-Analytics-Troubleshoot-Visualization-Intermediate-Steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
