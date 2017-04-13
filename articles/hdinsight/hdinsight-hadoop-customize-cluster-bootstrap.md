<properties
    pageTitle="Personalizzare cluster HDInsight mediante avvio | Microsoft Azure"
    description="Informazioni su come personalizzare cluster HDInsight mediante avvio."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizzare cluster HDInsight mediante avvio

In alcuni casi si desidera configurare i file di configurazione che includono:

- clusterIdentity.xml
- principali site.xml
- gateway.Xml
- hbase env.xml
- hbase site.xml
- hdfs site.xml
- hive env.xml
- hive site.xml
- sito mapred
- oozie site.xml
- oozie env.xml
- eccesso site.xml
- tez site.xml
- webhcat site.xml
- filati site.xml

I cluster non è possibile mantenere le modifiche a causa di acquisizione immagini nuovamente. Per ulteriori informazioni sulla nuova acquisizione immagini, vedere [Ruolo istanza riavvia scadenza agli aggiornamenti del sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Per mantenere le modifiche per tutta la durata i cluster, è possibile utilizzare la personalizzazione di cluster HDInsight durante il processo di creazione. Questo è il modo consigliato per modificare le configurazioni di un cluster e mantenere questi eventi riavvio di Azure reimage riavviare il computer. Queste modifiche alla configurazione vengono applicate prima dell'avvio del servizio, in modo che i servizi di non essere riavviati. 

Sono disponibili 3 metodi per utilizzare avvio:

- Usare PowerShell Azure

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
    
- Utilizzare SDK .NET
- Utilizzare il modello di gestione risorse di Azure

Per informazioni sull'installazione di componenti aggiuntivi in cluster HDInsight durante l'ora di creazione, vedere:

- [Personalizzare i cluster HDInsight tramite Script azione (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [Personalizzare i cluster HDInsight tramite Script azione (Windows)](hdinsight-hadoop-customize-cluster.md)

## <a name="use-azure-powershell"></a>Usare PowerShell Azure

Il codice di PowerShell seguente consente di definire una configurazione Hive:

    # hive-site.xml configuration
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
    
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $existingResourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -Config $config 

Un lavoro completamento dello script di PowerShell disponibili [Appendice A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample).

**Per verificare le modifiche:**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel riquadro sinistro fare clic su **Sfoglia**e quindi fare clic su **Cluster HDInsight**.
3. Selezionare il cluster che appena creata utilizzando lo script di PowerShell.
4. Fare clic su **Dashboard** nella parte superiore e il per aprire UI Ambari.
5. Fare clic su **Hive** dal menu a sinistra.
6. Fare clic su **HiveServer2** da **Riepilogo**.
7. Fare clic sulla scheda **configurazioni** .
8. Fare clic su **Hive** dal menu a sinistra.
9. Fare clic sulla scheda **Avanzate** .
10. Scorrere verso il basso e quindi espandere **hive avanzata**.
11. Cercare **hive.metastore.client.socket.timeout** nella sezione.

Alcuni altri esempi su come personalizzare altri file di configurazione:

    # hdfs-site.xml configuration
    $HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

    # core-site.xml configuration
    $CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

    # mapred-site.xml configuration
    $MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

    # oozie-site.xml configuration
    $OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Per ulteriori informazioni, vedere blog di Azim Uddin intitolato [la creazione di personalizzazione HDInsight Cluster](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## <a name="use-net-sdk"></a>Utilizzare SDK .NET

Vedere [cluster basati su Linux creare in HDInsight utilizzando .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Modello di utilizzo delle risorse Manager

È possibile utilizzare avvio nel modello di Manager delle risorse:

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop personalizzare modello manager delle risorse azure avvio cluster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## <a name="see-also"></a>Vedere anche

- [Creare cluster Hadoop in HDInsight] [ hdinsight-provision-cluster] fornisce istruzioni su come creare un cluster di HDInsight con altre opzioni personalizzate.
- [Sviluppare script azione Script per HDInsight][hdinsight-write-script]
- [Installare e usare motori nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster HDInsight][hdinsight-install-r]
- [Installare e utilizzare i cluster Solr su HDInsight](hdinsight-hadoop-solr-install.md).
- [Installare e utilizzare i cluster Giraph su HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasi durante la creazione di cluster"

## <a name="appx-a-powershell-sample"></a>R: AppX PowerShell campione

Questo script di PowerShell crea un cluster di HDInsight e consente di personalizzare un'impostazione di Hive:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
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
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion
