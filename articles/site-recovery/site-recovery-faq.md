<properties
    pageTitle="Il ripristino del sito Azure: Domande frequenti | Microsoft Azure"
    description="In questo articolo vengono illustrati domande comuni il ripristino del sito di Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Il ripristino del sito Azure: Domande frequenti domande frequenti

Questo articolo contiene domande frequenti sul ripristino del sito di Azure. Se hai domande in questo articolo, pubblicarli nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Generale

### <a name="what-does-site-recovery-do"></a>Qual è il ripristino del sito?

Il ripristino del sito contribuisce alla strategia di ripristino (BCDR) emergenza e continuità aziendale, tramite la gestione e automatizzazione della replica da server fisici e macchine virtuali di locale in Azure o a un Data Center secondario. [Altre informazioni](site-recovery-overview.md).


### <a name="what-can-site-recovery-protect"></a>Cosa può proteggere il ripristino del sito?

- **Macchine virtuali Hyper-V**: il ripristino del sito è possibile proteggere un carico di lavoro in esecuzione in una macchina virtuale Hyper-V.
- **Server fisici**: il ripristino del sito possono proteggere fisico server che esegue Windows o Linux.
- **Macchine virtuali VMware**: proteggere qualsiasi carico di lavoro in esecuzione in una VM VMware il ripristino del sito.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Il ripristino del sito supporta il modello di gestione di risorse Azure?

Oltre il ripristino del sito nel portale di classica Azure, il ripristino del sito è disponibile nel portale di Azure con supporto per Manager delle risorse. Per la maggior parte degli scenari il ripristino del sito nel Azure portale offre un'esperienza di distribuzione semplificata ed è possibile replicare macchine virtuali e server fisici in archiviazione classica o lo spazio di archiviazione di Manager delle risorse. Di seguito sono distribuzioni supportate:

- [Replicare macchine virtuali VMware o server fisici in Azure nel portale di Azure](site-recovery-vmware-to-azure.md)
- [Replicare macchine virtuali di Hyper-V nel cloud VMM Azure nel portale di Azure](site-recovery-vmm-to-azure.md)
- [Replicare macchine virtuali di Hyper-V (senza VMM) Azure nel portale di Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicare macchine virtuali di Hyper-V nel cloud VMM in un sito secondario nel portale di Azure](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Che cos'è necessario in Hyper-V progettare replica con il ripristino del sito?

Per il server di host Hyper-V è necessario dipende lo scenario di distribuzione. Estrarre i prerequisiti di Hyper-V in:

- [La replica macchine virtuali di Hyper-V (senza VMM) in Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [La replica macchine virtuali di Hyper-V (con VMM) in Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [La replica macchine virtuali di Hyper-V in un Data Center secondario](site-recovery-vmm-to-vmm.md#before-you-start)

- Se sta replicate in un Data Center secondario informazioni su [sistemi operativi per guest supportati per le macchine virtuali Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Se si sta replicare in Azure, il ripristino del sito supporta tutti i sistemi operativi guest sono [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>È possibile proteggere macchine virtuali durante l'esecuzione di Hyper-V in un sistema operativo client?

No, macchine virtuali devono trovarsi in un server di host Hyper-V che è in esecuzione in un computer server Windows supportato. Se è necessario per proteggere un computer client è possibile replicare come un computer fisico [Azure](site-recovery-vmware-to-azure.md) o un [Data Center secondario](site-recovery-vmware-to-vmware.md).


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quali carichi di lavoro è possibile proteggere con il ripristino del sito?

È possibile utilizzare il ripristino del sito per proteggere la maggior parte dei carichi di lavoro in esecuzione su un server fisico o macchine Virtuali supportati. Il ripristino del sito fornisce supporto per la replica compatibili con le applicazioni, in modo che uno stato intelligente consente di ripristinare le applicazioni. Si integra con le applicazioni di Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory e collabora con fornitori principali, inclusi Oracle, SAP, IBM e Red Hat. [Ulteriori](site-recovery-workload.md) informazioni sulla protezione di carico di lavoro.


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Gli host Hyper-V devono essere nel cloud VMM?

Se si desidera replicare in un Data Center secondario, quindi macchine virtuali di Hyper-V deve essere acceso Hyper-V ospita server che si trovano in un'area VMM. Se si desidera replicare in Azure, è possibile replicare macchine virtuali sui server di host Hyper-V con o senza cloud VMM. [Per ulteriori](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>È possibile distribuire il ripristino del sito con VMM se utilizza un solo VMM server?

Sì. È possibile replicare in macchine virtuali nel server di Hyper-V nel cloud VMM in Azure oppure è possibile replicare tra cloud VMM nello stesso server. Per locale per la replica locale, è consigliabile avere un server VMM nei siti principali e secondari.  [Per saperne di più](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>Quali server fisici è proteggere?

È possibile replicare fisico server che eseguono Windows e Linux in Azure o a un sito secondario. [Informazioni sui](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) requisiti del sistema operativo.  Se si sta replica server fisici in Azure o a un sito secondario stesse condizioni.

Si noti che server fisici verrà eseguito come macchine virtuali di Azure se il server locale viene interrotto. Non è attualmente supportato failback a un server fisico locale, ma potrebbe non riuscire a una macchina virtuale in esecuzione su Hyper-V o VMware.


### <a name="what-vmware-vms-can-i-protect"></a>Quali macchine virtuali VMware è proteggere?

Per proteggere macchine virtuali VMware è necessario un hypervisor vSphere e macchine virtuali di esecuzione degli strumenti di VMware. È anche consigliabile si dispone di un server di vCenter VMware per gestire l'hypervisor. [Altre informazioni](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sui requisiti esatti per replicare server VMware e macchine virtuali di Azure o a un sito secondario.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>È possibile gestire il ripristino di emergenza per gli uffici ramo con il ripristino del sito?

Sì. Quando si utilizza il ripristino del sito per poter gestire le replica e failover negli uffici diramazione, viene visualizzato un orchestrazione unificato e visualizzazione di tutti i ramo office carichi di lavoro in una posizione centrale. È possibile facilmente eseguire failover e amministrare ripristino di tutti i rami dall'ufficio testa, senza visitare le diramazioni.

## <a name="security"></a>Sicurezza

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Dati di replica vengono inviati al servizio il ripristino del sito?

No, il ripristino del sito non intercetta replicati dati e non dispone di tutte le informazioni relative che cos'è in esecuzione su macchine virtuali o server fisici.
Replica scambio di dati host Hyper-V in locale, hypervisor VMware, o server fisici e Azure dello spazio di archiviazione o il sito secondario. Il ripristino del sito non è alcun grado di intercettare tali dati. Solo i metadati necessari per gestire la replica e failover viene inviato al servizio il ripristino del sito.

Il ripristino del sito è ISO 27001:2013, 27018, HIPAA DPA certified e il processo di valutazioni SOC2 e FedRAMP JAB.


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Per motivi di conformità, anche il nostro metadati locale devono rimanere all'interno dell'area geografica stessa. Il ripristino del sito consentono?

Sì. Quando si crea un archivio il ripristino del sito in un'area, per garantire che tutti i metadati che è necessario abilitare e gestire la replica e failover rimane all'interno di tale area 's geografici limite.

### <a name="does-site-recovery-encrypt-replication"></a>Il ripristino del sito crittografare replica?

Per server fisici e macchine virtuali di replica tra locale siti crittografia-in transito è supportata. Per macchine virtuali e server fisici replica di Azure, la crittografia in transito e la crittografia in altre applicazioni (in Azure) sono supportati.


## <a name="replication"></a>Replica


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Esistono prerequisiti della replica macchine virtuali di Azure?

Macchine virtuali da replicare in Azure deve soddisfare i [requisiti di Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>È possibile replicare macchine virtuali di generazione 2 Hyper-V in Azure?

Sì. Il ripristino del sito converte dalla generazione 2 alla generazione 1 durante il failover. In failback il computer viene convertito alla generazione 2. [Per ulteriori](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Se si replica in Azure come posso pagare per macchine virtuali di Azure?

Durante la replica normale, dati replicati geografico ridondanti dello spazio di archiviazione Azure e non è necessario effettuare il pagamento in base alle tariffe macchina virtuale qualsiasi IaaS Azure, fornire un grande vantaggio. Quando si esegue caso di errore in Azure, il ripristino del sito creato automaticamente macchine virtuali di Azure IaaS e successivamente sarà effettuata per le risorse di elaborazione che utilizzano in Azure.


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>Esiste un SDK è possibile utilizzare per automatizzare il flusso di lavoro di ripristino automatico di sistema?

Sì. È possibile automatizzare flussi di lavoro il ripristino del sito tramite l'API Rest, PowerShell o Azure SDK. Scenari attualmente supportati per la distribuzione il ripristino del sito tramite PowerShell:

- [Replicare macchine virtuali di Hyper-V in VMM nuvole Azure PowerShell classica](site-recovery-deploy-with-powershell.md)
- [Replicare macchine virtuali di Hyper-V in VMM aree di Manager delle risorse di PowerShell Azure](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Replicare macchine virtuali di Hyper-V senza VMM Azure PowerShell classica](site-recovery-hyper-v-site-to-azure-classic.md)
- [Replicare macchine virtuali di Hyper-V senza VMM Manager delle risorse PowerShell Azure](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Se si replicano in Azure quali tipi di account di archiviazione è necessario?

- **Portale classica Azure**: se si distribuisce il ripristino del sito nel portale di classica Azure, è necessario un [account di archiviazione ridondanti geografico standard](../storage/storage-redundancy.md#geo-redundant-storage). Spazio di archiviazione Premium non è attualmente supportata. L'account deve essere nella stessa regione come archivio il ripristino del sito.

- **Portale di Azure**: se si distribuisce il ripristino del sito nel portale di Azure, è necessario un account di archiviazione LRS o GRS. È consigliabile GRS in modo da dati flessibili se si verifica un'interruzione internazionali oppure se non è possibile ripristinare l'area principale. L'account deve essere nella stessa regione archivio di servizi di recupero. Spazio di archiviazione Premium è supportata solo se si sta replicare macchine virtuali VMware o server fisici.

### <a name="how-often-can-i-replicate-data"></a>Con quale frequenza è possibile replicare i dati?

- **Hyper-v:** Macchine virtuali Hyper-V possono essere replicate ogni 30 secondi, 5 minuti o 15 minuti. Se è stato configurato replica SAN replica è icona del.
- **VMware e server fisici:** Frequenza di replica non è rilevante qui. La replica è continua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>È possibile estendere la replica dal sito di ripristino esistente a un altro sito di terzo livello?

La replica "Extended" o concatenata non è supportata. Richiedere questa caratteristica area [feedback](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>È possibile eseguire una replica non in linea la prima volta che si replica in Azure?

Questa operazione non è supportata. Richiedere questa caratteristica nel [forum di commenti e suggerimenti](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="can-i-exclude-specific-disks-from-replication"></a>È possibile escludere dischi specifici dalla replica?

È supportata quando si è [replicare macchine virtuali VMware e server fisici](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) in Azure, tramite il portale di Azure.


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>È possibile replicare macchine virtuali con dischi dinamici?

Dischi dinamici sono supportati durante la replica macchine virtuali Hyper-V. Sono supportati anche quando la replica macchine virtuali VMware e computer fisici e Azure. Il disco di sistema operativo deve essere un disco di base.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>È possibile aggiungere un nuovo computer a un gruppo di replica esistente?

Aggiunta di nuovi computer a gruppi di replica esistenti è supportato. A tale scopo, selezionare il gruppo di replica (da 'Replicato elementi' blade) e menu di scelta rapida di scelta rapida o selezionare il gruppo di replica e selezionare l'opzione appropriata.

![Aggiungere al gruppo di replica](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>È possibile limitare la larghezza di banda allocata per il traffico di replica Hyper-V?

Sì. È possibile leggere informazioni sulla limitazione della larghezza di banda negli articoli di distribuzione:

- [Pianificazione della replica macchine virtuali VMware e server fisici](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Pianificazione della replica macchine virtuali di Hyper-V nel cloud VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Pianificazione della replica macchine virtuali di Hyper-V senza VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Failover


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Se sta mancata su Azure, come si accede a macchine virtuali Azure dopo il failover?

È possibile accedere macchine virtuali di Azure tramite una connessione Internet protetta, in una rete VPN da sito o su Azure ExpressRoute. È necessario preparare un numero di elementi per la connessione. Per saperne di più in:

- [Connettersi a macchine virtuali di Azure dopo failover di macchine virtuali VMware o server fisici](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Connettersi a macchine virtuali di Azure dopo il failover delle macchine virtuali di Hyper-V in cloud VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Connettersi a macchine virtuali di Azure dopo il failover delle macchine virtuali di Hyper-V senza VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se hanno esito negativo su in Azure come Azure in modo che i dati sono flessibili?

Azure è progettato per adattabilità. Il ripristino del sito è progettato per il controllo a un Data Center Azure secondario, secondo il contratto di servizio di Azure già in caso di necessità. In questo caso, ci si assicura che i metadati e archivi deve rimanere nella propria area geografica di stesso che si è scelto per l'archivio.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se sta replica tra due Data Center cosa succede se la data center principale si verifica un'interruzione imprevista?

È possibile attivare un failover non pianificato dal sito secondario. Il ripristino del sito non è necessaria la connettività dal sito principale per eseguire il failover.

### <a name="is-failover-automatic"></a>È failover automatico?

Failover non è automatico. Si avvia failover con singolo clic del mouse nel portale di oppure è possibile utilizzare [PowerShell di ripristino del sito](https://msdn.microsoft.com/library/dn850420.aspx) per impostare un trigger caso di errore. Non funziona indietro è un'operazione semplice nel portale del ripristino del sito.

Per automatizzare si potrebbe usare Orchestrator locale o Operations Manager di rilevare un errore di macchina virtuale e quindi attivare failover utilizzando SDK.

- [Per ulteriori](site-recovery-create-recovery-plans.md) informazioni sui piani di ripristino.
- [Per ulteriori](site-recovery-failover.md) informazioni sui failover.
- [Per ulteriori](site-recovery-failback-azure-to-vmware.md) sull'errore indietro macchine virtuali VMware e server fisici


## <a name="service-providers"></a>Provider di servizi


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Si è un provider di servizi. Il ripristino del sito funziona per i modelli di infrastruttura dedicato e condiviso?

Sì, il ripristino del sito supporta entrambi i modelli di infrastruttura dedicato e condiviso.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Per un provider di servizi, è l'identità del tenant condiviso con il servizio il ripristino del sito?

No. Tenant identità rimarrà anonimo. Il tenant non necessario l'accesso al portale il ripristino del sito. Solo l'amministratore di provider del servizio interagisce con il portale.


### <a name="will-tenant-application-data-ever-go-to-azure"></a>Dati delle applicazioni tenant andrà mai a Azure?

Durante la replica tra siti proprietà provider di servizi di dati dell'applicazione non è mai passa alla Azure. Dati vengono crittografati in transito e replicato direttamente tra i siti di provider del servizio.

Se si sta replicare in Azure, dati dell'applicazione viene inviati per lo spazio di archiviazione Azure ma non per il servizio il ripristino del sito. Dati sono crittografato in transito e rimangano crittografati in Azure.


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>I tenant riceveranno una fattura per uno o più servizi Azure?

No. Rapporto di fatturazione di Azure è direttamente con il provider di servizi. Provider di servizi sono responsabili per la generazione fatture specifiche per il tenant.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se sta replicate in Azure, è necessario eseguire macchine virtuali di Azure sempre?

No, viene replicati dati a un account di archiviazione Azure nell'abbonamento. Quando si esegue test caso di errore (DR drill) o un failover effettivo, il ripristino del sito creato automaticamente macchine virtuali nell'abbonamento.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>È possibile garantire isolamento a livello di tenant durante la replica di Azure?

Sì.

### <a name="what-platforms-do-you-currently-support"></a>Quali piattaforme è attualmente supportata?

È supportato Azure Pack sistema piattaforma Cloud, e System Center in base a distribuzioni (2012 e versioni successive). [Altre informazioni](https://technet.microsoft.com/library/dn850370.aspx) sull'integrazione di Azure Pack e il ripristino del sito.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>È supportata solo Azure Pack e singolo VMM server?

Sì, è possibile replicare macchine virtuali Hyper-V in Azure o replicare tra siti provider del servizio.  Si noti che se si replica tra siti provider del servizio, l'integrazione di Azure runbook non è disponibile.


## <a name="next-steps"></a>Passaggi successivi

- Leggere la [Panoramica il ripristino del sito](site-recovery-overview.md)
- Informazioni sulle [architettura il ripristino del sito](site-recovery-components.md)  
