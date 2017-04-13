<properties 
    pageTitle="Spostare i dati da/a DocumentDB | Microsoft Azure" 
    description="Informazioni su come spostare i dati a / dall'insieme di Azure DocumentDB utilizzo dei dati di Azure" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Spostare i dati da e verso DocumentDB utilizzo dei dati di Azure

Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare dati di Azure DocumentDB dai dati di un altro archiviare e spostare dati da DocumentDB a un altro archivio dati. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

Negli esempi seguenti viene illustrato come copiare i dati da e verso DocumentDB Azure e archiviazione Blob Azure. Dati possono essere copiati **direttamente** da una qualsiasi delle origini su uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  

> [AZURE.NOTE] Copia di dati da dati IaaS nella distribuzione locale/Azure memorizza DocumentDB Azure e viceversa è supportata con Gateway di gestione dati versione 2.1 e versioni successive.

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>Esempio: Copiare i dati da DocumentDB a Blob Azure

Nell'esempio seguente viene illustrato:

1. Servizio collegato di tipo [DocumentDb](#azure-documentdb-linked-service-properties).
2. Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un input [set di dati](data-factory-create-datasets.md) di tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Dati di esempio copiati in Azure DocumentDB Blob Azure. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi.

**Servizio DocumentDB collegato Azure:**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Servizio di archiviazione collegata Blob Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Set di dati input documento DB Azure:**

L'esempio presuppone che si abbia un insieme denominato **persona** in un database di Azure DocumentDB.
 
Impostazione "esterni": "true" e specificare le informazioni di criteri externalData il servizio di Azure Data Factory che la tabella esterna su factory dati e non prodotto da un'attività nell'ambiente di produzione di dati.

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Archivio Blob Azure output set di dati:**

Dati vengono copiati in un nuovo blob ogni ora con il percorso per il blob che riflettono datetime specifica con granularità ora.

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Documento JSON di esempio nella raccolta di persona in un database DocumentDB: 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB supporta documenti query utilizzando SQL come sintassi sui documenti JSON gerarchici. 

Esempio: Selezionare Person.Name.Middle Person.PersonId, Person.Name.First come nome, come MiddleName, come Person.Name.Last cognome dalla persona

La pipeline seguente copia dati dalla raccolta persona nel database DocumentDB blob Azure. Come parte dell'attività di copia di input e output set di dati sono stati specificati.  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>Esempio: Copiare i dati da Blob Azure a DocumentDB Azure

Nell'esempio seguente viene illustrato:

1. Servizio collegato di tipo [DocumentDb](#azure-documentdb-linked-service-properties).
2. Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un input [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un output [set di dati](data-factory-create-datasets.md) di tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


Nell'esempio copia i dati da Azure blob di Azure DocumentDB. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi.

**Servizio di archiviazione collegata Blob Azure:**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Servizio DocumentDB collegato Azure:**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Set di dati input Blob Azure:**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Azure DocumentDB set di dati di output:**

Nell'esempio copia dati in una raccolta denominata "Utente".

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Pipeline seguente consente di copiare dati dal Blob Azure all'insieme persona la DocumentDB. Come parte dell'attività di copia di input e output set di dati sono stati specificati. 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
Se l'input blob di esempio è possibile eseguire 

    1,John,,Doe

L'output JSON in DocumentDB sarà come:

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB è un archivio NoSQL per i documenti JSON, in cui sono consentite strutture annidate. Azure Data Factory consente utente indicare gerarchia tramite **nestingSeparator**, ovvero "." In questo esempio. Con il separatore, l'attività di copia genererà l'oggetto "Nome" con elementi tre figlio prima di tutto, secondo nome e cognome, in base al "Name.First", "Name.Middle". "Cognome" nella definizione della tabella.

## <a name="azure-documentdb-linked-service-properties"></a>Proprietà servizi collegati DocumentDB Azure

La tabella seguente contiene una descrizione per gli elementi JSON specifici di servizio DocumentDB Azure collegato. 

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| -------- | ----------- | --------- |
| tipo | Impostare la proprietà tipo: **DocumentDb** | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi al database DocumentDB Azure. | Sì |

## <a name="azure-documentdb-dataset-type-properties"></a>Proprietà di tipo DocumentDB Dataset Azure

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).
 
La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **DocumentDbCollection** è le seguenti proprietà.

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| -------- | ----------- | -------- |
| NomeInsieme | Nome della raccolta documenti di DocumentDB. | Sì |


Esempio:

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>Schema da dati Factory
Per gli archivi dati senza schema, ad esempio DocumentDB, il servizio dati Factory deriva lo schema in uno dei modi seguenti:  

1.  Se si specifica la struttura dei dati utilizzando la proprietà **struttura** nella definizione del set di dati, il servizio dati Factory rispetta questa struttura dello schema. In questo caso, se una riga non contiene alcun valore per una colonna, un valore null verrà fornito per esso.
2.  Se non si specifica la struttura dei dati utilizzando la proprietà **struttura** nella definizione del set di dati, il servizio dati Factory deriva lo schema utilizzando la prima riga nei dati. In questo caso, se la prima riga contiene lo schema completo, alcune colonne saranno disponibile nel risultato dell'operazione di copia.

Pertanto, per le origini dati senza schema, la procedura consigliata consiste nello specificare la struttura dei dati utilizzando la proprietà di **struttura** .

## <a name="azure-documentdb-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia DocumentDB Azure

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e criteri sono disponibili per tutti i tipi di attività.
 
**Nota:** Attività Copia accetta un solo input e un solo l'output.

Le proprietà disponibili nella sezione typeProperties dell'attività invece variano a ogni tipo di attività e in caso di attività Copia variano a seconda dei tipi di origini e sink.

In caso di attività Copia quando origine è di tipo **DocumentDbCollectionSource** le proprietà seguenti sono disponibili nella sezione **typeProperties** :

| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| ------------ | --------------- | ------------------ | ------------ |
| query | Specificare la query per leggere i dati. | Stringa supportata dalla DocumentDB della query. <br/><br/>Esempio:`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | No <br/><br/>Se non viene specificato, l'istruzione SQL che viene eseguito:`select <columns defined in structure> from mycollection` 
| nestingSeparator | Carattere speciale per indicare che il documento è nidificato | Qualsiasi carattere. <br/><br/>DocumentDB è un archivio NoSQL per i documenti JSON, in cui sono consentite strutture annidate. Azure Data Factory consente utente indicare gerarchia tramite nestingSeparator, ovvero "." Negli esempi precedenti. Con il separatore, l'attività di copia genererà l'oggetto "Nome" con elementi tre figlio prima di tutto, secondo nome e cognome, in base al "Name.First", "Name.Middle". "Cognome" nella definizione della tabella. | No

**DocumentDbCollectionSink** supporta le proprietà seguenti:

| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | È necessario un carattere speciale nel nome di colonna di origine per indicare che il documento annidato. <br/><br/>Ad esempio sopra: `Name.First` nell'output della tabella, viene prodotta la struttura JSON seguente nel documento DocumentDB:<br/><br/>"Nome": {<br/>  "Prima": "John"<br/>}, | Carattere utilizzato per separare i livelli di annidamento.<br/><br/>Valore predefinito è `.` (punto). | Carattere utilizzato per separare i livelli di annidamento. <br/><br/>Valore predefinito è `.` (punto). | No | 
| writeBatchSize | Numero di richieste parallele al servizio DocumentDB per creare documenti.<br/><br/>È possibile ottimizzare le prestazioni quando si copiano dati da/verso DocumentDB tramite questa proprietà. È possibile prevedere prestazioni migliori quando si aumenta writeBatchSize poiché vengono inviate più richieste parallele a DocumentDB. Tuttavia è necessario evitare la limitazione che può generare il messaggio di errore: "Richiesta tasso di grandi dimensioni".<br/><br/>Limitazione è deciso di un numero di fattori, tra cui dimensioni dei documenti, numero di termini in documenti, indicizzazione criteri della raccolta di destinazione e così via. Per le operazioni di copia, è possibile utilizzare una raccolta migliore (ad esempio S3) per avere il massimo velocità disponibili (2500 richiesta unità/secondo). | Numero intero | Non (impostazione predefinita: 10000) |
| writeBatchTimeout | Tempo di attesa per l'operazione da eseguire prima del timeout. | TimeSpan<br/><br/> Esempio: "00:30:00" (30 minuti). | No |
 
## <a name="appendix"></a>Appendice
1. **Domanda:**  
    esegue l'aggiornamento del supporto di attività di copia di record esistente?

    **Risposta:**  
    No.

2. **Domanda:**  
    funzionamento di una copia in caso di DocumentDB di ritentare già copiato record?

    **Risposta:**  
    se l'operazione di copia tenta di inserire un record con lo stesso ID record hanno un campo "ID", l'operazione di copia genera un errore.  
 
3. **Domanda:** 
    Data Factory supporta [intervallo o dati basate su hash partizioni](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)? 

    **Risposta:** 
    No. 
4. **Domanda:** 
    è possibile specificare più di un insieme di DocumentDB per una tabella?
    
    **Risposta:** 
    No. È possibile specificare un unico insieme al momento.
     
## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.
