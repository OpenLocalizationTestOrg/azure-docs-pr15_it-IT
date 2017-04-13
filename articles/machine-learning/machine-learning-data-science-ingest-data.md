<properties 
    pageTitle="Caricare i dati in ambienti di spazio di archiviazione per analitica | Microsoft Azure" 
    description="Spostare i dati da e verso archiviazione Blob Azure" 
    services="machine-learning,storage" 
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

# <a name="load-data-into-storage-environments-for-analytics"></a>Caricare i dati in ambienti di spazio di archiviazione per analitica

Il processo di scienze Team dati richiede che caricamento o caricati in numerosi ambienti diversi dello spazio di archiviazione devono essere elaborati o analizzare in modo più appropriato in ogni fase del processo di dati. Destinazioni dati generalmente utilizzate per l'elaborazione includono archiviazione Blob Azure, database di SQL Azure, SQL Server in macchine Virtuali di Azure HDInsight (Hadoop) e apprendimento Azure. 

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Questo **menu** fornisce collegamenti ad argomenti che descrivono come l'acquisizione di dati in questi ambienti di destinazione in cui i dati vengono archiviati ed elaborati.

Problemi e le esigenze aziendali, nonché la posizione iniziale, formattazione e dimensioni dei dati consente di verificare gli ambienti di destinazione in cui i dati devono essere caricamento per raggiungere gli obiettivi dell'analisi. Non è insolito per uno scenario in modo da richiedere dati da spostare tra diversi ambienti per ottenere diversi tipi di attività necessarie per creare un modello di stima. Questa sequenza delle attività possa includono, ad esempio, esplorazione dei dati, pre-elaborazione, pulizia, campioni verso il basso e training modello.
