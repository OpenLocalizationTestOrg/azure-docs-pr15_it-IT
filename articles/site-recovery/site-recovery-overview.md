<properties
    pageTitle="Che cos'è il ripristino del sito? | Microsoft Azure"
    description="Viene fornita una panoramica del servizio il ripristino del sito di Azure e contiene un riepilogo scenari di distribuzione."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>Che cos'è il ripristino del sito?

Introduzione a ripristino sito Azure! Questo articolo fornisce una breve panoramica del servizio il ripristino del sito e come contribuisce per le aziende.

L'organizzazione necessita di una strategia di ripristino (BCDR) di emergenza che consentono di mantenere App, carichi di lavoro e dati attendibili e disponibili durante il tempo di inattività pianificato e non pianificato e la continuità aziendale e ripristina in condizioni normali di uso più presto possibile. Il ripristino del sito è un servizio di Azure che contribuisce a questa strategia.

Il ripristino del sito coordina replica di carichi di lavoro in esecuzione server fisici locale e macchine virtuali. È possibile replicare server e macchine virtuali da un centro dati principale nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nel sito principale, è Impossibile tramite il sito secondario per mantenere carichi di lavoro e le applicazioni accessibili e disponibile. Riescano a tornare alla propria posizione primaria restituisce al funzionamento normale.

## <a name="site-recovery-in-the-azure-portal"></a>Ripristino del sito nel portale di Azure

Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse. Il modello di gestione di risorse Azure e il modello di gestione dei servizi classica. Azure include inoltre due portali: [portale classica Azure](https://manage.windowsazure.com/) che supporta il modello di distribuzione classica e il [portale di Azure](https://portal.azure.com) con supporto per la visualizzazione classica e modelli di Manager delle risorse.

- Il ripristino del sito è disponibile nel portale di classica e il portale di Azure.
- Nel portale di classica Azure, è possibile supportare il ripristino del sito con il modello di gestione dei servizi classica.
- Nel portale di Azure, è possibile supportare il modello classico o distribuzioni Manager delle risorse. 

Le informazioni in questo articolo si applicano alle distribuzioni portale classiche e Azure. Differenze sono indicate eventualmente.


## <a name="why-deploy-site-recovery"></a>Perché distribuire il ripristino del sito?

Ecco cosa è possibile eseguire il ripristino del sito per le aziende:

- **Semplificare BCDR**, è possibile gestire replica, failover e ripristino di più carichi di lavoro in un'unica posizione nel portale di Azure. Il ripristino del sito coordina replica e failover ma non intercetta i dati dell'applicazione o le informazioni.
- **Replica flessibile specifica**, utilizzando il ripristino del sito, è possibile replicare carichi di lavoro in esecuzione in macchine virtuali di Hyper-V supportati, macchine virtuali VMware e server fisici Windows/Linux.
- **Verifica della replica di facile eseguire**, ovvero il ripristino del sito fornisce failover test per supportare esercitazioni di ripristino di emergenza, senza influenzare ambienti di produzione.
- **Esito negativo su e recuperare**, è possibile eseguire failover pianificato per le interruzioni previste con una perdita di dati di zero o failover non previsti con perdita di dati minima (a seconda della frequenza di replica) per guasti imprevisti. Dopo il failover, è possibile failback ai siti principali. Il ripristino del sito fornisce piani di ripristino che possono includere script e le cartelle di lavoro di automazione Azure, in modo che è possibile personalizzare failover e ripristino delle applicazioni a più livelli.
- **Eliminare un Data Center secondario**, è possibile replicare carichi di lavoro in Azure, piuttosto che a un sito secondario. Per evitare i costi e semplifica la gestione di un Data Center secondario. Replicato dati vengono archiviati in archiviazione di Azure, con tutte le capacità di recupero che fornisce. Macchine virtuali vengono create con i dati replicati in caso di failover.
- **Integrazione con le tecnologie BCDR esistenti**, ovvero il ripristino del sito si integra con altre funzioni BCDR. Ad esempio, è possibile utilizzare il ripristino del sito per proteggere il back-end di SQL Server di aziendale carichi di lavoro, incluso il supporto nativo per SQL Server AlwaysOn per gestire il failover dei gruppi di disponibilità.

## <a name="what-can-i-replicate"></a>Cosa è possibile replicare?

Di seguito è riportato un riepilogo di cosa è possibile replicare utilizzando il ripristino del sito.

![Locale a locale](./media/site-recovery-overview/asr-overview-graphic.png)

**LA REPLICA** | **REPLICARE** 
---|---
Carichi di lavoro in esecuzione in macchine virtuali VMware locale | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sito secondario](site-recovery-vmware-to-vmware.md)
Carichi di lavoro in esecuzione in macchine virtuali di Hyper-V locale gestito in cloud VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Sito secondario](site-recovery-vmm-to-vmm.md) 
Gestito il carico di lavoro in esecuzione in macchine virtuali di Hyper-V in locale in cloud VMM, con lo spazio di archiviazione SAN|  [Sito secondario](site-recovery-vmm-san.md)
Carichi di lavoro in esecuzione in macchine virtuali di Hyper-V in locale, senza VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Carichi di lavoro in esecuzione su server Windows/Linux fisici locale | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sito secondario](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>Quali carichi di lavoro è proteggere?

Il ripristino del sito consente BCDR compatibili con le applicazioni, in modo che carichi di lavoro e App continuano a eseguire in modo coerente quando si verificano interruzioni. Il ripristino del sito sono disponibili:

- **Snapshot coerenti con l'applicazione**, ovvero macchine replicano utilizzando snapshot coerenti con l'applicazione, per le applicazioni di unica o più livelli. Oltre ad acquisire dati su disco, acquisizione snapshot coerenti applicazione acquisire tutti i dati in memoria e tutte le transazioni nel processo.
- **Replica vicino sincronizzata**, ovvero il ripristino del sito fornisce la frequenza di replica è arrivare fino a 30 secondi per Hyper-V e replica continua per VMware.
- **Piani di ripristino flessibili**, è possibile creare e personalizzare i piani di ripristino con script esterni e le azioni manuale. Integrazione con l'automazione Azure runbook consentono di recuperare una serie intera applicazione con un solo clic.
- **Integrazione con SQL Server AlwaysOn**, è possibile gestire il failover dei gruppi di disponibilità nei piani di ripristino il ripristino del sito.
- **Libreria di automazione**, ovvero una libreria di automazione di Azure completa fornisce script di produzione specifici dell'applicazione che può essere scaricato e integrato con il ripristino del sito.
- **Gestione della rete semplice**, ovvero la gestione di rete avanzate nel ripristino del sito e Azure semplifica requisiti di rete dell'applicazione, tra cui la prenotazione di indirizzi IP, configurare servizi di bilanciamento del carico e integrazione di Azure il traffico Manager per switchovers rete efficiente.


## <a name="next-steps"></a>Passaggi successivi

- Per saperne di più in [quali carichi di lavoro possono proteggere il ripristino del sito?](site-recovery-workload.md)
- Altre informazioni sull'architettura il ripristino del sito in [come funziona il ripristino del sito?](site-recovery-components.md)
 
