<properties 
    pageTitle="Spostare i dati da locale HDFS | Dati di Azure Factory" 
    description="Informazioni su come spostare i dati da HDFS locali con Azure Data Factory." 
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

# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Spostare i dati da HDFS locali con Azure Data Factory
Questo articolo illustra come è possibile utilizzare l'attività di copia di una factory di dati di Azure per spostare dati da un HDFS locale a un altro archivio dati. In questo articolo si basa sull'articolo di [attività di spostamento dei dati](data-factory-data-movement-activities.md) che viene fornita una panoramica generale di spostamento con attività di copia e le combinazioni di archivio di dati supportati.

Dati factory supporta attualmente solo spostare i dati da un HDFS locale per altri archivi dati, ma non per spostare dati da altri archivi di dati in un HDFS locale.


## <a name="enabling-connectivity"></a>Abilitazione della connettività
Servizio Data Factory supporta la connessione a HDFS locale utilizzando il Gateway di gestione dati. Vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per imparare a Gateway di gestione dati e istruzioni dettagliate sulla configurazione del gateway. Utilizzare il gateway per connettersi a HDFS, anche se è ospitato in una macchina virtuale IaaS Azure. 

Durante l'installazione di gateway nello stesso computer locale o la macchina virtuale Azure come la HDFS, è consigliabile installare il gateway su un distinta machine/Azure IaaS macchine Virtuali. Visto gateway su un computer separato riduce conflitti tra le risorse e migliorare le prestazioni. Quando si installa il gateway su un computer separato, il computer dovrebbe essere possibile accedere al computer con l'HDFS. 


## <a name="copy-data-wizard"></a>Copia guidata dati
Il modo più semplice per creare una pipeline che consente di copiare i dati dal HDFS locale consiste nell'utilizzare la procedura guidata dati copia. Vedere [Esercitazione: creare una pipeline utilizzando Copia guidata](data-factory-copy-data-wizard-tutorial.md) per una rapida procedura dettagliata sulla creazione di una pipeline utilizzando Copia guidata dati. 

Di seguito sono riportati definizioni JSON di esempio che è possibile utilizzare per creare una pipeline tramite [portal Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Verranno visualizzati come copiare dati da un HDFS locale a un archivio Blob Azure. Tuttavia, dati possono essere copiati in uno del sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Esempio: Copiare i dati da HDFS locale a Blob Azure

In questo esempio viene illustrato come copiare i dati da un HDFS locale all'archivio Blob Azure. Dati possono essere copiati **direttamente** al sink indicate [di seguito](data-factory-data-movement-activities.md#supported-data-stores) utilizzando l'attività di copia in Azure Data Factory.  
 
Nell'esempio è entità factory dati seguenti:

1.  Servizio collegato di tipo [OnPremisesHdfs](#hdfs-linked-service-properties).
2.  Servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  Un input [set di dati](data-factory-create-datasets.md) di tipo [FileShare](#hdfs-dataset-type-properties).
4.  Un output [set di dati](data-factory-create-datasets.md) di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Pipeline](data-factory-create-pipelines.md) con attività di copia che utilizza [FileSystemSource](#hdfs-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio dati copiati da un HDFS locale blob Azure ogni ora. Le proprietà JSON utilizzate in questi esempi sono descritti nelle sezioni seguenti gli esempi. 

Come primo passaggio, configurare il gateway di gestione dati. Istruzioni vedere l'articolo di [spostamento di dati tra ambienti locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) . 

**HDFS collegato servizio** In questo esempio viene usata l'autenticazione di Windows. Vedere la sezione [HDFS collegato servizio](#hdfs-linked-service-properties) per diversi tipi di autenticazione è possibile utilizzare. 

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
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

**Set di dati di input HDFS** Questo set di dati fa riferimento alla cartella HDFS DataTransfer/UnitTest /. La pipeline di copia tutti i file in questa cartella di destinazione. 

Impostazione "esterni": "true" servizio Factory dati si informa che il set di dati esterno su factory dati e non viene generato da un'attività nell'ambiente di produzione di dati.
    
    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Set di dati di output Blob Azure**

I dati vengono scritti in un nuovo blob ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il blob viene valutato in modo dinamico in base alla data di inizio della sezione che viene elaborata. Il percorso della cartella Usa anno, mese, giorno e parti di ore dell'ora di inizio.

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

La pipeline contiene un'attività di copia è configurato per utilizzare questi set di dati di input e di output, che è programmata per l'esecuzione di ogni ora. Pipeline definizione JSON, il tipo di **origine** è impostato su **FileSystemSource** e tipo **sink** è impostato su **BlobSink**. Query SQL specificata per la proprietà **query** seleziona i dati nell'ultima ora da copiare.
    
    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>Proprietà del servizio collegata HDFS

La tabella seguente contiene una descrizione per gli elementi JSON specifici di HDFS collegato servizio.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| tipo | Impostare la proprietà tipo: **Hdfs** | Sì | 
| URL | URL per il HDFS | Sì |
| encryptedCredential | [Nuovo AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) l'output delle credenziali di accesso. | No |
| nome utente | Autenticazione di nome utente per Windows. | Sì (per l'autenticazione di Windows)
| password | Password per l'autenticazione di Windows. | Sì (per l'autenticazione di Windows)
| authenticationType | Windows, o anonimi. | Sì |
| gatewayName | Nome del gateway utilizzate il servizio Factory dati a cui connettersi il HDFS. | Sì |   

Per informazioni dettagliate sull'impostazione credenziali per HDFS locale, vedere [impostare le credenziali e sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

### <a name="using-anonymous-authentication"></a>Utilizza l'autenticazione anonima

    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>Mediante l'autenticazione di Windows
    
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }
 


## <a name="hdfs-dataset-type-properties"></a>Proprietà dei tipi di set di dati HDFS

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione set di dati](data-factory-create-datasets.md) . Le sezioni, ad esempio struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL di Azure, blob Azure, tabelle Azure, ecc.).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per set di dati di tipo **FileShare** (che include set di dati HDFS) è le seguenti proprietà

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
folderPath | Percorso della cartella. Esempio:`myfolder`<br/><br/>Utilizzare il carattere di escape "\" per i caratteri speciali nella stringa. Ad esempio: per folder\subfolder, specificare cartella\\\\sottocartella e per d:\samplefolder, specificare d:\\\\EsempioFolder.<br/><br/>È possibile combinare questa proprietà con **partitionBy** di percorsi delle cartelle in base a sezione Data-ora inizio/fine. | Sì
fileName | Specificare il nome del file in **folderPath** se si desidera che la tabella per fare riferimento a un file specifico nella cartella. Se non si specifica un valore per questa proprietà, la tabella rimanda a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato dovrebbero essere seguito in questo formato: <br/><br/>Dati. <Guid>txt (ad esempio:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | No
partitionedBy | partitionedBy può essere utilizzato per specificare un folderPath dinamico, nome del file di dati della serie ora. Esempio: folderPath parametrizzata per ogni ora di dati. | No
fileFilter | Specificare un filtro da utilizzare per selezionare un sottoinsieme di file nel folderPath piuttosto che tutti i file. <br/><br/>Valori consentiti: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempio 1:`"fileFilter": "*.log"`<br/>Esempio 2:`"fileFilter": 2014-1-?.txt"`<br/><br/>**Nota**: fileFilter è applicabile per un set di dati di input FileShare | No
| formato | Sono supportati i tipi di formato seguenti: **formato testo**, **AvroFormat**, **JsonFormat**, **OrcFormat**e **ParquetFormat**. Impostare la proprietà di **tipo** in formato a uno dei seguenti valori. Vedere le sezioni di [Formato testo che specifica](#specifying-textformat), [Specificando AvroFormat](#specifying-avroformat), [Specificando JsonFormat](#specifying-jsonformat), [Specificando OrcFormat](#specifying-orcformat)e [Specificando ParquetFormat](#specifying-parquetformat) per informazioni dettagliate. Se si desidera copiare i file come-è tra archivi basati su file (copia binario), è possibile ignorare la sezione formato in entrambe le definizioni di set di dati di input e di output. | No 
| compressione | Specificare il tipo e il livello di compressione per i dati. Sono supportati: **GZip**, **Deflate**e **BZip2** e livelli supportati sono: **ottimale** e **più veloce**. Impostazioni di compressione attualmente non sono supportate per i dati in **AvroFormat** o **OrcFormat**. Per ulteriori informazioni, vedere la sezione [supporto per la compressione](#compression-support) .  | No |



> [AZURE.NOTE] nome file e fileFilter non possono essere utilizzate contemporaneamente.


### <a name="using-partionedby-property"></a>Utilizzo di proprietà partionedBy

Come indicato nella sezione precedente, è possibile specificare un folderPath dinamico nome file per i dati di serie ora con partitionedBy. È possibile eseguire con le macro di dati Factory e la variabile di sistema SliceStart, SliceEnd che indicano il periodo di tempo logico per una sezione di dati specificato. 

Per ulteriori informazioni su come set di dati di ora serie, pianificazione e le sezioni, vedere gli articoli di [Creazione di set di dati](data-factory-create-datasets.md), [pianificazione ed esecuzione](data-factory-scheduling-and-execution.md)e [La creazione di condutture](data-factory-create-pipelines.md) . 

#### <a name="sample-1"></a>Esempio 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

In questo esempio {sezione} viene sostituito con il valore della variabile di sistema di Data Factory SliceStart nel formato (YYYYMMDDHH) specificato. Il SliceStart fa riferimento per l'ora di inizio della sezione. Il folderPath è diverso per ogni sezione. Ad esempio: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Esempio 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

In questo esempio, anno, mese, giorno e ora di SliceStart vengono estratti in variabili separate utilizzati dalle proprietà folderPath e il nome file.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Proprietà di tipo HDFS Copia attività

Per un elenco completo delle sezioni e le proprietà disponibili per la definizione di attività, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md) . Proprietà, ad esempio nome, descrizione, input e output tabelle e i criteri sono disponibili per tutti i tipi di attività. 

Le proprietà disponibili nella sezione typeProperties dell'attività possono essere invece a ogni tipo di attività. Per l'attività di copia, variano in base ai tipi di origini e sink.

Per copiare attività, l'origine è di tipo **FileSystemSource** le proprietà seguenti sono disponibili nella sezione typeProperties:

**FileSystemSource** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| ricorsive | Indica se i dati vengono letti in modo ricorsivo le sottocartelle e viceversa solo nella cartella specificata. | True, False (impostazione predefinita)| No |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>L'ottimizzazione delle prestazioni  
Vedere [le prestazioni di attività di copia e Tuning Guide](data-factory-copy-activity-performance.md) per informazioni sui fattori che impatto sulle prestazioni di spostamento dei dati (copia attività) in diversi modi per ottimizzare la e Azure Data Factory.

