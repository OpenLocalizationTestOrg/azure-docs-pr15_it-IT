<properties
   pageTitle="Caricare dati dallo spazio di archiviazione blob Azure nell'archivio di dati di SQL Azure, Azure Data Factory | Microsoft Azure"
   description="Informazioni su come caricare i dati con Azure Data Factory"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-from-azure-blob-storage-into-azure-sql-data-warehouse-azure-data-factory"></a>Caricare dati dallo spazio di archiviazione blob Azure nell'archivio di dati di SQL Azure, Azure Data Factory,

> [AZURE.SELECTOR]
- [Dati Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

 In questa esercitazione viene illustrato come creare una pipeline di Azure Data Factory per spostare i dati da Azure lo spazio di archiviazione Blob SQL Data Warehouse. Con la procedura seguente è necessario:

+ Dati di esempio di configurazione in un Blob di spazio di archiviazione Azure.
+ Connettere risorse Azure Data Factory.
+ Creare una pipeline per spostare i dati dallo spazio di archiviazione BLOB SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Prima di iniziare

Per acquisire familiarità con Azure Data Factory, vedere [Introduzione alle Azure Data Factory][].

### <a name="create-or-identify-resources"></a>Creare o identificare le risorse

Prima di iniziare questa esercitazione, è necessario che le risorse seguenti.

   + **Azure lo spazio di archiviazione Blob**: in questa esercitazione utilizza Azure lo spazio di archiviazione Blob come origine dati per la pipeline di Azure Data Factory e caso è necessario disporre di una per archiviare i dati di esempio. Se non hai uno già, informazioni su come creare [un account di archiviazione][].

   + **SQL Data Warehouse**: in questa esercitazione si sposta i dati da Azure lo spazio di archiviazione Blob SQL Data Warehouse e in tal caso è necessario specificare una data warehouse online che è stata caricata con i dati di esempio AdventureWorksDW. Se non si dispone già di un data warehouse, informazioni su come effettuare [il provisioning di uno][Create a SQL Data Warehouse]. Se si dispone di un data warehouse ma non si è effettuare il provisioning con i dati di esempio, è possibile [caricare manualmente][Load sample data into SQL Data Warehouse].

   + **Azure Data Factory**: Azure Data Factory completeranno il carico effettivo e pertanto è necessario avere uno che è possibile utilizzare per creare la pipeline di spostamento dei dati. Se ne non ha già uno, ecco come crearne uno nel passaggio 1 della [Guida introduttiva di Azure Data Factory (dati Factory Editor)][].

   + **AZCopy**: È necessario AZCopy per copiare i dati di esempio da client locale per il Blob di spazio di archiviazione Azure. Per istruzioni di installazione, vedere la [documentazione AZCopy][].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Passaggio 1: Copiare i dati di esempio in Azure lo spazio di archiviazione Blob

Dopo aver configurato tutte le parti pronti, si è pronti copiare i dati di esempio per il Blob di spazio di archiviazione Azure.

1. [Scaricare i dati di esempio][]. Questi dati aggiungerà un altro tre anni di dati delle vendite per i dati di esempio AdventureWorksDW.

2. Utilizzare questo comando AZCopy per copiare il Blob di spazio di archiviazione di Azure tre anni di dati.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Passaggio 2: Connettersi risorse su Factory di dati di Azure

Ora che i dati sono in posizione è possibile creare la pipeline di Azure Data Factory per spostare i dati dallo spazio di archiviazione blob Azure nell'archivio di dati SQL.

Per iniziare, aprire il [portale di Azure][] e selezionare il produttore dati dal menu a sinistro.

### <a name="step-21-create-linked-service"></a>Passaggio 2.1: Creare servizi collegati

Collegare l'account di archiviazione Azure e SQL Data Warehouse su factory i dati.  

1. Prima di tutto, avviare il processo di registrazione, fare clic su sezione 'Servizi collegate' della factory dati e quindi fare clic su "nuovo archivio dati." Scegliere un nome per registrare lo spazio di archiviazione azure in selezionare lo spazio di archiviazione di Azure durante la digitazione e quindi immettere il nome dell'Account e la chiave Account.

2. Per registrare SQL Data Warehouse passare alla sezione 'Autore e la distribuzione', seleziona "Archivio dati nuovo" e "Warehouse dati di SQL Azure". Copiare e incollare in questo modello e quindi compilare le informazioni specifiche.

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### <a name="step-22-define-the-dataset"></a>Passaggio 2.2: Definire il set di dati

Dopo aver creato i servizi collegati, sarà necessario definire il set di dati.  Di seguito ciò significa che definisce la struttura dei dati che viene spostati dallo spazio di archiviazione per la warehouse dati.  È possibile leggere informazioni sulla creazione di

1. Avviare il processo, passare alla sezione 'Autore e la distribuzione' dell'ambiente di produzione dati.

2. Fare clic su 'Nuovo set di dati' e quindi 'Archiviazione Blob Azure' a cui collegarsi lo spazio di archiviazione dell'ambiente di produzione di dati.  È possibile usare il seguente script per definire i dati in archiviazione Blob Azure:

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
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
```


3. Si verrà anche definire il set di dati per SQL Data Warehouse.  Iniziamo nello stesso modo, facendo clic su 'Nuovo set di dati' e quindi "Warehouse dati di SQL Azure".

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## <a name="step-3-create-and-run-your-pipeline"></a>Passaggio 3: Creare ed eseguire la pipeline

Infine, verranno configurazione ed eseguire la pipeline in Azure Data Factory.  Questa è l'operazione che completerà lo spostamento dei dati effettivi.  È possibile trovare una visualizzazione completa delle operazioni che è possibile completare con SQL Data Warehouse e Azure Data Factory [qui][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Nella sezione 'Autore e la distribuzione' ora scegliere 'Altri comandi' 'Nuova Pipeline'.  Dopo aver creato la pipeline, è possibile usare il seguente codice per trasferire i dati per il proprio data warehouse:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, prima di tutto la visualizzazione:

- [Percorso formativo: azure Data Factory][].
- [Dati di SQL azure Warehouse connettore][]. Questo è l'argomento di riferimento principali per l'uso di Azure Data Factory con Data Warehouse di SQL Azure.


Per informazioni dettagliate su Factory di dati di Azure, questi argomenti: Vengono illustrate Database SQL Azure o HDinsight, ma le informazioni si applicano anche nell'archivio di dati di SQL Azure.

- [Esercitazione: Introduzione a Azure Data Factory][] Verrà visualizzata l'esercitazione di base per l'elaborazione dei dati con Azure Data Factory. In questa esercitazione si creerà la pipeline prima che utilizza HDInsight per trasformare e analizzare i registri web su base mensile. Non nota, non esiste alcun attività Copia in questa esercitazione.
- [Esercitazione: copiare i dati da Azure lo spazio di archiviazione Blob al Database SQL Azure][]. In questa esercitazione, è necessario creare una pipeline di Azure Data Factory per copiare i dati da Azure lo spazio di archiviazione Blob al Database SQL Azure.


<!--Image references-->

<!--Article references-->
[Documentazione AZCopy]: ../storage/storage-use-azcopy.md
[Connettore Warehouse dati SQL Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Creare un account di archiviazione]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Guida introduttiva di Azure Data Factory (dati Factory Editor)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduzione ai dati di Azure Factory]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Esercitazione: Copiare i dati da Azure lo spazio di archiviazione Blob al Database SQL Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Esercitazione: Introduzione a Factory di dati di Azure]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Percorso formativo Factory dati Azure]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Portale di Azure]: https://portal.azure.com
[Scaricare i dati di esempio]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
