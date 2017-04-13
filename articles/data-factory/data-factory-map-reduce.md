<properties 
    pageTitle="Richiamare MapReduce programma da Factory dati Azure" 
    description="Informazioni su come elaborare i dati eseguendo MapReduce programmi in un cluster di Azure HDInsight da una factory di dati di Azure." 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>Richiamare programmi MapReduce dal produttore di dati
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Maialino](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Streaming Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprendimento](data-factory-azure-ml-batch-execution-activity.md) 
[Stored Procedure](data-factory-stored-proc-activity.md)
[Dati Lake Analitica U-SQL](data-factory-usql-activity.md)
[.NET personalizzato](data-factory-use-custom-activities.md)

Attività HDInsight MapReduce in una Data Factory [pipeline](data-factory-create-pipelines.md) esegue MapReduce programmi sul [proprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster basato su Windows/Linux HDInsight. In questo articolo si basa su articolo [le attività di trasformazione dati](data-factory-data-transformation-activities.md) , che viene fornita una panoramica generale di trasformazione dei dati e le attività di trasformazione supportati.

## <a name="introduction"></a>Introduzione 
Una pipeline di una factory di dati di Azure elabora i dati in servizi di archiviazione collegate mediante i servizi di calcolo collegato. La presentazione contiene una sequenza di attività in cui ogni attività esegue un'operazione di elaborazione specifico. In questo articolo vengono illustrati utilizzando l'attività di MapReduce HDInsight.
 
Per informazioni sull'esecuzione di script maialino/Hive in un cluster basato su Windows/Linux HDInsight da una pipeline utilizzando attività HDInsight maialino e Hive, vedere [maialino](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) . 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON per attività HDInsight MapReduce 

Nella definizione del JSON per l'attività HDInsight: 
 
1. Impostare il **tipo** di **attività** a **HDInsight**.
3. Specificare il nome della classe per **NomeClasse** proprietà.
4. Specificare il percorso del file VASO con il nome del file per **jarFilePath** proprietà.
5. Specificare il servizio collegato che fa riferimento all'archiviazione Blob Azure che contiene il file VASO per **jarLinkedService** proprietà.   
6. Specificare gli argomenti per il programma MapReduce nella sezione **argomenti** . In fase di esecuzione, viene visualizzato alcuni argomenti aggiuntivi (ad esempio: mapreduce.job.tags) da framework MapReduce. Per distinguere gli argomenti con gli argomenti MapReduce, si consiglia di utilizzare l'opzione e valore come argomenti come illustrato nell'esempio seguente (- s, input, --output ecc., delle opzioni seguite dai valori).

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

È possibile utilizzare l'attività di MapReduce HDInsight per eseguire qualsiasi file di vaso MapReduce in un cluster di HDInsight. Definizione JSON di esempio seguente della pipeline, l'attività HDInsight è configurato per eseguire un file JAR Mahout.

## <a name="sample-on-github"></a>Esempio in GitHub
È possibile scaricare un esempio per l'uso di attività di MapReduce HDInsight da: [Esempi di Factory dati su GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Eseguire il programma di conteggio parole
La pipeline in questo esempio esegue il programma di conteggio mappa/ridurre il cluster Azure HDInsight.   

### <a name="linked-services"></a>Servizi collegati
Creare innanzitutto un servizio collegato per creare un collegamento sull'archiviazione di Azure utilizzato dal cluster Azure HDInsight su factory dati Azure. Se si copia/incolla il codice seguente, non dimenticare sostituire il **nome dell'account** e **la chiave account** con il nome e la chiave dello spazio di archiviazione Azure. 

#### <a name="azure-storage-linked-service"></a>Servizio di archiviazione collegato di Azure

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
Successivamente, si crea un servizio collegato per collegare il cluster di Azure HDInsight factory dati Azure. Se si copia/incolla il codice seguente, sostituire **il nome del cluster HDInsight** con il nome del cluster HDInsight e modificare valori nome utente e password.   

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
La pipeline in questo esempio non accetta qualsiasi input. Specificare un set di dati di output per l'attività di MapReduce HDInsight. Questo set di dati è solo un set di fittizio dati necessari per risorse sulla programmazione pipeline.  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline
La pipeline in questo esempio dispone di una sola attività che è di tipo: HDInsightMapReduce. Alcune delle proprietà più importanti nel JSON sono: 

Proprietà | Note
:-------- | :-----
tipo | Il tipo deve essere impostato su **HDInsightMapReduce**. 
NomeClasse | Nome della classe è: **wordcount**
jarFilePath | Percorso file vaso contenente la classe. Se si copia/incolla il codice seguente, non dimenticare di modificare il nome del cluster. 
jarLinkedService | Servizio di archiviazione collegato Azure che contiene il file vaso. Questo servizio collegato fa riferimento allo spazio di archiviazione associato cluster HDInsight. 
argomenti | Il programma wordcount accetta due argomenti, input e un output. File di input è il file davinci.txt.
Frequenza/intervallo | I valori per queste proprietà corrispondono il set di dati di output. 
linkedServiceName | fa riferimento al servizio HDInsight collegato è stato creato in precedenza.   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>Eseguire programmi ad
È possibile utilizzare MapReduce attività per l'esecuzione ad applicazioni il cluster HDInsight Spark. Per informazioni dettagliate, vedere [richiamare i programmi di Azure Data Factory](data-factory-spark.md) .  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>Vedere anche
- [Attività di hive](data-factory-hive-activity.md)
- [Maialino attività](data-factory-pig-activity.md)
- [Attività di flusso Hadoop](data-factory-hadoop-streaming-activity.md)
- [Richiamare programmi ad](data-factory-spark.md)
- [Richiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
