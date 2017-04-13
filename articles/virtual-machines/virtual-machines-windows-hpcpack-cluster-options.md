<properties
 pageTitle="Opzioni di cluster di Windows HPC Pack nel cloud | Microsoft Azure"
 description="Informazioni sulle opzioni con Microsoft HPC Pack per creare e gestire elevato Windows performance computing cluster (HPC) nel cloud Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Opzioni per HPC Pack per creare e gestire un cluster di HPC di Windows Azure

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

In questo articolo è incentrato sulle opzioni per creare cluster HPC Pack per eseguire carichi di lavoro di Windows. Sono inoltre disponibili opzioni per la creazione di cluster eseguire [HPC Linux carichi di lavoro con HPC Pack](virtual-machines-linux-hpcpack-cluster-options.md).


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Eseguire un cluster HPC Pack in macchine virtuali di Azure

### <a name="azure-templates"></a>Modelli di Azure

* (Marketplace) [Cluster HPC Pack per carichi di lavoro di Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Cluster HPC Pack per carichi di lavoro di Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Guida introduttiva) [Creare un cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Guida introduttiva) [Creare un cluster HPC con immagine del nodo di calcolo personalizzato](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Immagini di macchine Virtuali Azure

* [HPC Pack in Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nodo di calcolo HPC Pack in Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nodo con Excel in Windows Server 2012 R2 calcolo HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### <a name="powershell-deployment-script"></a>Script di PowerShell distribuzione

* [Creare un cluster HPC con lo script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Esercitazioni

* [Esercitazione: Introduzione a un cluster HPC Pack in Azure eseguire carichi di lavoro di Excel e SOA](virtual-machines-windows-excel-cluster-hpcpack.md)



### <a name="manual-deployment-with-the-azure-portal"></a>Distribuzione manuale con il portale di Azure

* [Configurare il nodo principale di un cluster HPC Pack in una macchina virtuale Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### <a name="cluster-management"></a>Gestione cluster

* [Gestire i nodi di calcolo in un cluster HPC Pack in Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Ingrandimento e riduzione del calcolo Azure risorse in un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Inviare i processi a un cluster HPC Pack in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestione dei processi in HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Aggiungere i nodi di ruolo lavoro a un cluster HPC Pack


* [Burst istanze di lavoro Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Esercitazione: Configurare un cluster di ibrido con HPC Pack in Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Aggiungere un nodo principale HPC Pack in Azure nodi di Azure "burst"](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## <a name="integrate-with-azure-batch"></a>Integrazione con Azure Batch 

* [Burst Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Creare cluster RDMA per carichi di lavoro MPI

* [Configurare un cluster di Windows RDMA con HPC Pack per l'esecuzione di applicazioni MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)
