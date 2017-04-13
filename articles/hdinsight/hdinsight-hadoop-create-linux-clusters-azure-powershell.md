<properties
    pageTitle="Creare cluster Hadoop, HBase, eccesso o motori su Linux in uso di PowerShell Azure HDInsight | Microsoft Azure"
    description="Informazioni su come creare cluster Hadoop, HBase, eccesso o motori su Linux per HDInsight tramite PowerShell Azure."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>Creare cluster basati su Linux in HDInsight tramite PowerShell Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

PowerShell Azure è un ambiente di script potente che è possibile utilizzare per controllare e automatizzare la distribuzione e gestione dei carichi di lavoro di Microsoft Azure. In questo documento vengono fornite informazioni su come creare un cluster basati su Linux HDInsight tramite PowerShell Azure. Inoltre, include uno script di esempio.

> [AZURE.NOTE] Azure PowerShell è disponibile solo nei client Windows. Se si utilizza un client Linux, Unix o Mac OS X, vedere [creare un cluster basati su Linux HDInsight utilizzando Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) per informazioni sull'utilizzo CLI Azure per creare un cluster.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre le operazioni seguenti prima di iniziare questa procedura:

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- Azure PowerShell.
    Per ulteriori informazioni sull'utilizzo di PowerShell Azure con HDInsight, vedere [amministrare HDInsight tramite PowerShell](hdinsight-administer-use-powershell.md). Per l'elenco dei cmdlet di Windows PowerShell HDInsight, vedere [informazioni di riferimento cmdlet HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creare cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per creare un cluster HDInsight tramite PowerShell di Azure, è necessario completare le procedure seguenti:

- Creare un gruppo di risorse Azure
- Creare un account di archiviazione Azure
- Creare un contenitore di Blob Azure
- Creare un cluster di HDInsight

I due parametri più importanti che è necessario impostare per creare cluster Linux sono quelli che specificano il tipo di sistema operativo e i dettagli utente SSH:

- Verificare che si specifica il parametro **- OSType** come **Linux**.
- Per utilizzare SSH per le sessioni remote sui cluster, è possibile specificare la password dell'utente SSH o la chiave pubblica SSH. Se si specifica la password dell'utente SSH e la chiave pubblica SSH, la chiave verrà ignorata. Se si desidera utilizzare la chiave SSH per le sessioni remote, è necessario specificare una password SSH vuota quando viene richiesto per uno. Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere uno degli articoli seguenti:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Lo script seguente viene illustrato come creare un nuovo cluster:

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

I valori specificati per **$clusterCredentials** vengono utilizzati per creare l'account utente Hadoop per il cluster. Utilizzare questo account per connettersi al cluster.

I valori specificati per **$sshCredentials** vengono utilizzati per creare l'utente SSH per il cluster. Utilizzare questo account per avviare una sessione remota di SSH nel cluster ed eseguire i processi.

> [AZURE.IMPORTANT] In questo script, è necessario specificare il numero dei nodi di lavoro che si trova il cluster. Se si prevede di utilizzare più di 32 nodi di lavoro (al momento della creazione cluster o modificando il cluster dopo la creazione), è anche necessario specificare una dimensione di nodo principale con almeno 8 core e 14 GB di RAM.
>
> Per ulteriori informazioni su costi associati e le dimensioni massime nodo, vedere [prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Può richiedere fino a 20 minuti per creare un cluster.

Nell'esempio seguente viene illustrato come aggiungere un account di spazio di archiviazione aggiuntivo:

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>Personalizzare i cluster

- Vedere [personalizzare HDInsight cluster tramite avvio](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Vedere [cluster basato su Windows personalizzare HDInsight tramite Script azione](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="delete-the-cluster"></a>Eliminare il cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato correttamente un cluster di HDInsight, utilizzare le risorse seguenti per informazioni su come lavorare con i cluster.

### <a name="hadoop-clusters"></a>Hadoop cluster

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
* [Utilizzare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase cluster

* [Guida introduttiva a HBase in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Sviluppo di applicazioni Java per HBase in HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Cluster eccesso

* [Sviluppare topologie Java per eccesso in HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizzare i componenti di Python in eccesso in HDInsight](hdinsight-storm-develop-python-topology.md)
* [Distribuire e monitorare topologie con eccesso su HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Motori cluster

* [Creare un'applicazione autonoma utilizza Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster di motori tramite inserire il](hdinsight-apache-spark-livy-rest-interface.md)
* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)
* [Motori di apprendimento: usare i in HDInsight per prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Motori Streaming: Usare motori in HDInsight per la creazione di applicazioni di trasmissione in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
