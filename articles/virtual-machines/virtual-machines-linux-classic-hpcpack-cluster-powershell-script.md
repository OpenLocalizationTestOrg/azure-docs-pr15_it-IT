<properties
   pageTitle="Script di PowerShell per distribuire cluster HPC Linux | Microsoft Azure"
   description="Eseguire un script di PowerShell per distribuire un cluster di Linux HPC Pack in macchine virtuali di Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Creare un Linux elevato performance computing cluster (HPC) con lo script di distribuzione HPC Pack IaaS

Eseguire la distribuzione di HPC Pack IaaS script di PowerShell per distribuire un cluster HPC completato per Linux carichi di lavoro in macchine virtuali di Azure. Il cluster è costituito da un nodo principale fanno parte di Active Directory che eseguono Windows Server e Microsoft HPC Pack e nodi di calcolo da eseguono una delle distribuzioni Linux supportate da HPC Pack. Se si desidera distribuire un cluster HPC Pack in carichi di lavoro di Azure per Windows, vedere [creare un cluster di Windows HPC con lo script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). È anche possibile usare un modello di gestione di risorse Azure per distribuire un cluster HPC Pack. Ad esempio, vedere [creare un cluster HPC con Linux nodi di calcolo](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>File di configurazione di esempio

File di configurazione seguente viene creato un nuovo controller di dominio e delle strutture e lo distribuisce un cluster HPC Pack con 1 nodo principale con i database locali e 10 nodi di calcolo Linux. Tutti i servizi cloud vengono creati direttamente nel percorso Asia orientale. I nodi di elaborazione Linux vengono creati in 2 servizi cloud e gli account di archiviazione 2 (ad esempio _MyLnxCN 0001_ a _0005 MyLnxCN_ in _MyLnxCNService01_ e _mylnxstorage01_) e _MyLnxCN 0006_ a _0010 MyLnxCN_ in _MyLnxCNService02_ e _mylnxstorage02_. I nodi di elaborazione vengono creati da un'immagine di OpenLogic CentOS versione 7.0 Linux. 

Sostituire i valori per il nome dell'abbonamento e i nomi di account e servizio.

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
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Risoluzione dei problemi

* **Errore "VNet non esiste"** - se si esegue lo script di distribuzione HPC Pack IaaS per distribuire più cluster in Azure contemporaneamente in una sottoscrizione, una o più distribuzioni potrebbero non riuscire con l'errore "VNet *VNet\_nome* non esiste".
Se si verifica questo errore, eseguire nuovamente lo script per la distribuzione non riuscita.

* **Problemi di accesso a Internet dalla rete virtuale Azure** - se si crea un cluster HPC Pack con un nuovo controller di dominio tramite lo script di distribuzione o si manualmente innalzare un nodo testa macchine Virtuali al controller di dominio, possono verificarsi problemi di connessione macchine virtuali nella rete virtuale Azure a Internet. Questo può verificarsi se un server DNS server d'inoltro viene configurato automaticamente sul controller di dominio e il server DNS server d'inoltro non viene risolto correttamente.

    Per risolvere il problema, accedere a controller di dominio e rimuovere le impostazioni di configurazione del server di inoltro o configurare un server DNS server d'inoltro valido. A tale scopo, in Server Manager, fare clic su **Strumenti** >
    **DNS** per aprire Gestore DNS e quindi fare doppio clic su **inoltro**.
    
## <a name="next-steps"></a>Passaggi successivi

* Vedere [Guida introduttiva a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md) per informazioni sulle distribuzioni Linux supportate, lo spostamento dei dati e l'invio di processi a un cluster HPC Pack con Linux nodi di calcolo.
* Per esercitazioni che utilizzano lo script per creare un cluster ed eseguire un carico di lavoro HPC Linux, vedere:
    * [Eseguire NAMD con Microsoft HPC Pack su Linux nodi di calcolo in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Eseguire OpenFOAM con Microsoft HPC Pack su Linux nodi di calcolo in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Eseguire STELLA-CCM + con Microsoft HPC Pack su Linux nodi in Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
