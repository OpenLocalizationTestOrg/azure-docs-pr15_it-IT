<properties
 pageTitle="Opzioni di raggruppamento Linux HPC Pack nel cloud | Microsoft Azure"
 description="Informazioni sulle opzioni con Microsoft HPC Pack per creare e gestire un Linux elaborazione con prestazioni cluster (HPC) nel cloud Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Opzioni per HPC Pack per creare e gestire un cluster HPC in Azure per Linux carichi di lavoro

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Questo articolo è incentrato sulle opzioni per l'utilizzo di HPC Pack eseguire Linux carichi di lavoro. Sono inoltre disponibili opzioni per l'esecuzione di [Windows HPC carichi di lavoro con HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Eseguire un cluster HPC Pack in macchine virtuali di Azure

### <a name="azure-templates"></a>Modelli di Azure


* (Marketplace) [Cluster HPC Pack per Linux carichi di lavoro](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Guida introduttiva) [Creare un cluster HPC con Linux nodi di calcolo](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>Script di PowerShell distribuzione

* [Creare un cluster HPC Linux con lo script di distribuzione HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Esercitazioni

* [Esercitazione: Introduzione a Linux nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Esercitazione: Esegui NAMD con Microsoft HPC Pack su Linux nodi in Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Esercitazione: Esegui OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Esercitazione: Esegui STELLA-CCM + con Microsoft HPC Pack su un RDMA Linux cluster Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>Gestione cluster

* [Inviare i processi a un cluster HPC Pack in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestione dei processi in HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>Creare cluster RDMA per carichi di lavoro MPI

* [Esercitazione: Esegui OpenFOAM con Microsoft HPC Pack in un cluster Linux RDMA in Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurare un cluster Linux RDMA per l'esecuzione di applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md)

