<properties 
    pageTitle="Maialino attività" 
    description="Informazioni su come utilizzare in una factory di dati di Azure attività maialino per eseguire gli script maialino in un cluster HDInsight personalizzato nella richiesta/il." 
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
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>Maialino attività
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Maialino](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Streaming Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprendimento](data-factory-azure-ml-batch-execution-activity.md) 
[Stored Procedure](data-factory-stored-proc-activity.md)
[Dati Lake Analitica U-SQL](data-factory-usql-activity.md)
[.NET personalizzato](data-factory-use-custom-activities.md)

Attività HDInsight maialino in una Data Factory [pipeline](data-factory-create-pipelines.md) esegue maialino query nel [proprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster basato su Windows/Linux HDInsight. In questo articolo si basa su articolo [le attività di trasformazione dati](data-factory-data-transformation-activities.md) , che viene fornita una panoramica generale di trasformazione dei dati e le attività di trasformazione supportati.

## <a name="syntax"></a>Sintassi

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
                "inputs": [
                    {
                        "name": "input tables"
                    }
                ],
                "outputs": [
                    {
                        "name": "output tables"
                    }
                ],
                "linkedServiceName": "MyHDInsightLinkedService",
                "typeProperties": {
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>Informazioni dettagliate sulla sintassi

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
nome | Nome dell'attività | Sì
Descrizione | Testo che descrive l'attività utilizzato per | No
tipo | HDinsightPig | Sì
ingressi | Uno o più input consumato da attività maialino | No
output | Output di uno o più prodotti dall'attività maialino | Sì
linkedServiceName | Riferimento a cluster HDInsight registrato come servizio collegato nella Factory dati | Sì
script | Specificare in linea di script maialino | No
percorso di script | Archiviare script maialino in un'archiviazione blob Azure e specificare il percorso del file. Utilizzare 'script' o 'scriptPath'. Entrambi non possono essere utilizzati insieme. Il nome del file è maiuscole e minuscole. | No
definisce | Specificare come coppie di parole chiave/valore per fare riferimento all'interno di script maialino | No

## <a name="example"></a>Esempio

Si consideri un esempio dei registri gioco analitica in cui si desidera identifica il tempo speso da lettori giochi avviate dall'azienda.
 
Nel log di gioco di esempio seguente è un file separati da virgola (,). Contiene i campi seguenti: profilo, SessionStart, durata, SrcIPAddress e GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

**Script maialino** per l'elaborazione di questi dati:

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Per eseguire questo script maialino nella pipeline di dati Factory, eseguire le operazioni seguenti:

1. Creare un servizio collegato per registrare [il proprio HDInsight calcolare cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o configurare [cluster di elaborazione HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Di seguito chiamare il servizio collegato **HDInsightLinkedService**.
2.  Creare un [servizio collegata](data-factory-azure-blob-connector.md) per configurare la connessione allo spazio di archiviazione Blob Azure che ospita i dati. Di seguito chiamare il servizio collegato **StorageLinkedService**.
3.  Creare [set di dati](data-factory-create-datasets.md) che puntano all'input e i dati di output. Si chiama il set di dati di input **PigSampleIn** e il set di dati di output **PigSampleOut**.
4.  Copiare la query maialino in un file di archivio Blob Azure configurato nel passaggio 2 #. Se lo spazio di archiviazione Azure che ospita i dati è diverso da quello contenente il file di query, creare un servizio di archiviazione di Azure collegato separato. Fare riferimento al servizio collegato nella configurazione dell'attività. Consente di specificare il percorso di file di script maialino e **scriptLinkedService** **scriptPath **. 
    
    > [AZURE.NOTE] È anche possibile inline di script maialino nella definizione dell'attività utilizzando la proprietà di **script** . Tuttavia, si sconsiglia di questo approccio come tutti i caratteri speciali script deve essere di tipo escape e possono causare problemi di debug. La procedura consigliata consiste nell'eseguire il passaggio 4 #.
5. Creare la pipeline con l'attività HDInsightPig. Questa attività elabora i dati di input tramite l'esecuzione di script maialino nei cluster HDInsight.

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. Distribuire la pipeline. Vedere l'articolo [pipeline di creazione](data-factory-create-pipelines.md) per informazioni dettagliate. 
7. Controllare la pipeline utilizzando le visualizzazioni dati monitoraggio e la gestione del produttore. Vedere [monitoraggio e la gestione dei dati Factory condotti](data-factory-monitor-manage-pipelines.md) articolo per informazioni dettagliate.

## <a name="specifying-parameters-for-a-pig-script"></a>Specificare i parametri di uno script maialino 

Tenere presente quanto segue: registri gioco sono caricamento giornaliero in archiviazione Blob Azure e archiviati in una cartella partizionata in base alla data e ora. Si desidera aggiungere un parametro lo script maialino e passare in modo dinamico il percorso della cartella input durante l'esecuzione e anche generare l'output partizionato con data e ora.
 
Per utilizzare parametri script maialino, eseguire le operazioni seguenti:

- Definire i parametri in **definisce**.

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- In Script maialino fare riferimento ai parametri tramite '**$parameterName**come illustrato nell'esempio seguente:

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>Vedere anche
- [Attività di hive](data-factory-hive-activity.md)
- [Attività MapReduce](data-factory-map-reduce.md)
- [Attività di flusso Hadoop](data-factory-hadoop-streaming-activity.md)
- [Richiamare programmi ad](data-factory-spark.md)
- [Richiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


