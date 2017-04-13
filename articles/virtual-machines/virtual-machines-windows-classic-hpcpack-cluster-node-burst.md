<properties
 pageTitle="Aggiungere i nodi burst a un cluster HPC Pack | Microsoft Azure"
 description="Informazioni su come espandere un cluster HPC Pack in Azure su richiesta aggiungendo istanze del ruolo di lavoro in esecuzione in un servizio cloud"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Aggiungere i nodi su richiesta "burst" a un cluster HPC Pack in Azure



Se è stato configurato un cluster di [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) in Azure, è possibile ridimensionare rapidamente la capacità di cluster, verso l'alto o verso il basso, senza mantenere un insieme di nodo di calcolo preconfigurato macchine virtuali. In questo articolo viene illustrato come aggiungere nodi su richiesta "burst" (istanze del ruolo di lavoro in esecuzione in un servizio cloud) come risorse di calcolo per un nodo principale in Azure. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

![Nodi burst][burst]

La procedura descritta in questo articolo consentono di aggiungere nodi Azure rapidamente a un tipo basato su cloud HPC Pack testa nodo macchine Virtuali per una distribuzione di prova o di prova. La procedura generale sarà la stessa procedura per "burst in Azure" per aggiungere cloud calcolare capacità a un cluster di HPC Pack locale. Per un'esercitazione, vedere [configurare una distribuzione ibrida di calcolare cluster con Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Per istruzioni dettagliate e considerazioni per le installazioni di produzione, vedere [Burst Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).


## <a name="prerequisites"></a>Prerequisiti

* **Nodo principale HPC Pack distribuita in una macchina virtuale Azure** , è possibile utilizzare un nodo principale autonomo macchine Virtuali o uno che fa parte di un cluster di dimensioni maggiore. Per creare un nodo principale autonomo, vedere [distribuire un nodo HPC Pack testa una macchina virtuale Azure](virtual-machines-windows-hpcpack-cluster-headnode.md). Per le opzioni di distribuzione cluster HPC Pack automatica, vedere [Opzioni per creare e gestire un cluster di Windows HPC in Azure con Microsoft HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

    >[AZURE.TIP] Se si usa lo [script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) per creare il cluster di Azure, è possibile includere nodi burst Azure nella distribuzione automatica. Vedere gli esempi riportati in questo articolo.

* **Abbonamento azure** , per aggiungere nodi Azure, è possibile scegliere lo stesso abbonamento utilizzato per distribuire il nodo principale macchine Virtuali, o una sottoscrizione diversa (o abbonamenti).

* **Quota core** - è possibile aumentare la quota di core, soprattutto se si sceglie di distribuire diversi nodi Azure con dimensioni multi-core. Per aumentare una quota, [aprire una richiesta di assistenza clienti online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) senza costi aggiuntivi.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Passaggio 1: Creare un servizio cloud e un account di archiviazione per i nodi di Azure

Usare il portale classica Azure o strumenti equivalenti per configurare le risorse seguenti sono necessari per distribuire i nodi Azure:

* Un nuovo servizio cloud Azure
* Un nuovo account di archiviazione Azure

>[AZURE.NOTE] Non riutilizzare un servizio cloud esistente nell'abbonamento. 

**Considerazioni**

* Configurare un servizio cloud distinta per ogni modello di Azure nodo che si intende creare. Tuttavia, è possibile utilizzare lo stesso account di archiviazione per più modelli di nodo.

* È consigliabile individuare il servizio cloud e account di archiviazione per la distribuzione nella stessa regione Azure.




## <a name="step-2-configure-an-azure-management-certificate"></a>Passaggio 2: Configurare un certificato di gestione di Azure

Per aggiungere nodi Azure come risorse di elaborazione, è necessario un certificato di gestione sul nodo principale e carica un corrispondente certificato per la sottoscrizione di Azure utilizzata per la distribuzione.

Per questo scenario, è possibile scegliere il **Certificato di gestione Azure HPC predefinito** che HPC Pack installa e configura automaticamente sul nodo principale. Il certificato è utile per testare le distribuzioni di prova e scopi. Per utilizzare questo certificato, caricare il 2012\Bin\hpccert.cer c:\Programmi\Microsoft comuni\Microsoft HPC Pack file dal nodo principale macchine Virtuali all'abbonamento. Per caricare il certificato nel [portale classica Azure](https://manage.windowsazure.com), fare clic su **Impostazioni** > **I certificati di gestione**.

Per ulteriori opzioni configurare il certificato di gestione, vedere [scenari per configurare il certificato di gestione di Azure per distribuzioni Burst Azure](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Passaggio 3: Distribuire Azure nodi al cluster



La procedura per aggiungere e avviare Azure nodi in questo scenario vengono in genere la stessa procedura usata con un nodo principale locale. Per ulteriori informazioni, vedere [procedure per distribuire i nodi Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx)nelle sezioni seguenti:

* Creare un modello di nodo Azure

* Aggiungere cluster HPC Windows Azure nodi

* Avviare nodi (provisioning) di Azure

Dopo avere aggiunto e avviare i nodi, sono pronti per l'uso per eseguire i processi di cluster.

Se si verificano problemi durante la distribuzione di Azure nodi, vedere [Risolvere i problemi di distribuzioni di Azure nodi con Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Passaggi successivi

* Per utilizzare un'istanza di complesse di dimensioni dei nodi burst, vedere le considerazioni [sulle macchine virtuali di serie di una serie di H e complesse](virtual-machines-windows-a8-a9-a10-a11-specs.md).

* Se si vuole automaticamente Ingrandisci o Riduci le risorse di elaborazione Azure secondo il carico di lavoro di raggruppamento, vedere [automaticamente aumentare e ridurre le risorse di elaborazione Azure in un cluster HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-cluster-node-burst/burst.png
