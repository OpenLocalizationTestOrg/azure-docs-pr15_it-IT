<properties
 pageTitle="Sulle macchine virtuali complesse con Linux | Microsoft Azure"
 description="Ottenere informazioni di base e considerazioni utilizzando le dimensioni di complesse serie H e A8, A9, A10 e A11 per macchine virtuali Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Sulle macchine virtuali di serie H serie e complesse 

Ecco alcune considerazioni per l'uso di Azure serie H più recenti e le dimensioni di A8, A9, A10 e A11 precedente, dette anche le istanze di *complesse* e informazioni. In questo articolo viene illustrato l'utilizzo di questi formati per le macchine virtuali Linux. In questo articolo è anche disponibile per [Macchine virtuali di Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accesso alla rete RDMA

È possibile creare cluster di dotati di RDMA macchine virtuali Linux che Esegui una delle operazioni seguenti supportate distribuzioni HPC Linux e un'implementazione di MPI supportata in modo da sfruttare la rete RDMA Azure. Per le opzioni di distribuzione e passaggi di configurazione di esempio, vedere [configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md) .

* **Distribuzioni** - distribuire macchine virtuali da immagini in grado di RDMA SUSE Linux Enterprise Server (SLES) o HPC basati su OpenLogic CentOS in Azure Marketplace. Le seguenti immagini Marketplace supportano i driver Linux RDMA necessari:

    * SLES 12 SP1 per HPC, SLES 12 SP1 per HPC (Premium)
    
    * SLES 12 per HPC, SLES 12 per HPC (Premium)
    
    * In base centOS 7.1 HPC
    
    * In base centOS HPC 6.5
    
    >[AZURE.NOTE]Per le macchine virtuali H serie, è consigliabile una SP1 12 SLES per immagine HPC o immagini basate su CentOS 7.1 HPC.
    >
    >Le immagini basate su CentOS HPC gli aggiornamenti kernel sono disabilitati nel file di configurazione **slurp** . Ciò avviene perché il driver Linux RDMA vengono distribuiti come un pacchetto RPM e aggiornamenti dei driver potrebbero non funzionare se kernel viene aggiornato.

* **MPI** - libreria MPI Intel 5. x

    A seconda dell'immagine Marketplace si sceglie, installazione delle licenze, separata, o la configurazione di Intel MPI potrebbe essere necessarie come indicato di seguito: 
    
    * **SLES 12 SP1 per immagine HPC** - installa pacchetti Intel MPI distribuiti nella macchina virtuale eseguendo il comando seguente:
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **12 SLES per immagine HPC** - è necessario registrare separatamente per scaricare e installare Intel MPI. Vedere la [Guida all'installazione di Intel MPI raccolta](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
    
    * **Immagini basate su centOS HPC** - Intel MPI 5.1 è già installato.  

    Configurazione di sistema aggiuntivi è necessario eseguire i processi MPI in macchine virtuali raggruppate. In un cluster di macchine virtuali, ad esempio, è necessario stabilire l'attendibilità tra i nodi di elaborazione. Per accedere a impostazioni tipici, vedere [configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="considerations-for-hpc-pack-and-linux"></a>Considerazioni per HPC Pack e Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster HPC gratuito di Microsoft e soluzione di gestione dei processi, offre un'opzione per è possibile utilizzare le varianti complesse con Linux. Le versioni più recenti di supporto HPC Pack 2012 R2 diverse distribuzioni Linux per eseguire analisi sui nodi distribuito in macchine virtuali di Azure, gestito da un nodo principale di Windows Server. Con Linux dotati di RDMA nodi di calcolo in esecuzione Intel MPI, HPC Pack possono pianificare ed eseguire Linux MPI applicazioni che accedono alla rete RDMA. Per iniziare, vedere [Introduzione a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="network-topology-considerations"></a>Considerazioni sulla topologia di rete

* In abilitato RDMA Linux macchine virtuali di Azure, Eth1 riservato RDMA il traffico di rete. Non modificare le impostazioni di Eth1 o tutte le informazioni nel file di configurazione che fa riferimento a questa rete. Eth0 riservato regolare Azure il traffico di rete.

* In Azure, IP sopra i InfiniBand (b) non è supportato. È supportata solo RDMA sopra i b.

## <a name="rdma-driver-updates-for-sles-12"></a>Aggiornamenti del driver RDMA per SLES 12

Dopo aver creato una macchina virtuale in base a un'immagine SLES 12 HPC, potrebbe essere necessario aggiornare il driver RDMA in macchine virtuali RDMA la connettività di rete. 

>[AZURE.IMPORTANT]Questa operazione è **necessaria** per 12 SLES per distribuzioni HPC VM in tutti i paesi Azure. 
>Questo passaggio non è necessaria se si distribuisce una SP1 12 SLES per HPC, in base CentOS HPC 7.1 o basate su CentOS 6.5 HPC macchine Virtuali. 

Prima di aggiornare il driver, interrompere tutti i processi di **zypper** o sui processi bloccano i database di repo SUSE nella macchina virtuale. In caso contrario i driver potrebbero non vengono aggiornate correttamente.  

Per aggiornare il driver Linux RDMA in ogni macchina virtuale, eseguire una delle seguenti gruppi di comandi CLI Azure dal computer client.

**Viene completato il provisioning di 12 SLES per HPC VM nel modello di distribuzione classica**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Viene completato il provisioning di 12 SLES per HPC VM nel modello di distribuzione Manager delle risorse**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Può richiedere del tempo per installare i driver e il comando restituisce senza output. Dopo l'aggiornamento di una macchina virtuale verrà riavviato e dovrebbe essere pronta per l'utilizzo in alcuni minuti.

### <a name="sample-script-for-driver-updates"></a>Script di esempio per gli aggiornamenti dei driver

Se si dispone di un cluster di 12 SLES per macchine virtuali HPC, è possibile creare script l'aggiornamento del driver in tutti i nodi del cluster. Ad esempio il seguente script aggiorna i driver in un cluster di 8 nodi.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulla disponibilità e prezzi di dimensioni elevate, vedere [macchine virtuali di prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Per informazioni su disco e capacità di archiviazione, vedere [dimensioni per macchine virtuali](virtual-machines-linux-sizes.md).

* Per iniziare la distribuzione e utilizzo di dimensioni elevate con RDMA su Linux, vedere [configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md).


