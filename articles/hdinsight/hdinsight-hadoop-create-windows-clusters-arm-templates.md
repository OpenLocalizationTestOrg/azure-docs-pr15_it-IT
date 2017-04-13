<properties
   pageTitle="Creare cluster Hadoop basato su Windows in uso dei modelli di gestione di risorse Azure HDInsight | Microsoft Azure"
    description="Informazioni su come creare cluster per utilizzare i modelli di gestione di risorse Azure Azure HDInsight."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Creare cluster Hadoop basato su Windows in uso dei modelli di gestione di risorse Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare cluster HDInsight utilizzare i modelli di gestione di risorse Azure. Per ulteriori informazioni, vedere [distribuire un'applicazione con il modello di gestione risorse di Azure](../resource-group-template-deploy.md). Per la creazione di altri cluster caratteristiche e strumenti fare clic su Seleziona scheda nella parte superiore di questa pagina oppure [metodi di creazione Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Prima di iniziare le istruzioni fornite in questo articolo, è necessario disporre le operazioni seguenti:

- [Abbonamento azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI PowerShell Azure o Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Modelli di Manager delle risorse

Modello di gestione risorse è facile creare cluster HDInsight, le risorse dipendenti (ad esempio, l'account di archiviazione predefinito) e altre risorse (ad esempio Database di SQL Azure usare Apache Sqoop) per l'applicazione in un'operazione su una singola e coordinata. Nel modello di definire le risorse necessarie per l'applicazione e specificare i parametri di distribuzione per valori di input per ambienti diversi. Il modello è composto da JSON ed espressioni che è possibile utilizzare per creare i valori per la distribuzione.

Un modello di Manager delle risorse per la creazione di un cluster di HDInsight e l'account di archiviazione Azure dipendente sono disponibili [Appendice A](#appx-a-arm-template). Utilizzare un editor di testo per salvare il modello in un file nella workstation. Viene spiegato chiamare il modello utilizzando diversi strumenti.

Per ulteriori informazioni sui modelli di Manager delle risorse, vedere

- [Modelli di gestione di risorse Azure autore](../resource-group-authoring-templates.md)
- [Distribuire un'applicazione con il modello di gestione risorse di Azure](../resource-group-template-deploy.md)


## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

La procedura seguente viene creato un cluster di HDInsight.

**Distribuire un cluster utilizzando il modello di Manager delle risorse**

1. Salvare il file json [nell'Appendice](#appx-a-arm-template) workstation in uso.
2. Impostare i parametri se necessario.
3. Eseguire il modello utilizzando il seguente script di PowerShell:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    Lo script di PowerShell configura solo il nome del cluster e il nome dell'account di archiviazione.  È possibile impostare altri valori nel modello di Manager delle risorse. 
    
Per ulteriori informazioni, vedere [distribuire con PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Distribuire con CLI Azure

Nell'esempio seguente viene creato un cluster, l'account di archiviazione dipendenti e contenitore chiamando un modello di Manager delle risorse:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Distribuire con API REST

Vedere [distribuire con l'API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## <a name="deploy-with-visual-studio"></a>Distribuire con Visual Studio

Con Visual Studio, è possibile creare un progetto di gruppo di risorse e distribuirlo a Azure tramite l'interfaccia utente. Si seleziona il tipo di risorse da includere nel progetto e le risorse vengono aggiunti automaticamente al modello di Manager delle risorse. Il progetto vengono forniti anche uno script di PowerShell per distribuire il modello.

Per un'introduzione all'utilizzo di Visual Studio con i gruppi di risorse, vedere [creazione e la distribuzione di gruppi di risorse Azure tramite Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##<a name="next-steps"></a>Passaggi successivi
In questo articolo sono state fornite diversi modi per creare un cluster di HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:


- Per un esempio di distribuzione delle risorse tramite la raccolta di client .NET, vedere [risorse di distribuzione mediante un modello e raccolte di .NET](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Per un esempio dettagliato della distribuzione di un'applicazione, vedere [disposizione e distribuire microservices prevedibili in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Per indicazioni su come distribuire una soluzione in ambienti diversi, vedere [ambienti di sviluppo e test in Microsoft Azure](../solution-dev-test-environments.md).
- Per informazioni sulle sezioni del modello di gestione risorse di Azure, vedere [modelli di creazione condivisa](../resource-group-authoring-templates.md).
- Per un elenco di funzioni che è possibile usare in un modello di gestione risorse di Azure, vedere [funzioni di modello](../resource-group-template-functions.md).



##<a name="appx-a-resource-manager-template"></a>Modello di gestione di risorse AppX r

Il modello di Manager delle risorse Azure seguente viene creato un cluster Hadoop basato su Windows con l'account di archiviazione di Azure dipendenti.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

