<properties 
    pageTitle="Spostare i dati da DB2 | Dati di Azure Factory" 
    description="Informazioni su come spostare i dati da un Database DB2 usando Factory di dati di Azure" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-db2-using-azure-data-factory"></a>Spostare i dati da DB2 utilizzo dei dati di Azure
Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati da DB2 ai dati di un altro archiviare. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

Factory dati supporta la connessione a origini di DB2 locale mediante il Gateway di gestione dati. Vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per imparare a Gateway di gestione dati e istruzioni dettagliate sulla configurazione del gateway. 

> [AZURE.NOTE]
Utilizzare il gateway per connettersi a DB2 anche se è ospitato in macchine virtuali IaaS Azure. Se si sta tentando di connettersi a un'istanza di DB2 ospitato nel cloud, è possibile installare anche l'istanza di gateway VM IaaS.

Dati factory supporta attualmente solo spostamento di dati da DB2 agli altri archivi di dati, ma non da altri archivi di dati a DB2. 

## <a name="installation"></a>Installazione 

Il Gateway di gestione dati offre un driver DB2 predefinito che supporta le operazioni seguenti: 

- SQLAM 9 / 10 / 11
- DB2 per LUW (Linux, Unix, Windows)
- DB2 per z/OS e DB2 per si (nello specifico come / 400)

Pertanto non è necessario installare manualmente i driver quando si copiano dati da DB2.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere [problemi di gateway di risoluzione dei](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) problemi. 

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare i dati da un Database DB2 consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Di seguito sono riportati definizioni JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare i dati da database DB2 e archiviazione Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Esempio: Copiare i dati da DB2 a Blob Azure

In questo esempio viene illustrato come copiare dati da un database DB2 locale a un archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

1.  Servizio collegato di tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

Nell'esempio dati copiati da un risultato della query in un database DB2 blob Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

Come primo passaggio, installare e configurare un gateway di gestione dati. Ulteriori istruzioni sono vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio DB2 collegato:**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }


**Servizio di archiviazione collegata Blob Azure:**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }

**Set di dati input DB2:**

Nell'esempio si presuppone che è stato creato una tabella "Tabella" in DB2 e contiene una colonna denominata "timestamp" per i dati di serie ora.

Impostazione "esterni": vero informa il servizio dati Factory che questo set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati. Si noti che il **tipo** è impostato su **RelationalTable**. 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }


**Archivio Blob Azure output set di dati:**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio.

    {
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **RelationalSource** e tipo **sink** è impostato su **BlobSink**. Query SQL specificata per la proprietà **query** seleziona i dati della tabella ordini.

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>Proprietà del servizio DB2 collegato

La tabella seguente contiene una descrizione per gli elementi JSON specifici di servizio DB2 collegato. 

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| tipo | Impostare la proprietà tipo: **OnPremisesDB2** | Sì |
| Server | Nome del server DB2. | Sì |
| database | Nome del database DB2. | Sì |
| schema | Nome dello schema del database. Il nome dello schema è maiuscole e minuscole. | No |
| authenticationType | Tipo di autenticazione utilizzato per la connessione al database DB2. I valori possibili sono: anonima, Basic e Windows. | Sì |
| nome utente | Specificare il nome utente se si utilizza l'autenticazione di base o di Windows. | No |
| password | Specificare la password per l'account utente specificato per il nome utente. | No |
| gatewayName | Nome del gateway che il servizio dati Factory deve utilizzare per connettersi al database DB2 locale. | Sì |

Per informazioni sull'impostazione credenziali per un'origine dati di DB2 locale, vedere [impostare le credenziali e sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) . 


## <a name="db2-dataset-type-properties"></a>Proprietà dei tipi di set di dati DB2

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per set di dati di tipo RelationalTable (che include set di dati DB2) è le seguenti proprietà.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| tableName | Nome della tabella dell'istanza di Database DB2 collegato servizio fa riferimento a. TableName è maiuscole e minuscole. | No (se è specificata una **query** di **RelationalSource** ) |

## <a name="db2-copy-activity-type-properties"></a>Proprietà del tipo di attività Copia DB2

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e i criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Per copiare attività, l'origine è di tipo **RelationalSource** (che include DB2) le proprietà seguenti sono disponibili nella sezione typeProperties:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------- | -------------- |
| query | Utilizzare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: `"query": "select * from "MySchema"."MyTable""`. | N (se è specificato **tableName** di **set di dati** )|

> [AZURE.NOTE] Nome schema e della tabella sono maiuscole e minuscole. Racchiudere i nomi in "" (doppie) nella query.  

**Esempio:**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Mapping dei tipi di DB2
Come indicato nell'articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatica del tipo di tipi di origine per elaborare tipi con il seguente approccio passaggio 2:

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Quando si sposta dati a DB2, i mapping seguenti vengono utilizzati da DB2 tipo per tipo .NET.

Tipo di Database DB2 | Tipo di .NET framework 
----------------- | ------------------- 
SmallInt | Int16
Numero intero | Int32
BigInt | Int64
Parte reale | Singolo
Effettuare un doppio | Effettuare un doppio
Margine di flessibilità | Effettuare un doppio
Decimale | Decimale
DecimalFloat | Decimale
Valore numerico | Decimale
Data | DateTime
Ora | TimeSpan
Timestamp | DateTime
XML | Byte
Caratt | Stringa
VarChar | Stringa
LongVarChar | Stringa
DB2DynArray | Stringa
Binario | Byte
VarBinary | Byte
LongVarBinary | Byte
Elemento grafico | Stringa
VarGraphic | Stringa
LongVarGraphic | Stringa
CLOB | Stringa
BLOB | Byte
DbClob | Stringa
SmallInt | Int16
Numero intero | Int32
BigInt | Int64
Parte reale | Singolo
Effettuare un doppio | Effettuare un doppio
Margine di flessibilità | Effettuare un doppio
Decimale | Decimale
DecimalFloat | Decimale
Valore numerico | Decimale
Data | DateTime
Ora | TimeSpan
Timestamp | DateTime
XML | Byte
Caratt | Stringa


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.


