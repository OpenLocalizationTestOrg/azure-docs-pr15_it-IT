<properties
   pageTitle="MATLAB cluster in macchine virtuali | Microsoft Azure"
   description="Consente di creare cluster MATLAB distribuito Computing Server per eseguire il carico di lavoro complesse parallele MATLAB macchine virtuali di Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Creare cluster di elaborazione di distribuito MATLAB Server in macchine virtuali di Azure 

Consente di creare uno o più cluster MATLAB distribuito Computing Server per eseguire il carico di lavoro complesse in parallelo MATLAB macchine virtuali di Microsoft Azure. Installare il software MATLAB distribuiti Computing Server in una macchina virtuale da utilizzare come un'immagine di base e utilizzare un modello di Guida introduttiva di Azure o script di PowerShell Azure (disponibile su [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) per distribuire e gestire il cluster. Dopo la distribuzione, connettersi al cluster per eseguire il carico di lavoro. 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Sulle MATLAB e MATLAB distribuita Computing Server 

La piattaforma [MATLAB](http://www.mathworks.com/products/matlab/) ottimizzata per la risoluzione dei problemi tecnici e scientifici. Gli utenti MATLAB simulazioni di grandi dimensioni e le attività di elaborazione dei dati possono usare calcolo prodotti parallelo MathWorks per velocizzare i carichi di lavoro complesse sfruttando i cluster di elaborazione e i servizi di griglia. [Casella degli strumenti Computing parallelo](http://www.mathworks.com/products/parallel-computing/) consente agli utenti MATLAB eseguire applicazioni e trarre vantaggio dalle processori multi-core, unità di elaborazione, grafica e cluster di elaborazione. [MATLAB distribuiti Computing Server](http://www.mathworks.com/products/distriben/) consente agli utenti MATLAB di utilizzare più computer in un cluster di elaborazione. 


Utilizzando macchine virtuali di Azure, è possibile creare cluster MATLAB distribuiti Computing Server che ha le stesse meccanismi disponibili per inviare il lavoro parallelo come cluster locale, ad esempio processi interattivi, processi batch, attività indipendenti e attività di comunicazione. Utilizzo Azure in combinazione con la piattaforma MATLAB è numerosi i vantaggi rispetto all'implementazione e utilizzo tradizionale locale hardware: dimensioni di un intervallo di macchina virtuale, la creazione di cluster su richiesta in modo pagando solo per le risorse di elaborazione è utilizzo e la possibilità di testare i modelli in scala.  

## <a name="prerequisites"></a>Prerequisiti di

* **Computer client** , è necessario un computer basato su Windows client per comunicare con Azure e cluster di elaborazione di distribuito MATLAB Server dopo la distribuzione. 

* **Azure PowerShell** , vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md) installarlo nel computer client. 

* **Abbonamento azure** , se non si dispone di una sottoscrizione, è possibile creare un [account gratuiti](https://azure.microsoft.com/free/) in solo un paio di minuti. Per cluster di grandi dimensioni, valutare la possibilità di una sottoscrizione in o altre opzioni di acquisto. 

* **Quota core** - potrebbe essere necessario aumentare la quota di base per distribuire un cluster di grandi dimensioni o più di un cluster di MATLAB distribuiti Computing Server. Per aumentare una quota, [aprire una richiesta di assistenza clienti online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) senza costi aggiuntivi. 

* **MATLAB, in parallelo Computing casella degli strumenti e MATLAB distribuiti Computing Server licenze** , gli script presuppongono che [Gestione licenze ospitato MathWorks](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) viene utilizzato per tutte le licenze.  

* **Software Server Computing distribuito MATLAB** - verrà installato in una macchina virtuale che verrà utilizzata come immagine macchine Virtuali di base per il cluster macchine virtuali. 


## <a name="high-level-steps"></a>Passaggi di livello elevati

Per utilizzare macchine virtuali di Azure per i cluster MATLAB distribuiti Computing Server, sono necessarie le operazioni seguenti. Istruzioni dettagliate disponibili nella documentazione di accompagnamento gli script e il modello di Guida introduttiva su [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Creare un'immagine di macchine Virtuali di base**  
    * Scaricare e installare software MATLAB distribuiti Computing Server su questa macchina virtuale. 

    >[AZURE.NOTE]Questo processo può richiedere alcune ore, ma è necessario farlo una volta per ogni versione di MATLAB utilizzare.   
    
2. **Creare uno o più cluster**  
    * Utilizzare lo script di PowerShell o utilizzare il modello di Guida introduttiva per creare un cluster dall'immagine macchine Virtuali di base.   
    * Gestire i cluster mediante lo script di PowerShell fornito che consente di elenco, sospendere, riprendere ed eliminare cluster. 
 
## <a name="cluster-configurations"></a>Configurazioni cluster 

Attualmente, il modello e lo script di creazione cluster consentono di creare una singola topologia MATLAB distribuiti Computing Server. Se si desidera creare uno o più cluster aggiuntive, con ogni cluster con un numero differente di lavoro macchine virtuali, con dimensioni macchine Virtuali diverse e così via. 

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB client e cluster di Azure 

Il nodo client MATLAB, nodo MATLAB processo Scheduler e nodi "Lavoro" MATLAB distribuiti Computing Server configurati come macchine virtuali di Azure in una rete virtuale, come illustrato nella figura seguente. 

![Topologia cluster](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* Per utilizzare il cluster, connettersi tramite Desktop remoto per il nodo client. Il nodo client viene eseguito il client MATLAB. 

* Il nodo client ha una condivisione file di cui è possibile accedere da tutti i colleghi.

* Gestione licenze ospitato MathWorks viene utilizzata per i controlli di licenza per tutto il software MATLAB. 

* Per impostazione predefinita, viene creato un lavoro MATLAB distribuiti Computing Server per core in macchine virtuali di lavoro, ma è possibile specificare un numero. 


## <a name="use-an-azure-based-cluster"></a>Utilizzare un Cluster di Azure 

Come con altri tipi di cluster di elaborazione di distribuito MATLAB Server, è necessario utilizzare Gestione profili di Cluster nel client MATLAB (in macchine Virtuali di client) per creare un profilo di cluster MATLAB processo Scheduler.

![Gestione profili di cluster](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Passaggi successivi

* Per istruzioni dettagliate per la distribuzione e Gestione cluster di Server Computing distribuito MATLAB in Azure, vedere archivio [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) contenente i modelli e gli script. 

* Passare al [sito MathWorks](http://www.mathworks.com/) per la documentazione dettagliata per MATLAB e MATLAB distribuiti Computing Server.
