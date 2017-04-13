<properties
    pageTitle="Creare cluster HBase in una rete virtuale | Microsoft Azure"
    description="Iniziare a utilizzare HBase in Azure HDInsight. Informazioni su come creare HDInsight HBase cluster su Azure virtuali."
    keywords=""
    services="hdinsight,virtual-network"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Creare cluster HBase in Azure virtuali 

Informazioni su come creare cluster di Azure HDInsight HBase in una [Rete virtuale Azure][1].

Con l'integrazione di rete virtuale, è possibile distribuire cluster HBase alla stessa rete virtuale delle applicazioni in modo che le applicazioni possono comunicare con HBase direttamente. I vantaggi includono:

- Connettività diretta dell'applicazione web per i nodi del cluster HBase, che consente la comunicazione tramite RPC HBase Java chiamare API (RPC).
- Miglioramento delle prestazioni senza che sia il traffico passare su più gateway e i servizi di bilanciamento del carico.
- Possibilità per l'elaborazione di informazioni riservate in modo più sicuro senza esporre un endpoint di pubblico.

###<a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un lavoro utilizzando Azure PowerShell**. Vedere [installazione e utilizzo di PowerShell Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). 

## <a name="create-hbase-cluster-into-virtual-network"></a>Creare cluster HBase in rete virtuale

In questa sezione, è necessario creare un cluster basati su Linux HBase con l'account di archiviazione Azure dipendente in una rete virtuale Azure utilizzando un [modello di gestione risorse di Azure](../resource-group-template-deploy.md). Per altri metodi di creazione cluster e la comprensione delle impostazioni, vedere [creare HDInsight cluster](hdinsight-hadoop-provision-linux-clusters.md). Per ulteriori informazioni sull'utilizzo di un modello per creare cluster Hadoop in HDInsight, vedere [creare Hadoop cluster in uso dei modelli di gestione risorse di Azure HDInsight](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] Alcune proprietà sono state hardcoded nel modello. Per esempio:
>
> * __Posizione__: Stati Uniti orientali 2
> * Versione __Cluster: 3.4
> * __Conteggio dei nodi di lavoro cluster__: 4
> * __Account di archiviazione predefinito__: &lt;nome Cluster > archiviare
> * __Nome di rete virtuale__: &lt;nome Cluster >-vnet
> * __Spazio di indirizzi di rete virtuale__: 10.0.0.0/16
> * __Nome subnet__: predefinito
> * __Intervallo di indirizzi subnet__: 10.0.0.0/24
>
> &lt;Nome del cluster > verrà sostituito con il nome cluster fornito quando si usa il modello.

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure. Il modello si trova in un contenitore di blob pubblico. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Da e il **distribuzione personalizzata** , immettere quanto segue:

    - **Abbonamento**: selezionare un abbonamento Azure usato per creare il cluster HDInsight, dipendenti account di archiviazione e la rete virtuale Azure.
    - **Gruppo risorse**: selezionare **Crea nuovo**, quindi specificare il nome di un nuovo gruppo di risorse.
    - **Posizione**: selezionare un percorso per il gruppo di risorse.
    - **Nome cluster**: immettere un nome per il cluster Hadoop che verrà creato.
    - **Nome di accesso cluster e la password**: il nome di accesso predefinito è **amministratore**.
    - **SSH nome utente e password**: il nome utente predefinito è **sshuser**.  È possibile rinominarla. 
    - **Accetto i termini e le condizioni sopra**: (selezionare)
    

3. Fare clic su **Acquista**. Richiede circa circa il 20 minuti per creare un cluster. Dopo aver creato il grafico, è possibile fare clic e il cluster nel portale per aprirlo.

Dopo aver completato l'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in archiviazione di Azure, in modo che è possibile eliminare un cluster quando non è in uso. Anche addebitate per un cluster di HDInsight, anche quando non è in uso. Poiché le spese per il cluster sono tutte le volte più le spese per lo spazio di archiviazione, è opportuno economico eliminare cluster quando non sono in uso. Per istruzioni dell'eliminazione di un cluster, vedere [gestire Hadoop cluster in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Per iniziare a lavorare con il nuovo cluster HBase, è possibile utilizzare le procedure riportate in [Introduzione all'utilizzo di HBase con Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Connettersi a cluster HBase utilizzando HBase Java RPC API

1.  Creare un'infrastruttura come macchina virtuale servizio (IaaS) nella stessa rete virtuale Azure e la stessa subnet. Per istruzioni sulla creazione di una nuova macchina virtuale IaaS, vedere [creare una macchina virtuale che eseguono Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Quando la procedura riportata in questo documento, è necessario utilizzare le operazioni seguenti per la configurazione di rete:

    - __Rete virtuale__: &lt;nome Cluster >-vnet
    - __Subnet__: predefinito

    > [AZURE.IMPORTANT] Sostituire &lt;nome Cluster > con il nome usato per creare il cluster HDInsight nei passaggi precedenti.

    Utilizzando questi valori configurerà la macchina virtuale per utilizzare la stessa rete virtuale e subnet come cluster HDInsight. In questo modo comunicare direttamente tra loro.

2.  Quando si usa un'applicazione Java per connettersi a HBase in modalità remota, è necessario utilizzare il nome di dominio completo (FQDN). A questo scopo, è necessario ottenere il suffisso DNS specifico del cluster HBase. A tale scopo, è possibile usare uno dei metodi seguenti:

    * Usare un Web browser per effettuare una chiamata Ambari:
    
        Passare a https://&lt;nome cluster >.azurehdinsight.net/api/v1/clusters/&lt;nome cluster > / hosts?minimal_response = true. Consente di un file JSON con suffissi DNS.

    * Utilizzare il sito Web Ambari:

        1. Passare a https://&lt;nome cluster >. azurehdinsight.net.
        2. Fare clic su **host** dal menu superiore.

    * Usare curvatura per effettuare chiamate resto:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Nei dati JavaScript Object Notation (JSON) restituiti, trovare la voce "host_name". Che conterrà il nome di dominio completo per i nodi del cluster. Per esempio:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        La parte dell'inizio di nome di dominio con il nome del cluster è il suffisso DNS. Ad esempio mycluster.b1.cloudapp.net.

    * Usare PowerShell Azure
    
        Utilizzare il seguente script di PowerShell Azure per registrare la funzione **Get-ClusterDetail** , che può essere utilizzata per restituire il suffisso DNS:

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Dopo avere eseguito lo script di PowerShell di Azure, utilizzare il comando seguente per restituire il suffisso DNS utilizzando la funzione **Get-ClusterDetail** . Quando si usa questo comando, specificare il nome del cluster HDInsight HBase, nome dell'amministratore e password di amministratore.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Verrà restituito il suffisso DNS. Ad esempio **yourclustername.b4.internal.cloudapp.net**.

    * Utilizzare RDP
    
        È anche possibile utilizzare Desktop remoto per connettersi al cluster HBase (verrà connesso il nodo principale) ed eseguire **ipconfig** al prompt dei comandi per ottenere il suffisso DNS. Per istruzioni sull'attivazione protocollo RDP (Remote Desktop) e connettersi al cluster utilizzando RDP, vedere [gestire Hadoop cluster nel portale di Azure HDInsight][hdinsight-admin-portal].
        
        ![hdinsight.hbase.DNS.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Per verificare che la macchina virtuale possano comunicare con i cluster HBase, usare il comando `ping headnode0.<dns suffix>` dal computer virtuale. Ad esempio, ping headnode0.mycluster.b1.cloudapp.net.

Per usare queste informazioni in un'applicazione Java, è possibile eseguire la procedura descritta in [Maven utilizzare la creazione di applicazioni Java che utilizzano HBase con HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) per creare un'applicazione. Per connettersi a un server remoto HBase l'applicazione, modificare il file **hbase site.xml** in questo esempio usare il nome di dominio completo per Zookeeper. Per esempio:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Per ulteriori informazioni sulla risoluzione dei nomi in Azure reti virtuali, incluso l'uso di server DNS vedere [Risoluzione DNS (Name)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come creare un cluster di HBase. Per ulteriori informazioni, vedere:

- [Guida introduttiva a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurare la replica HBase HDInsight](hdinsight-hbase-geo-replication.md)
- [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md)
- [Iniziare a usare HBase con Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analizzare valutazione in Twitter con HBase in HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Panoramica di rete virtuale][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Dettagli della disposizione per il nuovo cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Azione di Script per personalizzare un cluster di HBase"

[azure-preview-portal]: https://portal.azure.com
