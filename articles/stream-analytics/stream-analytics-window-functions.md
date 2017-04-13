<properties
    pageTitle="Introduzione alle funzioni di flusso Analitica finestra | Microsoft Azure"
    description="Informazioni sulle funzioni finestra tre in flusso Analitica (ha hopping, scorrimento)."
    keywords="lo scambio di finestra, finestra hopping finestra scorrevole"
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


# <a name="introduction-to-stream-analytics-window-functions"></a>Introduzione alle funzioni di flusso Analitica finestra

In tempo reale molti scenari di flusso, è necessario eseguire operazioni solo sui dati contenuti in windows temporale. Supporto per le funzioni di finestre è una funzionalità chiave di Azure flusso Analitica che sposta ago produttività per la creazione di processi di elaborazione del flusso complesse. Flusso Analitica consente agli sviluppatori di utilizzare [**lo scambio**](https://msdn.microsoft.com/library/dn835055.aspx), [**Hopping**](https://msdn.microsoft.com/library/dn835041.aspx) e [**scorrimento**](https://msdn.microsoft.com/library/dn835051.aspx) per eseguire operazioni temporale sul flusso di dati. È importante notare che tutte le operazioni di [finestra](https://msdn.microsoft.com/library/dn835019.aspx) di output risultati alla **Fine** della finestra. L'output della finestra sarà singolo evento in base a utilizzare la funzione di aggregazione. L'evento avrà il timbro dell'ora di fine della finestra e tutte le funzioni finestra sono definite con una lunghezza fissa. Infine, è importante tenere presente che tutte le funzioni finestra devono essere in una clausola [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) .

![Flusso Analitica finestra funziona concetti](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Lo scambio finestra

Lo scambio finestra vengono utilizzate per suddividere un flusso di dati in segmenti di tempo specifico ed eseguire una funzione in base a tali, come nell'esempio seguente. Le principali differenze di una finestra ha sono che ripetono, non si sovrappongono e un evento non appartiene a più di una finestra ha.

![Funzioni di finestra Analitica flusso lo scambio di introduzione](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Finestra hopping

Hopping finestra funzioni hop inoltra nel tempo da un determinato periodo. Potrebbe essere facile devono essere considerati come lo scambio finestre sovrapposte possono, in modo che gli eventi a cui possono appartenere più di un set di risultati finestra Hopping. Per creare una finestra Hopping diverso da quello di un ha finestra uno è sufficiente specificare le dimensioni di hop per restare invariate le dimensioni della finestra. 

![Funzioni di finestra Analitica flusso hopping intro](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Finestra scorrevole

Funzioni finestra scorrevole, a differenza di windows ha o Hopping, producano un output **solo** quando si verifica un evento. Ogni finestra avrà almeno un evento e la finestra continuamente avanza da un € (epsilon). Ad esempio Windows Hopping eventi a cui possono appartenere più di una finestra di scorrimento.

![Funzioni di finestra Analitica flusso scorrimento intro](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Visualizzazione della Guida con le funzioni di finestra

Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
