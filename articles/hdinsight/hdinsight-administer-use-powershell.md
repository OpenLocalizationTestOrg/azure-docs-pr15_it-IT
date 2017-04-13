<properties
    pageTitle="Gestire i cluster Hadoop in HDInsight con PowerShell | Microsoft Azure"
    description="Informazioni su come eseguire attività amministrative per i cluster Hadoop nell'utilizzo di PowerShell Azure HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gestire i cluster Hadoop in HDInsight tramite PowerShell Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

PowerShell Azure è un ambiente di script potente che è possibile utilizzare per controllare e automatizzare la distribuzione e gestione dei carichi di lavoro di Azure. In questo articolo si imparerà gestire i cluster Hadoop in Azure HDInsight con console Azure PowerShell locale mediante l'utilizzo di Windows PowerShell. Per l'elenco dei cmdlet di HDInsight PowerShell, vedere [informazioni di riferimento cmdlet HDInsight][hdinsight-powershell-reference].



**Prerequisiti**

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="install-azure-powershell"></a>Installare PowerShell Azure

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Se è stato installato PowerShell Azure versione 0,9 x, è necessario disinstallarlo prima di installare una versione più recente.

Per verificare la versione di PowerShell installati:

    Get-Module *azure*
    
Per disinstallare la versione precedente, eseguire programmi e funzionalità nel Pannello di controllo. 


##<a name="create-clusters"></a>Creare cluster

Vedere [basati su Linux creare cluster in uso di PowerShell Azure HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##<a name="list-clusters"></a>Elenco cluster
Usare il comando seguente per visualizzare un elenco di tutti i cluster di abbonamento corrente:

    Get-AzureRmHDInsightCluster

##<a name="show-cluster"></a>Mostra cluster

Usare il comando seguente per visualizzare i dettagli di un cluster specifico attuale abbonamento:

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##<a name="delete-clusters"></a>Eliminare cluster

Per eliminare un cluster, utilizzare il comando seguente:

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

È anche possibile eliminare un cluster rimuovendo il gruppo di risorse che contiene il grafico. Si noti, verranno eliminate tutte le risorse nel gruppo tra cui l'account di archiviazione predefinito.

    Remove-AzureRmResourceGroup -Name <Resource Group Name>
            
##<a name="scale-clusters"></a>Cluster di scala
Cluster proporzioni dei caratteri caratteristica consente di modificare il numero dei nodi di lavoro utilizzato da un cluster che è in esecuzione in Azure HDInsight senza dover ricreare il cluster.

>[AZURE.NOTE] Solo cluster con HDInsight versione 3.1.3 o superiore sono supportati. Se non si conosce la versione del cluster, è possibile controllare la pagina delle proprietà.  Vedere [cluster di elenco e la presentazione](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

Effetti della modifica del numero dei nodi di dati per ogni tipo di grafico supportato da HDInsight:

- Hadoop

    Diretta, è possibile aumentare il numero dei nodi di lavoro in un cluster di Hadoop che è in esecuzione senza impatto processi in sospeso o è in esecuzione. Nuovi processi possono essere inviati anche durante l'esecuzione dell'operazione. Gli errori in un'operazione di ridimensionamento normalmente vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster di Hadoop viene ridimensionato verso il basso, riducendo il numero dei nodi di dati, alcuni dei servizi del cluster, è necessario riavviare. In questo modo tutti in esecuzione e processi in sospeso in fase di completamento dell'operazione di ridimensionamento. Al termine dell'operazione, è possibile, tuttavia, inviare nuovamente i processi.

- HBase

    Diretta, è possibile aggiungere o rimuovere nodi al cluster HBase mentre è in esecuzione. Server regionali automaticamente Bilanciamento all'interno di pochi minuti per completare l'operazione di ridimensionamento. Tuttavia, è possibile saldare manualmente server locali con accesso a headnode di raggruppamento e l'esecuzione di comandi seguenti da una finestra del prompt dei comandi:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Eccesso

    Diretta, è possibile aggiungere o rimuovere dati nodi al cluster eccesso mentre è in esecuzione. Ma dopo il completamento dell'operazione di ridimensionamento, sarà necessario ribilanciare la topologia.

    Riequilibrare può essere eseguita in due modi:

    * Interfaccia utente web eccesso
    * Strumento dell'interfaccia della riga di comando (CLI)

    Fare riferimento alla [documentazione per eccesso Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) per altri dettagli.

    L'interfaccia web eccesso è disponibile in cluster HDInsight:

    ![Ribilanciamento scala di eccesso HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Di seguito viene illustrato come utilizzare il comando CLI per ribilanciare la topologia eccesso:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Per modificare le dimensioni di cluster Hadoop tramite PowerShell di Azure, eseguire il comando seguente da un computer client:

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    

##<a name="grantrevoke-access"></a>Concessione/revoca accesso

HDInsight cluster sono i seguenti servizi web HTTP (tutti questi servizi presentano endpoint REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Per impostazione predefinita, questi servizi vengono concessi per l'accesso. È possibile revocare/concedere l'accesso. Per revocare:

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Per concedere:

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Per concedere o revocare l'accesso, si verrà reimpostare il nome utente cluster e la password.

Questa operazione può essere eseguita tramite il portale. Vedere [Amministrare HDInsight tramite il portale Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Aggiornare le credenziali utente per HTTP

È la stessa procedura [accesso Grant/revoke HTTP](#grant/revoke-access). Se il cluster è stato concesso l'accesso tramite HTTP, è necessario revocare il.  E quindi concedere l'accesso con le credenziali utente per nuovi HTTP.


##<a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito

Script di Powershell seguente viene illustrato come ottenere il nome dell'account predefinito dello spazio di archiviazione e la chiave account di archiviazione predefinito per un cluster.

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##<a name="find-the-resource-group"></a>Individuare il gruppo di risorse

Nella modalità di gestione risorse, ogni cluster HDInsight appartiene a un gruppo di risorse Azure.  Per trovare il gruppo di risorse:

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


##<a name="submit-jobs"></a>Inviare i processi

**Per inviare i processi di MapReduce**

Vedere [esempi di esecuzione Hadoop MapReduce in HDInsight basato su Windows](hdinsight-run-samples.md).

**Per inviare i processi di Hive** 

Vedere [query eseguire Hive tramite PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Per inviare i processi di maialino**

Vedere [maialino eseguire processi tramite PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Per inviare i processi di Sqoop**

Vedere [usare Sqoop con HDInsight](hdinsight-use-sqoop.md).

**Per inviare i processi di Oozie**

Vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in HDInsight](hdinsight-use-oozie.md).

##<a name="upload-data-to-azure-blob-storage"></a>Caricare dati in archiviazione Blob Azure
Vedere [caricare dati da HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Vedere anche
* [Documentazione di riferimento cmdlet HDInsight][hdinsight-powershell-reference]
* [Amministrare HDInsight tramite il portale di Azure][hdinsight-admin-portal]
* [Amministrare HDInsight mediante un'interfaccia della riga di comando][hdinsight-admin-cli]
* [Creare cluster HDInsight][hdinsight-provision]
* [Caricare dati HDInsight][hdinsight-upload-data]
* [Inviare i processi di Hadoop a livello di programmazione][hdinsight-submit-jobs]
* [Guida introduttiva di Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
