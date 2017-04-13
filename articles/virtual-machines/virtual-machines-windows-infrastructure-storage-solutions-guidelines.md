<properties
    pageTitle="Linee guida per lo spazio di archiviazione soluzioni | Microsoft Azure"
    description="Informazioni sulle linee guida di progettazione e implementazione fondamentali per la distribuzione di soluzioni di archiviazione in servizi di infrastruttura Azure."
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

# <a name="storage-infrastructure-guidelines"></a>Linee guida sull'infrastruttura di archiviazione

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Questo articolo analizza la comprensione delle esigenze di archiviazione e considerazioni sulla progettazione per ottenere prestazioni ottimali macchine ().


## <a name="implementation-guidelines-for-storage"></a>Linee guida di implementazione per lo spazio di archiviazione

Decisioni:

- È necessario utilizzare lo spazio di archiviazione Standard o Premium per il carico di lavoro?
- È necessario striping del disco per creare dischi superiori a 1023 GB?
- È necessario striping del disco per ottenere prestazioni ottimali dei / o per il carico di lavoro?
- Quali set di account di archiviazione è necessario ospitare il carico di lavoro IT o infrastruttura?

Attività:

- Rivedere i/o richieste delle applicazioni di distribuzione e pianificare il numero appropriato e il tipo di account di archiviazione.
- Creare il set di account di archiviazione utilizzando la convenzione di denominazione. È possibile usare PowerShell Azure o il portale.


## <a name="storage"></a>Spazio di archiviazione

Archiviazione Azure è fondamentale di distribuzione e gestione di applicazioni e macchine (). Spazio di archiviazione Azure offre servizi per l'archiviazione dei dati dei file, dati non strutturati e i messaggi e fa parte dell'infrastruttura di macchine virtuali di supporto.

Sono disponibili due tipi di account di archiviazione per macchine virtuali di supporto:

- Consentono di account standard dello spazio di archiviazione accedere allo spazio di archiviazione blob (usato per l'archiviazione dischi macchine Virtuali di Azure), archiviazione tabella, lo spazio di archiviazione di coda e archiviazione dei file.
- Gli account di [archiviazione Premium](../storage/storage-premium-storage.md) offrono prestazioni elevate, latenza ridotta disco un supporto per i/o intensivo carichi di lavoro, ad esempio SQL Server in un cluster di AlwaysOn. Spazio di archiviazione Premium attualmente supporta solo dischi macchine Virtuali di Azure.

Azure crea macchine virtuali con un disco di sistema operativo, un disco temporaneo e uguali a zero o più dischi dati facoltativi. Il disco di sistema operativo e dischi di dati sono BLOB Azure pagina, mentre il disco temporaneo è archiviato in locale in nodo in cui si trova nel computer. Prestare particolare attenzione durante la progettazione di applicazioni da utilizzare solo il disco temporaneo per dati non persistenti come macchina virtuale potrebbe migrata tra host durante un evento di manutenzione. I dati memorizzati nel disco temporaneo sarebbero persi.

Disponibilità e durata viene fornito dall'ambiente di archiviazione Azure sottostante per assicurarsi che i dati rimangono contro interruzioni manutenzione o hardware. Quando si progetta l'ambiente di archiviazione Azure, è possibile scegliere di replicare lo spazio di archiviazione di macchine Virtuali:

- in locale all'interno di un determinata data center Azure
- tra Azure Data Center all'interno di una determinata regione
- tra Azure Data Center nelle diverse aree

È possibile leggere [altre informazioni sulle opzioni di replica per disponibilità](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Dischi di sistema operativo e dati hanno una dimensione massima di 1023 gigabyte (GB). La dimensione massima di un blob è GB 1024 e che deve contenere i metadati (piè di pagina) del disco rigido virtuale file (un GB 1024<sup>3</sup> byte). È possibile utilizzare gli spazi di archiviazione in Windows Server 2012 per superano il limite per il pool insieme dischi dati per presentare volumi logici superiori a 1023GB per la macchina virtuale.

Esistono alcuni limiti di scalabilità quando si progettano le distribuzioni di spazio di archiviazione di Azure, vedere [limiti di abbonamento e servizi Microsoft Azure, quote e vincoli](azure-subscription-service-limits.md#storage-limits) per altri dettagli. Vedere anche [lo spazio di archiviazione Azure prestazioni e scalabilità i siti di destinazione](../storage/storage-scalability-targets.md).

Per la memorizzazione delle applicazioni, è possibile archiviare i dati non strutturati oggetto, ad esempio documenti, immagini, l'esecuzione di backup, dati di configurazione, i registri e così via. uso archiviazione blob. Invece di applicazione di scrittura su un disco virtuale collegato la macchina virtuale, l'applicazione è possibile scrivere direttamente a archiviazione blob Azure. Archiviazione BLOB offre la possibilità di [spazio di archiviazione attivo e interessanti livelli](../storage/storage-blob-storage-tiers.md) in base alle proprie esigenze di disponibilità e vincoli di costo.


## <a name="striped-disks"></a>Dischi strisce
Oltre a consente di creare dischi superiori a 1023 GB, in molti casi, con striping per dischi dati migliora le prestazioni consentendo più BLOB eseguire il backup di spazio di archiviazione per un singolo volume. Con striping, i/o necessaria per scrivere e leggere i dati da un singolo disco logico procede in parallelo.

Azure impone limiti al numero di dati dischi e larghezza di banda disponibile, a seconda delle dimensioni di macchine Virtuali. Per informazioni dettagliate, vedere [le dimensioni per macchine virtuali](virtual-machines-windows-sizes.md).

Se si utilizza striping del disco per dischi di dati di Azure, considerare le seguenti linee guida:

- Dischi dati devono essere sempre le dimensioni massime (1023 GB).
- Allegare dischi dati massimo consentiti per la dimensione di memoria virtuale.
- Utilizzare gli spazi di archiviazione.
- Evitare di utilizzare le opzioni di cache disco di dati di Azure (criterio di cache = nessuno).

Per ulteriori informazioni, vedere [spazi di archiviazione - progettazione per prestazioni ottimali](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).


## <a name="multiple-storage-accounts"></a>Più account di archiviazione

Quando si progetta l'ambiente di archiviazione Azure, è possibile utilizzare più account di archiviazione come il numero di macchine virtuali si distribuisce aumenta. Consente di distribuire le / o tra l'infrastruttura di archiviazione Azure sottostante per ottimizzare le prestazioni per le applicazioni e macchine virtuali. Quando si progettano le applicazioni che si desidera distribuire, prendere in considerazione i requisiti di che ogni macchina virtuale è e saldo fuori tali macchine virtuali tra gli account di archiviazione di Azure. Provare a evitare di raggruppare i/o elevato complesse macchine virtuali in per gli account di archiviazione solo uno o due.

Per ulteriori informazioni sulle funzionalità dei / o di diverse opzioni di archiviazione Azure e alcune consigliabile massimi, vedere [lo spazio di archiviazione Azure prestazioni e scalabilità i siti di destinazione](../storage/storage-scalability-targets.md).


## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 