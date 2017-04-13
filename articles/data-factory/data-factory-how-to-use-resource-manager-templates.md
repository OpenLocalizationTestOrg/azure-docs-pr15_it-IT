<properties 
    pageTitle="Manager delle risorse usare modelli nella Factory dati | Microsoft Azure" 
    description="Informazioni su come creare e usare modelli di gestione di risorse Azure per creare entità Factory dati." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>Usare i modelli per creare entità Factory di dati di Azure

## <a name="overview"></a>Panoramica
Durante l'uso di Azure Data Factory adatti alle proprie esigenze integrazione dei dati, si troverà a riutilizzare lo stesso modello in ambienti diversi o implementare la stessa attività ripetitiva all'interno della soluzione stessa. Modelli consentono di implementare e gestire gli scenari seguenti in modo semplice. Modelli di Azure Data Factory sono ideali per scenari che comportano riutilizzo e la ripetizione.
 
Considerare la situazione in un'organizzazione dispone di 10 piante di produzione in tutto il mondo. I registri di ogni stabilimento di produzione sono archiviati in un database di SQL Server locale separato. La società desidera creare un solo data warehouse nel cloud per Reporting ad-hoc analitica. Nell'esempio anche avere la stessa logica ma configurazioni diverse per gli ambienti di sviluppo, test e produzione. 

In questo caso, un'attività deve essere ripetute nello stesso ambiente, ma con valori diversi su factory 10 dati per ogni stabilimento. La **ripetizione** è presente. Modello consente l'estrazione di questo flusso generico (vale a dire condutture con le stesse attività in ogni factory dati), ma viene utilizzato un file di parametro separato per ogni stabilimento di fabbricazione.

Inoltre, come l'organizzazione desidera distribuire questi factory 10 dati più volte in ambienti diversi, modelli possono utilizzare questo **riutilizzo** utilizzando i file di parametro separato per gli ambienti di sviluppo, test e produzione.

## <a name="templating-with-azure-resource-manager"></a>Modello con Azure Manager delle risorse
[Modelli di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md#template-deployment) sono un ottimo metodo per ottenere modelli in Azure Data Factory. Modelli di Manager delle risorse definiscono l'infrastruttura e la configurazione della soluzione Azure tramite un file di JSON. Poiché i modelli di gestione di risorse Azure funziona con i servizi di Azure tutte/la maggior parte, può essere ampiamente utilizzato per gestire facilmente tutte le risorse dei beni Azure. Vedere [modelli di creazione condivisa Manager delle risorse di Azure](../resource-group-authoring-templates.md) per altre informazioni sui modelli di Manager delle risorse in generale. 

## <a name="tutorials"></a>Esercitazioni
Vedere le seguenti esercitazioni per istruzioni dettagliate creare entità Factory dati con i modelli di Manager delle risorse:

- [Esercitazione: Creare una pipeline per copiare i dati utilizzando il modello di gestione risorse di Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [Esercitazione: Creare una pipeline per elaborare i dati utilizzando il modello di gestione risorse di Azure](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelli di dati Factory in Github
Estrarre i seguenti modelli di Azure introduttive su Github: 

- [Creare una factory di dati per copiare i dati dallo spazio di archiviazione Blob Azure al Database SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [Creare una factory di dati con attività Hive cluster Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [Creare una factory di dati per copiare i dati da Salesforce BLOB Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

È possibile condividere i modelli di Azure Data Factory in [avvio rapido Azure](https://azure.microsoft.com/documentation/templates/). Consultare la [Guida di contributo](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) durante lo sviluppo di modelli che possono essere condivisi tramite questo archivio. 

Nelle sezioni seguenti forniscono informazioni dettagliate sulla definizione le risorse dati in un modello di Manager delle risorse. 

## <a name="defining-data-factory-resources-in-templates"></a>Definire le risorse dati nei modelli
Il modello di primo livello per la definizione di una factory di dati è:

    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
    {
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "[variables('apiVersion')]",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "westus",
        "resources": [
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>Definire factory dati

Definire una factory di dati nel modello di gestione risorse come illustrato nell'esempio seguente:

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

Il dataFactoryName è definita in "variabili" come segue:

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>Definire i servizi collegati 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


Per ulteriori informazioni sulle proprietà JSON per il servizio collegata specifico che si desidera distribuire, vedere [Il servizio di archiviazione collegate](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [Calcolare servizi collegati](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Il parametro "dependsOn" Specifica nome di dati corrispondente. Definizione di JSON seguente è illustrato un esempio di definizione di un servizio collegato per lo spazio di archiviazione di Azure:

### <a name="define-datasets"></a>Definire set di dati

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

Per informazioni dettagliate sulle proprietà del JSON per il tipo di set di dati specifici che si desidera distribuire, fare riferimento alla [supportati archivi dati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Nota che il parametro "dependsOn" Specifica nome del produttore dati corrispondente e lo spazio di archiviazione collegata servizio. Definizione di JSON seguente è illustrato un esempio di definizione dei tipi di set di dati di archiviazione blob Azure:

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>Definire le tubazioni

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

Fare riferimento alla [definizione tubazioni](data-factory-create-pipelines.md#pipeline-json) per informazioni dettagliate sulle proprietà del JSON per definire le attività che si desidera distribuire e pipeline specifica. Nota Il parametro "dependsOn" Specifica nome di dati e qualsiasi corrispondente collegato servizi o set di dati. Frammento di JSON seguente è illustrato un esempio di una pipeline che consente di copiare dati dallo spazio di archiviazione Blob Azure al Database SQL Azure:

    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('azureSqlLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "activities": [
        {
            "name": "CopyFromAzureBlobToAzureSQL",
            "description": "Copy data frm Azure blob to Azure SQL",
            "type": "Copy",
            "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
            ],
            "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
            ],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink",
                    "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "columnMappings": "Column0:FirstName,Column1:LastName"
                }
            },
            "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }
        ],
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>Modello di dati Factory di parametri
Per le procedure consigliate sull'applicazione di parametri, vedere l'articolo [le procedure consigliate per la creazione di modelli di gestione risorse di Azure](../resource-manager-template-best-practices.md#parameters) . In generale, utilizzo del parametro deve essere ridotta a icona, soprattutto se è invece possono utilizzare le variabili. Fornire solo parametri negli scenari seguenti:

- Impostazioni possono variare dall'ambiente (esempio: sviluppo, test e produzione)
- Informazioni riservate (ad esempio le password)

Se è necessario estrarre informazioni riservate [dall'Archivio chiave Azure](../key-vault/key-vault-get-started.md) quando distribuzione di Azure Data Factory entità uso dei modelli, specificare il **nome segreta** e **l'archivio chiave** , come illustrato nell'esempio seguente:

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] Esportazione di modelli per factory dati esistenti non è ancora supportato, ma è nel programma works. 


