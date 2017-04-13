<properties
    pageTitle="Come identificare gli scenari e pianificare avanzate elaborazione dei dati analitica | Microsoft Azure"
    description="Pianificare analitica avanzate prendendo in una serie di domande."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev" />


# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Come identificare gli scenari e piano per l'elaborazione di dati analitica avanzate

Quali risorse devono si intende includere quando si configura un ambiente eseguire analitica avanzate elaborazione su un set di dati In questo articolo suggerisce una serie di domande da porsi che consenta di identificare le attività e risorse pertinenti proprio scenario. L'ordine dei passaggi di alto livello per analitica previsione è delineata [che cos'è il processo di scienze dati Team (TDSP)?](data-science-process-overview.md). Ognuna di queste operazioni richiederà risorse specifiche per le attività i contenuti pertinenti per lo scenario. Le domande principali per identificare lo scenario riguardano logistica dati, le caratteristiche, la qualità del set di dati e le lingue che si preferisce eseguire l'analisi e gli strumenti.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Domande logistiche: ubicazione dei dati e spostamento
Le domande logistiche riguardano la posizione dell' **origine dati**, la **destinazione di destinazione** in Azure e requisiti per spostare i dati, inclusa la pianificazione, quantità e risorse. I dati potrebbero essere necessario spostare più volte durante il processo di analitica. Uno scenario comune consiste nello spostare dati locali in una forma di spazio di archiviazione in Azure e quindi in computer risorse Studio.

1. **Che cos'è l'origine dati?** Si tratta locale o nel cloud? Per esempio:
    - I dati sono accessibili a un indirizzo HTTP.
    - I dati si trovano in un percorso file locale/rete.
    - I dati sono in un database di SQL Server.
    - I dati vengono memorizzati in un contenitore di archiviazione Azure

2. **Che cos'è la destinazione Azure?** Posizione in cui è necessario per l'elaborazione o modellazione? Per esempio:
    - Archiviazione Blob Azure
    - Database di SQL Azure
    - SQL Server in macchine Virtuali di Azure
    - HDInsight (Hadoop in Azure) o Hive tabelle
    - Apprendimento Azure
    - Montaggio su Azure dischi rigidi virtuali.

3. **Come si sta per spostare i dati?** Negli argomenti seguenti vengono descritte le procedure e le risorse disponibili per il caricamento o caricare i dati in una serie di archiviazione diverso e ambienti di elaborazione.

    -  [Caricare i dati in ambienti di spazio di archiviazione per analitica](machine-learning-data-science-ingest-data.md)
    -  [Importare i propri dati formazione in Azure Machine Learning Studio da varie origini dati](machine-learning-data-science-import-data.md).

4. **I dati sono necessario essere spostato a intervalli regolari o modificato durante la migrazione?** È possibile utilizzare Azure dati Factory (alimentatore automatico) quando i dati devono essere continuamente viene eseguita la migrazione, in particolare se uno scenario ibrido per l'accesso sia in locale e le risorse cloud di o quando i dati viene gestiti o devono essere modificate o una regola di business aggiunto nel corso della fase di migrazione. Per ulteriori informazioni, vedere [spostare dati da un server SQL locale di SQL Azure con Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md)

5. **Quantità di dati viene spostato in Azure?** Set di dati sono di dimensioni molto grandi può superare la capacità di archiviazione di determinati ambienti. Ad esempio, vedere la discussione di limiti di dimensioni per Studio risorse computer nella sezione successiva. In questo caso un campione dei dati può essere utilizzato durante l'analisi. Per informazioni dettagliate su come campioni verso il basso un set di dati nei vari ambienti Azure, vedere [dati di esempio del processo di scienze dei dati del Team](machine-learning-data-science-sample-data.md).


## <a name="data-characteristics-questions-type-format-and-size"></a>Domande di caratteristiche di dati: tipo, formato e le dimensioni
Queste domande sono fondamentali per la pianificazione lo spazio di archiviazione e l'elaborazione di ambienti, ognuno dei quali sono appropriate per diversi tipi di dati e ciascuna delle quali sono alcune limitazioni.

1. **Quali sono i tipi di dati?** Per esempio:
    - Numerico
    - Per categoria
    - Stringhe
    - Binario

2. **Come formattare i dati?** Per esempio:
    - File flat (TSV) delimitato da tabulazione o virgole (CSV)
    - Compresso o non compresso
    - BLOB Azure
    - Tabelle Hadoop Hive
    - Tabelle di SQL Server

2. **Le dimensioni sono i dati?**
    - Piccoli: inferiore a 2GB
    - Supporto: Maggiore di 2GB e minore di 10GB
    - Grande: Maggiore di 10GB

Eseguire l'ambiente di Azure Machine Learning Studio, ad esempio:

- Per un elenco dei formati di dati e tipi supportati da Azure Machine Learning Studio, vedere la sezione [tipi di dati e i formati di dati supportati](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) .
- Per informazioni sui limiti di dati di Azure Machine Learning Studio, vedere la **come grandi set di dati possibile per i moduli?** sezione [dell'importazione ed esportazione dei dati per l'apprendimento](machine-learning-faq.md#machine-learning-studio-questions)

Per informazioni sui limiti di altri servizi Azure nel processo di analitica, vedere [abbonamento Azure e limiti del servizio, le quote e i vincoli](../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Domande di qualità dei dati: esplorazione e pre-elaborazione

1. **Cosa si conoscono i dati?** Esplorare i dati quando è necessario ottenere un comprendere le caratteristiche di base. Quali modelli o tendenze gli oggetti, che cos'è valori erratici ha o il numero di valori mancanti. Questo passaggio è importante per determinare l'estensione della pre-elaborazione necessario per la formulazione ipotesi che potrebbero suggerire le caratteristiche più appropriate o tipo di analisi e per la formulazione piani per raccolta dati aggiuntivi. Il calcolo Statistica descrittiva e il tracciato dei valori visualizzazioni sono tecniche utili per il controllo di dati. Per informazioni dettagliate su come esplorare un set di dati nei vari ambienti Azure, vedere [esplorare i dati del processo di scienze dei dati del Team](machine-learning-data-science-explore-data.md).

2. **Pre-elaborazione o pulizia dei dati richiede correttamente?**
Pre-elaborazione e la pulizia dei dati sono attività importanti che in genere devono essere effettuate prima di utilizzare set di dati in modo efficace per l'apprendimento. Dati non elaborati sono spesso disturbata e origini non affidabili e potrebbero mancare valori. Uso di tali dati per una modellazione può generare risultati fuorvianti. Per una descrizione, vedere [attività per preparare i dati per computer avanzata formativo](machine-learning-data-science-prepare-data.md).

## <a name="tools-and-languages-questions"></a>Domande linguaggi e strumenti
Sono disponibili numerose opzioni a seconda di quali lingue e ambienti di sviluppo o strumenti è necessario o utilizzando più conformable.

1. **Quali lingue si preferisce utilizzare per l'analisi?**  
    - R
    - Python
    - SQL

2. **Quali strumenti è necessario utilizzare per l'analisi di dati?**
    - [Microsoft Azure Powershell](powershell-install-configure.md) - un linguaggio di script utilizzato per amministrare le risorse Azure in un linguaggio di script.
    - [Computer Azure risorse Studio](machine-learning-what-is-ml-studio/)
    - [Rivoluzione Analitica](http://www.revolutionanalytics.com/revolution-r-open)
    - [RStudio](http://www.rstudio.com)
    - [Python Tools per Visual Studio](http://microsoft.github.io/PTVS/)
    - [Anaconda](https://www.continuum.io/why-anaconda)
    - [Blocchi appunti Jupyter](http://jupyter.org/)
    - [Microsoft Power BI](http://powerbi.microsoft.com)


## <a name="identify-your-advanced-analytics-scenario"></a>Identificare il proprio scenario analitica avanzate
Dopo avere risposta alle domande nella sezione precedente, si è pronti determinare lo scenario migliore adatta le maiuscole/minuscole. Gli scenari di esempio sono strutturati in [scenari per analitica avanzate in Azure apprendimento](machine-learning-data-science-plan-sample-scenarios.md).
