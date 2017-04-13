<properties
    pageTitle="Estendere HDInsight con virtuali | Microsoft Azure"  
    description="Informazioni su come usare virtuali Azure a cui connettersi HDInsight altre risorse cloud o risorse nel centro dati"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="larryfr"/>


#<a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Estendere le funzionalità HDInsight utilizzando virtuali Azure

Azure virtuali consente di estendere le soluzioni Hadoop per incorporare risorse locale, ad esempio SQL Server, combinare più tipi di cluster HDInsight o per creare le reti private sicure tra le risorse nel cloud.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>Che cos'è virtuali Azure?

[Azure virtuali](https://azure.microsoft.com/documentation/services/virtual-network/) consente di creare una rete sicura, persistente contenente le risorse che necessarie per la soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud insieme in una rete privata (basata solo su cloud).

    ![diagramma della configurazione basata solo su cloud](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

    Usare virtuali per il collegamento servizi Azure con Azure HDInsight attiva gli scenari seguenti:

    * **Richiamare HDInsight servizi o processi** da siti Web Azure o servizi in esecuzione in macchine virtuali di Azure.

    * **Trasferire direttamente dati** tra HDInsight e Database di SQL Azure, SQL Server o un'altra soluzione di archiviazione di dati in esecuzione in una macchina virtuale.

    * **Combinare più HDInsight server** in una soluzione. HDInsight cluster siano disponibili numerosi tipi corrispondono al carico di lavoro o la tecnologia che il cluster è ottimizzato per. Non esiste alcun metodo supportato per creare un cluster che combina più tipi, ad esempio eccesso e HBase in un cluster. Uso di una rete virtuale consente più cluster comunicare direttamente tra loro.

* Connettere le risorse cloud alla rete Data Center locale (da sito o punto al sito) tramite una rete privata virtuale (VPN).

    Configurazione del sito a sito consente di connettere più risorse dal centro dati per la rete virtuale Azure mediante hardware VPN o il servizio Routing e accesso remoto.

    ![diagramma di configurazione del sito al sito](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

    Configurazione del punto di sito consente di connettere una risorsa specifica per la rete virtuale Azure mediante software VPN.

    ![diagramma di configurazione del punto di sito](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

    Usare virtuali per il collegamento nel cloud e la Data Center consente scenari simili alla configurazione basata solo su cloud. Ma anziché limitarsi a utilizzo delle risorse nel cloud, è anche possibile collaborare con le risorse nel centro dati.

    * **Trasferire direttamente dati** tra HDInsight e la Data Center. Un esempio utilizza Sqoop per trasferire i dati in o da SQL Server o si leggono dati generati da un'applicazione (Line) line-of-business.

    * **Richiamare HDInsight servizi o processi** da un'applicazione line. Un esempio utilizza le API Java HBase per archiviare e recuperare dati da un cluster di HDInsight HBase.

Per ulteriori informazioni sulle funzionalità virtuali, vantaggi e funzionalità, vedere [Panoramica di Azure virtuali](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE] È necessario creare la rete virtuale Azure prima il provisioning di un cluster di HDInsight. Per ulteriori informazioni, vedere [attività di configurazione di rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Requisiti di rete virtuali

> [AZURE.IMPORTANT] La creazione di un cluster di HDInsight in una rete virtuale richiede specifiche configurazioni di rete virtuale, descritte in questa sezione.

###<a name="location-based-virtual-networks"></a>Reti virtuali basata sulla posizione

Azure HDInsight supporta solo le reti virtuali basata sulla posizione e non funziona con le reti virtuali in base a gruppo affinità.

###<a name="classic-or-v2-virtual-network"></a>Classic o v2 virtuali

Basato su Windows cluster richiedono una rete virtuale classico, mentre cluster basati su Linux richiedono una rete virtuale di Azure Manager delle risorse. Se non è il tipo corretto di rete, non sarà utilizzabile quando si crea il cluster.

Se si dispone di risorse in una rete virtuale che non può essere utilizzata dal cluster che si intende creare, è possibile creare una nuova rete virtuale che può essere utilizzata dal cluster e connettersi alla rete virtuale compatibile. È possibile creare il cluster nella versione di rete che richiede l'esecuzione e sarà in grado di accedere alle risorse in un'altra rete poiché il join di due. Per ulteriori informazioni sulla connessione classica e nuove reti virtuali, vedere [connessione VNets classico per VNets nuovo](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

###<a name="custom-dns"></a>Custom DNS

Quando si crea una rete virtuale, Azure offre la risoluzione dei nomi predefiniti per i servizi Azure, ad esempio HDInsight che vengono installati nella rete. Tuttavia potrebbe essere necessario usare il proprio sistema DNS (Domain Name) per le situazioni ad esempio tra la risoluzione dei nomi di dominio di rete. Ad esempio, se le comunicazioni tra servizi che si trovano in due aggiungono reti virtuali. HDInsight supporta sia la risoluzione predefinita nome Azure, nonché DNS personalizzato quando si utilizza virtuali Azure.

Per ulteriori informazioni sull'utilizzo di server DNS con Azure virtuali, vedere la sezione __risoluzione dei nomi utilizzando server DNS__ del documento [La risoluzione dei nomi per macchine virtuali e istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) .

###<a name="secured-virtual-networks"></a>Reti virtuali protette

Il servizio HDInsight è un servizio gestito e richiede l'accesso a Internet durante il provisioning e mentre è in esecuzione. Si tratta di tale Azure monitorare l'integrità del cluster, avviare failover delle risorse cluster, cambiare il numero dei nodi in cluster tramite le operazioni di ridimensionamento e altre attività di gestione.

Se è necessario installare HDInsight in una rete virtuale protetto, è necessario consentire l'accesso in ingresso sulla porta 443 per gli indirizzi IP seguenti, che consentono di Azure gestire il cluster HDInsight.

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Consentire l'accesso in ingresso dalla porta 443 per questi indirizzi è possibile installare HDInsight in una rete virtuale protetta.

> [AZURE.IMPORTANT] HDInsight non supporta la limitazione del traffico in uscita, solo il traffico in ingresso. Quando si definiscono regole gruppo di sicurezza di rete per la subnet contenente HDInsight, utilizzare solo le regole in entrata.

Negli esempi seguenti viene illustrato come creare un nuovo gruppo di sicurezza di rete che consente di indirizzi richiesti e il gruppo di sicurezza viene applicata a una subnet all'interno della rete virtuale. Questa procedura si presuppone che è già stato creato un virtuali e subnet che si desidera installare HDInsight in.

__Utilizzo di PowerShell Azure__

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroupId $nsg

__Usa CLI Azure__

1. Usare il comando seguente per creare un nuovo gruppo di sicurezza di rete denominato `hdisecure`. Sostituire __RESOURCEGROUPNAME__ e un __percorso__ con il gruppo di risorse contenente la rete virtuale Azure e il percorso (area) che il gruppo è stato creato in.

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    Dopo aver creato il gruppo, si riceverà le informazioni sul nuovo gruppo. Individuare la riga simile al seguente e salvare la `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure` informazioni. Da utilizzare in un passaggio successivo.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. Utilizzare le operazioni seguenti per aggiungere regole per il nuovo gruppo di sicurezza di rete che consentono di comunicazioni in ingresso sulla porta 443 dal servizio di salute e gestione di Azure HDInsight. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo di risorse contenente la rete virtuale Azure.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. Dopo avere create le regole, utilizzare le operazioni seguenti per applicare il nuovo gruppo di sicurezza di rete a una subnet. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo di risorse contenente la rete virtuale Azure. Sostituire __VNETNAME__ e __SUBNETNAME__ con il nome della rete virtuale Azure e subnet che si desidera utilizzare per l'installazione di HDInsight.

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    Al termine di questo comando, è possibile installare correttamente HDInsight alla rete virtuale protetta subnet utilizzato in questa procedura.

> [AZURE.IMPORTANT] Mediante l'accesso solo Apri passaggi sopra al servizio di salute e gestione di HDInsight nel cloud Azure. In questo modo è possibile installare un cluster di HDInsight in subnet, ma l'accesso al cluster HDInsight dall'esterno della rete virtuale è bloccato per impostazione predefinita. È necessario aggiungere regole gruppo di sicurezza di rete aggiuntive se si desidera abilitare l'accesso dall'esterno della rete virtuale.
>
> Ad esempio, per consentire l'accesso SSH da internet, sarà necessario aggiungere una regola simile al seguente: 
>
> * Azure PowerShell-```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure CLI-```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

Per ulteriori informazioni sui gruppi di sicurezza di rete, vedere [Panoramica di gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Per informazioni su come controllare il routing in una rete virtuale Azure, vedere [route definiti dall'utente e indirizzi IP di inoltro](../virtual-network/virtual-networks-udr-overview.md).

##<a id="tasks"></a>Attività e delle informazioni

In questa sezione contiene informazioni sulle attività comuni e informazioni che potrebbe essere necessario quando si usa HDInsight con una rete virtuale.

###<a name="determine-the-fqdn"></a>Determinare il nome di dominio completo

Cluster HDInsight verrà assegnato un nome di dominio completo (FQDN) specifiche per l'interfaccia di rete virtuale. Questo è l'indirizzo da utilizzare quando ci si connette al cluster da altre risorse sulla rete virtuale. Per determinare il nome di dominio completo, utilizzare le operazioni seguenti per URL per il servizio di gestione Ambari della query:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Per ulteriori informazioni sull'utilizzo di Ambari con HDInsight, vedere [cluster Hadoop Monitor in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md).

È necessario specificare il nome del cluster e un servizio e componente in esecuzione su cluster, ad esempio Gestione risorse filati.

> [AZURE.NOTE] I dati restituiti sono un documento JavaScript Object Notation (JSON) che contiene una quantità elevata di informazioni sul componente. Per estrarre solo il nome di dominio completo, è necessario utilizzare un parser JSON per recuperare la `host_components[0].HostRoles.host_name` valore.

Ad esempio, per restituire il nome di dominio completo di un cluster di HDInsight Hadoop, è possibile utilizzare uno dei metodi seguenti per recuperare i dati per la gestione di risorse filati:

* [PowerShell Azure](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/     components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [voltare](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###<a name="connecting-to-hbase"></a>Connessione a HBase

Per connettersi a HBase in modalità remota tramite l'API di linguaggio, è necessario determinare gli indirizzi di base ZooKeeper per il cluster HBase e specificare questa operazione nell'applicazione.

Per ottenere il ZooKeeper indirizzo di base, usare uno dei metodi seguenti per il servizio di gestione Ambari della query:

* [PowerShell Azure](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [voltare](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Per ulteriori informazioni sull'utilizzo di Ambari con HDInsight, vedere [cluster Hadoop Monitor in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md).

Dopo avere inserito le informazioni di base, utilizzarlo nell'applicazione client.

Ad esempio, per un'applicazione di linguaggio che utilizza l'API HBase, da aggiungere un file di **hbase site.xml** al progetto e specificare le informazioni di base nel file come indicato di seguito:

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###<a name="verify-network-connectivity"></a>Verificare la connettività di rete

Alcuni servizi, ad esempio SQL Server, è possono limitare le connessioni di rete in ingresso. Per poter utilizzare correttamente con questi servizi ciò impedirà HDInsight.

Se si verificano problemi di accesso a un servizio da HDInsight, vedere la documentazione del servizio per assicurarsi di aver attivato accesso alla rete. È possibile verificare l'accesso alla rete mediante la creazione di una macchina virtuale Azure nella stessa rete virtuale e usare le utilità client per verificare che la macchina virtuale è possibile connettersi al servizio tramite la rete virtuale.

##<a id="nextsteps"></a>Passaggi successivi

Negli esempi seguenti viene illustrato come utilizzare HDInsight con Azure virtuali:

* [Dati sensore Analizza con eccesso e HBase in HDInsight](hdinsight-storm-sensor-data-analysis.md) - viene illustrato come configurare un cluster eccesso e HBase in una rete virtuale, nonché come scrivere in remoto dati HBase da eccesso.

* [Cluster di provisioning Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) - fornisce informazioni sul provisioning cluster Hadoop, incluse informazioni sull'uso di Azure virtuali.

* [Utilizzare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop-mac-linux.md) - fornisce informazioni sull'utilizzo di Sqoop per trasferire i dati con SQL Server in una rete virtuale.

Per ulteriori informazioni sulle reti virtuali Azure, vedere la [Panoramica virtuali Azure](../virtual-network/virtual-networks-overview.md).
