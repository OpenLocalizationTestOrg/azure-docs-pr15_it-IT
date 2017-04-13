<properties
   pageTitle="Caricare grandi quantità di dati nell'archivio Lake metodi offline | Microsoft Azure"
   description="Utilizzare lo strumento AdlCopy per copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio dati Lake"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Utilizzare il servizio di importazione-esportazione Azure per copia fuori rete di dati in archivio Lake dati

In questo articolo apprenderà come copiare enorme set di dati (> 200GB) in un archivio di Lake di dati di Azure tramite metodi di copia non in linea, ad esempio [Servizio di importazione/esportazione Azure](../storage/storage-import-export-service.md). In particolare, il file utilizzato come esempio in questo articolo è 339,420,860,416 byte ovvero circa 319GB su disco. Di seguito chiamare 319GB.tsv questo file.

Servizio di importazione/esportazione Azure consente di sicuro trasferire grandi quantità di dati a archiviazione blob Azure base spedizione unità disco rigido per un centro di dati di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Account di archiviazione di azure**.

- **Account azure dati Lake Analitica (facoltativo)** -, vedere [Guida introduttiva di Azure dati Lake Analitica](../data-lake-analytics/data-lake-analytics-get-started-portal.md) per istruzioni su come creare un account di archivio di dati Lake.


## <a name="preparing-the-data"></a>Preparare i dati

Prima di utilizzare il servizio di importazione/esportazione, è necessario interrompere il file di dati per essere trasferiti **in copie che sono meno di 200 GB** dimensioni. Ciò avviene perché lo strumento di importazione non funziona con file maggiori di 200GB. Rispettare la verifica, in questa esercitazione si dividere il file in blocchi di byte 100GB. È possibile eseguire questa operazione facilmente tramite [Cygwin](https://cygwin.com/install.html). Cygwin supporta comando Linux che consente agli utenti di eseguire questa operazione con facilità. Per questo caso è utilizzare il comando seguente.

    split -b 100m 319GB.tsv

L'operazione di divisione crea file con i nomi riportata di seguito.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Preparare i dati di dischi

Seguire le istruzioni in [Uso del servizio di importazione/esportazione Azure](../storage/storage-import-export-service.md) (nella sezione **preparare le unità** ) per preparare le unità disco rigido. Ecco il flusso generale su come preparare l'unità.

1. Acquisizione delle risorse un disco rigido che soddisfa i requisiti da utilizzare per il servizio di importazione/esportazione Auzre.

2. Identificare un account di archiviazione Azure in cui i dati saranno stati copiati una volta si spediti al centro dati Azure.

3. Usare lo [Strumento di importazione/esportazione Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), un'utilità della riga di comando. Ecco un frammento di esempio su come usare lo strumento.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Per ulteriori frammenti di esempio su come usare lo **Strumento di importazione/esportazione Azure**, vedere [Utilizzo del servizio di importazione/esportazione Azure](../storage/storage-import-export-service.md) .

4. Il comando crea un file diario nella posizione specificata. Creare un processo di importazione dal [Portale classica Azure](https://manage.windowsazure.com)si utilizzerà il file del diario.

## <a name="create-an-import-job"></a>Creare un processo di importazione

È ora possibile creare un processo di importazione usando le istruzioni incluse [all'Utilizzo del servizio di importazione/esportazione Azure](../storage/storage-import-export-service.md) (nella sezione **creare il processo di importazione** ). Per il processo di importazione con altri dettagli, offrono il file di registro creato durante la preparazione di unità disco.

## <a name="physically-ship-the-disks"></a>Consegna fisica dischi

A questo punto è possibile spedire fisica dischi a un centro di dati di Azure in cui i dati da copiare i BLOB di spazio di archiviazione di Azure fornito durante la creazione del processo di importazione. Inoltre, durante la creazione del processo, se si è scelto per fornire le informazioni di verifica in seguito, è ora possibile accedere nuovamente per il processo di importazione e aggiornato il numero di registrazione.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copiare i dati da Azure lo spazio di archiviazione BLOB Azure dati Lake Store

Una volta che viene visualizzato lo stato del processo di importazione completato, è possibile verificare se i dati sono disponibili nell'archiviazione BLOB fosse stato specificato. È quindi possibile utilizzare diversi metodi per spostare i dati dall'archiviazione BLOB all'archivio Lake dati di Azure. Per tutte le opzioni disponibili per il caricamento dei dati, vedere [dati Ingesting all'archivio Lake dati](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

In questa sezione viene fornito le definizioni di JSON che è possibile utilizzare per creare una pipeline di Azure Data Factory per la copia di dati. È possibile utilizzare queste definizioni JSON dal [portale di Azure](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md) [Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Origine collegata servizio di archiviazione Azure Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Destinazione collegata servizio (Azure dati Lake Store)

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Set di dati di input
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Set di dati di output
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (copia attività)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Per ulteriori informazioni su come utilizzare Azure Data Factory per spostare i dati da Azure lo spazio di archiviazione Blob e Azure dati Lake Store, vedere [spostare dati da Azure lo spazio di archiviazione Blob Azure dati Lake Store con Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Ricostruire i file di dati di Azure dati Lake archivio

Viene avviato con un file che è stata 319GB in dimensioni e si è interrotta verso il basso nel file di dimensioni minori, in modo che possono essere trasferito mediante il servizio di importazione/esportazione Azure. Ora che i dati sono nell'archivio Lake dati di Azure, è possibile reconstrcut il file alle dimensioni originali. È possibile utilizzare le seguenti cmldts Azure PowerShell per farlo.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Passaggi successivi

- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
