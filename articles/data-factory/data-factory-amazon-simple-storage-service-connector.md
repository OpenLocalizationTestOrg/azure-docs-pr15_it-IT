<properties 
    pageTitle="Spostare i dati da Amazon semplice servizio di archiviazione di utilizzo di dati | Microsoft Azure" 
    description="Informazioni su come spostare i dati dal servizio di archiviazione semplice Amazon (S3) con Azure Data Factory." 
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
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Spostare i dati da Amazon semplice servizio di archiviazione di utilizzo dei dati di Azure

Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati da Amazon semplice il servizio di archiviazione (S3) ai dati di un altro archiviare. In questo articolo si basa sull'articolo di [attività di spostamento dei dati](data-factory-data-movement-activities.md) che viene presentata una panoramica generale di spostamento dei dati e un elenco di archivi di dati di origine/sink supportati con attività di copia.  

Dati factory supporta attualmente solo spostare i dati da Amazon S3 agli altri archivi di dati, ma non per spostare i dati da altri archivi dati a S3 Amazon.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per copiare i dati da Amazon S3, assicurarsi che siano state concesse le autorizzazioni indicate seguito:

- **S3:GetObject** e **s3:GetObjectVersion** per le operazioni di Amazon S3 oggetto
- **S3:ListBucket** e **s3:ListAllMyBuckets** (utilizzato in copia guidata solo) per le operazioni di Amazon S3 intervallo 

È possibile trovare l'elenco completo delle autorizzazioni di Amazon S3 con dettagli da [Specificare le autorizzazioni in un criterio](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare i dati da Amazon S3 consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Nell'esempio seguente vengono fornite le definizioni di JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Viene illustrato come copiare i dati da Amazon S3 all'archivio Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Esempio: Copiare i dati da Amazon S3 a Blob Azure
In questo esempio viene illustrato come copiare dati da un S3 Amazon a un archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

- Servizio collegato di tipo [AwsAccessKey](#linked-service-properties).
- Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un input [set di dati](data-factory-create-datasets.md) di tipo [AmazonS3](#dataset-type-properties).
- Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [FileSystemSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio dati copiati da Amazon S3 blob Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

**Servizio di Amazon S3 collegato**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
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

**Set di dati input S3 Amazon**

Impostazione **"esterni": true** informa il servizio Factory dati che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati. Impostare questa proprietà su true in un set di dati di input non prodotto da un'attività nella pipeline.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
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
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **FileSystemSource** e tipo **sink** è impostato su **BlobSink**. 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Proprietà del servizio collegate

Nella tabella seguente fornisce una descrizione per gli elementi JSON specifici per Amazon S3 (**AwsAccessKey**) collegato servizio.

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | ID del tasto segreta. | stringa | Sì |
| secretAccessKey | Segreta tasto stesso. | Stringa segreta crittografata | Sì | 


## <a name="dataset-type-properties"></a>Proprietà dei tipi di set di dati

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per set di dati di tipo **AmazonS3** (che include set di dati di Amazon S3) è le seguenti proprietà

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------- | ------ | 
| bucketName | Il nome dell'intervallo S3. | Stringa | Sì |
| chiave | Il tasto oggetto S3. | Stringa | No | 
| prefisso | Prefisso per la chiave oggetto S3. Oggetti le cui chiavi iniziano con il prefisso siano selezionati. Si applica solo quando la chiave è vuota. | Stringa | No | 
| Versione | La versione dell'oggetto S3 in caso affermativo S3. | Stringa | No |  
| formato | Sono supportati i tipi di formato seguenti: **formato testo**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Impostare la proprietà di **tipo** in formato a uno dei seguenti valori. Vedere le sezioni di [Formato testo che specifica](#specifying-textformat), [Specificando AvroFormat](#specifying-avroformat), [Specificando JsonFormat](#specifying-jsonformat), [Specificando OrcFormat](#specifying-orcformat)e [Specificando ParquetFormat](#specifying-parquetformat) per informazioni dettagliate. Se si desidera copiare i file come-è tra archivi basati su file (copia binario), è possibile ignorare la sezione formato in entrambe le definizioni di set di dati di input e di output.| No
| compressione | Specificare il tipo e il livello di compressione per i dati. Sono supportati: **GZip**, **Deflate**e **BZip2** e livelli supportati sono: **ottimale** e **più veloce**. Attualmente, le impostazioni di compressione non sono supportate per i dati in **AvroFormat** o **OrcFormat**. Per ulteriori informazioni, vedere la sezione [supporto per la compressione](#compression-support) .  | No |

> [AZURE.NOTE] bucketName + tasto specifica la posizione dell'oggetto S3 nel punto in cui intervallo è il contenitore radice per gli oggetti S3 e chiave è il percorso completo dell'oggetto S3.

### <a name="sample-dataset-with-prefix"></a>Set di dati di esempio con prefisso

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>Set di dati di esempio (con una versione)

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Percorsi dinamici per S3

In questo esempio viene serve valori fissi per le proprietà chiave e bucketName nel set di dati di Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

È possibile impostare Data Factory calcolare la chiave e bucketName dinamicamente in fase di esecuzione usando le variabili di sistema, ad esempio SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

È possibile eseguire la stessa operazione per la proprietà prefisso di un set di dati di Amazon S3. Per un elenco di variabili e funzioni supportate, vedere [funzioni di Data Factory e le variabili di sistema](data-factory-functions-variables.md) . 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>Copiare le proprietà di tipo di attività

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e i criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione **typeProperties** dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Quando l'origine in attività di copia è di tipo **FileSystemSource** (che include Amazon S3), le proprietà seguenti sono disponibili nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- | 
| ricorsive | Specifica se ricorsivo elenco S3 oggetti all'interno della directory. | vero/falso | No | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 
- [Esercitazione attività Copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per istruzioni dettagliate per la creazione di una pipeline con un'attività di copia. 
