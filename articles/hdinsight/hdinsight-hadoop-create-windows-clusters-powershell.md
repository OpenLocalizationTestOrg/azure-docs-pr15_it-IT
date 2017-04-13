<properties
   pageTitle="Creare cluster Hadoop basato su Windows in uso di PowerShell Azure HDInsight | Microsoft Azure"
    description="Informazioni su come creare cluster per Azure HDInsight tramite PowerShell Azure."
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
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Creare cluster Hadoop basato su Windows in uso di PowerShell Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare cluster HDInsight tramite PowerShell Azure. PowerShell Azure è un modulo che fornisce i cmdlet per la gestione di Azure con Windows PowerShell. Per la creazione di altri cluster caratteristiche e strumenti fare clic su Seleziona scheda nella parte superiore di questa pagina oppure [metodi di creazione Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Prerequisiti:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Prima di iniziare le istruzioni fornite in questo articolo, è necessario disporre le operazioni seguenti:

- Azure abbonamento. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creare cluster
PowerShell Azure è un ambiente di script potente che è possibile utilizzare per controllare e automatizzare la distribuzione e gestione dei carichi di lavoro di Azure. In questa sezione fornisce istruzioni su come creare un cluster HDInsight tramite PowerShell Azure. Per informazioni su come configurare una workstation per eseguire i cmdlet HDInsight Windows PowerShell, vedere [installare e configurare Azure PowerShell](../powershell-install-configure.md). Per ulteriori informazioni sull'utilizzo di PowerShell Azure con HDInsight, vedere [amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet di Windows PowerShell HDInsight, vedere [informazioni di riferimento cmdlet HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Le procedure seguenti sono necessari per creare un cluster HDInsight tramite PowerShell Azure:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>Creare cluster modello ARM

È possibile utilizzare Azure PowerShell per distribuire un modello di ARM che consente di creare un cluster di HDInsight.  Vedere [modelli tramite PowerShell Azure chiamata](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell).

##<a name="customize-clusters"></a>Personalizzare i cluster

- Vedere [personalizzare HDInsight cluster tramite avvio](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Vedere [cluster basato su Windows personalizzare HDInsight tramite Script azione](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).


##<a name="next-steps"></a>Passaggi successivi
In questo articolo sono state fornite diversi modi per creare un cluster di HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Guida introduttiva di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - informazioni su come iniziare a lavorare con i cluster HDInsight
* [Inviare Hadoop processi a livello di programmazione](hdinsight-submit-hadoop-jobs-programmatically.md) - informazioni su come inviare a livello di programmazione processi a HDInsight
* [Cluster di gestire Hadoop in HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md) - informazioni su come utilizzare tramite PowerShell Azure HDInsight
* [Documentazione di Azure HDInsight SDK]  [ hdinsight-sdk-documentation] -alla scoperta di SDK HDInsight




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
