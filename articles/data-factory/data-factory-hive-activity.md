<properties 
    pageTitle="Attività di hive" 
    description="Informazioni su come utilizzare in una factory di dati di Azure attività Hive per eseguire query Hive in un cluster HDInsight personalizzato nella richiesta/il." 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>Attività di hive
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Maialino](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Streaming Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprendimento](data-factory-azure-ml-batch-execution-activity.md) 
[Stored Procedure](data-factory-stored-proc-activity.md)
[Dati Lake Analitica U-SQL](data-factory-usql-activity.md)
[.NET personalizzato](data-factory-use-custom-activities.md)

Attività HDInsight Hive in una Data Factory [pipeline](data-factory-create-pipelines.md) esegue query Hive nel [proprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster basato su Windows/Linux HDInsight. In questo articolo si basa su articolo [le attività di trasformazione dati](data-factory-data-transformation-activities.md) , che viene fornita una panoramica generale di trasformazione dei dati e le attività di trasformazione supportati.

## <a name="syntax"></a>Sintassi

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
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
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>Informazioni dettagliate sulla sintassi

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
nome | Nome dell'attività | Sì
Descrizione | Testo che descrive l'attività utilizzato per | No
tipo | HDinsightHive | Sì
ingressi | Ingressi consumate da attività Hive | No
output | Output di prodotti dall'attività Hive | Sì 
linkedServiceName | Riferimento a cluster HDInsight registrato come servizio collegato nella Factory dati | Sì 
script | Specificare in linea di script Hive | No
percorso di script | Archiviare script Hive in un'archiviazione blob Azure e specificare il percorso del file. Utilizzare 'script' o 'scriptPath'. Entrambi non possono essere utilizzati insieme. Il nome del file è maiuscole e minuscole. | No 
definisce | Specificare come coppie di parole chiave/valore per fare riferimento all'interno di script Hive usando 'hiveconf'  | No

## <a name="example"></a>Esempio

Si consideri un esempio dei registri gioco analitica in cui si desidera identifica il tempo dedicato dagli utenti giochi avviate dall'azienda. 

Nel log seguente è un esempio gioco file di log, virgola (`,`) separati e contiene i seguenti campi: profilo, SessionStart, durata, SrcIPAddress e GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

L' **Hive script** per l'elaborazione di questi dati:

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

Per eseguire questo script Hive nella pipeline di Factory dati, è necessario eseguire le operazioni seguenti

1. Creare un servizio collegato per registrare [il proprio HDInsight calcolare cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o configurare [cluster di elaborazione HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Si chiama il servizio collegato "HDInsightLinkedService".
2. Creare un [servizio collegata](data-factory-azure-blob-connector.md) per configurare la connessione allo spazio di archiviazione Blob Azure che ospita i dati. Si chiama il servizio collegato "StorageLinkedService"
3. Creare [set di dati](data-factory-create-datasets.md) che puntano all'input e i dati di output. Si chiama il set di dati input "HiveSampleIn" e il set di dati di output "HiveSampleOut"
4. Copiare la query Hive come file all'archivio Blob Azure configurato nel passaggio 2 #. Se lo spazio di archiviazione per l'hosting dei dati è diverso da quello che ospita il file di query, creare un servizio di archiviazione di Azure collegato separato e farvi riferimento nell'attività. Consente di specificare il percorso di file di query hive e **scriptLinkedService** per specificare lo spazio di archiviazione Azure che contiene il file di script **scriptPath **. 

    > [AZURE.NOTE] È anche possibile inline di script Hive nella definizione dell'attività utilizzando la proprietà di **script** . Si sconsiglia di questo approccio come tutti i caratteri speciali lo script all'interno del documento JSON deve essere di tipo escape e possono causare problemi di debug. La procedura consigliata consiste nell'eseguire il passaggio 4 #.
5.  Creare una pipeline all'attività HDInsightHive. L'attività processi/trasformazioni i dati.

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  Distribuire la pipeline. Vedere l'articolo [pipeline di creazione](data-factory-create-pipelines.md) per informazioni dettagliate. 
7.  Controllare la pipeline utilizzando le visualizzazioni dati monitoraggio e la gestione del produttore. Vedere [monitoraggio e la gestione dei dati Factory condotti](data-factory-monitor-manage-pipelines.md) articolo per informazioni dettagliate. 


## <a name="specifying-parameters-for-a-hive-script"></a>Specificare i parametri di uno script Hive  
In questo esempio, i registri gioco vengono caricati giornaliero in archiviazione Blob Azure e archiviati in una cartella partizionata con data e ora. Si desidera aggiungere un parametro script Hive e passare in modo dinamico il percorso della cartella input durante l'esecuzione e anche generare l'output partizionato con data e ora.

Per utilizzare parametri script Hive, eseguire le operazioni seguenti

- Definire i parametri in **definisce**.

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- In Script Hive fare riferimento al parametro utilizzando **${hiveconf:parameterName}**. 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>Vedere anche
- [Maialino attività](data-factory-pig-activity.md)
- [Attività MapReduce](data-factory-map-reduce.md)
- [Attività di flusso Hadoop](data-factory-hadoop-streaming-activity.md)
- [Richiamare programmi ad](data-factory-spark.md)
- [Richiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









