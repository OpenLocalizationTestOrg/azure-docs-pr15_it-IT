<properties 
    pageTitle="Spostare i dati a/da un Database di SQL Azure | Microsoft Azure" 
    description="Informazioni su come spostare i dati nel o dal Database di SQL Azure con Azure Data Factory." 
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

# <a name="move-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Spostare i dati da e verso il Database di SQL Azure con Azure Data Factory
Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati o dal Database SQL Azure da/verso un altro archivio dati. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati. 

## <a name="supported-sources-and-sinks"></a>Sink e origini supportate
Vedere la tabella [archivi di dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) per un elenco di archivi di dati supportati come origini o sink dall'attività copia. È possibile spostare dati da qualsiasi archivio di dati di origine supportata al Database SQL Azure o da un Database di SQL Azure in qualsiasi archivio di dati supportati sink.

## <a name="create-pipeline"></a>Creare pipeline
È possibile creare una pipeline con un'attività di copia che consente di spostare dati da/verso un database SQL Azure con diversi strumenti/API.  

- Copia guidata
- Portale di Azure
- Visual Studio
- PowerShell Azure
- API .NET
- API REST

Per istruzioni dettagliate per la creazione di una pipeline con un'attività di copia in diversi modi, vedere [esercitazione attività Copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .   

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare dati nel o dal Database SQL Azure consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Di seguito sono riportati definizioni JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare i dati da e verso il Database di SQL Azure e archiviazione Blob Azure. Dati possono essere copiati **direttamente** da una qualsiasi delle origini su uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.

## <a name="sample-copy-data-from-azure-sql-database-to-azure-blob"></a>Esempio: Copiare i dati da Database SQL Azure a Blob Azure

Lo stesso definisce entità Factory dati seguenti:

1. Servizio collegato di tipo [AzureSqlDatabase](#azure-sql-linked-service-properties).
2. Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un input [set di dati](data-factory-create-datasets.md) di tipo [AzureSqlTable](#azure-sql-dataset-type-properties). 
4. Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [SqlSource](#azure-sql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio di copiare i dati della serie orario (ora, giorno, ecc.) da una tabella di database SQL Azure blob ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi.  

**Servizio collegate SQL Azure**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

Vedere la sezione [Servizio collegata SQL Azure](#azure-sql-linked-service-properties) per l'elenco delle proprietà supportate da questo servizio collegato. 

**Servizio di archiviazione collegata Blob Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Vedere l'articolo [Blob Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) per l'elenco delle proprietà supportate da questo servizio collegato. 

**Set di dati input SQL Azure**

Nell'esempio si presuppone che è stato creato una tabella "Tabella" in SQL Azure e contiene una colonna denominata "timestampcolumn" per i dati di serie ora. 

Impostazione "esterni": "true" il servizio di Azure Data Factory si informa che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

Vedere la sezione [proprietà del tipo di set di dati di SQL Azure](#azure-sql-dataset-type-properties) per l'elenco delle proprietà supportate da questo tipo di set di dati.  

**Set di dati di output Blob Azure**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Vedere la sezione [proprietà di tipo Blob Azure set di dati](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) per l'elenco delle proprietà supportate da questo tipo di set di dati.  

**Pipeline con attività di copia**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **SqlSource** e tipo **sink** è impostato su **BlobSink**. Query SQL specificata per la proprietà **SqlReaderQuery** seleziona i dati nell'ultima ora da copiare.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

Nell'esempio, **sqlReaderQuery** viene specificato per il SqlSource. Attività Copia consente di eseguire la query nell'origine di Database SQL Azure per l'accesso ai dati. In alternativa, è possibile specificare una stored procedure specificando la **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione struttura del set di dati JSON vengono utilizzate per creare una query da eseguire sul Database di SQL Azure. Ad esempio: `select column1, column2 from mytable`. Se la definizione di set di dati non è disponibile la struttura, verranno selezionate tutte le colonne della tabella. 


Vedere la sezione [Origine Sql](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) per l'elenco delle proprietà supportate da SqlSource e BlobSink. 


## <a name="sample-copy-data-from-azure-blob-to-azure-sql-database"></a>Esempio: Copiare i dati da Blob Azure al Database SQL Azure

Nell'esempio viene definito entità Factory dati seguenti:  

1.  Servizio collegato di tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

Le copie di esempio serie temporale dati (ora, giorno, ecc.) da Azure blob a una tabella in SQL Azure database ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 


**Servizio collegate SQL Azure**
    
    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

Vedere la sezione [Servizio collegata SQL Azure](#azure-sql-linked-service-properties) per l'elenco delle proprietà supportate da questo servizio collegato. 

**Servizio di archiviazione collegata Blob Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Vedere l'articolo [Blob Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) per l'elenco delle proprietà supportate da questo servizio collegato.

**Set di dati input Blob Azure**

Dati acquisiti dal nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il cartella percorso e il nome per il blob vengono valutati in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella utilizza anno, mese e giorno parte dell'ora di inizio e nome del file viene utilizzata la parte ora dell'ora di inizio. "esterni": impostazione "true" indica il servizio Factory dati che in questa tabella esterna su factory dati e non viene generata da un'attività nell'ambiente di produzione dati.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
          "fileName": "{Hour}.csv",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

Vedere la sezione [proprietà di tipo Blob Azure set di dati](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) per l'elenco delle proprietà supportate da questo tipo di set di dati.

**Azure set di dati SQL output**

Nell'esempio copia i dati in una tabella denominata "Tabella" in SQL Azure. Creare la tabella in SQL Azure con lo stesso numero di colonne nel modo previsto nel file CSV Blob di. Nuove righe vengono aggiunti alla tabella ogni ora. 

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Vedere la sezione [proprietà del tipo di set di dati di SQL Azure](#azure-sql-dataset-type-properties) per l'elenco delle proprietà supportate da questo tipo di set di dati.

**Pipeline con attività di copia**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **BlobSource** e tipo **sink** è impostato su **SqlSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

Vedere la sezione [Sql Sink](#sqlsink) e [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) per l'elenco delle proprietà supportate da SqlSink e BlobSource. 


## <a name="azure-sql-linked-service-properties"></a>Proprietà del servizio di collegate SQL Azure
Negli esempi, è stato utilizzato un servizio collegato del tipo **AzureSqlDatabase** collegare un database SQL Azure una factory di dati. Nella tabella seguente fornisce descrizione elementi JSON specifici per il servizio SQL Azure collegato.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tipo | Impostare la proprietà tipo: **AzureSqlDatabase** | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza di Database SQL Azure per la proprietà connectionString. | Sì |

> [AZURE.NOTE] Configurare [Firewall di Database SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) il server di database per [consentire ai servizi di Azure ad accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Inoltre, se si siano copiando dati al Database SQL Azure esterno Azure, incluse origini dati locali con gateway factory dati, configurare l'intervallo di indirizzi IP appropriato per il computer per Invia dati a Database SQL Azure. 

## <a name="azure-sql-dataset-type-properties"></a>Proprietà dei tipi di set di dati SQL Azure
Negli esempi, è stato utilizzato un set di dati di tipo **AzureSqlTable** per rappresentare una tabella in un database SQL Azure. 

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.). 

La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **AzureSqlTable** è le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella dell'istanza di Database SQL Azure collegato servizio fa riferimento a. | Sì |

## <a name="azure-sql-copy-activity-type-properties"></a>Proprietà tipo di attività Copia SQL Azure
Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e criteri sono disponibili per tutti i tipi di attività.

> [AZURE.NOTE] Attività Copia accetta un solo input e un solo l'output.

Le proprietà disponibili nella sezione **typeProperties** dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink. 

Se si spostano dati da un database SQL Azure, impostare il tipo di origine nell'attività Copia da **SqlSource**. Allo stesso modo, se si spostano dati a un database SQL Azure, impostare il tipo di sink nell'attività Copia da **SqlSink**. In questa sezione fornisce un elenco delle proprietà supportate da SqlSource e SqlSink. 

### <a name="sqlsource"></a>SqlSource

Attività di copia, quando l'origine è di tipo **SqlSource**, le proprietà seguenti sono disponibili nella sezione **typeProperties** :

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utilizzare la query personalizzata per leggere i dati. | Stringa di query SQL. Esempio: `select * from MyTable`.  | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati della tabella di origine. | Nome della stored procedure. | No |
| storedProcedureParameters | Parametri della stored procedure. | Valore/nome coppie di parole. Nomi e le maiuscole e minuscole dei parametri devono corrispondere i nomi e maiuscole e minuscole della parametri della stored procedure. | No |

Se per il SqlSource viene specificato **sqlReaderQuery** , l'attività di copia consente di eseguire questa query nell'origine di Database SQL Azure per l'accesso ai dati. In alternativa, è possibile specificare una stored procedure specificando la **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri). 

Se non si specifica sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione struttura del set di dati JSON vengono usate per creare una query (`select column1, column2 from mytable`) per eseguire nel Database di SQL Azure. Se la definizione di set di dati non è disponibile la struttura, verranno selezionate tutte le colonne della tabella. 

> [AZURE.NOTE] Quando si utilizza **sqlReaderStoredProcedureName**, è necessario specificare un valore per la proprietà **tableName** di set di dati JSON. Sono non disponibili convalida eseguita attraverso questa tabella. 

### <a name="sqlsource-example"></a>Esempio di SqlSource

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Definizione della stored procedure:** 

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### <a name="sqlsink"></a>SqlSink 

**SqlSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch completare prima del timeout. | TimeSpan<br/><br/> Esempio: "00:30:00" (30 minuti). | No | 
| writeBatchSize | Inserisce dati nella tabella di SQL quando le dimensioni di buffer raggiungono writeBatchSize. | Numero intero (numero di righe)| Non (impostazione predefinita: 10000)
| sqlWriterCleanupScript | Specificare una query per copia attività da eseguire in modo che pulizia dei dati di una sezione specifica. Per ulteriori informazioni, vedere [la sezione ripetibilità](#repeatability-during-copy). | Un'istruzione di query.  | No |
| sliceIdentifierColumnName | Specificare un nome di colonna per l'attività di copia di riempimento con identificatore di sezione generato automaticamente, che consente di pulizia dei dati di una sezione specifica quando eseguire di nuovo. Per ulteriori informazioni, vedere [la sezione ripetibilità](#repeatability-during-copy). | Nome di colonna di una colonna con tipo di dati di binary(32). | No |
| sqlWriterStoredProcedureName | Nome della stored procedure che upserts (aggiornamenti/inserimenti) i dati nella tabella di destinazione. | Nome della stored procedure. | No |
| storedProcedureParameters | Parametri della stored procedure. | Valore/nome coppie di parole. Nomi e le maiuscole e minuscole dei parametri devono corrispondere i nomi e maiuscole e minuscole della parametri della stored procedure. | No | 
| sqlWriterTableType | Specificare un nome di tipo tabella da utilizzare nella stored procedure. Attività Copia rende i dati vengano spostati disponibili in una tabella temporanea con questo tipo di tabella. Codice stored procedure possibile unire i dati vengono copiati con i dati esistenti. | Un nome di tipo tabella. | No |

#### <a name="sqlsink-example"></a>Esempio di SqlSink

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }

## <a name="identity-columns-in-the-target-database"></a>Colonne di identità nel database di destinazione
In questa sezione viene fornito un esempio per la copia di dati da una tabella di origine senza una colonna di identità a una tabella di destinazione con una colonna di identità. 

**Tabella di origine:** 

    create table dbo.SourceTbl
    (
           name varchar(100),
           age int
    )

**Tabella di destinazione:**

    create table dbo.TargetTbl
    (
           id int identity(1,1),
           name varchar(100),
           age int
    )


Si noti che la tabella di destinazione contiene una colonna di identità. 

**Definizione di JSON set di dati di origine**

    {
        "name": "SampleSource",
        "properties": {
            "type": " SqlServerTable",
            "linkedServiceName": "TestIdentitySQL",
            "typeProperties": {
                "tableName": "SourceTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }

**Definizione di JSON set di dati di destinazione**

    {
        "name": "SampleTarget",
        "properties": {
            "structure": [
                { "name": "name" },
                { "name": "age" }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "TestIdentitySQLSource",
            "typeProperties": {
                "tableName": "TargetTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": false,
            "policy": {}
        }   
    }


Si noti che, come la tabella di origine e destinazione hanno schema diverso (destinazione ha una colonna aggiuntiva con identità). In questo scenario, è necessario specificare proprietà **struttura** nella definizione del set di dati di destinazione, che non include la colonna di identità. 

È quindi mappare le colonne di set di dati di origine per le colonne nel set di dati di destinazione. Vedere la sezione [esempi di mapping di colonna](#column-mapping-samples) per un esempio. 

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server--azure-sql-database"></a>Mapping dei tipi per SQL Server e Database di SQL Azure

Come detto attività Copia articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) esegue le conversioni automatica del tipo da tipi di origine per elaborare tipi con il seguente approccio passaggio 2:

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Quando si sposta dati dai SQL Azure, SQL server, Sybase mapping seguenti vengono utilizzati dal tipo SQL al tipo di .NET e viceversa.

Il mapping è come il Mapping di tipo di dati di SQL Server per ADO.NET.

| Tipo di motore di Database di SQL Server | Tipo di .NET framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binario | Byte |
| bit | Valore booleano |
| caratt | Stringa, Char] |
| Data | DateTime |
| DateTime | DateTime |
| datetime2 | DateTime |
| DateTimeOffset | DateTimeOffset |
| Decimale | Decimale |
| Attributo FILESTREAM (varbinary(max)) | Byte |
| Margine di flessibilità | Effettuare un doppio |
| immagine | Byte | 
| int | Int32 | 
| denaro | Decimale |
| nchar | Stringa, Char] |
| ntext | Stringa, Char] |
| numerico | Decimale |
| nvarchar | Stringa, Char] |
| parte reale | Singolo |
| RowVersion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimale | 
| sql_variant | Oggetto * |
| testo | Stringa, Char] |
| ora | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | GUID |
| varbinary |  Byte |
| varchar | Stringa, Char] |
| XML | XML |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.