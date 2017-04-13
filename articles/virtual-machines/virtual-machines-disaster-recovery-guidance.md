<properties
    pageTitle="Cosa fare nel caso in cui un'interruzione del servizio Azure influisce sulle macchine virtuali di Azure | Microsoft Azure"
    description="Cosa fare nel caso in cui un'interruzione del servizio Azure influisce sulle macchine virtuali di Azure."
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>Cosa fare nel caso in cui un'interruzione del servizio Azure influisce sulle macchine virtuali di Azure

Microsoft, viene costantemente per assicurarsi che i servizi siano sempre disponibili quando è necessario. Forza oltre il controllo talvolta influisce su Contattaci in diversi modi che causano interruzioni del servizio non pianificato.

Microsoft offre un livello di contratto di servizio per i servizi come impegno per il tempo di attività e la connettività. Contratto di servizio per i servizi di Azure individuali sono disponibili in [Azure i contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

Azure contiene già molte funzionalità di piattaforma incorporata che supportano applicazioni altamente disponibili. Per ulteriori informazioni su questi servizi, Leggi [il ripristino di emergenza e disponibilità per applicazioni Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

In questo articolo è descritta uno scenario di ripristino di emergenza true quando un'intera area si verifica un'interruzione a causa di gravi naturale o interruzione del servizio diffusi. Questi sono poche occasioni, ma è necessario preparare la possibilità che sia presente un'interruzione di un'intera regione. Se un'intera regione si verifica un'interruzione del servizio, copie localmente ridondanti dei dati sarebbe temporaneamente non disponibile. Se è stata attivata la replica geografico, tre copie aggiuntive delle tabelle Azure archiviazione BLOB sono archiviate in un'area diversa. In caso di un'interruzione di internazionali completa o un danno in cui l'area principale non è reversibile, Azure esegue la correlazione di tutte le voci DNS all'area replicato geografico.

>[AZURE.NOTE]Tenere presente che non si dispone di controllare questo processo e si verifica solo per le interruzioni dei servizi a livello di area geografica. Per questi motivi, è necessario utilizzare anche altre strategie di backup specifiche di un'applicazione per ottenere il massimo livello di disponibilità. Per ulteriori informazioni, vedere la sezione sulle [strategie di dati per il ripristino di emergenza](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

Per informazioni su come gestire queste poche occasioni, sono disponibili le seguenti linee guida per Azure macchine virtuali in caso di un'interruzione del servizio dell'intera area in cui viene distribuito l'applicazione di Azure macchina virtuale.

##<a name="option-1-wait-for-recovery"></a>Opzione 1: Attendere per il ripristino
In questo caso è richiesta alcuna azione da parte dell'utente. È possibile sapere che stiamo lavorando attentamente per ripristinare la disponibilità dei servizi. È possibile visualizzare lo stato corrente dei servizi sul nostro [Dashboard di integrità dei servizi di Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Questo è l'opzione ottimale se non sono state configurate il ripristino del sito di Azure, macchina virtuale backup, archiviazione geografico ridondanti di accesso in lettura o lo spazio di archiviazione ridondanti geografico prima le interruzioni. Se è stata configurata geografico ridondanti dello spazio di archiviazione o accesso in lettura geografico ridondanti dello spazio di archiviazione per l'account di archiviazione in cui sono archiviati i dischi rigidi virtuali macchine Virtuali (dischi rigidi virtuali), è possibile cercare per recuperare l'immagine di base disco rigido virtuale e provare a effettuare il provisioning di una nuova macchina virtuale del file. Questo non è un'opzione preferita perché non esistono garanzie di sincronizzazione dei dati a meno che non vengono utilizzati macchine Virtuali di Azure backup o ripristino di siti di Azure. Di conseguenza, questa opzione non è garantita per l'uso.

Per i clienti che hanno vuole avere accesso immediato alle macchine virtuali, sono disponibili due opzioni seguenti.  

>[AZURE.NOTE]Tenere presente che entrambe le opzioni seguenti hanno la possibilità di perdita di dati.     

##<a name="option-2-restore-a-vm-from-a-backup"></a>Opzione 2: Ripristinare una macchina virtuale da un backup
Per i clienti che hanno configurato una copia di backup di macchine Virtuali, è possibile ripristinare la macchina virtuale dal punto di backup e ripristino.

Per ripristinare una macchina virtuale nuova copia di Backup di Azure, vedere [ripristinare macchine virtuali in Azure](../backup/backup-azure-restore-vms.md).

Per informazioni su come pianificare l'infrastruttura di backup Azure macchine virtuali, vedere [pianificare l'infrastruttura di backup macchine Virtuali in Azure](../backup/backup-azure-vms-introduction.md).

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Opzione 3: Avviare caso di errore mediante il ripristino del sito di Azure
Se è stata configurata Azure il ripristino del sito per l'uso con macchine virtuali interessate Azure, è possibile ripristinare le macchine virtuali dalla rispettiva replica. Queste repliche possono trovarsi in Azure oppure in locale. In questo caso, è possibile creare una nuova macchina virtuale dalla relativa replica esistente. Per ripristinare le macchine virtuali da una replica il ripristino del sito di Azure, vedere [eseguire la migrazione di Azure IaaS macchine virtuali tra le aree Azure con il ripristino del sito di Azure](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Sebbene Azure macchina virtuale sistema operativo e dischi di dati da replicare in un disco rigido virtuale secondario, se si tratta di un account di accesso in lettura geografico ridondanti dello spazio di archiviazione o geografico ridondanti dello spazio di archiviazione, ogni disco rigido virtuale viene replicato in modo indipendente. Questo livello di replica non garanzia la coerenza tra i dischi rigidi virtuali replicati. Se l'applicazione e/o database che utilizzano tali dischi dati presenti dipendenze tra loro, non è possibile garantire che tutte dischi rigidi virtuali vengono replicati come uno snapshot. Non è inoltre possibile garantire che la replica disco rigido virtuale da geografico ridondanti dello spazio di archiviazione o lo spazio di archiviazione ridondanti geografico di accesso in lettura verrà generato in uno snapshot coerente applicazione per avviare la macchina virtuale.

##<a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su come implementare una strategia di disponibilità e il ripristino di emergenza, vedere [ripristino di emergenza e disponibilità per applicazioni Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Per sviluppare una conoscenza tecnica approfondita delle funzionalità della piattaforma cloud, vedere [indicazioni tecniche sulla resilienza Azure](../resiliency/resiliency-technical-guidance.md).

Per informazioni su come eseguire il backup macchine virtuali, vedere [eseguire il backup macchine virtuali di Azure](../backup/backup-azure-vms.md).

Informazioni su come utilizzare il ripristino del sito di Azure per progettare e automatizzare protezione di fisica (virtuale) Windows e Linux computer e che vengono eseguiti in macchine virtuali di Hyper-V e VMWare, vedere [Il ripristino del sito di Azure](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Se le istruzioni sono non è chiaro o se si desidera che Microsoft per eseguire le operazioni per conto dell'utente, contattare [Il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
