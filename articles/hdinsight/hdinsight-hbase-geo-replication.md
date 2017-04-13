<properties 
   pageTitle="Configurare la replica HBase tra due Data Center | Microsoft Azure" 
   description="Informazioni su come configurare HBase replica tra due centri dati e informazioni sui casi di utilizzo per replica di cluster." 
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
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>Configurare la replica di geografico HBase in HDInsight

> [AZURE.SELECTOR]
- [Configurare la connettività VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurare il sistema DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurare la replica HBase](hdinsight-hbase-geo-replication.md) 
 
Informazioni su come configurare la replica HBase tra due centri dati. Alcuni casi di utilizzo per la replica cluster includono:

- Copia di backup e ripristino di emergenza
- Aggregazione dei dati
- Distribuzione di dati geografici
- Acquisizione di dati online combinato con analitica dati non in linea

La replica cluster viene utilizzata una metodologia push di origine. Un cluster di HBase può essere un'origine, una destinazione, oppure possa soddisfare entrambi i ruoli contemporaneamente. La replica è asincrona e l'obiettivo della replica è coerenza finale. Quando l'origine riceve una modifica a una famiglia di colonna con replica abilitata, tale modifica viene propagato a tutti i cluster di destinazione. Quando si replica dati da un cluster a un altro, il cluster di origine e tutti i cluster che hanno già utilizzato i dati vengono rilevati per evitare cicli di replica. Per ulteriori informazioni, In questa esercitazione si configura una replica di origine e destinazione.  Per le altre tipologie cluster, vedere [Guida di riferimento HBase Apache](http://hbase.apache.org/book.html#_cluster_replication).

Questa è la terza parte della serie:

- [Configurare una connessione VPN tra due reti virtuale][hdinsight-hbase-replication-vnet]
- [Configurare il DNS per le reti virtuali][hdinsight-hbase-replication-dns]
- Configurare la replica geografico HBase (in questa esercitazione)

Il diagramma seguente illustra le due reti virtuale e la connettività di rete è stato creato in [configurare una connessione VPN tra due reti virtuali] [ hdinsight-hbase-geo-replication-vnet] e [Configurare DNS per le reti virtuali][hdinsight-hbase-replication-dns]: 

![Diagramma di rete virtuale replica HDInsight HBase][img-vnet-diagram]

## <a id="prerequisites"></a>Prerequisiti di

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un lavoro utilizzando Azure PowerShell**.

    Per eseguire gli script di PowerShell, è necessario eseguire PowerShell Azure come amministratore e impostare il criterio di esecuzione su *RemoteSigned*. Vedere utilizzando il cmdlet Set-ExecutionPolicy.
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Azure due virtuali con la connettività VPN e servizio DNS è configurato**.  Per ulteriori informazioni, vedere [configurare una connessione VPN tra due reti virtuali Azure][hdinsight-hbase-replication-vnet]e [Configura DNS tra due reti virtuale Azure][hdinsight-hbase-replication-dns].


    Prima di eseguire gli script di PowerShell, assicurarsi che si è connessi al proprio abbonamento Azure mediante il seguente cmdlet:

        Add-AzureAccount

    Se si hanno più abbonamenti Azure, utilizzare il cmdlet seguente per impostare l'abbonamento corrente:

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>Effettuare il provisioning di cluster HBase in HDInsight

In [Configura una connessione VPN tra due reti virtuali Azure][hdinsight-hbase-replication-vnet], aver creato una rete virtuale in un Europa centro dati e una rete virtuale in un data center US. La rete virtuale due sono connesse tramite VPN. In questa sessione viene viene effettuato il provisioning di un cluster di HBase in ognuna delle reti virtuali. Più avanti in questa esercitazione si renderà uno dei cluster HBase replicare altri cluster HBase.

Portale classica di Azure non supporta provisioning cluster HDInsight con le opzioni di configurazione personalizzata. Ad esempio, impostare *hbase.replication* su *true*. Se si imposta il valore nel file di configurazione dopo un cluster viene eseguito il provisioning, si perderanno l'impostazione dopo il cluster viene ricreato l'immagine. Per ulteriori informazioni vedere [cluster di provisioning Hadoop in HDInsight][hdinsight-provision]. Una delle opzioni per effettuare il provisioning cluster HDInsight con opzioni personalizzate utilizza Azure PowerShell.


**Effettuare il provisioning di un Cluster di HBase nell'Unione europea di VNet Contoso** 

1. Aprire Windows PowerShell ISE workstation.
2. Impostare le variabili all'inizio dello script e quindi eseguire lo script.

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Effettuare il provisioning di un Cluster di HBase in Contoso-VNet-US** 

- Utilizzare lo stesso script con i valori seguenti:

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    Perché già connessi al proprio account Azure, non è necessario eseguire più comlets seguenti:

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>Configurare i server d'inoltro condizionale DNS

[Configurare]DNS per le reti virtuali[hdinsight-hbase-replication-dns], è stato configurato i server DNS per le due reti. Cluster HBase hanno suffissi di dominio diverso. Pertanto è necessario configurare ulteriori inoltro condizionale.

Per configurare server d'inoltro condizionale, è necessario conoscere i suffissi del dominio di due cluster di HBase. 

**Per trovare i suffissi del dominio di due cluster di HBase**

1. RDP in **Contoso HBase UE**.  Per ulteriori informazioni, vedere [gestire Hadoop cluster in HDInsight tramite il portale classica Azure][hdinsight-manage-portal]. È effettivamente headnode0 del cluster.
2. Aprire una console di Windows PowerShell o un prompt dei comandi.
3. Eseguire **ipconfig**e prendere nota **suffisso DNS specifici della connessione**.
4. Non chiudere la sessione RDP.  Sarà necessario in seguito per verificare la risoluzione dei nomi di dominio.
5. Ripetere questi passaggi per individuare il **suffisso DNS specifici della connessione** di **Contoso-HBase-US**.


**Per configurare l'inoltro**
 
1.  RDP **Dell'Unione europea Contoso DNS**. 
2.  Premere il tasto Windows in basso a sinistra.
2.  Fare clic su **Strumenti di amministrazione**.
3.  Fare clic su **DNS**.
4.  Nel riquadro a sinistra espandere **l'errore DSN**, **Dell'Unione europea Contoso DNS**.
5.  Destro **Server d'inoltro condizionali**e quindi fare clic su **Nuovo server d'inoltro condizionale**. 
5.  Immettere le informazioni seguenti:
    - **Dominio DNS**: immettere il suffisso di Contoso-HBase-US. Ad esempio: Contoso-HBase-US.f5.internal.cloudapp.net.
    - **Indirizzi IP dei server master**: immettere 10.2.0.4, indirizzo IP di Contoso-DNS-US. Verificare l'indirizzo IP. Il server DNS può avere un indirizzo IP diverso.
6.  Premere **INVIO**e quindi fare clic su **OK**.  A questo punto sarà possibile risolvere indirizzo IP del Contoso-DNS-US dell'Unione europea Contoso DNS.
7.  Ripetere i passaggi per aggiungere un server di inoltro condizionale DNS per il servizio DNS sul computer virtuale degli Stati Uniti Contoso DNS con i valori seguenti:
    - **Dominio DNS**: immettere il suffisso dell'UE di HBase Contoso. 
    - **Indirizzi IP dei server master**: immettere 10.2.0.4, indirizzo IP di Contoso-DNS-dell'Unione europea.

**Per verificare la risoluzione dei nomi di dominio**

1. Passare alla finestra dell'Unione europea Contoso HBase RDP.
2. Aprire un prompt dei comandi.
3. Eseguire il comando ping:

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    Il protocollo colori è attivato i nodi di lavoro di cluster HBase

4. Non chiudere la sessione RDP. Sarà comunque essere necessario in un secondo momento nell'esercitazione.
5. Ripetere la stessa procedura per effettuare il ping headnode0 dell'Unione europea HBase Contoso da Contoso-HBase-US.

>[AZURE.IMPORTANT] DNS deve lavorare prima di procedere alla procedura seguente.

## <a name="enable-replication-between-hbase-tables"></a>Abilitare replica tra le tabelle HBase

A questo punto, è possibile creare una tabella di esempio HBase, abilitare la replica e poi provare con alcuni dati. La tabella di esempio si utilizzerà con due gruppi di colonna: Personal e Office. 

In questa esercitazione si renderà cluster HBase Europa come cluster di origine e il cluster HBase degli Stati Uniti come cluster di destinazione.

Creare tabelle HBase con gli stessi nomi e le famiglie di colonna nei cluster di origine e destinazione, in modo che il cluster di destinazione in grado di archiviare dati che verrà visualizzato. Per ulteriori informazioni sull'utilizzo di shell HBase, vedere [Introduzione a HBase Apache in HDInsight][hdinsight-hbase-get-started].

**Per creare una tabella di HBase su dell'Unione europea Contoso HBase**

1. Passare alla finestra **Dell'Unione europea Contoso HBase** RDP.
2. Dal desktop, fare clic su **riga di comando Hadoop**.
2. Modificare la cartella HBase home directory:

        cd %HBASE_HOME%\bin
3. Aprire la shell HBase:

        hbase shell
4. Creare una tabella di HBase:

        create 'Contacts', 'Personal', 'Office'
5. Non chiudere la sessione RDP né finestra della riga di comando Hadoop. Sarà comunque necessario loro in un secondo momento nell'esercitazione.
    
**Per creare una tabella di HBase su Contoso-HBase-US**

- Ripetere la stessa procedura per creare la stessa tabella su Contoso-HBase-US.


**Per aggiungere Contoso-HBase-US come peer replica**

1. Passare alla finestra RDP **Contso HBase_EU** .
2. Nella finestra di shell HBase aggiungere cluster di destinazione (Contoso-HBase-Stati Uniti) come peer, ad esempio:

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    Nell'esempio, il suffisso del dominio è *contoso-hbase-us.d4.internal.cloudapp.net*. È necessario aggiornare in modo che corrisponda il suffisso di dominio del cluster Contattaci HBase. Assicurarsi che non sia spazi tra il nome host.

**Per configurare ogni famiglia di colonna da replicare nel cluster di origine**

1. Nella finestra di shell HBase della sessione RDP **Dell'Unione europea Contso HBase** configurare ogni famiglia di colonna da replicare:

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**Per caricare i dati alla tabella HBase**

File di dati di esempio è stato caricato in un contenitore di Blob Azure pubblico con l'URL seguente:

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Il contenuto del file:

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

È possibile caricare lo stesso file di dati in cluster HBase e importare i dati da tale posizione.

1. Passare alla finestra **Dell'Unione europea Contoso HBase** RDP.
2. Dal desktop, fare clic su **riga di comando Hadoop**.
3. Modificare la cartella HBase home directory:

        cd %HBASE_HOME%\bin

4. caricare i dati:

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>Verificare che la replica di dati è stata effettuata

È possibile verificare che la replica è stata effettuata da analizzare le tabelle da entrambi i cluster con i comandi di shell HBase seguenti:

        Scan 'Contacts'


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono appreso come configurare la replica HBase tra due Data Center. Per ulteriori informazioni su HDInsight e HBase, vedere:

- [Pagina del servizio HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentazione HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Guida introduttiva a Apache HBase in HDInsight][hdinsight-hbase-get-started]
- [Panoramica di HDInsight HBase][hdinsight-hbase-overview]
- [Effettuare il provisioning di cluster HBase su Azure virtuali][hdinsight-hbase-provision-vnet]
- [Analizzare in tempo reale valutazione in Twitter con HBase][hdinsight-hbase-twitter-sentiment]
- [Analisi dei dati sensore con eccesso e HBase in HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
