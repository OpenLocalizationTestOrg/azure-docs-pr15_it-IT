<properties
   pageTitle="Script di PowerShell per distribuire cluster HPC Windows | Microsoft Azure"
   description="Eseguire un script di PowerShell per distribuire un cluster di Windows HPC Pack in macchine virtuali di Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Creare un cluster di elaborazione con prestazioni avanzate (HPC) di Windows con lo script di distribuzione HPC Pack IaaS

Eseguire la distribuzione di HPC Pack IaaS script di PowerShell per distribuire un cluster HPC completato per carichi di lavoro di Windows in macchine virtuali di Azure. Il cluster è costituito da un nodo principale fanno parte di Active Directory che eseguono Windows Server e Microsoft HPC Pack e altre finestre calcolano risorse specificato. Se si desidera distribuire un cluster di HPC Pack in Azure per Linux carichi di lavoro, vedere [creare un cluster HPC Linux con lo script di distribuzione HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). È anche possibile usare un modello di gestione di risorse Azure per distribuire un cluster HPC Pack. Per esempi, vedere [creare un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) e [creare un cluster HPC con una personalizzata calcolare immagine nodo](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>File di configurazione di esempio

Nell'esempio seguente, sostituire i valori per l'abbonamento Id o il nome e nomi dell'account e servizio.

### <a name="example-1"></a>Esempio 1

File di configurazione che distribuisce un cluster HPC Pack contenente un nodo principale con i database locali e cinque nodi eseguono il sistema operativo Windows Server 2012 R2. Tutti i servizi cloud vengono creati direttamente nel percorso US ovest. Il nodo principale funge da controller di dominio dell'insieme di strutture di dominio.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Esempio 2

File di configurazione che distribuisce un cluster HPC Pack in delle strutture esistenti. Il cluster ha 1 nodo principale con i database locali e 12 nodi di calcolo con estensione BGInfo VM applicata.
Installazione automatica degli aggiornamenti di Windows è disattivata per tutte le macchine virtuali nello stesso insieme. Tutti i servizi cloud vengono creati direttamente nel percorso Asia orientale. I nodi di elaborazione vengono creati in tre servizi cloud e tre gli account di archiviazione: _MyHPCCN 0001_ a _0005 MyHPCCN_ in _MyHPCCNService01_ e _mycnstorage01_; _0006 MyHPCCN_ a _MyHPCCN0010_ in _MyHPCCNService02_ e _mycnstorage02_; e _MyHPCCN 0011_ in _0012 MyHPCCN_ in _MyHPCCNService03_ e _mycnstorage03_). I nodi di elaborazione vengono creati da un'immagine esistente privata acquisita da un nodo di calcolo. Automatico ingrandimento e riduzione del servizio è attivato con predefinito ingrandimento e riduzione intervalli.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Esempio 3

File di configurazione che distribuisce un cluster HPC Pack in delle strutture esistenti. Il cluster contiene un nodo principale, un server di database con un disco di dati di 500 GB, 2 nodi di gestore del sistema operativo Windows Server 2012 R2 e cinque nodi di elaborazione del sistema operativo Windows Server 2012 R2. Il servizio cloud MyHPCCNService verrà creato nel gruppo affinità *MyIBAffinityGroup*e altri servizi cloud vengono creati nel gruppo affinità *MyAffinityGroup*. L'API REST di HPC processo di pianificazione e portale web HPC sono abilitate sul nodo principale.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Esempio 4

File di configurazione che distribuisce un cluster HPC Pack in delle strutture esistenti. Il cluster ha due nodo principale con i database locali, vengono creati due modelli nodo Azure e tre dimensioni medio Azure nodi vengono creati per il modello di Azure nodo _AzureTemplate1_. Un file di script viene eseguito sul nodo principale dopo aver configurato il nodo principale.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Risoluzione dei problemi


* **Errore "VNet non esiste"** - se si esegue lo script per distribuire più cluster in Azure contemporaneamente in una sottoscrizione, una o più distribuzioni potrebbero non riuscire con l'errore "VNet *VNet\_nome* non esiste".
Se si verifica questo errore, eseguire lo script nuovamente per la distribuzione non riuscita.

* **Problemi di accesso a Internet dalla rete virtuale Azure** - se si crea un cluster con un nuovo controller di dominio tramite lo script di distribuzione o si manualmente innalzare un nodo testa macchine Virtuali al controller di dominio, possono verificarsi problemi di connessione macchine virtuali a Internet. Questo problema può verificarsi se un server DNS server d'inoltro viene configurato automaticamente sul controller di dominio e il server DNS server d'inoltro non viene risolto correttamente.

    Per risolvere il problema, accedere a controller di dominio e rimuovere le impostazioni di configurazione del server di inoltro o configurare un server DNS server d'inoltro valido. Per configurare questa impostazione, in Server Manager, fare clic su **Strumenti** >
    **DNS** per aprire Gestore DNS e quindi fare doppio clic su **inoltro**.

* **Problemi di accesso alla rete RDMA da macchine virtuali complesse** - se si aggiunta elaborazione di Windows Server o del broker macchine virtuali di nodo utilizzando una dimensione in grado di RDMA, ad esempio A8 o A9, possono verificarsi problemi di connessione tali macchine virtuali alla rete dell'applicazione RDMA. Questo problema si verifica uno dei motivi è se quando le macchine virtuali vengono aggiunti al cluster non è installato correttamente l'estensione HpcVmDrivers. L'estensione, ad esempio, potrebbe essere bloccato nello stato di installazione.

    Per risolvere questo problema, verificare lo stato dell'estensione in macchine virtuali. Se l'estensione non è installato correttamente, provare a rimuovere i nodi dal cluster HPC e aggiungere di nuovo i nodi. Ad esempio, è possibile aggiungere il nodo di calcolo macchine virtuali eseguendo lo script HpcIaaSNode.ps1 Aggiungi sul nodo principale.
    
## <a name="next-steps"></a>Passaggi successivi

* Provare a eseguire un carico di lavoro test sul cluster. Ad esempio, vedere il HPC Pack [Guida introduttiva](https://technet.microsoft.com/library/jj884144).

* Per un'esercitazione per la distribuzione di cluster di script ed eseguire un carico di lavoro HPC, vedere [Guida introduttiva a un cluster HPC Pack in Azure eseguire carichi di lavoro di Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md).

* Provare a strumenti HPC Pack per iniziare, interrompere, aggiungere e rimuovere nodi di calcolo da un cluster che si crea. Vedere [gestire nodi in un cluster HPC Pack in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Per impostare se stessi a inviare processi al cluster da un computer locale, vedere [inviare HPC processi da un computer locale a un cluster HPC Pack in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).
