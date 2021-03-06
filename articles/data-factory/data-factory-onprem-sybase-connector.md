<properties 
    pageTitle="Spostare i dati da Sybase | Dati di Azure Factory" 
    description="Informazioni su come spostare i dati da un Sybase Database usando Azure Data Factory." 
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

# <a name="move-data-from-sybase-using-azure-data-factory"></a>Spostare i dati da Sybase utilizzo dei dati di Azure 

Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati da Sybase a un altro archivio di dati. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

Servizio Data Factory supporta la connessione a origini di Sybase locale mediante il Gateway di gestione dati. Vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per imparare a Gateway di gestione dati e istruzioni dettagliate sulla configurazione del gateway. 

> [AZURE.NOTE]
> Anche se il database di Sybase è ospitato in una macchina virtuale IaaS Azure, è necessario gateway. È possibile installare il gateway VM IaaS stesso come archivio dati o in una macchina virtuale diversa, purché il gateway è possibile connettersi al database. 

Dati factory supporta attualmente solo spostamento di dati da Sybase agli altri archivi di dati, ma non da altri archivi di dati a Sybase.

## <a name="installation"></a>Installazione

Per Gateway di gestione dati per connettersi al Sybase Database, è necessario installare il [provider di dati per Sybase](http://go.microsoft.com/fwlink/?linkid=324846) nello stesso sistema Gateway di gestione dati.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione dei problemi di connessione/gateway, vedere [problemi di gateway di risoluzione dei](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) problemi. 

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare dati da un database Sybase uno degli archivi di dati supportati sink consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Nell'esempio seguente vengono fornite le definizioni di JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare i dati da un database Sybase all'archivio Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.   

## <a name="sample-copy-data-from-sybase-to-azure-blob"></a>Esempio: Copiare i dati da Sybase a Blob Azure
In questo esempio viene illustrato come copiare dati da un database Sybase a un archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

1.  Servizio collegato di tipo [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties).
2.  Servizio positivo di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio dati copiati dal risultato di una query nel database di Sybase blob ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

Come primo passaggio, installare il gateway di gestione dati. Le istruzioni disponibili nell'articolo [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio di Sybase collegato:**

    {
        "name": "OnPremSybaseLinkedService",
        "properties": {
            "type": "OnPremisesSybase",
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


**Set di dati input Sybase:**

Nell'esempio si presuppone che è stato creato una tabella "Tabella" in Sybase e contiene una colonna denominata "timestamp" per i dati di serie ora.

Impostazione "esterni": vero informa il servizio dati Factory che questo set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati. Si noti che il **tipo** di servizio collegato è impostato su: **RelationalTable**. 
    
    {
        "name": "SybaseDataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremSybaseLinkedService",
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
        "name": "AzureBlobSybaseDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **RelationalSource** e tipo **sink** è impostato su **BlobSink**. Query SQL specificata per la proprietà **query** seleziona i dati dall'amministratore del database. Tabella Orders nel database.


    {
        "name": "CopySybaseToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from DBA.Orders"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "SybaseDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobSybaseDataSet"
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
                    "name": "SybaseToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="sybase-linked-service-properties"></a>Proprietà del servizio di Sybase collegato

La tabella seguente contiene descrizione per gli elementi JSON specifici al servizio di Sybase collegato.

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tipo | Impostare la proprietà tipo: **OnPremisesSybase** | Sì
Server | Nome del server di Sybase. | Sì
database | Nome del database di Sybase. | Sì 
schema  | Nome dello schema del database. | No
authenticationType | Tipo di autenticazione utilizzato per la connessione al database di Sybase. I valori possibili sono: anonima, Basic e Windows. | Sì
nome utente | Specificare il nome utente se si utilizza l'autenticazione di base o di Windows. | No
password | Specificare la password per l'account utente specificato per il nome utente. |  No
gatewayName | Nome del gateway che il servizio dati Factory deve utilizzare per connettersi al database di Sybase locale. | Sì 

Per informazioni sull'impostazione credenziali per un'origine dati di Sybase locale, vedere [impostare le credenziali e sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="sybase-dataset-type-properties"></a>Proprietà dei tipi di set di dati di Sybase

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per set di dati di tipo **RelationalTable** (che include set di dati di Sybase) è le proprietà seguenti:

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
tableName | Nome della tabella dell'istanza di Sybase Database collegato servizio fa riferimento a. | No (se è specificata una **query** di **RelationalSource** )

## <a name="sybase-copy-activity-type-properties"></a>Proprietà del tipo di attività Copia Sybase 

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [pipeline di creazione](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Quando l'origine è di tipo **RelationalSource** (che include Sybase) le proprietà seguenti sono disponibili nella sezione **typeProperties** :

Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | --------
query | Utilizzare la query personalizzata per leggere i dati. | Stringa di query SQL. Ad esempio: selezionare * dalla tabella. | N (se è specificato **tableName** di **set di dati** )

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-sybase"></a>Mapping dei tipi di Sybase

Come indicato nell'articolo [Le attività di spostamento dei dati](data-factory-data-movement-activities.md) , l'attività di copia esegue conversioni automatica del tipo di tipi di origine per elaborare tipi con il seguente approccio passaggio 2:

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Sybase supporta T-SQL e i tipi di T-SQL. Per una tabella di mapping tra tipi di sql in tipo .NET, vedere [Connettore di SQL Azure](data-factory-azure-sql-connector.md) articolo.

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.