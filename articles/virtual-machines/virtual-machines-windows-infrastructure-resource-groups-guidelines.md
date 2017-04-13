<properties
    pageTitle="Gruppi di risorse alle linee guida | Microsoft Azure"
    description="Informazioni sulle linee guida di progettazione e implementazione fondamentali per la distribuzione di gruppi di risorse in servizi di infrastruttura Azure."
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

# <a name="azure-resource-group-guidelines"></a>Linee guida per gruppi di risorse Azure

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

In questo articolo è incentrata su conoscere le procedure per creare l'ambiente in modo logico e raggruppare tutti i componenti di gruppi di risorse.


## <a name="implementation-guidelines-for-resource-groups"></a>Linee guida di implementazione per i gruppi di risorse

Decisioni:

- Si intende creare gruppi di risorse dei componenti dell'infrastruttura di base o la distribuzione di applicazioni completo?
- È necessario limitare l'accesso a gruppi di risorse utilizzando i controlli di accesso basato sui ruoli?

Attività:

- Definire i componenti dell'infrastruttura di base e dedicato gruppi di risorse necessarie.
- Esaminare sull'implementazione di modelli di Manager delle risorse per le distribuzioni coerente, riproducibile.
- Definire i ruoli di accesso utente è necessario per controllare l'accesso a gruppi di risorse.
- Creare il set di gruppi di risorse utilizzando la convenzione di denominazione. È possibile usare PowerShell Azure o il portale.


## <a name="resource-groups"></a>Gruppi di risorse

In Azure, in modo logico raggruppare risorse correlate, ad esempio gli account di archiviazione, reti virtuali e macchine () per distribuire, gestire e mantenerle come una singola entità. Questo approccio rende più facile per distribuire le applicazioni e mantenendo al contempo tutte le risorse correlate insieme da gestionale o per concedere ad altri utenti l'accesso a tale gruppo di risorse. Per più approfondita dei gruppi di risorse, vedere [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).

Caratteristica per i gruppi di risorse è possibilità di creare l'ambiente di uso dei modelli. Un modello è semplicemente un file JSON dichiara sull'archiviazione di rete e calcola le risorse. È inoltre possibile definire qualsiasi correlati script personalizzati o configurazioni da applicare. Con questi modelli, si crea distribuzioni coerente, riproducibile per le applicazioni. Questo approccio è semplice creare un ambiente di sviluppo e quindi utilizzare stesso modello per creare un ambiente di produzione o viceversa. Per comprendere meglio utilizzare i modelli, leggere [questa procedura dettagliata modello](../resource-manager-template-walkthrough.md) che guida l'utente attraverso le varie fasi di creazione di un modello.

Esistono due modi diversi che è possibile eseguire durante la progettazione dell'ambiente con gruppi di risorse:

- Gruppi di risorse per ogni distribuzione dell'applicazione che combina l'account di archiviazione, reti virtuali e subnet, macchine virtuali, caricare il bilanciamento del carico e così via.
- Gruppi di risorse centralizzata contenenti core virtuale rete e subnet o archiviazione account. Le applicazioni sono quindi nei propri gruppi di risorse contenenti solo macchine virtuali, servizi di bilanciamento del carico, le interfacce di rete e così via.

Come si scalabilità, la creazione di gruppi di risorse centralizzata per la rete e subnet virtuale risulta più facile creare connessioni per le opzioni di integrazione applicativa ibrido di rete tra locale. Alternativa è per ogni applicazione per la propria rete virtuale che richiede la configurazione e la manutenzione.  [Controlli dell'accesso basato sui ruoli](../active-directory/role-based-access-control-what-is.md) consentono granulare per controllare l'accesso a gruppi di risorse. Per le applicazioni di produzione, è possibile controllare gli utenti che possono accedere alle risorse o per le risorse dell'infrastruttura di base è possibile limitare solo tecnici dell'infrastruttura per lavorare con loro. Solo i proprietari di applicazione hanno accesso ai componenti dell'applicazione all'interno il proprio gruppo di risorse e non di base dell'infrastruttura di Azure dell'ambiente. Quando si progetta l'ambiente, considerare gli utenti che richiedono l'accesso alle risorse e progettare di conseguenza i gruppi di risorse. 


## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 