<properties
    pageTitle="linee guida macchine virtuali di Windows | Microsoft Azure"
    description="Informazioni sulle linee guida di progettazione e implementazione fondamentali per la distribuzione di macchine virtuali di windows in Azure"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="virtual-machines-guidelines"></a>Linee guida macchine virtuali

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

In questo articolo è incentrata su informazioni sulla pianificazione necessarie per creare e gestire macchine () all'interno dell'ambiente Azure.

## <a name="implementation-guidelines-for-vms"></a>Linee guida di implementazione per macchine virtuali
Decisioni:

- Quanti macchine virtuali necessari per i vari livelli dell'applicazione e componenti dell'infrastruttura
- Le risorse CPU e memoria ogni macchina virtuale necessita e quali sono i requisiti di spazio di archiviazione?

Attività:

- Definire i carichi di lavoro per l'applicazione e le risorse che richiedono macchine virtuali.
- Allineare le richieste di risorse per ogni macchina virtuale con il tipo di dimensioni e lo spazio di archiviazione macchine Virtuali appropriato.
- Definire i gruppi di risorse per i diversi livelli e componenti dell'infrastruttura.
- Definire la convenzione di denominazione macchine Virtuali.
- Creare le macchine virtuali tramite PowerShell Azure, web portale, o con i modelli di Manager delle risorse.

## <a name="virtual-machines"></a>Macchine virtuali

Uno dei componenti principali all'interno dell'ambiente Azure è probabile che macchine virtuali. Verrà visualizzata nel punto in cui si esegue le applicazioni, database, servizi di autenticazione e così via.

È importante conoscere le [dimensioni di macchine Virtuali diverse](virtual-machines-windows-sizes.md) correttamente la dimensione dell'ambiente da delle prestazioni e la prospettiva di costo. Se nelle macchine virtuali non dispone di memoria o abbastanza core CPU, indipendentemente dalla modalità in cui è progettato e sviluppato influisce negativamente sulle prestazioni dell'applicazione. Esaminare i carichi di lavoro consigliati per ogni serie di macchine Virtuali come punto di partenza per stabilire quali macchine Virtuali da utilizzare per ogni componente dell'infrastruttura di dimensioni. È possibile [modificare le dimensioni di una macchina virtuale](https://azure.microsoft.com/blog/resize-virtual-machines/) dopo la distribuzione.

Spazio di archiviazione svolge un ruolo chiave prestazioni macchine Virtuali. È possibile utilizzare lo spazio di archiviazione Standard, che utilizza dischi Vortice normali, o lo spazio di archiviazione Premium per carichi di lavoro i/o elevati e prestazioni ottimali, che utilizza dischi SSD. Come con dimensioni macchine Virtuali costi considerazioni a selezionare il supporto di archiviazione. È possibile leggere l' [articolo linee guida infrastruttura di archiviazione](virtual-machines-windows-infrastructure-storage-solutions-guidelines.md) per informazioni sulla progettazione di archiviazione appropriato per ottimizzare le prestazioni delle proprie macchine virtuali.


## <a name="resource-groups"></a>Gruppi di risorse
Componenti, ad esempio macchine virtuali sono raggruppati logicamente per semplificare la gestione e manutenzione con [I gruppi di risorse Azure](../azure-resource-manager/resource-group-overview.md). Mediante i gruppi di risorse, è possibile creare, gestire e monitorare tutte le risorse che costituiscono una determinata applicazione. È anche possibile implementare [controlli dell'accesso basato sui ruoli](../active-directory/role-based-access-control-what-is.md) per concedere l'accesso ad altri utenti all'interno del team solo alle risorse che necessarie. Tempo di pianificare i gruppi di risorse e assegnazioni di ruolo. Esistono diversi approcci per effettivamente progettare e implementare gruppi di risorse, prestare attenzione a leggere l' [articolo linee guida per i gruppi di risorsa](virtual-machines-windows-infrastructure-resource-groups-guidelines.md) per comprendere il modo migliore per creare le macchine virtuali.


## <a name="templates"></a>Modelli 
È possibile creare modelli, definito dai dichiarativi file JSON, per creare le macchine virtuali. I modelli in genere anche creare la memorizzazione richiesta, networking, le interfacce di rete, gli indirizzi IP e così via insieme macchine virtuali stessi. Si utilizzano modelli per creare ambienti coerenti, riproducibili per lo sviluppo e testing scopi replicare facilmente ambienti di produzione e viceversa. È possibile leggere informazioni sulla [creazione e utilizzo dei modelli](../azure-resource-manager/resource-group-overview.md#template-deployment) per capire come è possibile utilizzare per creare e distribuire le macchine virtuali.


## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 