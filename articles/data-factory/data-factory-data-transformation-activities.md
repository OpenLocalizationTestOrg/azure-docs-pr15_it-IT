<properties 
    pageTitle="Trasformazione dei dati: Dati di processo e trasforma | Microsoft Azure" 
    description="Informazioni su come trasformare i dati o grafici in Azure Data Factory usando Hadoop, apprendimento o Azure dati Lake Analitica." 
    keywords="trasformazione dei dati, i dati processo trasformare i dati, attività di trasformazione"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>Trasformare i dati di Azure Data Factory
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Maialino](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Streaming Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprendimento](data-factory-azure-ml-batch-execution-activity.md) 
[Stored Procedure](data-factory-stored-proc-activity.md)
[Dati Lake Analitica U-SQL](data-factory-usql-activity.md)
[.NET personalizzato](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Panoramica 
Questo articolo illustra le attività di trasformazione dati di Azure Data Factory consente di trasformare ed elabora i dati non elaborati nelle stime e approfondimenti. Attività di trasformazione viene eseguita in un ambiente informatico, ad esempio Azure HDInsight cluster o un Batch di Azure. Vengono forniti collegamenti ad articoli con informazioni dettagliate su ciascuna attività di trasformazione.
 
Dati Factory supporta i seguenti dati trasformazione attività che possono essere aggiunti alle [condutture](data-factory-create-pipelines.md) singolarmente o concatenate con un'altra attività.

> [AZURE.NOTE] Per una procedura dettagliata con istruzioni dettagliate, vedere l'articolo [creare una pipeline di trasformazione Hive](data-factory-build-your-first-pipeline.md) .  

## <a name="hdinsight-hive-activity"></a>Attività HDInsight Hive
Attività HDInsight Hive nella pipeline di dati Factory esegue query Hive privatamente o cluster basato su Windows/Linux HDInsight su richiesta. Vedere l'articolo [Hive attività](data-factory-hive-activity.md) per informazioni dettagliate sull'attività corrente. 

## <a name="hdinsight-pig-activity"></a>Attività maialino HDInsight
Attività HDInsight maialino nella pipeline di dati Factory esegue query maialino autonomamente o cluster basato su Windows/Linux HDInsight su richiesta. Vedere l'articolo [Maialino attività](data-factory-pig-activity.md) per informazioni dettagliate sull'attività corrente. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce attività
Attività HDInsight MapReduce nella pipeline di dati Factory esegue programmi MapReduce autonomamente o cluster basato su Windows/Linux HDInsight su richiesta. Vedere l'articolo [MapReduce attività](data-factory-map-reduce.md) per informazioni dettagliate sull'attività corrente.

È possibile utilizzare MapReduce attività per l'esecuzione ad applicazioni il cluster HDInsight Spark. Per informazioni dettagliate, vedere [richiamare i programmi di Azure Data Factory](data-factory-spark.md) .

## <a name="hdinsight-streaming-activity"></a>HDInsight flusso attività
L'attività di flusso HDInsight nella pipeline di dati Factory esegue Hadoop Streaming le applicazioni presenti nel proprio o cluster basato su Windows/Linux HDInsight su richiesta. Per informazioni su questa attività, vedere [attività di flusso HDInsight](data-factory-hadoop-streaming-activity.md) .

## <a name="machine-learning-activities"></a>Attività di formazione di computer
Azure Data Factory consente di creare facilmente pipeline che usano un servizio web apprendimento Azure pubblicato per previsione analitica. Usa il [Batch di esecuzione attività](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) in una pipeline di Azure Data Factory, è possibile richiamare un servizio web di apprendimento per rendere le stime sui dati in batch.

Nel tempo, è necessario essere training nuovi set di dati di input utilizzando i modelli di previsione in formazione Machine punteggio esperimenti. Dopo aver con formazione, si desidera aggiornare il punteggio servizio web con il modello di apprendimento retrained. È possibile utilizzare l' [Aggiornamento attività delle risorse](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) per l'aggiornamento del servizio web con il modello appena esperto.  

Per informazioni su queste attività di apprendimento, vedere [usare apprendimento attività](data-factory-azure-ml-batch-execution-activity.md) . 

## <a name="stored-procedure-activity"></a>Attività di stored procedure
È possibile utilizzare l'attività di SQL Server Stored Procedure nella pipeline di dati Factory per richiamare una stored procedure in uno dei seguenti archivi di dati: Database SQL Azure, Warehouse di dati di SQL Azure, Database di SQL Server nell'organizzazione oppure una macchina virtuale Azure. Vedere l'articolo [Stored Procedure attività](data-factory-stored-proc-activity.md) per informazioni dettagliate.  

## <a name="data-lake-analytics-u-sql-activity"></a>Attività Lake Analitica U-SQL dei dati
Dati Lake Analitica SQL U attività esegue uno script U SQL in un cluster di Azure dati Lake Analitica. Vedere l'articolo [Dati Analitica SQL U attività](data-factory-usql-activity.md) per informazioni dettagliate. 

## <a name="net-custom-activity"></a>Attività personalizzata .NET
Se è necessario trasformare i dati in modo che non è supportato dal produttore di dati, è possibile creare un'attività personalizzata con la logica di elaborazione dei dati e utilizzare l'attività nella pipeline. È possibile configurare l'attività .NET personalizzato per l'esecuzione tramite un servizio di Azure Batch o un cluster di Azure HDInsight. Vedere l'articolo [Usa attività personalizzata](data-factory-use-custom-activities.md) per informazioni dettagliate. 

È possibile creare un'attività personalizzata per eseguire script R il cluster HDInsight con R installato. Vedere [Esegui Script R con Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Calcolare ambienti
Creare un servizio per l'ambiente di calcolo collegato e quindi usare il servizio collegato quando si definisce un'attività di trasformazione. Esistono due tipi di calcolo ambienti supportati dal produttore dati. 

1. **Nella richiesta**: In questo caso, dell'ambiente informatico completamente viene gestita da Factory dati. Viene automaticamente creata da servizio dati Factory prima che venga inviato per elaborare i dati e rimosso al termine del processo di un processo. È possibile configurare e controllare le impostazioni di granulare dell'ambiente di elaborazione su richiesta per l'esecuzione del processo, Gestione cluster e avvio automatico di azioni. 
2. **Visualizzare il proprio**: In questo caso, è possibile registrare il proprio ambiente informatico (ad esempio HDInsight cluster) come servizio collegato nella Factory dati. Ambiente informatico viene gestito dall'utente e il servizio Factory dati utilizzata per eseguire le attività. 

Vedere l'articolo [Calcolare servizi collegati](data-factory-compute-linked-services.md) per imparare a calcolare servizi supportati da Factory dati. 


## <a name="summary"></a>Riepilogo
Azure Data Factory supporta le seguenti attività di trasformazione dei dati e gli ambienti di calcolo per le attività. Le attività di trasformazione possono essere aggiunto alle condutture singolarmente o concatenate con un'altra attività.

Attività di trasformazione dei dati |  Calcolare ambiente 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Maialino](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop di flusso](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Computer attività di apprendimento: esecuzione Batch e delle risorse di aggiornamento](data-factory-azure-ml-batch-execution-activity.md) | Macchine Virtuali di Azure 
[Stored Procedure](data-factory-stored-proc-activity.md) | SQL Azure, SQL Azure Data Warehouse o SQL Server |
[Dati Analitica Lake U-SQL](data-factory-usql-activity.md) | Dati di Azure Lake Analitica 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Batch Azure
   

