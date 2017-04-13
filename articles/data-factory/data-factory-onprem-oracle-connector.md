<properties 
    pageTitle="Spostare i dati a / dall'utilizzo di dati Oracle | Microsoft Azure" 
    description="Informazioni su come spostare i dati nel o dal database Oracle locali con Azure Data Factory." 
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

# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Spostare i dati da/verso Oracle locali con Azure Data Factory 

Questo articolo illustra come è possibile utilizzare l'attività Copia factory dei dati per spostare i dati da/verso Oracle da/verso un altro dati archiviare. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

## <a name="installation"></a>Installazione 
Per il servizio di Azure Data Factory in grado di connettersi al database Oracle locale, è necessario installare i componenti seguenti: 

- Gateway di gestione dati nello stesso computer che ospita i database o su un computer separato per evitare concorrenti per le risorse con il database. Gateway di gestione dati è un agente client si connette di origini dati locali ai servizi cloud in modo sicuro e gestito. Vedere l'articolo [spostare dati tra locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate su Gateway di gestione dati. 
- Provider di dati Oracle per .NET. Questo componente è incluso in [Oracle dati Access componenti per Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installare la versione appropriata (32 o 64 bit) nel computer host in cui è installato il gateway. [12.1 di .NET Provider di dati Oracle](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) possono accedere al Database Oracle 10g Release 2 o versione successiva.

    Se si sceglie "Installazione XCopy", seguire i passaggi di Leggimi. htm. Si consiglia di che scegliere il programma di installazione con interfaccia utente (non-XCopy uno). 
 
    Dopo l'installazione del provider, riavviare il servizio Host del Gateway gestione dati nel computer in uso tramite servizi applet (o) Gestione configurazione di Gateway di gestione di dati.  

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere [problemi di gateway di risoluzione dei](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) problemi. 

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare dati da/verso un database Oracle uno degli archivi di dati supportati sink consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Nell'esempio seguente vengono fornite le definizioni di JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare i dati da/verso un database Oracle da/verso archiviazione Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Esempio: Copiare i dati da Oracle a Blob Azure
In questo esempio viene illustrato come copiare dati da un database Oracle locale in un archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

1.  Servizio collegato di tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) come origine e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) come sink.

Nell'esempio di copiare dati da una tabella in un database Oracle locale blob ogni ora. Per ulteriori informazioni sulle diverse proprietà utilizzata in questo esempio, vedere la documentazione nelle sezioni seguenti gli esempi.

**Servizio Oracle collegata:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Servizio di archiviazione collegata Blob Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Set di dati input Oracle:**

Nell'esempio si presuppone che è stato creato una tabella "Tabella" in Oracle e contiene una colonna denominata "timestampcolumn" per i dati di serie ora. 

Impostazione "esterni": "true" servizio Factory dati si informa che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
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


**Archivio Blob Azure output set di dati:**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il cartella percorso e il nome per il blob vengono valutati in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio.
    
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **OracleSource** e tipo **sink** è impostato su **BlobSink**.  Query SQL specificata con **oracleReaderQuery** proprietà seleziona i dati nell'ultima ora da copiare.

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


È necessario regolare la stringa di query in base a configurazione delle date in un database Oracle. Se viene visualizzato il messaggio di errore seguente: 

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

potrebbe essere necessario modificare la query, come illustrato nell'esempio seguente viene (usando la funzione to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Esempio: Copiare i dati da Blob Azure a Oracle
In questo esempio viene illustrato come copiare dati da un archivio Blob Azure a un database Oracle locale. Dati possono essere copiati **direttamente** da qualsiasi le origini indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

1.  Servizio collegato di tipo [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
5.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) come [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) come sink di origine.

Nell'esempio consente di copiare dati da un blob a una tabella in un database Oracle locale ogni ora. Per ulteriori informazioni sulle diverse proprietà utilizzata in questo esempio, vedere la documentazione nelle sezioni seguenti gli esempi.

**Servizio Oracle collegata:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Servizio di archiviazione collegata Blob Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Set di dati input Blob Azure**

Dati acquisiti dal nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il cartella percorso e il nome per il blob vengono valutati in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella utilizza anno, mese e giorno parte dell'ora di inizio e nome del file viene utilizzata la parte ora dell'ora di inizio. "esterni": impostazione "true" indica il servizio Factory dati che in questa tabella esterna su factory dati e non viene generata da un'attività nell'ambiente di produzione dati.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Set di output dati Oracle:**

Nell'esempio si presuppone creata una tabella "Tabella" Oracle. Creare la tabella in Oracle con lo stesso numero di colonne nel modo previsto nel file CSV Blob di. Nuove righe vengono aggiunti alla tabella ogni ora.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Pipeline con attività di copia:**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **BlobSource** e il tipo di **sink** è impostato su **OracleSink**.  

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Proprietà del servizio Oracle collegato

La tabella seguente contiene una descrizione per gli elementi JSON specifici di servizio Oracle collegato. 

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tipo | Impostare la proprietà tipo: **OnPremisesOracle** | Sì
connectionString | Specificare le informazioni necessarie per connettersi all'istanza di Database Oracle per la proprietà connectionString. | Sì 
gatewayName | Nome del gateway che viene utilizzato per connettersi al server Oracle locale | Sì

Per informazioni dettagliate sull'impostazione credenziali per un'origine di dati Oracle locale, vedere [impostare le credenziali e sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .
## <a name="oracle-dataset-type-properties"></a>Proprietà dei tipi di set di dati Oracle

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (Oracle, blob Azure, tabelle Azure e così via).
 
La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo OracleTable è le proprietà seguenti:

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tableName | Nome della tabella nel Database Oracle che fa riferimento il servizio collegato a. | N (se è specificato **oracleReaderQuery** di **OracleSource** )

## <a name="oracle-copy-activity-type-properties"></a>Proprietà del tipo di attività Copia Oracle

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e criteri sono disponibili per tutti i tipi di attività. 

> [AZURE.NOTE]
Attività Copia accetta un solo input e un solo l'output.

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

### <a name="oraclesource"></a>OracleSource
Attività di copia, quando l'origine è di tipo **OracleSource** le proprietà seguenti sono disponibili nella sezione **typeProperties** :

Proprietà | Descrizione |Valori consentiti | Obbligatorio
-------- | ----------- | ------------- | --------
oracleReaderQuery | Utilizzare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare *dalla tabella <br/> <br/>se non viene specificato, l'istruzione SQL che viene eseguita: selezionare* dalla tabella | N (se è specificato **tableName** di **set di dati** )

### <a name="oraclesink"></a>OracleSink
**OracleSink** supporta le proprietà seguenti:

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch completare prima del timeout. | TimeSpan<br/><br/> Esempio: 00:30:00 (30 minuti). | No
writeBatchSize | Inserisce dati nella tabella di SQL quando le dimensioni di buffer raggiungono writeBatchSize.   | Numero intero (numero di righe)| Non (impostazione predefinita: 10000)  
sqlWriterCleanupScript | Specificare una query per copia attività da eseguire in modo che pulizia dei dati di una sezione specifica. | Un'istruzione di query. | No
sliceIdentifierColumnName | Specificare il nome di colonna per l'attività di copia di riempimento con identificatore di sezione generato automaticamente, che consente di pulizia dei dati di una sezione specifica quando eseguire di nuovo. | Nome di colonna di una colonna con tipo di dati di binary(32). | No


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Mapping dei tipi di Oracle

Come detto attività Copia articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) esegue le conversioni automatica del tipo da tipi di origine per elaborare tipi con il seguente approccio passaggio 2:

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Quando si spostano i dati da Oracle, i mapping seguenti vengono utilizzati dal tipo di dati Oracle al tipo di .NET e viceversa.

Tipo di dati Oracle | Tipo di dati .NET framework
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CARATT | Stringa
CLOB | Stringa
DATA | DateTime
MARGINE DI FLESSIBILITÀ | Decimale
NUMERO INTERO | Decimale
ANNO DI INTERVALLO AL MESE | Int32
GIORNO DELL'INTERVALLO PER SECONDO | TimeSpan
LUNGO | Stringa
TEMPO NON ELABORATI | Byte
NCHAR | Stringa
NCLOB | Stringa
NUMERO | Decimale
NVARCHAR2 | Stringa
NON ELABORATO | Byte
ROWID | Stringa
TIMESTAMP | DateTime
TIMESTAMP CON FUSO ORARIO | DateTime
TIMESTAMP AL FUSO ORARIO | DateTime
INTERO SENZA SEGNO | Numero
VARCHAR2 | Stringa
XML | Stringa

## <a name="troubleshooting-tips"></a>Suggerimenti sulla risoluzione dei problemi

**Problema:** Viene visualizzato il seguente **messaggio di errore**: attività Copia soddisfatte parametri non validi: 'UnknownParameterName', messaggio dettagliato: Impossibile trovare richiesto .net Framework Provider di dati. Potrebbe non essere installato".  

**Le possibili cause:**

1. Non è stato installato il Provider di dati .NET Framework per Oracle.
2. Il Provider di dati .NET Framework per Oracle per .NET Framework 2.0 è stato installato e non viene trovato nelle cartelle di .NET Framework 4.0. 

**Risoluzione/soluzione alternativa:**

1. Se è stato installato il Provider di .NET per Oracle, [installarlo](http://www.oracle.com/technetwork/topics/dotnet/downloads/) e riprovare allo scenario. 
2. Se viene visualizzato il messaggio di errore anche dopo l'installazione del provider, eseguire la procedura seguente: 
    1. Aprire la configurazione automatica di .NET 2.0 rispetto alla cartella: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Ricerca per **Il Provider di dati Oracle per .NET**e si dovrebbe essere possibile trovare una voce, come illustrato nella unwn di esempio seguente la seguente esempio di annullamentoder **system.data** -> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  Copiare questa voce al file Machine. config nella cartella 4.0 seguente: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config e modificare la versione 4.xxx.x.x.
3.  Installare "< percorso di installazione ODP.NET > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" nella cache globale (cache globale) eseguendo `gacutil /i [provider path]`.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.
