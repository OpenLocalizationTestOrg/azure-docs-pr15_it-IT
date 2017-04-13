Azure Data Factory supporta le seguenti attività trasformazione che può essere aggiunto alle condutture singolarmente o concatenate con un'altra attività.

Attività di trasformazione dei dati |  Calcolare ambiente 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Maialino](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop di flusso](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Computer attività di apprendimento: esecuzione Batch e delle risorse di aggiornamento](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Macchine Virtuali di Azure 
[Stored Procedure](../articles/data-factory/data-factory-stored-proc-activity.md) | SQL Azure, SQL Azure Data Warehouse o SQL Server |
[Dati Analitica Lake U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Dati di Azure Lake Analitica 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Batch Azure
   
> [AZURE.NOTE] 
> È possibile utilizzare MapReduce attività per l'esecuzione ad applicazioni il cluster HDInsight Spark. Per informazioni dettagliate, vedere [richiamare i programmi di Azure Data Factory](../articles/data-factory/data-factory-spark.md) .
> È possibile creare un'attività personalizzata per eseguire script R il cluster HDInsight con R installato. Vedere [Esegui Script R con Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).