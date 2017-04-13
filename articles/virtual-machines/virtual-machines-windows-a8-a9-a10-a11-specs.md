<properties
 pageTitle="Sulle macchine virtuali complesse con Windows | Microsoft Azure"
 description="Ottenere informazioni di base e considerazioni utilizzando le dimensioni di complesse H serie e A8, A9, A10 e A11 Azure per i servizi cloud e macchine virtuali di Windows"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Sulle macchine virtuali di serie H serie e complesse

Ecco alcune considerazioni per l'utilizzo della nuova serie H Azure e le istanze di A8, A9, A10 e A11 precedente, dette anche le istanze di *complesse* e informazioni. In questo articolo viene illustrato l'utilizzo di questi casi per macchine virtuali di Windows. In questo articolo è disponibile anche per [Le macchine virtuali Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accesso alla rete RDMA

È possibile creare cluster istanze del Server Windows in grado di RDMA e distribuire una delle implementazioni MPI supportate in modo da sfruttare la rete RDMA Azure. Questa rete bassa latenza, ad alta velocità riservata MPI solo per il traffico.

* **Sistema operativo**
    * **Macchine virtuali** - Windows Server 2012 R2, Windows Server 2012
    * **Servizi cloud** - Windows Server 2012 R2, Windows Server 2012, famiglia di sistemi operativi Guest di Windows Server 2008 R2

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 o versioni successive, libreria MPI Intel 5. x

Implementazioni MPI supportate utilizzano l'interfaccia Microsoft rete diretto per le comunicazioni tra le istanze. Per le opzioni di distribuzione e passaggi di configurazione di esempio, vedere [configurare un cluster di Windows RDMA con HPC Pack per l'esecuzione di applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) e [utilizzare più istanze attività per eseguire le applicazioni di passando interfaccia MPI (Message) nel Batch di Azure](../batch/batch-mpi.md) .


>[AZURE.NOTE]In macchine virtuali complesse dotati di RDMA, l'estensione HpcVmDrivers deve essere aggiunta ai macchine virtuali per installare i driver di dispositivo di rete Windows sono necessari per la connettività RDMA. Nella maggior parte delle distribuzioni estensione HpcVmDrivers viene aggiunto automaticamente. Se è necessario aggiungere l'estensione se stessi, vedere [gestire macchine Virtuali estensioni](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Considerazioni per HPC Pack e Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster HPC gratuito di Microsoft e soluzione di gestione dei processi, non è necessario per è possibile utilizzare le varianti complesse con Windows Server. Tuttavia, è un'opzione per la creazione di un cluster di elaborazione in Azure per l'esecuzione di applicazioni MPI basato su Windows e altri carichi di lavoro HPC. HPC Pack 2012 R2 e versioni successive includono un ambiente di runtime per MS-MPI che è possibile usare la rete RDMA Azure quando distribuito in macchine virtuali in grado di RDMA.

Per ulteriori informazioni e gli elenchi di controllo da utilizzare le varianti complesse con HPC Pack in Windows Server, vedere [configurare un cluster di Windows RDMA con HPC Pack per l'esecuzione di applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulla disponibilità e prezzi di dimensioni elevate, vedere [macchine virtuali di prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e i [servizi Cloud prezzi](https://azure.microsoft.com/pricing/details/cloud-services/).

* Per informazioni su disco e capacità di archiviazione, vedere [dimensioni per macchine virtuali](virtual-machines-linux-sizes.md).

* Per iniziare la distribuzione e utilizzando le istanze di complesse con HPC Pack in Windows, vedere [configurare un cluster di Windows RDMA con HPC Pack per l'esecuzione di applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Per informazioni sull'utilizzo di istanze A8 e A9 per l'esecuzione di applicazioni MPI con Azure Batch, vedere [attività di più istanze di usare per eseguire le applicazioni di messaggio passando Interface (MPI) nel Batch di Azure](../batch/batch-mpi.md).
