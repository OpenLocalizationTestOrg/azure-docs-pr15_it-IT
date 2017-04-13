<properties
    pageTitle="Disponibilità imposta le linee guida | Microsoft Azure"
    description="Informazioni sulle linee guida di progettazione e implementazione fondamentali per la distribuzione di set di disponibilità in servizi di infrastruttura Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="availability-sets-guidelines"></a>Disponibilità imposta linee guida

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

In questo articolo è incentrata su informazioni sulla pianificazione necessarie per i set di disponibilità per assicurarsi che i rimane applicazioni accessibili durante gli eventi pianificati o non pianificati.

## <a name="implementation-guidelines-for-availability-sets"></a>Linee guida di implementazione per i set di disponibilità

Decisioni:

- Quanti set di disponibilità sono necessari per i diversi ruoli e livelli dell'infrastruttura di applicazione?

Attività:

- Definire il numero di macchine virtuali in ogni livello di applicazione che è necessario.
- Determinare se è necessario modificare il numero di errore o aggiornare i domini da utilizzare per l'applicazione.
- Definire i set di disponibilità richiesti utilizzando una convenzione di denominazione e cosa macchine virtuali in esse contenuti. Una macchina virtuale può essere inserito solo la disponibilità di un set. 

## <a name="availability-sets"></a>Set di disponibilità

In Azure, è possibile inserire macchine () in a un gruppo logico definito un set di disponibilità. Quando si creano macchine virtuali all'interno di un set di disponibilità, la piattaforma Azure consente di distribuire la posizione di tali macchine virtuali infrastruttura sottostante. È necessario inserire un intervento di manutenzione pianificata la piattaforma Azure o un hardware sottostante / guasto infrastruttura, l'uso di set di disponibilità garantisce che almeno una macchina virtuale rimane in esecuzione.

Come ottimale, le applicazioni non devono trovarsi in una singola macchina virtuale. Un set di disponibilità contenente una singola macchine Virtuali non Ottiene qualsiasi protezione dagli eventi pianificati o non pianificati all'interno della piattaforma Azure. [Contratto di servizio Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) richiede due o più macchine virtuali all'interno di una disponibilità impostato per consentire la distribuzione di macchine virtuali attraverso l'infrastruttura sottostante.

Infrastruttura di Azure viene diviso per aggiornare guasto e i domini. Questi domini sono definiti da quali host condividere un ciclo di aggiornamenti comuni o Condividi infrastruttura fisica simili, ad esempio power e networking. Azure distribuisce automaticamente le macchine virtuali all'interno di una disponibilità impostata in domini per mantenere la disponibilità e tolleranza di errore. A seconda delle dimensioni dell'applicazione e il numero di macchine virtuali all'interno di un set di disponibilità, è possibile modificare il numero di domini che si desidera utilizzare. È possibile leggere informazioni sulla [gestione delle disponibilità e l'uso dei domini di aggiornamento e fault](virtual-machines-linux-manage-availability.md).

Durante la progettazione dell'infrastruttura di applicazione, è consigliabile anche i livelli dell'applicazione da usare. Set di macchine virtuali di gruppo che servono lo stesso scopo alla disponibilità, ad esempio una disponibilità impostare per le macchine virtuali front-end che esegue nginx o Apache. Creare una disponibilità separata impostato per l'esecuzione MongoDB o MySQL macchine virtuali di back-end. L'obiettivo è garantire che ogni componente dell'applicazione in uso è protetto da un set di disponibilità e almeno una volta istanza rimane in esecuzione.

Servizi di bilanciamento del carico possono essere utilizzate davanti a ogni livello di applicazione di lavorare insieme a un set di disponibilità e assicurarsi che il traffico possa sempre instradato istanza in esecuzione. Senza un bilanciamento del carico, nelle macchine virtuali possono continuare l'esecuzione all'interno di eventi di manutenzione pianificata e non pianificata, ma gli utenti finali potrebbe non riuscire a risolverli se non è disponibile la macchina virtuale principale.


## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 