<properties
    pageTitle="Disponibilità di Hadoop cluster in HDInsight | Microsoft Azure"
    description="HDInsight distribuisce elevata disponibilità e affidabilità cluster con un nodo principale aggiuntivo."
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>


#<a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Disponibilità e l'affidabilità di cluster basato su Windows Hadoop in HDInsight


>[AZURE.NOTE] I passaggi utilizzati in questo documento sono specifici per cluster HDInsight basato su Windows. Se si utilizza un cluster basato su Linux, vedere [disponibilità e l'affidabilità dei cluster basati su Linux Hadoop in HDInsight](hdinsight-high-availability-linux.md) per informazioni specifiche Linux.

HDInsight consente ai clienti di distribuire numerosi tipi di grafico, per carichi di lavoro analitica dati diversi. Tipi di grafico disponibili oggi sono i cluster Hadoop per la query e carichi di lavoro di analisi, cluster HBase per NoSQL carichi di lavoro e cluster eccesso per carichi di lavoro di elaborazione di eventi in tempo reale. All'interno di un tipo di grafico specificato, sono disponibili diversi ruoli per i nodi diversi. Per esempio:



- Cluster Hadoop per HDInsight vengono distribuite con due ruoli:
    - Nodo testa (2 nodi)
    - Dati nodo (almeno 1)

- Cluster HBase per HDInsight vengono distribuite con tre ruoli:
    - Server di testa (2 nodi)
    - Server area (almeno 1 nodo)
    - Nodi schema/Zookeeper (3 nodi)

- Cluster eccesso per HDInsight vengono distribuite con tre ruoli:
    - Nodi nimbus (2 nodi)
    - Server Supervisore (almeno 1 nodo)
    - Nodi zookeeper (3 nodi)

In genere implementazioni standard di Hadoop cluster hanno un singolo nodo principale. HDInsight rimuove il singolo punto di errore con l'aggiunta di un nodo principale secondario /head nodo server/Nimbus per aumentare la disponibilità e l'affidabilità del servizio necessario per gestire carichi di lavoro. Questi nodi server/Nimbus testa nodi/testa sono progettati per gestire l'errore dei nodi di lavoro in modo uniforme, ma le interruzioni dei servizi master in esecuzione sul nodo principale causa del cluster di attività.


Nodi [zooKeeper](http://zookeeper.apache.org/ ) (ZKs) sono state aggiunte e vengono usati per scelta responsabile dei nodi testa e per garantire che lavoro nodi e gateway (GWs) sapere quando l'esito negativo su nodo principale secondario (testa nodo 1) quando il nodo principale attivo (testa Node0) diventa inattivo.

![Diagramma dei nodi testa affidabili l'implementazione di HDInsight Hadoop.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## <a name="check-active-head-node-service-status"></a>Controllare lo stato dei servizi active nodo principale
Per determinare quali nodo principale è attivo e per controllare lo stato dei servizi in esecuzione su tale nodo principale, è necessario connettersi al cluster Hadoop utilizzando il Remote Desktop Protocol (RDP). Per istruzioni RDP, vedere [gestire Hadoop cluster in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). Una volta che si è connessi al cluster, fare doppio clic sull'icona **Hadoop servizio disponibile** sul desktop per ottenere lo stato su cui nodo principale di Namenode, servizi Jobtracker, Templeton, Oozieservice, Metastore e Hiveserver2 sono in esecuzione, o per i servizi di 3.0 HDI, Namenode, Manager delle risorse, cronologia Server, Templeton, Oozieservice, Metastore e Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

Nella schermata, il nodo principale attivo è *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>File di log di accesso sul nodo principale secondario

Per accedere al processo registri sul nodo principale secondario nel caso in cui è necessario creare un nodo principale attivo, esplorazione UI JobTracker ancora avviene per il nodo principale attivo. Per accedere a JobTracker, è necessario connettersi al cluster Hadoop tramite RDP come descritto nella sezione precedente. Dopo avere inserito remoti nel cluster, fare doppio clic sull'icona di **Stato del nodo di nome Hadoop** sul desktop e quindi fare clic sul **log NameNode** per accedere alla directory dei registri sul nodo principale secondario.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## <a name="configure-head-node-size"></a>Configurare le dimensioni di nodo principale
I nodi testa assegnati come grandi macchine () per impostazione predefinita. Le dimensioni sono sufficienti per la gestione della maggior parte dei processi Hadoop sul cluster. Ma vi sono scenari che potrebbero richiedere macchine virtuali di grandi dimensioni per i nodi testa. Ad esempio: il cluster dispone gestire un numero elevato di piccoli processi Oozie.

Macchine virtuali di grandi dimensioni possono essere configurate con i cmdlet di PowerShell Azure o SDK HDInsight.

La creazione e il provisioning di un cluster tramite PowerShell Azure descritte [amministrare HDInsight](hdinsight-administer-use-powershell.md)tramite PowerShell. La configurazione di un nodo principale eccessivo richiede l'aggiunta del `-HeadNodeVMSize ExtraLarge` parametro per il `New-AzureRmHDInsightcluster` cmdlet utilizzati in questo codice.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName ` 
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

Per SDK, il brano è simile. La creazione e il provisioning di un cluster tramite SDK descritte in [Uso HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk). La configurazione di un nodo principale eccessivo richiede l'aggiunta del `HeadNodeSize = NodeVMSize.ExtraLarge` parametro per il `ClusterCreateParameters()` metodo utilizzato in questo codice.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Passaggi successivi

- [Apache ZooKeeper](http://zookeeper.apache.org/ )
- [Connettersi a cluster HDInsight utilizzando RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Mediante HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk)
