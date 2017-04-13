<properties
   pageTitle="Creare i cluster basati su Linux Hadoop in uso dei modelli di gestione di risorse Azure HDInsight | Microsoft Azure"
    description="Informazioni su come creare cluster per utilizzare i modelli di Manager delle risorse di Azure Azure Azure HDInsight."
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-linux-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Creare i cluster basati su Linux Hadoop in uso dei modelli di gestione di risorse Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare cluster HDInsight utilizzare i modelli di Azure risorsa Manager(ARM). Per ulteriori informazioni, vedere [distribuire un'applicazione con il modello di gestione risorse di Azure](../resource-group-template-deploy.md). Per la creazione di altri cluster caratteristiche e strumenti fare clic su Seleziona scheda nella parte superiore di questa pagina oppure [metodi di creazione Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Prima di iniziare le istruzioni fornite in questo articolo, è necessario disporre le operazioni seguenti:

- [Abbonamento azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI PowerShell Azure e/o Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Modelli di Manager delle risorse

Modello di gestione risorse è facile creare cluster HDInsight, le risorse dipendenti (ad esempio, l'account di archiviazione predefinito) e altre risorse (ad esempio Database di SQL Azure usare Apache Sqoop) per l'applicazione in un'operazione su una singola e coordinata. Nel modello di definire le risorse necessarie per l'applicazione e specificare i parametri di distribuzione per valori di input per ambienti diversi. Il modello è composto da JSON ed espressioni che è possibile utilizzare per creare i valori per la distribuzione.

Un modello di Manager delle risorse per la creazione di un cluster di HDInsight e l'account di archiviazione Azure dipendente sono disponibili [Appendice A](#appx-a-arm-template). Utilizzare multipiattaforma [VSCode](https://code.visualstudio.com/#alt-downloads) con [estensione Manager delle risorse](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) o un editor di testo per salvare il modello in un file nella workstation. Viene spiegato chiamare il modello usando vari metodi.

Per ulteriori informazioni sui modelli di Manager delle risorse, vedere

- [Modelli di gestione di risorse Azure autore](../resource-group-authoring-templates.md)
- [Distribuire un'applicazione con il modello di gestione risorse di Azure](../resource-group-template-deploy.md)

Per scoprire lo schema JSON per alcuni elementi, è possibile seguire la procedura seguente:

1. Aprire [il portale di Azure](https://porta.azure.com) per creare un cluster di HDInsight.  Vedere [cluster basati su Linux creare nel portale di Azure HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).
2. Configurare gli elementi necessari e gli elementi necessari lo schema JSON.
3. Prima di fare clic su **Crea**, fare clic su **Opzioni di automazione** come illustrato nella schermata seguente:

    ![HDInsight Hadoop creare cluster opzioni di automazione schema modello Manager delle risorse](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-automation-option.png)

    Il portale consente di creare un modello di Manager delle risorse in base alle configurazioni.
## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

La procedura seguente crea cluster basati su Linux HDInsight.

**Distribuire un cluster utilizzando il modello di Manager delle risorse**

1. Salvare il file json [nell'Appendice](#appx-a-arm-template) workstation in uso. In script di PowerShell, il nome del file è *C:\HDITutorials-ARM\hdinsight-arm-template.json*.
2. Impostare i parametri e variabili, se necessario.
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
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    Lo script di PowerShell configura solo il nome del cluster. Il nome dell'account di archiviazione è codificato nel modello. Verrà richiesto di immettere la password dell'utente cluster (il nome utente predefinito è *amministratore*); e la password dell'utente SSH (il nome utente SSH predefinito è *sshuser*).  
    
Per ulteriori informazioni, vedere [distribuire con PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Distribuire con CLI Azure

Nell'esempio seguente viene creato un cluster, l'account di archiviazione dipendenti e contenitore chiamando un modello di Manager delle risorse:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"
    
Verrà richiesto di immettere il nome del cluster, la password dell'utente cluster (il nome utente predefinito è *amministratore*) e la password dell'utente SSH (il nome utente SSH predefinito è *sshuser*). Per fornire i parametri in linea:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

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

Il modello di Manager delle risorse Azure seguente viene creato un cluster basati su Linux Hadoop con l'account di archiviazione di Azure dipendenti. 

> [AZURE.NOTE] L'esempio include le informazioni di configurazione per Hive metastore e Oozie metastore.  Rimuovere la sezione o configurare la sezione prima di utilizzare il modello.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
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
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
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
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
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
