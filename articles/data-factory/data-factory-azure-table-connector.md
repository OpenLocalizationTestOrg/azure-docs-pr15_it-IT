<properties 
    pageTitle="Spostare i dati a/da tabella Azure | Microsoft Azure" 
    description="Informazioni su come spostare i dati da/verso archivio tabelle Azure con Azure Data Factory." 
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
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Spostare i dati da e verso tabella Azure usando Factory di dati di Azure

Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati da/verso tabella Azure da/verso un altro archivio dati. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento dei dati e le combinazioni di archivio di dati supportati con attività di copia.

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare dati dal piano archivio tabelle Azure consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Di seguito sono riportati definizioni JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare i dati nel e dall'archivio tabelle Azure e Database Blob Azure. Dati possono essere copiati **direttamente** da una qualsiasi delle origini su uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>Esempio: Copiare i dati dalla tabella Azure a Blob Azure

Nell'esempio seguente viene:

1.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilizzato per tabella & blob).
2.  Un input [set di dati](data-factory-create-datasets.md) di tipo [Azure](#azure-table-dataset-type-properties).
3.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [AzureTableSource](#azure-table-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

Nell'esempio copia i dati che appartengono a partizione predefinita in una tabella di Azure in un blob ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi.

**Servizio di archiviazione Azure collegato:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory supporta due tipi di servizi di archiviazione di Azure collegato: **AzureStorage** e **AzureStorageSas**. Per il primo, specificare la stringa di connessione che include la chiave account e per la versione più recente, specificare l'Uri condiviso accesso firma (SA). Per ulteriori informazioni vedere la sezione [Servizi collegati](#linked-services) .  

**Set di dati input tabella Azure:**

Nell'esempio si presuppone che aver creato una tabella "Tabella" nella tabella di Azure.
 
Impostazione "esterni": "true" servizio Factory dati si informa che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
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

**Archivio Blob Azure output set di dati:**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio. 

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

**Pipeline con l'attività di copia:**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **AzureTableSource** e tipo **sink** è impostato su **BlobSink**. Query SQL specificata con **AzureTableSourceQuery** proprietà seleziona i dati da quella predefinita ogni ora per copiare.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>Esempio: Copiare i dati da Blob Azure a tabella di Azure

Nell'esempio seguente viene:

1.  Un servizio collegato del tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilizzato per tabella & blob)
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [Azure](#azure-table-dataset-type-properties). 
4.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [AzureTableSink](#azure-table-copy-activity-type-properties). 


Le copie di esempio serie temporale dati da un Azure blob in un Azure tabella ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi.

**Servizio di archiviazione Azure (per tabella Azure & Blob) collegato:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory supporta due tipi di servizi di archiviazione di Azure collegato: **AzureStorage** e **AzureStorageSas**. Per il primo, specificare la stringa di connessione che include la chiave account e per la versione più recente, specificare l'Uri condiviso accesso firma (SA). Per ulteriori informazioni vedere la sezione [Servizi collegati](#linked-services) . 

**Set di dati input Blob Azure:**

Dati acquisiti dal nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il cartella percorso e il nome per il blob vengono valutati in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella utilizza anno, mese e giorno parte dell'ora di inizio e nome del file viene utilizzata la parte ora dell'ora di inizio. "esterni": impostazione "true" indica il servizio Factory dati che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.
    
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

**Tabelle di Azure output set di dati:**

Nell'esempio copia i dati in una tabella denominata "Tabella" nella tabella di Azure. Creare una tabella di Azure con lo stesso numero di colonne nel modo previsto nel file CSV Blob di. Nuove righe vengono aggiunti alla tabella ogni ora. 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline con l'attività di copia:**

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **BlobSource** e tipo **sink** è impostato su **AzureTableSink**. 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
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

## <a name="linked-services"></a>Servizi collegati
Esistono due tipi di servizi collegati, che è possibile utilizzare per creare un collegamento a una factory di dati di Azure un'archiviazione blob Azure. Sono: **AzureStorage** collegato servizi e **AzureStorageSas** collegato. Il servizio di archiviazione di Azure collegato fornisce factory dati con access globale per l'archiviazione di Azure. Invece di Azure archiviazione SA (condiviso accesso firma) collegato servizio fornisce factory dati con l'accesso con restrizioni/associate alla durata per l'archiviazione di Azure. Ci sono altre differenze tra questi due servizi collegate. Scegliere il servizio collegato alle proprie esigenze. Nelle sezioni seguenti vengono forniscono ulteriori informazioni su questi due servizi collegati.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Proprietà di tipo Azure set di dati di tabella

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per il set di dati di tipo **Azure** è le seguenti proprietà.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| tableName | Nome della tabella dell'istanza di Database di Azure tabella collegata servizio fa riferimento a. | Sì. Quando si specifica un nome tableName senza un azureTableSourceQuery, tutti i record della tabella vengono copiati alla destinazione. Se si specifica anche un azureTableSourceQuery, i record della tabella che soddisfa la query vengono copiati alla destinazione. |

### <a name="schema-by-data-factory"></a>Schema da dati Factory
Per gli archivi dati senza schema, ad esempio tabella Azure, il servizio dati Factory deriva lo schema in uno dei modi seguenti:

1.  Se si specifica la struttura dei dati utilizzando la proprietà **struttura** nella definizione del set di dati, il servizio dati Factory rispetta questa struttura dello schema. In questo caso, se una riga non contiene alcun valore per una colonna, per renderla viene fornito un valore null.
2. Se non si specifica la struttura dei dati utilizzando la proprietà **struttura** nella definizione del set di dati, Data Factory deriva lo schema utilizzando la prima riga nei dati. In questo caso, se la prima riga contiene lo schema completo, alcune colonne vengono persi nel risultato dell'operazione di copia.

Pertanto, per le origini dati senza schema, la procedura consigliata consiste nello specificare la struttura dei dati utilizzando la proprietà di **struttura** .

## <a name="azure-table-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia tabella Azure

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output set di dati e i criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Nella sezione typeProperties, **AzureTableSource** supporta le proprietà seguenti:

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Utilizzare la query personalizzata per leggere i dati. | Stringa di query tabelle Azure. Vedere gli esempi nella sezione successiva. | No. Quando si specifica un nome tableName senza un azureTableSourceQuery, tutti i record della tabella vengono copiati alla destinazione. Se si specifica anche un azureTableSourceQuery, i record della tabella che soddisfa la query vengono copiati alla destinazione.
azureTableSourceIgnoreTableNotFound | Indicare se deve assorbire l'eccezione della tabella non esiste. | VERO<br/>FALSO | No |

### <a name="azuretablesourcequery-examples"></a>esempi di azureTableSourceQuery

Se la colonna di tabella Azure è di tipo stringa: 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Se la colonna di tabella Azure è di tipo datetime: 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


Nella sezione typeProperties, **AzureTableSink** supporta le proprietà seguenti:


Proprietà | Descrizione | Valori consentiti | Obbligatorio  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Partizione chiave valore predefinito che può essere usate dal sink. | Un valore stringa. | No 
azureTablePartitionKeyName | Nome della colonna cui valori vengono utilizzati come chiavi partizione. Se non viene specificato, AzureTableDefaultPartitionKeyValue viene utilizzato come chiave di partizione. | Nome di colonna. | No |
azureTableRowKeyName | Nome della colonna cui valori di colonna vengono utilizzati come chiave di riga. Se non viene specificato, utilizzare un GUID per ogni riga. | Nome di colonna. | No  
azureTableInsertType | La modalità per inserire dati in tabelle Azure.<br/><br/>Questa proprietà controlla se i rispettivi valori sostituiti o uniti righe esistenti nella tabella di output con chiavi partizione e riga corrispondenti. <br/><br/>Per informazioni sul funzionano di queste impostazioni (unione e Sostituisci), vedere [inserimento o giuridiche unione](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [inserimento o sostituzione di entità](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Questa impostazione viene applicata a livello di riga, non il livello di tabella e nessuna opzione Elimina righe nella tabella di output non presenti nell'input. | unione (impostazione predefinita)<br/>Sostituisci | No 
writeBatchSize | Inserisce dati nella tabella di Azure quando viene raggiunto il writeBatchSize o writeBatchTimeout. | Numero intero (numero di righe)| Non (impostazione predefinita: 10000) 
writeBatchTimeout | Consente di inserire dati in tabelle Azure quando viene raggiunto il writeBatchSize o writeBatchTimeout | TimeSpan<br/><br/>Esempio: "00: 20:00" (20 minuti) | Non (impostazioni predefinite per lo spazio di archiviazione client predefinito timeout valore 90 sec)

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mappare una colonna di origine a una colonna di destinazione mediante il convertitore proprietà JSON prima di poter utilizzare la colonna di destinazione come la azureTablePartitionKeyName.

Nell'esempio seguente la colonna di origine DivisionID sia associata alla colonna di destinazione: DivisionID.  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

Il DivisionID viene specificato come chiave di partizione. 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Mapping dei tipi di tabelle Azure

Come indicato nell'articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , Copia attività esegue le conversioni automatica del tipi di origine per elaborare tipi con l'approccio in due passaggi seguenti.

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Quando si sposta dati su & dalla tabella di Azure, il seguente [mapping definite dal servizio di Azure tabella](https://msdn.microsoft.com/library/azure/dd179338.aspx) vengono utilizzati da tipi di Azure tabella OData in tipo .NET e viceversa. 

| Tipo di dati OData | Tipo .NET | Dettagli |
| --------------- | --------- | ------- |
| Edm.Binary | byte | Matrice di byte fino a 64 KB. |
| Edm.Boolean | bool | Un valore Boolean. |
| EDM | DateTime | Un valore a 64 bit espresso come ora UTC (Coordinated Universal Time). Intervallo DateTime supportato inizia da mezzanotte, 1 gennaio 1601 D.C. (D.C.) UTC. L'intervallo termina il 31 dicembre 9999. |
| EDM | doppia | Valore a virgola mobile a 64 bit. |
| Edm.Guid | GUID | Identificatore univoco globale a 128 bit. |
| Edm.Int32 | Int32 o int | Intero a 32 bit. |
| Edm.Int64 | Int64 o long | Un intero a 64 bit. |
| Edm.String | Stringa | Un valore con codifica UTF-16. Valori stringa possono essere fino a 64 KB. |

### <a name="type-conversion-sample"></a>Esempio di conversione di tipo

Nell'esempio seguente è per la copia di dati da un archivio Blob Azure alla tabella Azure con le conversioni. 

Si supponga che il set di dati Blob siano in formato CSV e contiene tre colonne. Uno di essi è una colonna datetime con un formato datetime personalizzati utilizzando nomi francese abbreviati per giorno della settimana. 

Definire il set di dati di origine Blob come indicato di seguito insieme definizione dei tipi di colonne.
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
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

Dato il mapping dei tipi dal tipo di Azure tabella OData in tipo .NET, definire la tabella nella tabella di Azure con il seguente schema. 

**Azure schema della tabella:**

Nome di colonna | Tipo
----------- | --------
ID utente | Edm.Int64
nome | Edm.String 
LastLoginDate | EDM

Successivamente, definire il set di dati di Azure tabella come indicato di seguito. Non è necessario specificare la sezione "struttura" con le informazioni sul tipo, dal momento che le informazioni sul tipo è già specificati nell'archivio dati sottostante.

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

In questo caso, Data Factory automaticamente la conversione dei tipi tra cui il campo di data e ora nel formato datetime personalizzati utilizzando le impostazioni cultura "fr-fr" quando si spostano i dati dal Blob Azure tabella.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Per altre informazioni su fattori chiave che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory, vedere [Tuning Guide e le prestazioni del attività di copia](data-factory-copy-activity-performance.md).







