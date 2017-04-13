<properties 
    pageTitle="Spostare i dati da origini OData | Dati di Azure Factory" 
    description="Informazioni su come spostare i dati da origini OData tramite Azure Data Factory." 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Spostare i dati di origine da OData tramite Factory di dati di Azure
Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare i dati da un'origine OData a un altro archivio di dati. In questo articolo si basa su articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

> [AZURE.NOTE] Il supporto di connettore OData copia di dati da entrambe cloud OData e origini locali OData. Per informazioni, è necessario installare il Gateway di gestione dati. Vedere l'articolo [spostare dati tra locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate su Gateway di gestione dati.

## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare i dati da un'origine OData consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Di seguito sono riportati definizioni JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare i dati da un'origine OData a un'archiviazione Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Esempio: Copiare i dati da un'origine OData a Blob Azure

In questo esempio viene illustrato come copiare i dati da un'origine OData all'archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

1.  Servizio collegato del tipo di [OData](#odata-linked-service-properties).
2.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [ODataResource](#odata-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [RelationalSource](#odata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio copia dati da query su un'origine OData a un archivio blob Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

**OData collegato servizio** In questo esempio viene usata l'autenticazione di base. Vedere la sezione [OData collegato servizio](#odata-linked-service-properties) per diversi tipi di autenticazione è possibile utilizzare. 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**Set di dati OData input**

Impostazione "esterni": "true" servizio Factory dati si informa che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

**Percorso** nel set di dati definizione è facoltativo. 


**Set di dati di output Blob Azure**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La pipeline contiene un'attività di copia che è configurato per usare i set di dati di input e di output e pianificato per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **RelationalSource** e tipo **sink** è impostato su **BlobSink**. Query SQL specificata per la proprietà **query** seleziona i dati (dal più recente) più recenti dal sito di origine OData.
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


**Query** nella definizione del pipeline è facoltativo. L' **URL** utilizzato dal servizio Factory dati per recuperare i dati: URL specificato nel servizio collegato (obbligatorio) + percorso specificato nel set di dati (facoltativo) + query nella pipeline (facoltativa). 

## <a name="odata-linked-service-properties"></a>OData collegato proprietà del servizio

Nella tabella seguente fornisce una descrizione per gli elementi JSON specifici di servizio OData collegato.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| tipo | Impostare la proprietà tipo: **OData** | Sì |
| URL| URL del servizio di OData. | Sì |
| authenticationType | Tipo di autenticazione utilizzata per connettersi all'origine OData. <br/><br/> Per il cloud OData, i valori possibili sono anonimo e Basic. OData locale, i valori possibili sono anonima, Basic e Windows. | Sì | 
| nome utente | Specificare il nome utente se si utilizza l'autenticazione di base. | Sì (solo se si utilizza l'autenticazione di base) | 
| password | Specificare la password per l'account utente specificato per il nome utente. | Sì (solo se si utilizza l'autenticazione di base) | 
| gatewayName | Nome del gateway che il servizio dati Factory deve utilizzare per connettersi al servizio OData locale. Specificare solo se si desidera copiare i dati da un'origine OData locale. | No |

### <a name="using-basic-authentication"></a>Mediante l'autenticazione di base

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>Utilizza l'autenticazione anonima
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Mediante l'autenticazione di Windows accesso locale OData origine

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>Proprietà dei tipi di set di dati OData

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per set di dati di tipo **ODataResource** (che include set di dati OData) è le seguenti proprietà

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| percorso | Percorso della risorsa OData | No | 

## <a name="odata-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia OData

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Quando l'origine è di tipo **RelationalSource** (che include OData) le proprietà seguenti sono disponibili nella sezione typeProperties:

| Proprietà | Descrizione | Esempio | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilizzare la query personalizzata per leggere i dati. | "? $select = nome, descrizione e $top = 5" | No | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Mapping dei tipi di OData

Come indicato nell'articolo [le attività di spostamento dei dati](data-factory-data-movement-activities.md) , Copia attività esegue le conversioni automatica del tipi di origine per elaborare tipi con il seguente approccio passaggio 2:

1. Convertire i tipi di origine nativi nel tipo di .NET
2. Convertire dal tipo di .NET tipo sink nativo

Quando si archivia spostamento di dati da dati OData, tipi di dati OData sono mappati a tipi di .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.

