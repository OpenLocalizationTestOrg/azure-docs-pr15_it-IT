<properties 
    pageTitle="Spostare i dati da Amazon Redshift utilizzo di dati | Microsoft Azure" 
    description="Informazioni su come spostare i dati da Amazon Redshift utilizzo dei dati di Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Spostare i dati da Amazon Redshift utilizzo dei dati di Azure

Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati da Amazon Redshift ai dati di un altro archiviare. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e un elenco di archivi di dati di origine/sink.  

Dati factory supporta attualmente solo spostamento di dati da Amazon Redshift agli altri archivi di dati, ma non per spostare dati da altri archivi di dati in Redshift di Amazon.

## <a name="prerequisites"></a>Prerequisiti

- Se si spostano dati in un archivio di dati in locale, concedere l'accesso a cluster Redshift Amazon Gateway di gestione dati (utilizza indirizzo IP del computer). Per istruzioni, vedere [autorizzare l'accesso al cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) . 
- Se si spostano dati a un archivio di dati di Azure, vedere [Intervalli di indirizzi IP di dati di Azure](https://www.microsoft.com/download/details.aspx?id=41653) per calcolare intervalli di indirizzi IP (inclusi gli intervalli SQL) utilizzati da Microsoft Azure data center. 

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare i dati da Amazon Redshift consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Nell'esempio seguente vengono fornite le definizioni di JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Viene illustrato come copiare i dati da Amazon Redshift all'archivio Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Esempio: Copiare i dati da Amazon Redshift a Blob Azure
In questo esempio viene illustrato come copiare dati da un database di Amazon Redshift a un archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

- Servizio collegato di tipo [AmazonRedshift](#linked-service-properties).
- Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un input [set di dati](data-factory-create-datasets.md) di tipo [RelationalTable](#dataset-type-properties).
- Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [RelationalSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio dati copiati da un risultato della query in Redshift Amazon blob ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

**Servizio di Amazon Redshift collegato**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
            }
        }
    }


**Servizio di archiviazione collegato di Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Set di dati input Redshift di Amazon**

Impostazione **"esterni": true** informa il servizio Factory dati che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati. Impostare questa proprietà su true in un set di dati di input non prodotto da un'attività nella pipeline.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


**Set di dati di output Blob Azure**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Pipeline con attività di copia**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **RelationalSource** e tipo **sink** è impostato su **BlobSink**. Query SQL specificata per la proprietà **query** seleziona i dati nell'ultima ora da copiare.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Proprietà del servizio collegate

La tabella seguente contiene descrizione per gli elementi JSON specifici di servizio Redshift Amazon collegato.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| tipo | Impostare la proprietà tipo: **AmazonRedshift**. | Sì |
| Server | Indirizzo IP o host nome del server Redshift di Amazon. | Sì |
| porta | Il numero di porta TCP utilizzato dal server di Amazon Redshift ascoltare le connessioni client. | No, il valore predefinito: 5439 |
| database | Nome del database Redshift di Amazon. | Sì |
| nome utente | Nome dell'utente che ha accesso al database.| Sì |
| password | Password per l'account utente.| Sì |


## <a name="dataset-type-properties"></a>Proprietà dei tipi di set di dati

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per set di dati di tipo **RelationalTable** (che include set di dati di Amazon Redshift) è le seguenti proprietà

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella nel database di Amazon Redshift servizio collegata si riferisce a. | No (se è specificata una **query** di **RelationalSource** ) | 

## <a name="copy-activity-type-properties"></a>Copiare le proprietà di tipo di attività

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e i criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione **typeProperties** dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Quando l'origine di attività di copia è di tipo **RelationalSource** (che include Redshift Amazon) le proprietà seguenti sono disponibili nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilizzare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * dalla tabella. | N (se è specificato **tableName** di **set di dati** ) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Mapping dei tipi di Amazon Redshift

Come indicato nell'articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , Copia attività esegue le conversioni automatica del tipi di origine per elaborare tipi con l'approccio in due passaggi seguenti:

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Quando si spostano dati a Redshift Amazon, i mapping seguenti verranno usati da tipi di Amazon Redshift ai tipi di .NET.

Tipo di Redshift di Amazon | Tipo basato su .NET
-------------------- | ---------------
SMALLINT | Int16
NUMERO INTERO | Int32
BIGINT | Int64
DECIMALE | Decimale
PARTE REALE | Singolo
PRECISIONE DOPPIA | Effettuare un doppio
VALORE BOOLEANO | Stringa
CARATT | Stringa
VARCHAR | Stringa
DATA | DateTime
TIMESTAMP | DateTime
TESTO | Stringa



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 
- [Esercitazione attività Copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per istruzioni dettagliate per la creazione di una pipeline con un'attività di copia. 