<properties 
    pageTitle="Attività di flusso Hadoop" 
    description="Informazioni su come è possibile utilizzare l'attività di flusso Hadoop di una factory di dati di Azure per eseguire programmi Hadoop Streaming in un cluster HDInsight personalizzato nella richiesta/il." 
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
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Attività di flusso Hadoop
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Maialino](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Streaming Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprendimento](data-factory-azure-ml-batch-execution-activity.md) 
[Stored Procedure](data-factory-stored-proc-activity.md)
[Dati Lake Analitica U-SQL](data-factory-usql-activity.md)
[.NET personalizzato](data-factory-use-custom-activities.md)

È possibile utilizzare l'attività HDInsightStreamingActivity richiamare un processo Hadoop Streaming da una pipeline di Azure Data Factory. Frammento JSON riportato di seguito è illustrata la sintassi per l'utilizzo di HDInsightStreamingActivity in un file JSON pipeline. 

L'attività di flusso HDInsight in una Data Factory [pipeline](data-factory-create-pipelines.md) esegue Hadoop Streaming programmi sul [proprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basato su Windows/Linux HDInsight cluster. In questo articolo si basa su articolo [le attività di trasformazione dati](data-factory-data-transformation-activities.md) , che viene fornita una panoramica generale di trasformazione dei dati e le attività di trasformazione supportati.

## <a name="json-sample"></a>Esempio JSON
Cluster HDInsight popolato automaticamente con dati (davinci.txt) e programmi di esempio (wc.exe e cat.exe). Per impostazione predefinita, nome del contenitore utilizzato da cluster HDInsight è il nome del cluster stesso. Ad esempio, se il nome del cluster myhdicluster, nome del contenitore di blob associato sarebbe myhdicluster. 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

Noti quanto segue:

1. Impostare il nome del servizio collegato che punta al cluster HDInsight in cui viene eseguito il processo di mapreduce flusso **linkedServiceName** .
2. Impostare il tipo di attività **HDInsightStreaming**.
3. Per la proprietà **mapping** , specificare il nome della mappatura eseguibile. Nell'esempio, cat.exe è il mapping degli eseguibile.
4. Per la proprietà **riduttore** , specificare il nome del riduttore eseguibile. Nell'esempio, wc.exe è riduttore eseguibile.
5. Per la proprietà tipo di **input** , specificare il file di input (incluso il percorso) per il mapping degli. Nell'esempio: "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt ": adfsample è il contenitore blob, esempio/dati/Gutenberg è la cartella e davinci.txt è il blob.
6. Per la proprietà di tipo di **output** , specificare il file di output (inclusa la posizione) di riduttori. L'output del processo di Hadoop Streaming è scritto nella posizione specificata per questa proprietà.
7. Nella sezione **filePaths** specificare i percorsi per i file eseguibili mapping e riduttore. Nell'esempio: "adfsample/example/apps/wc.exe" adfsample è il contenitore blob, esempio/App è la cartella e wc.exe è il file eseguibile.
8. Per la proprietà **fileLinkedService** , specificare il servizio di archiviazione di Azure collegato che rappresenta lo spazio di archiviazione Azure che contiene i file specificati nella sezione filePaths.
9. Per la proprietà di **argomenti** , specificare gli argomenti per il processo di flusso.
10. La proprietà **getDebugInfo** è un elemento facoltativo. Quando è impostata su esito negativo, i registri vengono scaricati solo in caso di errore. Quando è impostato su tutti, i registri vengono scaricati sempre indipendentemente dallo stato di esecuzione.

> [AZURE.NOTE] Come mostrato nell'esempio, specificare un set di dati di output per l'attività di flusso Hadoop per la proprietà **output** . Questo set di dati è solo un set di fittizio dati necessari per risorse sulla programmazione pipeline. Non è necessario specificare un set di dati di input per l'attività per la proprietà di **input** .  

    
## <a name="example"></a>Esempio
La pipeline di questa procedura dettagliata esegue il programma di mapping e riduzione flusso conteggio il cluster Azure HDInsight. 

### <a name="linked-services"></a>Servizi collegati

#### <a name="azure-storage-linked-service"></a>Servizio di archiviazione collegato di Azure
Creare innanzitutto un servizio collegato per creare un collegamento sull'archiviazione di Azure utilizzato dal cluster Azure HDInsight su factory dati Azure. Se si copia/incolla il codice seguente, non dimenticare sostituire il nome dell'account e la chiave account con il nome e la chiave dello spazio di archiviazione Azure. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Servizio di Azure HDInsight collegato
Successivamente, si crea un servizio collegato per collegare il cluster di Azure HDInsight factory dati Azure. Se si copia/incolla il codice seguente, sostituire HDInsight cluster con il nome del cluster HDInsight e modificare valori nome utente e password. 
    
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>Set di dati

#### <a name="output-dataset"></a>Set di dati di output
La pipeline in questo esempio non accetta qualsiasi input. Specificare un set di dati di output per l'attività di flusso HDInsight. Questo set di dati è solo un set di fittizio dati necessari per risorse sulla programmazione pipeline. 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline

La pipeline in questo esempio dispone di una sola attività che è di tipo: **HDInsightStreaming**. 

Cluster HDInsight popolato automaticamente con dati (davinci.txt) e programmi di esempio (wc.exe e cat.exe). Per impostazione predefinita, nome del contenitore utilizzato da cluster HDInsight è il nome del cluster stesso. Ad esempio, se il nome del cluster myhdicluster, nome del contenitore di blob associato sarebbe myhdicluster.  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>Vedere anche
- [Attività di hive](data-factory-hive-activity.md)
- [Maialino attività](data-factory-pig-activity.md)
- [Attività MapReduce](data-factory-map-reduce.md)
- [Richiamare programmi ad](data-factory-spark.md)
- [Richiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

