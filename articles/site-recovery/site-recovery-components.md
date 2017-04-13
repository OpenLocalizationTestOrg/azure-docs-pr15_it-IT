<properties
    pageTitle="Come funziona il ripristino del sito? | Microsoft Azure"
    description="In questo articolo viene fornita una panoramica dell'architettura il ripristino del sito"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>Come funziona il ripristino del sito di Azure?

Leggere questo articolo per comprendere l'architettura sottostante del servizio il ripristino del sito di Azure e il funzionamento dei componenti che rendono. 

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.


## <a name="overview"></a>Panoramica

Le organizzazioni devono una strategia BCDR che determina come App, carichi di lavoro e dati rimangono in esecuzione e disponibili durante il tempo di inattività pianificato e non pianificato e ripristino in condizioni di lavoro normali più presto possibile. La strategia BCDR deve mantenere i dati business attendibili e recuperabili e garantire che carichi di lavoro sempre disponibile quando si verifica danno. 

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR da coordinazione replica di server fisici locale e macchine virtuali nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nella propria posizione principale, non si riesce sopra a posizione secondaria per mantenere le applicazioni e carichi di lavoro disponibili. Riescano a tornare alla propria posizione primaria restituisce al funzionamento normale. Altre informazioni, vedere [che cos'è il ripristino del sito?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Ripristino del sito nel portale di Azure

Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse: il modello di gestione di risorse Azure e il modello di Gestione servizi classica. Azure include inoltre due portali: [portale classica Azure](https://manage.windowsazure.com/) che supporta il modello di distribuzione classica e il [portale di Azure](https://portal.azure.com) con supporto per entrambi i modelli di distribuzione.

Il ripristino del sito è disponibile nel portale di classica e il portale di Azure. Nel portale di classica Azure è possibile supportare il ripristino del sito con il modello di gestione dei servizi classica. Nel portale di Azure è possibile supportare il modello classico o distribuzioni di modello di risorse. [Per ulteriori](site-recovery-overview.md#site-recovery-in-the-azure-portal) informazioni con il portale di Azure.

Le informazioni in questo articolo si applicano alle distribuzioni portale classiche e Azure. Differenze sono indicate eventualmente.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

È possibile distribuire il ripristino del sito per la gestione di replica in diversi scenari:

- **Macchine virtuali VMware replicare**: È possibile replicare macchine virtuali VMware locale a Azure o a un Data Center secondario.
- - **Replicare macchine fisiche**: È possibile replicare computer che esegue Windows o Linux in Azure o a un Data Center secondario. Il processo di replica computer fisici è simile al processo per replicare macchine virtuali VMware
- **Replicare macchine virtuali di Hyper-V (senza VMM)**: È possibile replicare macchine virtuali di Hyper-V che non siano gestiti da VMM in Azure.
- **Replicare macchine virtuali di Hyper-V gestito in System Center VMM nuvole**: È possibile replicare macchine virtuali di Hyper-V in locale in esecuzione su server host Hyper-V in cloud VMM in Azure o a un Data Center secondario. È possibile replicare utilizzando standard Hyper-V Replica o replica SAN.
- **Eseguire la migrazione di macchine virtuali**: È possibile utilizzare il ripristino del sito consente di [eseguire la migrazione di macchine virtuali di Azure IaaS](site-recovery-migrate-azure-to-azure.md) tra le aree geografiche o per [eseguire la migrazione istanze AWS Windows](site-recovery-migrate-aws-to-azure.md) Azure macchine virtuali di IaaS. È supportata la migrazione attualmente sola che indica che può avere esito negativo su queste macchine virtuali ma non è possibile errore indietro.

Il ripristino del sito è possibile replicare la maggior parte delle applicazioni in esecuzione su tali macchine virtuali e server fisici. È possibile ottenere un intero riepilogo delle applicazioni supportate in [quali carichi di lavoro possono proteggere il ripristino del sito di Azure?](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Replica di Azure: VMware macchine virtuali o server Windows/Linux fisici

Esistono due modi per replicare macchine virtuali VMware con il ripristino del sito.

- **Tramite il portale di Azure**-quando si distribuisce il ripristino del sito nel portale di Azure può avere esito negativo su macchine virtuali allo spazio di archiviazione manager servizio classica o di Manager delle risorse. La replica macchine virtuali VMware nel portale di Azure porta numerosi vantaggi, tra cui la possibilità di replicare classic o Manager delle risorse l'archiviazione in Azure. [Altre informazioni](site-recovery-vmware-to-azure.md).
- **Tramite il portale classico**-è possibile distribuire il ripristino del sito nel portale di classica usando un'esperienza avanzata. Dovrebbe essere usata per tutte le nuove distribuzioni nel portale di classica. In questa distribuzione è solo può eseguire il macchine virtuali per l'archiviazione classica in Azure e non per lo spazio di archiviazione di Manager delle risorse. [Altre informazioni](site-recovery-vmware-to-azure-classic.md). È inoltre disponibile un' [esperienza legacy](site-recovery-vmware-to-azure-classic-legacy.md) per la configurazione della replica VMware nel portale di classica. Non devono essere utilizzato per le distribuzioni di nuove.  Se è già stato distribuito utilizzando l'esperienza legacy [informazioni sulla migrazione](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) alla distribuzione avanzata.



Requisiti dell'architettura per la distribuzione il ripristino del sito per replicare server VMware macchine virtuali/fisici nel portale di Azure o il portale classico Azure (avanzate) sono simili, con un paio di differenze:

- Se si distribuisce nel portale di Azure, è possibile replicare archiviazione basata su Manager delle risorse e utilizzare Gestione risorse reti per la connessione macchine virtuali di Azure dopo il failover.
- Quando si distribuisce nel portale di Azure entrambi LRS e lo spazio di archiviazione GRS è supportato. Nel portale di classica GRS è necessario.
- Il processo di distribuzione è semplificata e più semplice da utilizzare nel portale di Azure.


Ecco cosa è necessario:

- **Account Azure**: È necessario un account di Microsoft Azure.
- **Spazio di archiviazione Azure**: È necessario un account di archiviazione Azure per archiviare i dati replicati. È possibile usare un account classico o un account di archiviazione di Manager delle risorse. L'account può essere LRS o GRS quando si distribuisce nel portale di Azure. Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure disattivate backup in caso di failover. 
- **Rete Azure**: È necessario un virtuali Azure che macchine virtuali di Azure si connetterà quando vengono creati in caso di failover. Nel portale di Azure possono essere reti create nel modello di gestione di servizio classica o nel modello di Manager delle risorse.
- **Server di configurazione locale**: È necessario un computer Windows Server 2012 R2 locale che esegue il server di configurazione e altri componenti il ripristino del sito. Se si sta replicare macchine virtuali VMware deve essere una VM VMware disponibilità. Se si desidera replicare server fisici il computer può essere fisico. Questi componenti il ripristino del sito verranno installati nel computer:
    - **Server di configurazione**: coordinare le comunicazioni tra l'ambiente locale e Azure e gestire la replica dei dati e ripristino.
    - **Server di processo**: funge da gateway replica. Ricezione dei dati di replica da computer origine protetto, Ottimizza con la memorizzazione nella cache, compressione e la crittografia e invia i dati allo spazio di archiviazione Azure. Viene anche gestito installazione push del servizio mobilità ai computer protetto ed esegue il rilevamento automatico delle macchine virtuali VMware. Man mano che aumenta la distribuzione è possibile aggiungere server aggiuntivi processo dedicato separato per gestire volumi di traffico.
    - **Server di destinazione master**: gestisce i dati di replica durante failback comuni. 
- **Macchine virtuali VMware o server fisici replicare**: ogni computer in cui si desidera replicare in Azure sarà necessario installato il componente di servizio mobilità. Questo servizio acquisisce scrive dati nel computer e la inoltra al server di processo. Questo componente può essere installato manualmente, o possono essere inserito e installato automaticamente dal server di processo quando si abilita la replica per un computer.
- **server host/vCenter vSPhere**: È necessario uno o più server host vSphere macchine virtuali VMware in esecuzione. È consigliabile distribuire un server vCenter per gestire gli host.
- **Failback**: Ecco cosa occorre:
    - **Failback fisici a fisici non sono supportate**: questo errore indica che se si esito negativo su server fisici per Azure e si desidera eseguire nuovamente, deve avere esito negativo a una VM VMware. Non è possibile non si riesce a un server fisico. È necessario un macchine Virtuali Azure in e se non si è distribuisce il server di configurazione come una VM VMware è necessario configurare un server di destinazione master separato come una VM VMware. Questa operazione è necessaria perché il server di destinazione master interagisce e si connette allo spazio di archiviazione di VMware per ripristinare i dischi una VM VMware.
    - - **Server di processo temporaneo in Azure**: se si vuole avere esito negativo proveniente da Azure dopo il failover è necessario impostare una macchina virtuale Azure configurato come un server di processo, per gestire la replica da Azure. È possibile eliminare questo macchine Virtuali termine failback.
    - **Connessione VPN**: per failback occorre una connessione VPN (o Azure ExpressRoute) configurata dalla rete Azure al sito locale.
    - **Locale separato master server di destinazione**: il server di destinazione master locale gestisce failback. È installato il server di destinazione principale per impostazione predefinita nei server di gestione, ma se si sta non funziona indietro grandi volumi di traffico è necessario impostare backup di un server di destinazione master locale distinta a tale scopo.

**Architettura generale**

![Enhanced](./media/site-recovery-components/arch-enhanced.png)

**Componenti di distribuzione**

![Enhanced](./media/site-recovery-components/arch-enhanced2.png)

**Failback**

![Failback avanzato](./media/site-recovery-components/enhanced-failback.png)


- [Altre informazioni](site-recovery-vmware-to-azure.md#azure-prerequisites) sui requisiti per la distribuzione del portale Azure.
- [Altre informazioni](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sui requisiti di distribuzione avanzato nel portale di classica.
- [Altre informazioni](site-recovery-failback-azure-to-vmware.md) sui failback nel portale di Auzre.
- [Altre informazioni](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) sui failback nel portale di classica.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Replica di Azure: macchine virtuali di Hyper-V non gestite da VMM

È possibile replicare macchine virtuali di Hyper-V che non siano gestiti da System Center VMM in Azure con il ripristino del sito come indicato di seguito:

- **Tramite il portale di Azure**-quando si distribuisce il ripristino del sito nel portale di Azure può avere esito negativo su macchine virtuali allo spazio di archiviazione classica o di Manager delle risorse. [Altre informazioni](site-recovery-hyper-v-site-to-azure.md).
- **Tramite il portale classico**-è possibile distribuire il ripristino del sito nel portale di classica. In questa distribuzione è solo può eseguire il macchine virtuali per l'archiviazione classica in Azure e non per lo spazio di archiviazione di Manager delle risorse. [Altre informazioni](site-recovery-hyper-v-site-to-azure-classic.md).

L'architettura per entrambe le distribuzioni è simile, tranne per il fatto che:

- Se si distribuisce nel portale di Azure, è possibile replicare allo spazio di archiviazione di Manager delle risorse e utilizzare le reti di Manager delle risorse per la connessione macchine virtuali di Azure dopo il failover.
- Il processo di distribuzione è semplificata e più semplice da utilizzare nel portale di Azure.

Ecco cosa è necessario:

- **Account Azure**: È necessario un account di Microsoft Azure.
- **Spazio di archiviazione Azure**: È necessario un account di archiviazione Azure per archiviare i dati replicati. Nel portale di Azure è possibile usare un account classico o un account di archiviazione di Manager delle risorse. Nel portale di classica è possibile usare solo un account classico. Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure vengono create in caso di failover.
- **Rete Azure**: È necessario un network Azure macchine virtuali di Azure si connetterà quando vengono creati dopo il failover. 
- **Host Hyper-v**: È necessario uno o più server host Windows Server 2012 R2 Hyper-V. Durante la distribuzione il ripristino del sito verrà installato il Provider di ripristino del sito di Azure e l'agente di servizi di recupero di Microsoft Azure nell'host.
- **Macchine virtuali di Hyper-V**: È necessario uno o più macchine virtuali sul server host Hyper-V. Provider di ripristino del sito Azure e agente di servizi di ripristino di Windows Azure nell'host Hyper-V durante la distribuzione il ripristino del sito. Il Provider di coordinate e di orchestrare replica con il servizio il ripristino del sito tramite internet. L'agente gestisce i dati di replica dati HTTPS 443. Comunicazioni dal Provider e l'agente sono protetti e crittografato. Dati replicati in archiviazione Azure anche è crittografati.

**Architettura generale**

![Sito di Hyper-V in Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [Altre informazioni](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sui requisiti per la distribuzione del portale Azure.
- [Altre informazioni](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sui requisiti per la distribuzione del portale classica.



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Replica di Azure: macchine virtuali di Hyper-V gestite da VMM

È possibile replicare macchine virtuali di Hyper-V nel cloud VMM in Azure con il ripristino del sito come indicato di seguito:

- **Tramite il portale di Azure**-quando si distribuisce il ripristino del sito nel portale di Azure può avere esito negativo su macchine virtuali allo spazio di archiviazione classica o di Manager delle risorse. [Altre informazioni](site-recovery-vmm-to-azure.md).
- **Tramite il portale classico**-è possibile distribuire il ripristino del sito nel portale di classica. In questa distribuzione è solo può eseguire il macchine virtuali per l'archiviazione classica in Azure e non per lo spazio di archiviazione di Manager delle risorse. [Altre informazioni](site-recovery-vmm-to-azure-classic.md).

L'architettura per entrambe le distribuzioni è simile, tranne per il fatto che:

- Se si distribuisce nel portale di Azure, è possibile replicare archiviazione basata su Manager delle risorse e utilizzare Gestione risorse reti per la connessione macchine virtuali di Azure dopo il failover.
- Il processo di distribuzione è semplificata e più semplice da utilizzare nel portale di Azure.


Ecco cosa è necessario:

- **Account Azure**: È necessario un account di Microsoft Azure.
- **Spazio di archiviazione Azure**: È necessario un account di archiviazione Azure per archiviare i dati replicati. Nel portale di Azure è possibile usare un account classico o un account di archiviazione di Manager delle risorse. Nel portale di classica è possibile usare solo un account classico. Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure vengono create in caso di failover.
- **Rete Azure**: È necessario configurare la rete mapping in modo che macchine virtuali di Azure sono connesse a reti appropriate quando vengono creati dopo il failover. 
- **Server VMM**: sarà necessario uno o più server VMM locale in esecuzione su System Center 2012 R2 e configurato con una o più aree privati. Se si distribuisce il Azure portale è necessario logico e configurare reti macchine Virtuali in modo che è possibile configurare una connessione di rete. Nel portale di classica operazione è facoltativa.  Una rete macchine Virtuali deve essere collegata a una rete logica associata nel cloud.
- **Host Hyper-v**: È necessario uno o più server host Windows Server 2012 R2 Hyper-V nel cloud VMM.
- **Macchine virtuali di Hyper-V**: È necessario uno o più macchine virtuali sul server host Hyper-V.

**Architettura generale**

![VMM in Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [Altre informazioni](site-recovery-vmm-to-azure.md#azure-requirements) sui requisiti per la distribuzione del portale Azure.
- [Altre informazioni](site-recovery-vmm-to-azure-classic.md#before-you-start) sui requisiti per la distribuzione del portale classica.




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replica a un sito secondario: VMware macchine virtuali o server fisici 

Replicare macchine virtuali VMware o server fisici in un sito secondario come download esploratore InMage incluso nella sottoscrizione il ripristino del sito di Azure. Può essere scaricato dal portale di Azure o dal portale di classica Azure. 

Si imposta il server di componenti in ogni sito (processo di configurazione, destinazione master) e installare l'agente unificata nei computer in cui si desidera replicare. Dopo la replica iniziale l'agente in ogni computer invia le modifiche di replica delta al server di processo. Il server di processo consente di ottimizzare i dati e li trasferisce al server di destinazione master nel sito secondario. Il server di configurazione consente di gestire il processo di replica.

Ecco cosa occorre:

**Account Azure**: distribuire questo scenario mediante InMage esploratore. Per ottenerle è necessario un abbonamento a Azure. Dopo aver creato un archivio il ripristino del sito per scaricare esploratore InMage e installare gli aggiornamenti più recenti per configurare la distribuzione.
**Server di processo (sito principale)**: configurare il componente server processo nel sito principale per gestire la memorizzazione nella cache, compressione e ottimizzazione di dati. Consente di gestire anche installazione push dell'agente unificata ai computer che si desidera proteggere. 
**VMware ESX/ESXi e vCenter server (sito principale)**: se si sta proteggere macchine virtuali VMware è necessario un hypervisor VMware EXS/ESXi e, facoltativamente, un server di vCenter VMware per gestire gli hypervisor.
- **Server macchine virtuali/fisica (sito principale)**: macchine virtuali VMware o Windows/Linux server fisici che si desidera proteggere sarà necessario installato l'agente unificata. L'agente unificata anche è installato nel computer funge da server di destinazione principale. L'agente agisce come provider di comunicazione tra tutti i componenti. 
- - **Server di configurazione (sito secondario)**: il server di configurazione è il primo componente l'installazione, ed è installato nel sito secondario per gestire, configurare e controllare la distribuzione tramite il sito Web gestione o la console di vContinuum. Esiste solo un server di configurazione singolo in una distribuzione e deve essere installato in un computer che eseguono Windows Server 2012 R2.
- **server vContinuum (sito secondario)**: viene installata nello stesso percorso (sito secondario) con il server di configurazione. Fornisce una console per gestire e controllare l'ambiente protetto. In un'installazione predefinita server vContinuum è il primo server di destinazione master ed è installato l'agente unificata.
- **Server di destinazione (sito secondario) master**: il server di destinazione master contiene dati replicati. Riceve dati dal server di processo, crea una macchina replica nel sito secondario e contiene i punti di conservazione dei dati. Il numero di server di destinazione principale che è necessario dipende dal numero di computer da proteggere. Se si desidera eseguire il sito principale è necessario anche un server di destinazione master non esiste. 

**Architettura generale**

![VMware per VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Replica a un sito secondario: macchine virtuali di Hyper-V gestito da VMM


È possibile replicare macchine virtuali di Hyper-V gestiti da System Center VMM a un Data Center secondario con il ripristino del sito come indicato di seguito:

- **Tramite il portale di Azure**-quando si distribuisce il ripristino del sito nel portale di Azure. [Altre informazioni](site-recovery-hyper-v-site-to-azure.md).
- **Tramite il portale classico**-è possibile distribuire il ripristino del sito nel portale di classica. [Altre informazioni](site-recovery-hyper-v-site-to-azure-classic.md).

L'architettura per entrambe le distribuzioni è simile, tranne per il fatto che:

- Se si distribuisce nel portale di Azure è necessario impostare il mapping di rete. Questo è facoltativo nel portale di classica.
- Il processo di distribuzione è semplificata e più semplice da utilizzare nel portale di Azure.
- - Se si distribuisce di Azure portale classica [mapping dello spazio di archiviazione](site-recovery-storage-mapping.md) è disponibile.

Ecco cosa è necessario:

- **Account Azure**: È necessario un account di Microsoft Azure.
- **Server VMM**: È consigliabile un server VMM nel sito principale e uno nel sito secondario, ognuno contenente almeno un cloud privato VMM. Il server deve essere in esecuzione almeno System Center 2012 SP1 con gli aggiornamenti più recenti e connessi a internet. Nuvole devono essere il profilo di funzionalità di Hyper-V impostata. È necessario installare il Provider di ripristino del sito di Azure sul server VMM. Il Provider di coordinate e di orchestrare replica con il servizio il ripristino del sito tramite internet. Le comunicazioni tra il Provider e Azure sono protetti e crittografato.
- **Server di Hyper-V**: server host Hyper-V devono essere distribuiti in aree VMM primarie e secondarie. L'host server devono essere in esecuzione almeno Windows Server 2012 con gli ultimi aggiornamenti installati e connessi a internet. Dati replicati tra server primario e secondario Hyper-V host tramite la rete LAN o VPN mediante l'autenticazione Kerberos o certificato.  
- **Computer protetto**: il server di origine Hyper-V host deve avere almeno una macchina virtuale che si desidera proteggere.

**Architettura generale**

![Locale a locale](./media/site-recovery-components/arch-onprem-onprem.png)


- [Altre informazioni](site-recovery-vmm-to-vmm.md#azure-prerequisites) sui requisiti di distribuzione nel portale di Azure.
- - [Altre informazioni](site-recovery-vmm-to-vmm-classic.md#before-you-start) sui requisiti di distribuzione nel portale di classica Azure.




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Replica a un sito secondario con replica SAN: macchine virtuali di Hyper-V gestito da VMM

È possibile replicare macchine virtuali di Hyper-V gestito in cloud VMM in un sito secondario utilizzando replica SAN tramite il portale classico Azure. Questo scenario non è attualmente supportato nel portale di Azure nuovo. 

In questo scenario durante la distribuzione il ripristino del sito si installano il Provider di ripristino del sito di Azure server VMM. Il Provider di coordinate e di orchestrare replica con il servizio il ripristino del sito tramite internet. Dati replicati tra matrici dello spazio di archiviazione primario e secondario utilizzando l'icona del replica SAN.

Ecco cosa è necessario:

**Account Azure**: È necessario un abbonamento a Azure
- **Matrice di SAN**: una [matrice SAN è supportato](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gestiti dal server VMM principale. SAN condivide un'infrastruttura di rete con un'altra matrice SAN nel sito secondario.
- **Server VMM**: È consigliabile un server VMM nel sito principale e uno nel sito secondario, ognuno contenente almeno un cloud privato VMM. Il server deve essere in esecuzione almeno System Center 2012 SP1 con gli aggiornamenti più recenti e connessi a internet. Nuvole devono essere il profilo di funzionalità di Hyper-V impostata.
- **Server di Hyper-V**: server host Hyper-V nel cloud VMM primario e secondario. L'host server devono essere in esecuzione almeno Windows Server 2012 con gli ultimi aggiornamenti installati e connessi a internet.
- **Computer protetto**: il server di origine Hyper-V host deve avere almeno una macchina virtuale che si desidera proteggere.

**Architettura della replica SAN**

![Replica di SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Altre informazioni](site-recovery-vmm-san.md#before-you-start) sui requisiti di distribuzione.
### <a name="on-premises"></a>Locale



## <a name="hyper-v-protection-lifecycle"></a>Ciclo di vita di protezione di Hyper-V

Questo flusso di lavoro viene illustrato il processo per la protezione, replica e non funziona in macchine virtuali Hyper-V. 

1. **Attivare la protezione**: configurare l'archivio il ripristino del sito, configurare le impostazioni di replica per un cloud VMM o un sito di Hyper-V e attivare la protezione per macchine virtuali. Un processo denominato **Attiva protezione** viene avviato ed è possibile monitoraggio nella scheda **processi** . Il processo di verifica che il computer conforme ai prerequisiti e quindi chiama il metodo [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) che consente di impostare la replica di Azure con le impostazioni di che aver configurato. Il processo di **attivare la protezione** richiama anche il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) per inizializzare una replica macchine Virtuali completa.
2. **La replica iniziale**: un'istantanea macchina virtuale e dischi rigidi virtuali sono replicato uno alla volta finché non verranno tutti copiati in Azure o al Data Center secondario. Il tempo necessario per completare questa operazione dipende dalla dimensione memoria virtuale, della larghezza di banda di rete e il metodo di replica iniziale. Se si verificano modifiche disco mentre è in corso la replica iniziale il Registro di replica di Replica Hyper-V traccia tali modifiche come i registri di replica di Hyper-V (.hrl) che si trovano nella stessa cartella in cui i dischi. Ogni disco è un file associato .hrl che verrà inviato all'archiviazione secondaria. Si noti che i file di log e snapshot utilizzano risorse disco mentre è in corso la replica iniziale. Al termine della replica iniziale snapshot macchine Virtuali verrà eliminato e le modifiche disco delta nel registro sono sincronizzate e unite.
3. **Protezione Finalize**: termine del processo di **protezione Finalize** replica iniziale Configura rete e le altre impostazioni successive alla replica in modo che il computer virtuale è protetto. Se sta replicate in Azure potrebbe essere necessario modificare le impostazioni per la macchina virtuale in modo che sia pronto per failover. A questo punto è possibile eseguire test caso di errore per verificare che il funzionamento come previsto.
4. **Replica**: dopo la replica iniziale verrà avviata la sincronizzazione delta, in base alle impostazioni di replica. 
    - **Errore di replica**: se la replica delta non riesce, una replica completa sarebbe costosa in termini di larghezza di banda o un'ora, quindi risincronizzazione si verifica. Per esempio se i file .hrl 50% delle dimensioni del disco raggiungono la macchina virtuale quindi verrà contrassegnato per la risincronizzazione. Risincronizzazione riduce la quantità di dati inviati dal calcolo della checksum macchine virtuali di origine e destinazione e inviare solo il delta. Al termine della risincronizzazione delta replica verrà ripresa. Per impostazione predefinita risincronizzazione è programmata per l'esecuzione automatica all'esterno di momento di office, ma è possibile sincronizzare di nuovo manualmente una macchina virtuale.
    - **Errore di replica**: se si verifica un errore di replica è presente un tentativi incorporato. Se si tratta di un errore irreversibile, ad esempio un errore di autenticazione o autorizzazione oppure una macchina replica è stato non valido, non verrà tentato nessun tentativo. Se si tratta di un errore reversibile, ad esempio un errore di rete o spazio/memoria insufficiente, quindi si verifica un Riprova crescenti intervalli tra tentativi (1, 2, 4, 8, 10 e quindi ogni 30 minuti).
4. **Failover pianificato/non previsti**: È possibile eseguire failover pianificato o non pianificato in base alle esigenze. Se si esegue un failover pianificato quindi origine macchine virtuali arrestato automaticamente per assicurarsi che nessuna perdita di dati. Dopo la creazione di macchine virtuali di replica vengano inseriti in un commit in sospeso. È necessario eseguire il commit in modo da completare il failover, a meno che non sta eseguendo la replica con SAN nel qual caso il commit automatico. Dopo il sito principale sia in esecuzione failback può verificarsi. Se è stata replicate in Azure inverso della replica è automatica. In caso contrario avviano replica inversa manualmente.
 

![flusso di lavoro](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Passaggi successivi

[Prepararsi per la distribuzione](site-recovery-best-practices.md)
