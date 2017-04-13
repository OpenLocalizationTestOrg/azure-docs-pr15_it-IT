<properties 
    pageTitle="Spostare i dati da MySQL | Dati di Azure Factory" 
    description="Informazioni su come spostare i dati da un database MySQL usando Azure Data Factory." 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-mysql-using-azure-data-factory"></a>Spostare i dati da MySQL utilizzo dei dati di Azure

Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati da MySQL ai dati di un altro archiviare. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

Servizio Data Factory supporta la connessione a origini di MySQL locale mediante il Gateway di gestione dati. Vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per imparare a Gateway di gestione dati e istruzioni dettagliate sulla configurazione del gateway. 

> [AZURE.NOTE] Gateway è necessario anche se il database MySQL è ospitato in una Azure IaaS macchine (). È possibile installare il gateway in macchine Virtuali stesso come archivio dati o in una macchina virtuale diversa, purché il gateway è possibile connettersi al database.  

Dati factory supporta attualmente solo spostare i dati da MySQL a altri archivi dati, ma non per spostare dati da altri archivi di dati in MySQL.

## <a name="installation"></a>Installazione 
Per Gateway di gestione dati per connettersi al MySQL Database, è necessario installare [MySQL connettore/Net 6.6.5 per Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) nello stesso sistema Gateway di gestione dati.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere [problemi di gateway di risoluzione dei](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) problemi. 

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare i dati da un database MySQL a uno degli archivi di dati supportati sink consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Nell'esempio seguente vengono fornite le definizioni di JSON di esempio che è possibile utilizzare per creare una pipeline. Per una procedura dettagliata completa con istruzioni dettagliate, vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) . Dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>Esempio: Copiare i dati da MySQL a Blob Azure
In questo esempio viene illustrato come copiare dati da un database MySQL locale in un archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  

> [AZURE.IMPORTANT] In questo esempio viene frammenti JSON. Non include istruzioni dettagliate per la creazione della factory di dati. Per istruzioni dettagliate, vedere [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) . 
 
Nell'esempio è entità factory dati seguenti:

1.  Servizio collegato di tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties).
2.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio dati copiati dal risultato di una query di database MySQL blob ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

Come primo passaggio, installare il gateway di gestione dati. Le istruzioni disponibili nell'articolo [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) . 

**Servizio MySQL collegato**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
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

**Set di dati input MySQL**

Nell'esempio si presuppone aver creato una tabella "Tabella" nel MySQL e contiene una colonna denominata "timestampcolumn" per i dati di serie ora.

Impostazione "esterni": "true" servizio Factory dati si informa che la tabella esterna su factory dati e non prodotto da un'attività nell'ambiente di produzione di dati.
    
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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



**Set di dati di output Blob Azure**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio.

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
        "name": "CopyMySqlToBlob",
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
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>Proprietà del servizio MySQL collegato

Nella tabella seguente fornisce una descrizione per gli elementi JSON specifici di servizio MySQL collegato.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| tipo | Impostare la proprietà tipo: **OnPremisesMySql** | Sì |
| Server | Nome del server MySQL. | Sì |
| database | Nome del database MySQL. | Sì | 
| schema  | Nome dello schema del database. | No | 
| authenticationType | Tipo di autenticazione utilizzato per connettersi al database MySQL. I valori possibili sono: anonima, Basic e Windows. | Sì | 
| nome utente | Specificare il nome utente se si utilizza l'autenticazione di base o di Windows. | No | 
| password | Specificare la password per l'account utente specificato per il nome utente. | No | 
| gatewayName | Nome del gateway che il servizio dati Factory deve utilizzare per connettersi al database MySQL locale. | Sì |

Per informazioni dettagliate sull'impostazione credenziali per un'origine dati di MySQL locale, vedere [impostare le credenziali e sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="mysql-dataset-type-properties"></a>Proprietà dei tipi di set di dati MySQL

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per set di dati di tipo **RelationalTable** (che include set di dati MySQL) è le seguenti proprietà

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella dell'istanza di MySQL Database collegato servizio fa riferimento a. | No (se è specificata una **query** di **RelationalSource** ) | 

## <a name="mysql-copy-activity-type-properties"></a>Proprietà del tipo di attività Copia MySQL

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione e le tabelle di input e di output, sono i criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione **typeProperties** dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Quando l'origine in attività di copia è di tipo **RelationalSource** (che include MySQL) le proprietà seguenti sono disponibili nella sezione typeProperties:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilizzare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * dalla tabella. | N (se è specificato **tableName** di **set di dati** ) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>Mapping dei tipi di MySQL

Come indicato nell'articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , Copia attività esegue le conversioni automatica del tipi di origine per elaborare tipi con l'approccio in due passaggi seguenti:

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Quando si spostano dati a MySQL, i mapping seguenti vengono utilizzati da tipi di MySQL per i tipi di .NET.

| Tipo di MySQL Database | Tipo di .NET framework |
| ------------------- | ------------------- | 
| bigint unsigned | Decimale |
| bigint | Int64 |
| bit | Decimale |
| BLOB | Byte |
| bool |  Valore booleano | 
| caratt | Stringa |
| Data | DateTime |
| DateTime | DateTime |
| decimale | Decimale |
| precisione doppia | Effettuare un doppio |
| doppia | Effettuare un doppio |
| enumerazione | Stringa |
| margine di flessibilità | Singolo |
| int senza segno | Int64 |
| int | Int32 |
| numero intero senza segno | Int64 |
| numero intero | Int32 | 
| varbinary lungo | Byte |
| varchar lungo | Stringa |
| longblob | Byte |
| LONGTEXT | Stringa | 
| mediumblob |  Byte | 
| mediumint senza segno | Int64 |
| mediumint | Int32 | 
| mediumtext | Stringa |
| numerico | Decimale |
| parte reale |  Effettuare un doppio |
| impostare | Stringa |
| unsigned smallint | Int32 |
| smallint | Int16 |
| testo | Stringa |
| ora | TimeSpan |
| timestamp | DateTime |
| tinyblob | Byte |
| tinyint senza segno |  Int16 | 
| tinyint | Int16 |
| tinytext | Stringa | 
| varchar | Stringa | 
| anno | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.

