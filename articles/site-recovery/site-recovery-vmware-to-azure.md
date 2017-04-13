<properties
    pageTitle="Replicare macchine virtuali VMware e server fisici su Azure con il ripristino del sito di Azure nel portale di Azure | Microsoft Azure"
    description="Descrive come distribuire Azure il ripristino del sito per la gestione di replica, failover e ripristino di macchine virtuali di VMware locale e Windows/Linux server fisici in Azure tramite il portale di Azure"
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
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Replicare macchine virtuali VMware e fisica Azure con il ripristino del sito di Azure tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmware-to-azure.md)
- [Azure classica](site-recovery-vmware-to-azure-classic.md)
- [Azure classica (legacy)](site-recovery-vmware-to-azure-classic-legacy.md)

Introduzione al ripristino del sito Azure! Se si desidera replicare macchine virtuali VMware locale o server fisici/Linux di Windows Azure utilizzando il ripristino del sito di Azure nel portale di Azure, usare questo articolo.

> [AZURE.NOTE] Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse: gestione delle risorse Azure (ARM) e classica. Azure include inoltre due portali: portale classico Azure che supporta il modello di distribuzione classica e il portale Azure con supporto per entrambi i modelli di distribuzione.

Il ripristino del sito nel portale di Azure sono disponibili numerose nuove funzionalità:

- I servizi di Azure eseguire il Backup e il ripristino del sito di Azure vengono combinati in un singolo archivio di servizi di recupero in modo che è possibile configurare e gestire la continuità aziendale e il ripristino di emergenza (BCDR) da un'unica posizione. Nel dashboard unificato è possibile monitorare e gestire le operazioni tra i siti locali e cloud pubblica Azure.
- Gli utenti con abbonamenti Azure viene completato il provisioning con il programma Cloud soluzione Provider (CSP) ora possono gestire operazioni di ripristino del sito nel portale di Azure.
- Il ripristino del sito nel portale di Azure possibile replicare macchine per gli account di archiviazione ARM. In caso di failover, il ripristino del sito crea macchine virtuali basato su ARM in Azure.
- Il ripristino del sito continua per il supporto della replica per gli account di archiviazione classica. In caso di failover, il ripristino del sito consente di creare macchine virtuali utilizzando il modello classico.

Dopo aver letto eventuali commenti nella parte inferiore di commenti Disqus post in questo articolo. Porre domande tecniche nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Panoramica

Le organizzazioni devono una strategia BCDR che determina come App, carichi di lavoro e dati rimangono in esecuzione e disponibili durante il tempo di inattività pianificato e non pianificato e ripristino in condizioni di lavoro normali più presto possibile. La strategia BCDR deve mantenere i dati business attendibili e recuperabili e garantire che carichi di lavoro sempre disponibile quando si verifica danno.

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR da coordinazione replica di server fisici locale e macchine virtuali nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nella propria posizione principale, non si riesce sopra a posizione secondaria per mantenere le applicazioni e carichi di lavoro disponibili. Riescano a tornare alla propria posizione primaria restituisce al funzionamento normale. Altre informazioni, vedere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

Questo articolo fornisce tutte le informazioni che necessarie per replicare locale macchine virtuali VMware e Windows/Linux server fisici in Azure. Include una panoramica dell'architettura, informazioni e procedure di distribuzione per la configurazione di Azure, server locali, le impostazioni di replica e pianificazione della capacità di pianificazione. Dopo aver configurato l'infrastruttura è possibile abilitare la replica nei computer che si desidera proteggere e verificare che failover funzioni.

## <a name="business-advantages"></a>Vantaggi di Business

- Il ripristino del sito offre una protezione fuori sede per carichi di lavoro di business e le applicazioni in macchine virtuali VMware e server fisici.
- Il portale di servizi di recupero rappresenta una posizione centralizzata per configurare, gestire e monitorare replica, failover e ripristino.
- Il ripristino del sito di individuare automaticamente macchine virtuali VMware aggiunti vSphere host.
- Facilmente, è possibile eseguire failover dall'infrastruttura locale Azure e failback (ripristino) comuni ai server VMware VM nel sito locale.
- È possibile abilitare macchine Virtuali con più e creare gruppi di replica in modo che carichi di lavoro di applicazione a più livelli in più computer replicare nello stesso momento. Tutti i computer di un gruppo di replica sono i punti di ripristino di un arresto anomalo coerenti e app coerente quando non riescono sopra. Per il failover, è possibile raccogliere più computer nei piani di ripristino in modo da carichi di lavoro di applicazione a più livelli esito negativo su insieme.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

### <a name="windows64-bit-only"></a>Windows (solo versione a 64 bit)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (solo versione a 64 bit)
- Red Hat Enterprise Linux 6,7, 7.1, 7.2
- CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 esegue kernel compatibile Red Hat o estremamente affidabile e Enterprise Kernel versione 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Architettura di uno scenario

Questi sono i componenti di uno scenario:

- **Server di configurazione**: un computer locale che coordina la comunicazione e gestione i processi di replica e ripristino di dati. In questo computer si eseguirà un singolo file di installazione del server di configurazione e i componenti aggiuntivi:
    - **Server di processo**: funge da gateway replica. Ricezione dei dati di replica da computer origine protetto, Ottimizza con la memorizzazione nella cache, compressione e la crittografia e invia allo spazio di archiviazione Azure. Viene anche gestito installazione push del servizio mobilità ai computer protetto ed esegue il rilevamento automatico delle macchine virtuali VMware. Il server di processo predefinito è installato sul server di configurazione. È possibile distribuire server di processo aggiuntive autonomi per ridimensionare la distribuzione.
    - **Server di destinazione master**: gestisce i dati di replica durante failback comuni.

- **Servizio mobilità**: questo componente viene distribuito in ogni computer (VMware VM o server fisico) che si desidera replicare in Azure. Acquisisce scrive dati nel computer e li inoltra al server di processo.
- **Azure**: non è necessario creare macchine virtuali Azure per gestire la replica e il controllo a Azure.  È necessario un abbonamento Azure, un account di archiviazione Azure per archiviare dati replicati e una rete virtuale Azure in modo da macchine virtuali di Azure sono connessi a una rete dopo il failover. L'account di archiviazione e di rete deve essere nella stessa regione archivio di servizi di recupero.
- **Failback**: quando si è pronti a esito negativo proveniente da Azure al sito locale dopo caso di errore, è necessario creare una macchina virtuale Azure come server processo temporaneo. Al termine failback, è possibile eliminarlo. Per opzione, è necessario anche una connessione VPN (o Azure ExpressRoute) tra il sito locale e la rete Azure in cui si trovano nelle macchine virtuali di Azure. Se il traffico failback è notevole è anche necessario impostare uno schema dedicato server target computer locale. Per il traffico più chiaro è possibile utilizzare il server di destinazione master predefinito con il server di configurazione.


L'immagine mostra come questi componenti interagiscono.

![architettura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/fisici a Azure**

## <a name="azure-prerequisites"></a>Prerequisiti Azure

Ecco cosa occorre in Azure per distribuire questo scenario.

**Prerequisito** | **Dettagli**
--- | ---
**Account Azure**| È necessario un account di [Microsoft Azure](http://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**Spazio di archiviazione Azure** | Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure vengono create in caso di failover. <br/><br/>Per archiviare dati è necessario un account di archiviazione standard o premium nella stessa regione archivio di servizi di recupero.<br/><br/>È possibile usare un account di archiviazione LRS o GRS. È consigliabile GRS in modo da dati flessibili se si verifica un'interruzione internazionali oppure se non è possibile ripristinare l'area principale. [Altre informazioni](../storage/storage-redundancy.md).<br/><br/> [Spazio di archiviazione Premium](../storage/storage-premium-storage.md) viene generalmente utilizzata per macchine virtuali che devono avere un in modo coerente prestazioni IO e latenza ridotta a carichi di lavoro in modo intensivo IO host.<br/><br/> Se si desidera utilizzare un account premium per memorizzare dati replicati, è necessario anche un account di archiviazione standard per l'archiviazione dei registri di replica che acquisire le modifiche ai dati in locale.<br/><br/> Si noti che gli account di archiviazione creati nel portale di Azure non possono essere spostati tra gruppi di risorse. Anche protezione per gli account di archiviazione premium India centrale e Sud India non è supportata.<br/><br/> [Ulteriori informazioni sui](../storage/storage-introduction.md) Spazio di archiviazione Azure.
**Rete Azure** | È necessario un virtuali Azure che macchine virtuali di Azure si connetterà in caso di failover. La rete virtuale Azure deve essere nella stessa regione archivio di servizi di recupero.
**Failback da Azure** | È necessario un temporaneo server processo impostato come una macchina virtuale Azure. È possibile creare questo quando è pronti per avere esito negativo indietro ed eliminarlo al termine fail indietro.<br/><br/> Per avere esito negativo indietro occorre una connessione VPN o Azure ExpressRoute, dalla rete Azure al sito locale.

## <a name="configuration-server--scale-out-process-prerequisites"></a>Server di configurazione / scalabilità prerequisiti di processo

È necessario configurare un computer locale del server di configurazione / scalabilità server processo.

**Prerequisito** | **Dettagli**
--- | ---
**Server di configurazione**| È necessario un fisici locale o computer virtuale che esegue Windows Server 2012 R2. Tutti i componenti del ripristino del sito locale vengono installati nel computer in uso.<br/><br/>Per la replica VMware VM, è consigliabile che si distribuisce il server come una VM VMware disponibilità. Se si sta replicare macchine fisiche il computer può essere un server fisico.<br/><br/> Failback al sito locale da Azure sia sempre in macchine virtuali VMware indipendentemente dal fatto che non è riuscita in macchine virtuali o server fisici. Se non distribuisce il server di configurazione come una VM VMware è necessario configurare un server di destinazione master separato come una VM VMware per ricevere il traffico failback.<br/><br/>Se il server è un VM VMware, il tipo di scheda di rete deve essere VMXNET3. Se si usa un altro tipo di rete è necessario installare un [aggiornamento VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) nel server vSphere 5.5.<br/><br/>Il server deve disporre di un indirizzo IP statico.<br/><br/>Il server non deve essere un Controller di dominio.<br/><br/>Il nome host del server deve essere 15 caratteri o meno.<br/><br/>Il sistema operativo dovrebbe essere solo in lingua inglese.<br/><br/> È necessario installare VMware vSphere 6.0 PowerCLI. sul server di configurazione.<br/><br/>Il server di configurazione deve accesso a internet. Accesso in uscita è necessario come indicato di seguito:<br/><br/>Accesso temporaneo su HTTP 80 durante l'installazione dei componenti il ripristino del sito (per scaricare MySQL)<br/><br/>Accesso in uscita in corso in HTTPS 443 della gestione della replica<br/><br/>Accesso in uscita in corso nel 9443 HTTPS per il traffico di replica (questa porta può essere modificata)<br/><br/>Il server sarà necessario l'accesso ai seguenti URL in modo che sia possibile connetterlo al Azure: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net<br/><br/>Se si dispone di regole firewall basato su indirizzo IP sul server, assicurarsi che le regole di consentano la comunicazione con Azure. È necessario consentire di [Intervalli di indirizzi IP del Data Center del Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e il protocollo HTTPS (443).<br/><br/>Consentire a intervalli di indirizzi IP per l'area geografica Azure dell'abbonamento e per Usa ovest.<br/><br/>Consentire a questo URL per il download di MySQL:.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>Prerequisiti per l'host vCenter/vSphere VMware

**Prerequisito** | **Dettagli**
--- | ---
**vSphere**| È necessario uno o più hypervisor vSphere VMware.<br/><br/>Hypervisor devono essere in esecuzione la versione vSphere 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti.<br/><br/>È consigliabile che l'host vSphere e vCenter server si trovino nella stessa rete del server di processo (sarà in rete in cui il server di configurazione si trova a meno che non è stato configurato un server di processo dedicato).
**vCenter** | È consigliabile distribuire un server di vCenter VMware per gestire l'host vSphere. Deve essere eseguita vCenter versione 6.0 o 5.5 con gli aggiornamenti più recenti.<br/><br/>Tenere presente che il ripristino del sito non supporta vCenter nuove caratteristiche vSphere 6.0 incrociato come vCenter vMotion, volumi virtuali e lo spazio di archiviazione DRS. Supporto per il ripristino del sito è limitato alle funzionalità che sono anche disponibili nella versione 5.5.


## <a name="protected-machine-prerequisites"></a>Computer protetto prerequisiti

**Prerequisito** | **Dettagli**
--- | ---
**Locale (macchine virtuali VMware)** | Macchine virtuali VMware che si desidera proteggere necessario disporre di strumenti di VMware installato e in esecuzione.<br/><br/> Computer che si desidera proteggere devono essere conformi con [Azure prerequisiti](site-recovery-best-practices.md#azure-virtual-machine-requirements) per la creazione di macchine virtuali di Azure.<br/><br/>Disco singoli computer protetto non deve essere superiore a 1023 GB. Una macchina virtuale possono essere presenti fino a 64 dischi (in questo modo fino a 64 TB). <br/><br/>Minimo 2 GB di spazio disponibile su unità di installazione per l'installazione di componenti.<br/><br/>Protezione delle macchine virtuali con dischi crittografati non è supportata.<br/><br/>Condiviso guest disco cluster non sono supportati.<br/><br/>**Porta 20004** deve essere aperto nel firewall locale del computer virtuale protetto, se si desidera abilitare **la coerenza dell'applicazione**.<br/><br/>Computer in cui sono Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) avvio non è supportato.<br/><br/>Nomi dei computer deve contenere tra 1 e 63 caratteri (lettere, numeri e trattini). Il nome deve iniziare con una lettera o un numero e terminano con una lettera o un numero. Dopo aver attivato la replica per un computer è possibile modificare il nome di Azure.<br/><br/>Se l'origine macchine Virtuali include il raggruppamento NIC viene convertita in una singola scheda dopo il controllo a Azure.<br/><br/>Se macchine virtuali protette dispone di un disco iSCSI il ripristino del sito converte il disco di iSCSI macchine Virtuali protetto in un file disco rigido virtuale a quando la macchina virtuale ha esito negativo su in Azure. Se la destinazione iSCSI può essere raggiunto dalla macchina virtuale Azure verranno effettuare la connessione e vedere essenzialmente a due dischi: il disco disco rigido virtuale nella macchina virtuale Azure e disco iSCSI di origine. In questo caso è necessario disconnettere la destinazione iSCSI che viene visualizzato nella macchina virtuale Azure.
**Computer Windows (fisica o VMware)** | Il computer deve essere in esecuzione un sistema operativo a 64 bit supportato: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con in almeno SP1.<br/><br/> Il sistema operativo deve essere installato sull'unità C:\. Il disco del sistema operativo deve essere un disco di base di Windows e non dinamica. Il disco di dati può essere dinamico.<br/><br/>Il ripristino del sito supporta macchine virtuali con un disco RDM. Durante l'opzione, il ripristino del sito riutilizza disco RDM se è disponibile su disco macchine Virtuali e RDM origine originale. Se non sono disponibili, durante il failback il ripristino del sito verrà creato un nuovo file VMDK per ogni disco.
**Computer Linux** (phyical o VMware)|  È necessario un sistema operativo a 64 bit supportato: Red Hat Enterprise Linux 6.7,7.1,7.2; Centos 6.5, 6.6,6.7,7.0,7.1,7.2; Oracle Enterprise Linux 6.4, 6.5 esegue kernel compatibile Red Hat o estremamente affidabile e Enterprise Kernel versione 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>file /etc/hosts nel computer protetto devono contenere voci da associano il nome host locale a indirizzi IP associati a tutte le schede di rete.<br/><br/>Se si desidera connettersi a una macchina virtuale Azure che eseguono Linux dopo failover utilizzando un Secure Shell client (ssh), assicurarsi che il servizio di Secure Shell sul computer protetto sia impostato per avviare automaticamente all'avvio di sistema e che le regole firewall consentano un ssh la connessione.<br/><br/>Nome host, punti di montaggio, i nomi dei dispositivi e percorsi di sistema Linux e nomi di file (ad esempio/ecc /; /usr) devono essere solo in lingua inglese.<br/><br/>Protezione può essere attivata solo per i computer Linux con lo spazio di archiviazione seguente: File di sistema (EXT3, ETX4, ReiserFS XFS); Percorsi multipli software dispositivo mapping (percorsi multipli)); Gestione di volume: (LVM2). Server fisici con lo spazio di archiviazione di HP CCISS controller non sono supportate. Il file System ReiserFS è supportata solo su SUSE Linux Enterprise Server 11 SP3.<br/><br/>Il ripristino del sito supporta macchine virtuali con un disco RDM.  Durante l'opzione per Linux, il ripristino del sito non riutilizzare il disco RDM. Crea invece un nuovo file VMDK per ogni disco RDM corrispondente.<br/><br/>Assicurarsi che sia impostata l'impostazione disk.enableUUID=true nei parametri di configurazione di macchine Virtuali di VMware. Creare la voce se non esiste. È necessario fornire un UUID coerente per il file VMDK in modo che installa correttamente. Aggiunta di questa impostazione garantisce inoltre che le modifiche solo delta vengono trasferite alla locale durante il failback e non una replica completa.
**Servizio di mobilità** |  **Windows**: sarà necessario per inviare automaticamente il servizio di mobilità di macchine virtuali in esecuzione Windows è necessario specificare un account di amministratore (amministratore locale del computer Windows), in modo che il server di processo può eseguire un'installazione push.<br/><br/>**Linux**: per inserire automaticamente il servizio di mobilità in macchine virtuali che eseguono Linux è necessario creare un account che può essere usato dal server di processo per eseguire un'installazione push.<br/><br/> Per impostazione predefinita vengono replicati dischi in un computer. Per [escludere un disco dalla replica](#exclude-disks-from-replication), il servizio mobilità deve essere installato manualmente il computer prima di attivare la replica.<br/>

## <a name="prepare-for-deployment"></a>Prepararsi per la distribuzione

Per prepararsi per la distribuzione che è necessario:

1. [Configurare una rete Azure](#set-up-an-azure-network) in cui macchine virtuali di Azure sarà trovare quando si sta caricare un dopo il failover. Inoltre, per il failback è necessario configurare una connessione VPN (o Azure ExpressRoute) dalla rete Azure al sito locale.
2. [Configurare un account Azure dello spazio di archiviazione](#set-up-an-azure-storage-account) per i dati replicati.
3. [Preparare un account](#prepare-an-account-for-automatic-discovery) sul server vCenter o vSphere ospita in modo che il ripristino del sito può rilevare automaticamente macchine virtuali VMware che vengono aggiunti.
4. [Preparare il server di configurazione](#prepare-the-configuration-server) per garantire possono accedere agli URL necessari e installare vSphere 6.0 PowerCLI.


### <a name="set-up-an-azure-network"></a>Configurare una rete Azure

- La rete dovrebbe essere nella stessa regione Azure a quello in cui verrà distribuito nell'archivio di servizi di recupero.
- A seconda del modello di risorsa che si desidera utilizzare per non riuscita su macchine virtuali di Azure, è necessario configurare la rete Azure in [modalità di ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o [classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Per avere esito negativo proveniente da Azure al sito locale VMware è necessario una connessione VPN (o una connessione di Azure ExpressRoute) dalla rete Azure in cui si trovano, alla rete locale in cui si trova il server di configurazione macchine virtuali di Azure replicato.
- Modelli [disponibili ulteriori informazioni sulla](../vpn-gateway/vpn-gateway-site-to-site-create.md) distribuzione supportata per le connessioni da sito VPN e su come [configurare una connessione](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- In alternativa è possibile impostare [Azure ExpressRoute](../expressroute/expressroute-introduction.md). [Altre informazioni](../expressroute/expressroute-howto-vnet-portal-classic.md) sull'impostazione di una rete Azure con ExpressRoute.

> [AZURE.NOTE] [Migrazione delle reti](../resource-group-move-resources.md) attraverso gruppi di risorse all'interno della stessa sottoscrizione o sottoscrizioni non è supportata per le reti utilizzate per la distribuzione il ripristino del sito.

### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione Azure

- È necessario standard o un account di archiviazione Azure premium per contenere dati replicati in Azure. L'account deve essere nella stessa regione archivio di servizi di recupero. A seconda del modello di risorsa che si desidera utilizzare per non riuscita su macchine virtuali di Azure, è necessario configurare un account in [modalità ARM](../storage/storage-create-storage-account.md) o [modalità classica](../storage/storage-create-storage-account-classic-portal.md).
- Se si usa un account premium per i dati replicati che necessarie per creare un altro account standard per memorizzare i registri di replica che acquisire le modifiche ai dati in locale.  

> [AZURE.NOTE] [Migrazione degli account di archiviazione](../resource-group-move-resources.md) tra gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni non è supportata per gli account di archiviazione utilizzati per la distribuzione il ripristino del sito.

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparare un account per il rilevamento automatico

Il server di processo di ripristino del sito di individuare automaticamente macchine virtuali VMware vSphere hosts o su un server vCenter che gestisce host. Per eseguire il rilevamento delle credenziali il ripristino del sito che può accedere al server VMware. Non è rilevante se si sta replicare solo computer fisici.

1. Per utilizzare un account dedicato per il rilevamento automatico creare un ruolo (ad esempio Azure_Site_Recovery) livello vCenter con le [autorizzazioni necessarie](#vmware-account-permissions).
2. Creare un nuovo utente nel server di host o vCenter vSphere e assegnare il ruolo all'utente. In un secondo momento è necessario informare il ripristino del sito queste credenziali in modo che possa eseguire il rilevamento automatico.

    >[AZURE.NOTE] Un account utente vCenter con un ruolo di sola lettura è possibile eseguire failover ma non è possibile arrestare macchine di origine protetta. Se si desidera arrestare tali macchine è necessario il ruolo [Azure_Site_Recovery](#vmware-account-permissions) . Se si sta solo la migrazione macchine virtuali da VMware in Azure e non sia necessario failback il ruolo di sola lettura è sufficiente.

### <a name="prepare-the-configuration-server"></a>Preparare il server di configurazione

1.  Assicurarsi che il computer in uso per il server della configurazione è conforme ai [Prerequisiti](#configuration-server-prerequisites). In particolare assicurarsi che il computer sia connesso a internet con queste impostazioni:

    - Consentire l'accesso a questi URL: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net
    - Consentire l'accesso a [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) per scaricare MySQL.
    - Consentire la comunicazione firewall in Azure con gli [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/download/confirmation.aspx?id=41653) e il protocollo HTTPS (443).

2.  Scaricare e installare [VMware vSphere 6.0 PowerCLI](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) sul server di configurazione. (Al momento le altre versioni di PowerCLI non sono supportati, incluse le versioni R della versione 6.0.)


## <a name="create-a-recovery-services-vault"></a>Creare un archivio di servizi di recupero

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Management** > **eseguire il Backup e il ripristino del sito (OMS)**. In alternativa è possibile fare clic su **Sfoglia** > **Archivio di servizi di recupero** > **Aggiungi**.

    ![Nuovo archivio](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Nella **casella Nome** specificare un nome descrittivo per identificare l'archivio. Se si dispone di più di una sottoscrizione, selezionare una di esse.
4. [Creare un nuovo gruppo di risorse](../resource-group-template-deploy-portal.md) o selezionarne uno esistente. Specificare un'area di Azure. Computer verranno replicati in quest'area. Si noti che lo spazio di archiviazione Azure e reti utilizzate per il ripristino del sito devono essere presenti nell'area stessa. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se si desidera accedere rapidamente l'archivio nel dashboard fare clic su **Aggiungi al dashboard** e quindi fare clic su **Crea**.

    ![Nuovo archivio](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

Nuovo archivio verrà visualizzato nel **Dashboard** > **tutte le risorse**, quindi nella finestra principale blade **archivi di servizi di recupero** .

## <a name="getting-started"></a>Guida introduttiva

Il ripristino del sito offre un'esperienza introduzione progettato per utenti di iniziare e l'esecuzione di presto. Controlla prerequisiti e illustra i passaggi che necessari per ottenere il ripristino del sito distribuito.

È necessario selezionare il tipo di computer che si desidera replicare e in cui si desidera replicare. Configurazione dell'infrastruttura, inclusi server locali, le impostazioni di Azure, criteri di replica e pianificazione delle capacità. Al termine dell'infrastruttura si abilita la macchine virtuali e server fisici replica. È possibile eseguire failover per computer specifici oppure creare piani di ripristino per avere esito negativo su più dispositivi.

Iniziare introduzione scegliendo la modalità con cui si desidera distribuire il ripristino del sito. Le modifiche flusso introduzione leggermente in base ai propri requisiti di replica.


## <a name="step-1-choose-your-protection-goals"></a>Passaggio 1: Scegliere i propri obiettivi di protezione

Selezionare gli elementi da replicare e in cui si desidera replicare.

1. In e il **ripristino servizi archivi** selezionare l'insieme di credenziali e fare clic su **Impostazioni**.
2. In **Impostazioni** > **Introduzione** fare clic sul **Ripristino del sito** > **passaggio 1: preparare infrastruttura** > **obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. In **obiettivo di protezione** selezionare **Per Azure**e selezionare **Sì, con VMware vSphere Hypervisor**. Fare clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Passaggio 2: Configurare l'ambiente di origine

Configurare il server di configurazione e la registrazione nell'archivio di servizi di recupero. Se si sta replicare macchine virtuali VMware specificare l'account di VMware si usa per il rilevamento automatico.

1. Fare clic su **passaggio 1: preparare infrastruttura** > **origine**. **Nell'origine preparare** se non si dispone di un server di configurazione fare clic su **+ Configurazione server** per crearne una.

    ![Impostare un'origine](./media/site-recovery-vmware-to-azure/set-source1.png)

2. In controllo di blade **Aggiungi Server** il **Server di configurazione** viene visualizzata nel **Server digitare**.
3. Prima di configurare il server di configurazione verificare i [Prerequisiti](#configuration-server-prerequisites). In controllo particolare che il computer possa accedere gli URL necessari.
4.  Scaricare il file di installazione unificata installazione di ripristino del sito.
5.  Scaricare la chiave di registrazione di archivio. È necessario questo durante l'installazione unificata. La chiave è valida per 5 giorni dopo aver generato.

    ![Impostare un'origine](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  Nel computer in uso del server di configurazione, eseguire l'installazione unificata per installare il server di configurazione, il server di processo e il server di destinazione principale.


### <a name="run-site-recovery-unified-setup"></a>Il ripristino del sito Esegui unificata il programma di installazione

1.  Eseguire il file di installazione di installazione unificata.
2.  Nella **prima di iniziare** selezionare **Installa il server di configurazione processo**.

    ![Prima di iniziare](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. In **Licenza Software di terze parti** fare clic sui **accetto** per scaricare e installare MySQL.

    ![Terzo = software di terze parti](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. In **registrazione** individuare e selezionare la chiave di registrazione che è stato scaricato dall'archivio.

    ![Registrazione](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Nelle **Impostazioni di Internet** specificare come si connetterà il ripristino del sito di Azure Provider eseguito sul server di configurazione tramite internet.

    - Se si desidera connettersi con i proxy che è attualmente configurato nel computer selezionare **Connetti con le impostazioni del proxy esistente**.
    - Se si desidera che il Provider di connettersi direttamente selezionare **Connetti direttamente senza un proxy**.
    - Se il proxy esistente richiede l'autenticazione o che si desidera utilizzare un proxy personalizzato per la connessione al Provider, selezionare **Connetti con le impostazioni del proxy personalizzato**.
        - Se si utilizza un proxy personalizzato è necessario specificare l'indirizzo, porta e le credenziali
        - Se si usa un proxy che deve è già stato consentito URL descritto in [Prerequisiti](#configuration-server-prerequisites).

    ![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. In **Controllo prerequisiti** installazione viene eseguito un controllo per assicurarsi che è possibile eseguire l'installazione. Se viene visualizzato un avviso sulle **selezionare Sincronizza ora generale** verificare che l'ora dell'orologio di sistema (impostazioni di**Data e ora** ) è diverso da quello di fuso orario.

    ![Prerequisiti](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. Configurazione di **MySQL** creare le credenziali per l'accesso all'istanza di server MySQL che verrà installato.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. Nella pagina **Dettagli sull'ambiente** selezione se si intende replicare macchine virtuali VMware. Se si è il programma di installazione verifica che sia installato 6.0 PowerCLI.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. Nella pagina selezione **Percorso di installazione** nel punto in cui si desidera installare i file binari e archiviare la cache. È possibile selezionare un'unità con almeno 5 GB di spazio di archiviazione disponibile, ma è consigliabile un'unità di cache con almeno 600 GB di spazio libero.

    ![Percorso di installazione](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. Selezione di **Rete** specificare comunicare ascoltatore (scheda di rete e la porta SSL) in cui il server di configurazione verrà inviare e ricevere replica dati. È possibile modificare l'impostazione predefinita porta (9443). Oltre a questa porta porta 443 verrà utilizzata da un server web che coordina operazioni di replica. 443 non devono essere utilizzato per la ricezione di traffico.


    ![Selezione di rete](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  In **Riepilogo** esaminare le informazioni e fare clic su **Installa**. Al termine dell'installazione viene generata una frase. Sarà necessario quando si abilita la replica quindi copiarlo e mantenerlo in un percorso sicuro.

    ![Riepilogo](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Al termine del registrazione il server viene visualizzato nelle **Impostazioni** > **server** blade nell'archivio di.



#### <a name="run-setup-from-the-command-line"></a>Eseguire l'installazione dalla riga di comando

È possibile configurare il server di configurazione dalla riga di comando:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parametri:

- / ServerMode: obbligatorio. Specifica se la configurazione e processo server deve essere installato o solo il server di processo. Valori di input: CS, Mr
- InstallLocation: obbligatorio. La cartella in cui siano installati i componenti.
- / MySQLCredsFilePath. Obbligatorio. Il percorso del file in cui sono archiviate le credenziali del server MySQL. Il file deve essere in questo formato:
    - [MySQLCredentials]
    - MySQLRootPassword = "<Password>"
    - MySQLUserPassword = "<Password>"
- / VaultCredsFilePath. Obbligatorio. Il percorso del file archivio credenziali
- / EnvType. Obbligatorio. Il tipo di installazione. Valori: VMware, NonVMware
- / PSIP e /CSIP. Obbligatorio. L'indirizzo IP del server di processo e configurazione del server.
- / PassphraseFilePath. Obbligatorio. Il percorso del file passphrase.
- / BypassProxy. Facoltativo. Specifica che il server di configurazione si connette a Azure senza un proxy.
- / ProxySettingsFilePath. Facoltativo. Impostazioni del proxy (proxy predefinito richiede l'autenticazione o un proxy personalizzato). Il file deve essere in questo formato:
    - [ProxySettings]
    - ProxyAuthentication = "Sì/No"
    - Proxy IP = "indirizzo IP >"
    - ProxyPort = "<Port>"
    - ProxyUserName = "<User Name>"
    - ProxyPassword = "<Password>"
- DataTransferSecurePort. Facoltativo. Numero di porta da utilizzare per i dati di replica.
- SkipSpaceCheck. Facoltativo. Salta controllo spazio della cache.
- AcceptThirdpartyEULA. Obbligatorio. Contrassegno implica l'accettazione di terze parti, contratto di licenza.
- ShowThirdpartyEULA. Obbligatorio. Visualizza contratto di licenza di terze parti. Se specificato come input vengono ignorati tutti gli altri parametri.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Aggiungere l'account di VMware utilizzato per il rilevamento automatico

 Quando si è pronti per la distribuzione è [stato creato un account di VMware](#prepare-an-account-for-automatic-discovery) utilizzabili il ripristino del sito per il rilevamento automatico. Aggiungere l'account come segue:

1. Aprire **CSPSConfigtool.exe**. È disponibile come collegamento sul desktop e si trova nella cartella \home\svsystems\bin [percorso di installazione].
2. Fare clic su **Gestisci account** > **Aggiungi Account**.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure/credentials1.png)

3. In **Dettagli conto** aggiungere l'account che verrà utilizzato per il rilevamento automatico. Si noti che può richiedere più di 15 minuti per il nome dell'account all'interno del portale. Per aggiornare immediatamente, fare clic su **Configurazione server** > nome server > **Aggiorna Server**.

    ![Dettagli](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Connettersi a host vSphere e vCenter server

Se si sta replicare macchine virtuali VMware connettersi all'host vSphere e vCenter server.

1. Verificare che il server di configurazione abbia accesso alla rete e host vSphere vCenter server.
2. Fare clic su **Prepara infrastruttura** > **origine**. **Preparare** origine selezionare il server di configurazione e fare clic su **+ vCenter** per aggiungere un server di host o vCenter vSphere.
3. In **Aggiungi vCenter** specificare un nome descrittivo per il server di host o vCenter vSphere e specificare l'indirizzo IP o il nome di dominio completo del server. Lasciare la porta come 443, a meno che il server VMware sono configurati per le richieste in un'altra porta. Quindi selezionare l'account da utilizzare per connettersi al server VMware. Fare clic su **OK**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Se si sta aggiungendo il server di vCenter o vSphere host con un account che non dispone di privilegi di amministratore sul server vCenter o host, quindi assicurarsi che l'account disponga questi privilegi abilitati: Data Center, archivio dati, cartelle, Host, rete, risorsa, virtuale computer, vSphere distribuito cambia. Il server di vCenter deve inoltre i privilegi di visualizzazioni di spazio di archiviazione.

Il ripristino del sito si connette al server VMware utilizzando le impostazioni specificate e individua macchine virtuali.

## <a name="step-3-set-up-the-target-environment"></a>Passaggio 3: Configurare l'ambiente di destinazione

Verificare di che avere un account di archiviazione per la replica e una rete Azure a cui si connetterà macchine virtuali di Azure dopo il failover.

1.  Fare clic su **Prepara infrastruttura** > **destinazione** e selezionare l'abbonamento Azure che si desidera utilizzare.
2.  Specificare il modello di distribuzione che si desidera utilizzare per le macchine virtuali dopo il failover.
3.  Il ripristino del sito verifica la presenza di uno o più account di archiviazione di Azure compatibile e reti.

    ![Destinazione](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Se non sono state create un account di archiviazione e si desidera creare una utilizzando ARM fare clic su **+ account di archiviazione** per eseguire tale all'interno del testo.  In e il **conto dello spazio di archiviazione crea** specificare un nome dell'account, tipo, sottoscrizione e posizione. L'account dovrebbe essere nella stessa regione archivio di servizi di recupero.

    ![Spazio di archiviazione](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Si noti che:

    - Se si desidera creare un account di archiviazione utilizzando il modello classico è necessario farlo nel portale di Azure. [Ulteriori informazioni](../storage/storage-create-storage-account-classic-portal.md)
    - Se si usa un account di archiviazione premium per i dati replicati da che è necessario configurare un account di spazio di archiviazione aggiuntivo standard archivio replica registra che le modifiche in corso di acquisizione di dati locali.

    > [AZURE.NOTE] Proteggere gli account di archiviazione premium India centrale e Sud India non è supportato.

4.  Selezionare una rete Azure. Se non è stata creata una rete e si desidera eseguire tale operazione utilizzando ARM fare clic su **+ rete** per eseguire tale all'interno del testo. Nella e **Crea rete virtuale** specificare un nome di rete, indirizzi di intervalli, dettagli subnet, sottoscrizione e posizione. La rete deve essere nello stesso percorso archivio di servizi di recupero.

    ![Rete](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Se si desidera creare una rete utilizzando il modello classico è necessario farlo nel portale di Azure. [Altre informazioni](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica

1. Per creare una nuova replica criteri, fare clic su **Prepara infrastruttura** > **Le impostazioni di replica** > **+ creare e associare**.
2. **Creare** e associati criteri specificare un nome di criteri.
3. In **soglia rilasciato**: specificare il limite di rilasciato. Verranno generati avvisi quando replica continua supera questo limite.
5. In **criteri di conservazione punto di ripristino**, specificare in ore il periodo di tempo la finestra di conservazione per ogni punto di ripristino. Computer protetto possono essere recuperate in un punto qualsiasi all'interno di una finestra. Fino a conservazione di 24 ore è supportata per i computer replicato allo spazio di archiviazione premium.
6. **Frequenza snapshot coerente App**, specificare la frequenza (in minuti) verranno creati i punti di ripristino contenente snapshot coerenti con l'applicazione.
7. Quando si crea un criterio di replica, per impostazione predefinita un criterio di corrispondenza viene automaticamente creato per failback. Ad esempio se il criterio di replica è **Agente criteri** i criteri di failback verrà **failback di criteri di rappresentazione**. Il criterio non viene utilizzato fino a quando non si avvia un failback.  
8. Fare clic su **OK** per creare il criterio.

    ![Criteri di replica](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Quando si crea un nuovo criterio ha automaticamente associato con il server di configurazione. Fare clic su **OK**.

    ![Criteri di replica](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>Passaggio 5: Pianificazione delle capacità

Dopo aver creato il basic infrastruttura configurare è possibile considerare la pianificazione della capacità e stabilire la necessità di risorse aggiuntive.

Il ripristino del sito fornisce una pianificazione capacità che ne faciliti allocare risorse per l'ambiente di origine, componenti di ripristino del sito, rete e lo spazio di archiviazione. È possibile eseguire la pianificazione in modalità rapida per le stime in base a un numero medio di macchine virtuali, dischi e lo spazio di archiviazione o in visualizzazione dettagliata in cui verrà input figure a livello di carico di lavoro. Prima di iniziare è necessario:

- Raccogliere informazioni sull'ambiente di replica, tra cui macchine virtuali, dischi per macchine virtuali e lo spazio di archiviazione per disco.
- Valutare le modifiche (varianza) giornaliere che saranno disponibili per i dati replicati. È possibile utilizzare la [capacità di vSphere pianificazione accessorio](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) per eseguire questa operazione.

1.  Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Leggere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2.  Al termine selezionare **Sì** **è completato la pianificazione della capacità?**

    ![Pianificazione delle capacità](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

Nella tabella seguente consente di acquisire un numero di punti che consentono di pianificazione delle capacità per questo scenario.


**Componente** | **Dettagli**
--- | --- | ---
**Replica** | **Massimo giornaliero modificare tariffe**, ovvero un computer protetto è possibile utilizzare solo un server di processo e un server singolo processo può gestire giornaliera cambia valutare fino a 2 TB. In questo modo 2 TB è che la data giornaliera massima modifica frequenza con cui è supportato per un computer protetto.<br/><br/> **Velocità massima**, ovvero una macchina replicata a cui può appartenere a un account di archiviazione in Azure. Un account di archiviazione standard può gestire un massimo di 20.000 richieste al secondo ed è consigliabile mantenere il numero di IOPS in un computer di origine a 20.000. Per esempio se si dispone di un computer di origine con dei ogni 5 dischi genera 120 IOPS (8 KB dimensione) nel sito di origine verranno essere all'interno di Azure al limite di IOPS di 500. Il numero di account di archiviazione necessario = origine totale IOPs/20000.
**Server di configurazione** | Il server di configurazione sarà in grado di gestire la capacità di frequenza modifica giornaliera attraverso tutte carichi di lavoro in esecuzione nel computer protetto e deve larghezza di banda sufficiente replicare continuamente i dati allo spazio di archiviazione Azure.<br/><br/> È buona norma è consigliabile che il server di configurazione si trovi nella stessa rete e segmento LAN come computer di cui si desidera proteggere. Può trovarsi in una rete diversa ma computer che si desidera proteggere dovrebbe essere visibilità rete L3 ad esso.<br/><br/> Consigli dimensioni per il server di configurazione sono riepilogati nella tabella seguente.
**Server di processo** | Per impostazione predefinita sul server di configurazione è installato il primo server di processo. È possibile distribuire server processo aggiuntivi per ridimensionare l'ambiente. Si noti che:<br/><br/> Il server di processo riceve replica dati dal computer protetto e ottimizza con la memorizzazione nella cache, compressione e crittografia prima di inviare a Azure. Il computer server processo deve avere risorse sufficienti per eseguire queste attività.<br/><br/> Il server di processo utilizza cache basata su disco. È consigliabile un disco separato della cache di 600 GB o più per gestire le modifiche ai dati archiviate in caso di bottiglia di rete o un'interruzione del.

### <a name="size-recommendations-for-the-configuration-server"></a>Consigli dimensioni per il server di configurazione

**CPU** | **Memoria** | **Dimensione della cache disco** | **Modifica dei dati** | **Computer protetto**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 core @ 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | Replicare macchine meno di 100.
12 vCPUs (2 sockets * 6 core @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replicare tra 100-150 computer.
16 vCPUs (2 sockets * 8 core @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replicare tra 150-200 computer.
Distribuire un altro server di processo | | | > 2 TB | Distribuire server processo aggiuntivi se si sta replicare più di 200 computer o se i dati giornalieri cambiano tasso supera i 2 TB.

Dove:

- Ogni computer di origine è configurato con 3 dischi di 100 GB.
- È stato usato benchmark lo spazio di archiviazione di 8 unità sa di 10 K RPM con RAID 10 per le misurazioni disco cache.

### <a name="size-recommendations-for-the-process-server"></a>Dimensioni consigliate per il server di processo

Se è necessario proteggere più di 200 computer o modifiche giornaliere è maggiore di 2 TB è possibile aggiungere server processo aggiuntivi per gestire il carico di replica. Per ridimensionare è possibile:

- Aumentare il numero di configurazione server. Ad esempio è possibile proteggere computer fino a 400 con due server di configurazione.
- Aggiunta di server aggiuntivi process e utilizzarli per gestire il traffico anziché (o in aggiunta a) il server di configurazione.

La tabella seguente descrive uno scenario in cui:

- Non si prevede di utilizzare il server di configurazione come un server di processo.
- Configurato un server di processo aggiuntivi.
- Configurare protetta macchine virtuali di utilizzare il server di processo aggiuntivi.
- Ogni computer protetto origine è configurato con tre dischi di 100 GB.

**Server di configurazione** | **Server di processo aggiuntivi**| **Dimensione della cache disco** | **Modifica dei dati** | **Computer protetto**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 core @ 2,5 GHz), 16 GB di memoria | 4 vCPUs (2 sockets * 2 core @ 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | Replicare macchine minore o uguale a 85.
8 vCPUs (2 sockets * 4 core @ 2,5 GHz), 16 GB di memoria | 8 vCPUs (2 sockets * 4 core @ 2,5 GHz), 12 GB di memoria | 600 GB | 250 GB a 1 TB | Replicare tra 85 150 computer.
12 vCPUs (2 sockets * 6 core @ 2,5 GHz), 18 GB di memoria | 12 vCPUs (2 sockets * 6 core @ 2,5 GHz) 24 GB di memoria | 1 TB | 1 TB a 2 TB | Replicare tra 225 150 computer.


Il modo in cui si modifica la scala dei server verrà dipendono la preferenza per una scala di backup o ridimensionare il modello.  È scalare distribuendo alcuni configurazione high-end e i server o scalabilità distribuendo più server con meno risorse. Ad esempio: se è necessario proteggere 220 computer è possibile eseguire una delle operazioni seguenti:

- Configurare il server di configurazione con 12vCPU e 18 GB di memoria, un server di processo aggiuntivi con 12vCPU, 24 GB di memoria e configurare i computer protetto da utilizzare solo il server di processo aggiuntivi.
- In alternativa si potrebbe configurare due server di configurazione (8vCPU x 2, 16 GB di RAM) e due server di processo aggiuntivi (1x 8vCPU) e 4vCPU x1 per gestire i computer (220) 135 + 85 e configurare i computer protetto da utilizzare solo il server di processo aggiuntivi.

[Seguire queste istruzioni](#deploy-additional-process-servers) per configurare un server di processo aggiuntivi.

### <a name="network-bandwidth-considerations"></a>Considerazioni sulla larghezza di banda di rete

È possibile utilizzare lo strumento di pianificazione delle capacità per calcolare la larghezza di banda che necessaria per la replica (replica iniziale e quindi delta). Per controllare la quantità della larghezza di banda della replica è necessario tra alcune opzioni:

- **Banda**: VMware che replica in Azure del traffico attraverso un server di processo specifico. È possibile limitare la larghezza di banda nei computer che eseguono come server di processo.
- **Influenza della larghezza di banda**: È possibile determinare la larghezza di banda per la replica tramite un paio di chiavi del Registro di sistema:
    - Il valore del Registro di sistema **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** specifica il numero di thread utilizzati per il trasferimento di dati (replica iniziale o delta) di un disco. Un valore superiore aumenta la larghezza di banda di rete utilizzato per la replica.
    - **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** specifica il numero di thread utilizzato per il trasferimento di dati durante il failback.

#### <a name="throttle-bandwidth"></a>Limitare larghezza di banda

1. Aprire lo snap-in Microsoft Azure Backup MMC computer funge da server processo. Per impostazione predefinita è disponibile sul desktop o in c:\Programmi\Microsoft c:\Programmi\Microsoft Azure ripristino servizi Agent\bin\wabadmin una scelta rapida per Microsoft Azure Backup.
2. In snap-in fare clic su **Modifica proprietà**.

    ![Limitare larghezza di banda](./media/site-recovery-vmware-to-azure/throttle1.png)

3. Nella scheda **Throttling** selezionare **Abilita l'utilizzo della larghezza di banda internet la limitazione per le operazioni di backup**e impostare i limiti per il lavoro e non-lavoro ore. Gli intervalli validi sono compresi 512kbps e 102 Mbps al secondo.

    ![Limitare larghezza di banda](./media/site-recovery-vmware-to-azure/throttle2.png)

È anche possibile utilizzare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Ecco un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set OBMachineSetting NoThrottle** non indica che nessuna limitazione richiesta.


#### <a name="influence-network-bandwidth"></a>Influenza della larghezza di banda di rete

1. Nel Registro di sistema passare **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    - Per influenzare il traffico di larghezza di banda su un disco replica, modificare il valore **UploadThreadsPerVM**o creare la chiave se non esiste.
    - Per influenzare la larghezza di banda per il traffico failback da Azure, modificare il valore **DownloadThreadsPerVM**.
2. Il valore predefinito è "4". In una rete "overprovisioned", queste chiavi del Registro di sistema devono essere modificate dai valori predefiniti. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.

## <a name="step-6-replicate-applications"></a>Passaggio 6: Applicazioni replica

Assicurarsi che sono pronti per l'installazione di servizio mobilità macchine da replicare e quindi abilitare la replica.

### <a name="install-the-mobility-service"></a>Installare il servizio di mobilità

Il primo passaggio per consentire la protezione per macchine virtuali e server fisici è per installare il servizio di mobilità. È possibile farlo in due modi:

- **Push server processo**: quando si abilita la replica in un computer, push e installare il componente di servizio mobilità dal server di processo. Si noti che l'installazione push non verificarsi se macchine già in esecuzione una versione remota todate del componente.
- **Push Enterprise**: automaticamente installare il componente utilizzando il processo di push dell'organizzazione, ad esempio Windows Server Update Services System Center Configuration Manager o [Azure automazione e configurazione di stato desiderato](./site-recovery-automate-mobility-service-install.md). Configurare il server di configurazione prima di eseguire la verifica.
- **Installazione manuale**: installare il componente manualmente in ogni computer in cui si desidera replicare. Configurare il server di configurazione prima di eseguire la verifica.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparare l'ambiente per push automatica nel computer Windows

Ecco come preparare il computer Windows in modo che il servizio di mobilità può essere installato automaticamente dal server di processo.

1.  Creare un account che può essere usato dal server di processo di accedere al computer. L'account deve disporre dei privilegi di amministratore (locale o dominio) e vengono usata unicamente per l'installazione push.

    >[AZURE.NOTE] Se non si usa un account di dominio che è necessario disattivare il controllo di accesso remoto degli utenti del computer locale. A tale scopo, nel registro in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System aggiungere la voce DWORD LocalAccountTokenFilterPolicy con il valore 1. Per aggiungere la voce del Registro di sistema da un tipo CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  In Windows Firewall del computer si desidera proteggere, selezionare **Consenti un'app o con Firewall**. Abilitare **condivisione File e stampanti** e **Strumentazione gestione Windows**. Per i computer che appartengono a un dominio è possibile configurare le impostazioni del firewall con un oggetto Criteri di gruppo.

    ![Impostazioni del firewall](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Aggiungere l'account che è stato creato:

    - Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella \home\svsystems\bin [percorso di installazione].
    - Nella scheda **Gestisci account** , fare clic su **Aggiungi Account**.
    - Aggiungere l'account che è stato creato. Dopo avere aggiunto l'account è necessario fornire le credenziali quando si abilita la replica per un computer.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparare l'ambiente per push automatica nel server Linux

1.  Assicurarsi che il computer Linux che si desidera proteggere è supportato come descritto nel [computer protetto prerequisiti](#protected-machine-prerequisites). Assicurarsi che non esiste connettività tra computer Linux e il server di processo di rete.

2.  Creare un account che può essere usato dal server di processo di accedere al computer. L'account deve essere un utente radice nel server Linux di origine e vengono usata unicamente per l'installazione push.

    - Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella \home\svsystems\bin [percorso di installazione].
    - Nella scheda **Gestisci account** , fare clic su **Aggiungi Account**.
    - Aggiungere l'account che è stato creato. Dopo avere aggiunto l'account è necessario fornire le credenziali quando si abilita la replica per un computer.

3.  Verificare che il file /etc/hosts nel server Linux di origine contiene le voci che mappano hostname locale agli indirizzi IP associati a tutte le schede di rete.
4.  Installare il più recente openssh, server di openssh, pacchetti openssl del computer in cui che si desidera replicare.
5.  Assicurarsi che SSH sia abilitato e in esecuzione su porta 22.
6.  Autenticazione SFTP sottosistema e la password nel file sshd_config come indicato di seguito:

    - Eseguire l'accesso come radice.
    - Nel file di /etc/ssh/sshd_config, individuare la riga che inizia con **PasswordAuthentication**.
    - Rimuovere il commento e modificare il valore da **alcuna** su **Sì**.
    - Individuare la riga che inizia con **sottosistema** e rimuovere il commento.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installare manualmente il servizio di mobilità

I programmi di installazione sono disponibili sul server di configurazione in **file c:\Programmi\Microsoft (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

Sistema operativo di origine | File di installazione del servizio di mobilità
--- | ---
Windows Server (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>Installare il servizio mobilità su un Server di Windows


1. Scaricare ed eseguire il programma di installazione pertinenti.
2. Nella **prima di iniziare** selezionare **mobilità servizio**.

    ![Servizio di mobilità](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Dettagli della **configurazione Server** specificare l'indirizzo IP del server di configurazione e la passphrase generati quando è stato eseguito il programma di installazione unificata. È possibile recuperare la passphrase eseguendo: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** sul server di configurazione.

    ![Servizio di mobilità](./media/site-recovery-vmware-to-azure/mobility6.png)

4. In **Percorso di installazione** lasciare l'impostazione predefinita e fare clic su **Avanti** per iniziare l'installazione.
5. Monitorare l'installazione in **Corso l'installazione** e, se richiesto, riavviare il computer. Dopo l'installazione del servizio può richiedere circa 15 minuti relativi allo stato di aggiornamento nel portale.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Installare il servizio mobilità su un server di Windows mediante il prompt dei comandi

1. Copiare il programma di installazione in una cartella locale (ad esempio C:\Temp) nel server che si desidera proteggere. Il programma di installazione sono disponibili sul Server di configurazione in **\home\svsystems\pushinstallsvc\repository [percorso di installazione]**. Il pacchetto per sistemi operativi Windows avrà un nome simile a Microsoft ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. **Rinominare** il file in MobilitySvcInstaller.exe
3. Eseguire il seguente comando per estrarre il programma di installazione MSI </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>Nella sintassi della riga di comando completa

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parametri**

- **/Role:** Obbligatorio. Specifica se il servizio mobilità deve essere installato. Agente di valori di input | MasterTarget
- **/InstallLocation:** Obbligatorio. Specifica la posizione installare il servizio.
- **/PassphraseFilePath:** Obbligatorio. La passphrase server di configurazione.
- **/LogFilePath:** Obbligatorio. Posizione in cui devono essere creati i file di log di installazione.



#### <a name="uninstall-mobility-service-manually"></a>Disinstallazione manuale di servizio mobilità

È possibile disinstallare servizio mobilità mediante l'aggiunta di installazione applicazioni dal Pannello di controllo o riga di comando.

Il comando per disinstallare il servizio di mobilità tramite riga di comando

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>Installare il servizio mobilità su un server Linux mediante della riga di comando

1. Copiare l'archivio tar appropriato in base alla tabella sopra al computer Linux da replicare.
2. Aprire un'applicazione di shell ed estrarre l'archivio tar compressi in un percorso locale eseguendo:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio tar. Per eseguire questa operazione copiare la passphrase C:\ProgramData\Microsoft Azure sito Recovery\private\connection.passphrase sul server di configurazione e salvarlo in passphrase.txt eseguendo *`echo <passphrase> >passphrase.txt`* nella shell.
4. Installare il servizio mobilità eseguendo *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Specificare l'indirizzo IP interno del server di configurazione e verificare che sia selezionata la porta 443. Dopo l'installazione del servizio può richiedere circa 15 minuti relativi allo stato di aggiornamento nel portale.

**È inoltre possibile installare dalla riga di comando**:

1. Copiare la passphrase \InMage Systems\private\connection file c:\Programmi\Microsoft (x86) sul server di configurazione e salvarlo come "passphrase.txt" sul server di configurazione. Eseguire questi comandi. In questo esempio l'indirizzo IP del server configurazione 104.40.75.37 e la porta HTTPS deve essere 443:

Per installare in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Per installare nel server di destinazione master:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Abilitare la replica

#### <a name="before-you-start"></a>Prima di iniziare

Se si sta replicare VMware virtuale computer tenere presente quanto segue:

- Macchine virtuali VMware vengono rilevate ogni 15 minuti e può richiedere 15 minuti o più per poter comparire nel portale dopo l'individuazione. Analogamente individuazione può richiedere 15 minuti o più quando si aggiunge un nuovo host di server o vSphere vCenter.
- Le modifiche dell'ambiente del computer virtuale (ad esempio installazione degli strumenti di VMware) possono richiedere anche 15 minuti o più aggiornati nel portale.
- È possibile cercare l'ultima volta individuato macchine virtuali VMware nel campo **contatto ultima** per l'host server/vSphere vCenter su e il **Server di configurazione** .
- Per aggiungere macchine per la replica senza attendere che l'individuazione pianificata, evidenziare il server di configurazione (non selezionarla) e fare clic sul pulsante **Aggiorna** .
- Quando si attiva la replica, se il computer è il processo server preparato automaticamente installa il servizio mobilità su di esso.

#### <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Quando si abilita la replica, per impostazione predefinita vengono replicati tutti i dischi in un computer. È possibile escludere dischi dalla replica. Ad esempio, è consigliabile non replicare dischi con dati temporanei o dati che sono stato aggiornato ogni volta che un computer o il riavvio dell'applicazione (ad esempio Pagefile o tempdb di SQL Server). Se si desidera escludere dischi si noti che:

- È possibile escludere solo dischi che già installato il servizio di mobilità. È necessario installare [manualmente il servizio mobilità](#install-the-mobility-service-manually) perché il servizio mobilità viene installato solo utilizzando il meccanismo di push dopo l'attivazione della replica.
- Solo dischi di base possono essere esclusi dalla replica. Non è possibile escludere OS o dischi dinamici.
- Dopo l'attivazione della replica non è possibile aggiungere o rimuovere dischi della replica. Se si desidera aggiungere o escludere un disco sarà necessario disattivare la protezione per il computer e quindi riattivarla.
- Se si esclude un disco che è necessari per un'applicazione di funzionare, dopo il controllo a Azure sarà necessario crearlo manualmente in Azure in modo che è possibile eseguire l'applicazione replicato. In alternativa è possibile integrare automazione Azure un piano di ripristino per creare il disco durante il failover del computer.
- Creare manualmente in Azure dischi Impossibile eseguire nuovamente. Ad esempio se si esito negativo su tre dischi e creare due direttamente in Azure, tutti e cinque Impossibile eseguire nuovamente. Non è possibile escludere dischi creati manualmente da failback.

**Ora la replica come indicato di seguito**:

1. Fare clic su **passaggio 2: replicare applicazione** > **origine**. Dopo aver attivato la replica per la prima volta che si sarà fare clic su **+ replicare** nell'archivio di abilitare la replica per computer aggiuntivi.
2. In e **l'origine** > **origine** selezionare il server di configurazione.
3. Nel **tipo di computer** selezionare **macchine virtuali** o **Computer fisici**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host vSphere oppure selezionare l'host. Questa impostazione non è rilevante se si sta replicare macchine fisiche.
5. Selezionare il server di processo. Se non è stato creato un server di processo aggiuntivi questo sarà il nome del server di configurazione. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. In **destinazione** selezionare l'abbonamento di archivio e nel **modello di distribuzione Post failover** selezionare il modello (gestione classic o delle risorse) che si desidera utilizzare in Azure dopo il failover.
7. Selezionare l'account di archiviazione Azure da usare per la replica dei dati. Si noti che:

    - È possibile selezionare un account di archiviazione standard o premium. Se si seleziona un account premium è necessario specificare un account di spazio di archiviazione aggiuntivo standard per i registri di replica in corso. Account devono essere presenti nell'area stesso come archivio di servizi di recupero.
    - Se si vuole usare un account di archiviazione diverso rispetto a quelle ottenute è necessario è possibile [crearne uno](#set-up-an-azure-storage-account). Per creare un archivio account utilizzando il modello ARM fare clic su **Crea nuovo**. Se si desidera creare un account di archiviazione utilizzando il modello classico è necessario eseguire tale [nel portale di Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Selezionare la rete Azure e subnet a cui si connetterà macchine virtuali di Azure quando si sta caricare un dopo il failover. La rete deve essere nella stessa regione archivio di servizi di recupero. Selezionare **Configura adesso per i computer selezionato** per applicare le impostazioni di rete per tutti i computer che si seleziona per la protezione. Selezionare **Configura in seguito** per selezionare la rete Azure in base al computer. Se non si dispone di una rete è necessario [crearne uno](#set-up-an-azure-network). Per creare una rete utilizzando il modello ARM fare clic su **Crea nuovo**. Se si desidera creare una rete utilizzando il modello classico è necessario eseguire tale [nel portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. In **macchine virtuali di** > **Selezionare macchine virtuali** fare clic su e selezionare ogni computer che si desidera replicare. È possibile selezionare solo computer per il quale è possibile abilitare la replica. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. Nella **finestra delle proprietà** > **configurare le proprietà**, selezionare l'account che verrà utilizzata automaticamente dal server di processo per installare il servizio mobilità nel computer. Per impostazione predefinita vengono replicati tutti i dischi. Fare clic su **Tutti i dischi** e deselezionare i dischi che non si desidera replicare. Fare clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. In **impostazioni di replica** > **configurare le impostazioni della replica** verificare che sia selezionato il criterio di replica corretto. È possibile modificare le impostazioni dei criteri di replica nelle **Impostazioni** > **criteri di replica** > nome del criterio > **Modifica impostazioni**. Verranno applicate le modifiche applicate a un criterio di replica e nuovo computer.

12. Abilitare **la coerenza macchine Virtuali con più** se si desidera raccogliere macchine in un gruppo di replica e specificare un nome per il gruppo. Fare clic su **OK**. Si noti che:

    - Computer in replica raggruppare replicare e condivisi punti di ripristino di un arresto anomalo coerenti e app coerente quando non riescono sopra.
    - Si consiglia di raggruppare macchine virtuali e server fisici insieme in modo speculare i carichi di lavoro. Attivazione macchine Virtuali con più coerenza può influire sulle prestazioni di carico di lavoro e deve essere utilizzata solo se macchine esegue il carico di lavoro stesso ed è necessario coerenza.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Fare clic su **Attiva replica**. È possibile tenere traccia dello stato del processo di **Attivazione della protezione tramite** **le impostazioni di** > **processi** > **Processi di ripristino del sito**. Viene eseguito il processo di **Protezione finalizzare** il computer è pronto per il failover.

> [AZURE.NOTE] Se il computer preparazione per l'installazione push che verrà installato il componente di servizio mobilità quando è abilitata la protezione. Dopo che il componente è installato nel computer che viene avviato un processo di protezione e non riesce. Dopo l'errore è necessario riavviare manualmente tutti i computer. Dopo il riavvio inizia nuovamente il processo di protezione e si verifica della replica iniziale.

### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà di macchine Virtuali

È consigliabile verificare la proprietà del computer di origine. Tenere presente che il nome di macchine Virtuali di Azure deve essere conforme ai [requisiti di Azure macchina virtuale](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Fare clic su **Impostazioni** > **elementi replicato** > e selezionare il computer. E il **Essentials** Mostra informazioni sulle impostazioni di computer e lo stato.

2. Nella **finestra delle proprietà** per visualizzare informazioni di replica e failover per la macchina virtuale.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. Nel **calcolo e rete** > **calcolare proprietà** è possibile specificare le dimensioni di nome e la destinazione macchine Virtuali di Azure. Modificare il nome per soddisfare i requisiti di Azure, se necessario.
È anche possibile visualizzare e aggiungere le informazioni di rete di destinazione, alla subnet e indirizzo IP assegnato a macchina virtuale Azure. Tenere presente quanto segue:

    - È possibile impostare l'indirizzo IP di destinazione. Se non si fornisce un indirizzo eseguito il computer utilizzerà DHCP. Se si imposta un indirizzo che non è disponibile in caso di failover, il failover non funziona. Se l'indirizzo è disponibile nella rete failover test, è possibile utilizzare lo stesso indirizzo IP di destinazione per failover test.
    - Il numero di schede di rete è dovuto la dimensione specificata per la macchina virtuale di destinazione, come indicato di seguito:

        - Se il numero di schede di rete del computer di origine è minore o uguale al numero di schede consentiti per la dimensione di computer di destinazione, la destinazione avrà lo stesso numero di schede come origine.
        - Se il numero di schede per la macchina virtuale di origine è maggiore del numero consentito per verrà usata le dimensioni di destinazione, quindi la dimensione massima di destinazione.
        - Se, ad esempio un computer di origine con due schede di rete e le dimensioni del computer di destinazione supporta quattro, il computer di destinazione sarà disponibili due schede. Se il computer di origine con due schede, ma le dimensioni di destinazione supportati supporta solo uno computer di destinazione avrà solo una scheda.     
    - Se la macchina virtuale ha più schede di rete che tutti si connetterà alla stessa rete.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. In **dischi** è possibile vedere il sistema operativo e dischi di dati in macchine Virtuali da replicare.


## <a name="step-7-test-the-deployment"></a>Passaggio 7: Verificare la distribuzione

Per verificare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale o un piano di ripristino che contiene uno o più macchine virtuali.


### <a name="prepare-for-failover"></a>Preparare l'ambiente per failover

- Per eseguire test caso di errore è consigliabile creare una nuova rete Azure che è isolati dalla rete produzione Azure (questo è il comportamento predefinito quando si crea una nuova rete in Azure). [Altre informazioni](site-recovery-failover.md#run-a-test-failover) sull'esecuzione di test failover.
- Per ottenere le massime prestazioni quando non si riesce sopra a Azure, installare l'agente di Azure nel computer protetto. Fa avvio più veloce e utile per la risoluzione dei problemi. Installare l'agente di [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) o [Linux](https://github.com/Azure/WALinuxAgent) .
- Per testare completamente la distribuzione è necessario un'infrastruttura per il computer replicato a funzionare come previsto. Se si desidera verificare Active Directory e DNS è possibile creare una macchina virtuale come controller di dominio con il sistema DNS e questo replicare Azure utilizzando il ripristino del sito di Azure. Leggere in altre [Considerazioni sul failover test per Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Assicurarsi che il server di configurazione. In caso contrario failover avrà esito negativo.
- Se è stata escluso dischi dalla replica potrebbe essere necessario creare tali dischi manualmente in Azure dopo il failover in modo che l'applicazione viene eseguita come previsto.
- Se si desidera eseguire un failover non pianificato anziché caso di errore test tenere presente quanto segue:

    - Se possibile sarà necessario arrestare macchine principale prima di eseguire un failover non pianificato. In questo modo che non si dispone di origine e di replica computer che eseguono nello stesso momento. Se si sta replicare macchine virtuali VMware è possibile specificare che il ripristino del sito da semplificare meglio per arrestare il computer di origine. In base allo stato del sito principale questo potrebbe o potrebbe non funzionare. Se si sta replica server fisici che non offre questa opzione, il ripristino del sito.
    - Quando si esegue un failover non pianificato interrompe la replica dei dati dal computer principale in modo che non è possibile trasferire qualsiasi delta dati dopo l'avvio di un failover non pianificato. Anche se si esegue un failover non pianificato su un piano di ripristino verrà eseguito fino al completamento, anche se si verifica un errore.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Prepararsi a cui connettersi macchine virtuali di Azure dopo il failover

Se si desidera connettersi alle macchine virtuali di Azure utilizzando RDP dopo il failover, assicurarsi di eseguire le operazioni seguenti:

**Nel computer locale prima del failover**:

- Per l'accesso tramite internet abilitare RDP, assicurarsi che le regole di porte TCP e UDP vengono aggiunte per il **pubblico**e assicurarsi che sia consentito RDP in **Windows Firewall** -> **consentiti App e funzionalità** per tutti i profili.
- Per l'accesso tramite una connessione da sito abilitare RDP nel computer e assicurarsi che sia consentito RDP in **Windows Firewall** -> **consentiti App e funzionalità** per le reti di **dominio** e **Private** .
- Installare l' [agente di macchine Virtuali di Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) nel computer locale.
- Per [installare manualmente il servizio di mobilità](#install-the-mobility-service-manually) nel computer invece di usare il server di processo da inserire automaticamente il servizio. Ciò avviene perché l'installazione push viene riprodotto solo dopo che il computer è abilitato per la replica.
- Assicurarsi che i criteri SAN del sistema operativo sia impostato su OnlineAll. [Ulteriori informazioni]( https://support.microsoft.com/kb/3031135)
- Disattivare il servizio IPSec prima di eseguire il failover.

**In the Azure macchine Virtuali dopo failover**:

- Aggiungere un endpoint pubblico per il protocollo RDP (porta 3389) e specificare le credenziali per l'accesso.
- Assicurarsi che non si dispone di alcun criterio di dominio che impedisca la connessione a un computer virtuale tramite un indirizzo pubblico.
- Provare a connettersi. Se non è possibile connettersi verificare che la macchina virtuale sia in esecuzione. Per ulteriori suggerimenti sulla risoluzione dei problemi, leggere questo [articolo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Se si desidera accedere a un sistema operativo Linux dopo failover utilizzando un Secure Shell client (ssh) macchine Virtuali di Azure, eseguire le operazioni seguenti:

**Nel computer locale prima del failover**:

- Assicurarsi che il servizio Secure Shell nella macchina virtuale Azure è impostato per l'avvio automatico all'avvio di sistema.
- Verificare che le regole del firewall consentano una connessione SSH.

**In the Azure macchine Virtuali dopo failover**:

- Le regole gruppo di sicurezza di rete su non riuscito su macchine Virtuali e subnet Azure a cui è connesso necessario consentire le connessioni in ingresso alla porta SSH.
- Per consentire le connessioni in ingresso sulla porta SSH (porta TCP 22 per impostazione predefinita), è necessario creare un endpoint di pubblico.
- Se si accede a macchina virtuale tramite una connessione VPN (strada o da un sito VPN) il client può essere utilizzato per direttamente connettere la macchina virtuale su SSH.

**In the Azure Windows/Linux macchine Virtuali dopo failover**:

Se si dispone di un gruppo di sicurezza di rete associati alla macchina virtuale o subnet a cui appartiene il computer, assicurarsi che il gruppo di sicurezza di rete con una regola in uscita per consentire a HTTP/HTTPS. Assicurarsi che il DNS della rete per macchina virtuale è Impossibile ricevere oltre a è configurato correttamente. In caso contrario il failover Impossibile timeout con errore-"Task PreFailoverWorkflow WaitForScriptExecutionTask timeout". Per comprendere questo in modo dettagliato, fare riferimento alla sezione sul ripristino di [monitoraggio e risoluzione dei problemi guida](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Eseguire test caso di errore

1. L'esito negativo su un singolo computer, **le impostazioni di** > **Replicato elementi**, fare clic su macchina virtuale > icona **+ Failover Test** .

    ![Test failover](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. L'esito negativo su un piano di ripristino, in **Impostazioni** > di**Ripristino plan di messaggistica unificata**rapida il piano > **Failover Test**. Per creare un pianificazione di ripristino [seguire queste istruzioni](site-recovery-create-recovery-plans.md).

3. **Test** failover selezionare la rete Azure a cui verrà connesso macchine virtuali di Azure dopo che si verifica il failover.
4. Fare clic su **OK** per avviare il failover. È possibile tenere traccia dello stato facendo clic su Virtual Machine per visualizzare le relative proprietà o il processo di **Test Failover** in nome archivio > **Impostazioni** > **processi** > **processi di ripristino del sito**.
5. Quando il failover raggiunge lo stato di **completamento test** , eseguire le operazioni seguenti:

    1. Visualizzare la macchina virtuale duplicata nel portale di Azure. Verificare che la macchina virtuale viene avviato correttamente.
    2. Se si è impostato in macchine virtuali di accesso alla rete locale è possibile avviare una connessione Desktop remoto alla macchina virtuale.
    3. Fare clic su **test di completamento** per terminare.

        ![Test failover](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Fare clic su **note** per registrare e salvare le osservazioni associate failover test.
    5. Fare clic su Pulisci automaticamente l'ambiente di testing **failover test è stata completata** . A questo punto failover test verrà visualizzato uno stato di **completamento** .
    6.  In questa fase vengono eliminate eventuali elementi o macchine virtuali create automaticamente dal ripristino del sito durante il failover di test. Eventuali elementi aggiuntivi creati per il failover test non vengono eliminati.

    > [AZURE.NOTE] Se continua più di due settimane in caso di errore di test è completata obbligatoriamente.


6. Dopo il failover anche dovrebbe essere possibile vedere replica Azure computer vengono visualizzati nel portale di Azure > **macchine virtuali**. È necessario assicurarsi che la macchina virtuale è la dimensione appropriata, che si è connesso alla rete appropriato e che sia in esecuzione.
7. Se si [preparato per le connessioni dopo failover](#prepare-to-connect-to-azure-vms-after-failover) dovrebbe essere possibile connettere la macchina virtuale Azure.

## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Ecco come è possibile monitorare le impostazioni di configurazione, stato e dell'integrità per la distribuzione il ripristino del sito:

1. Fare clic sul nome dell'archivio per accedere al dashboard di **Nozioni di base** . In questo dashboard è possibile processi il ripristino del sito, lo stato della replica, piani di ripristino, lo stato dei server e gli eventi.  È possibile personalizzare Essentials per mostrare le sezioni e i layout che risultano particolarmente utili per l'utente, tra cui lo stato degli altri archivi di Backup e ripristino del sito.<br>
![Nozioni di base](./media/site-recovery-vmware-to-azure/essentials.png)

2. Nella sezione **integrità** è possibile monitorare sito server, ovvero VMM o configurazione server in cui si verificano problemi e gli eventi generati per il ripristino del sito ultime 24 ore.
3. È possibile gestire e il monitoraggio della replica di **Elementi replicato**, **Ripristino plan di messaggistica unificata**e riquadri di **Processi di ripristino del sito** . È possibile drill-down dei processi nelle **Impostazioni** -> **processi** -> **Processi di ripristino del sito**.


## <a name="deploy-additional-process-servers"></a>Distribuire server processo aggiuntivi

Se si dispone di scalare la distribuzione oltre 200 computer di origine o di un tasso di varianza del giorno totale di più di 2 TB, sarà necessario server processo aggiuntivi per gestire il volume di traffico.

Controllare le [dimensioni consigliate per i server di processo](#size-recommendations-for-the-process-server) e quindi seguire le istruzioni per configurare il server di processo. Dopo la configurazione del server verrà eseguire la migrazione computer di origine per utilizzarlo.

### <a name="install-an-additional-process-server"></a>Installare un server di processo aggiuntivi

1. In **Impostazioni** > **server il ripristino del sito** fare clic sul server di configurazione > **server processo**.

    ![Aggiungere il server di processo](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. In **Tipo di Server** fare clic su **processo server (locale)**.

    ![Aggiungere il server di processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Scaricare il file di configurazione del sito ripristino unificata ed eseguirlo per installare il server di processo e registrare nell'archivio di.
4. Nella **prima di iniziare a** selezionare **Aggiungi processo aggiuntivi server scalabilità distribuzione**.
5. Completare la procedura guidata nello stesso modo è stata quando è [impostato](#step-2-set-up-the-source-environment) il server di configurazione.

    ![Aggiungere il server di processo](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Dettagli della **configurazione Server** specificare l'indirizzo IP del server di configurazione e la passphrase. Per ottenere la passphrase eseguita ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** sul server di configurazione.

    ![Aggiungere il server di processo](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Eseguire la migrazione di computer per usare il nuovo server di processo

1. In **Impostazioni** > **server il ripristino del sito** fare clic sul server di configurazione e quindi espandere **processo server**.

    ![Aggiornare il server di processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Pulsante destro del mouse server processo attualmente in uso e fare clic su **Cambia**.

    ![Aggiornare il server di processo](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. In **Selezionare il server di processo di destinazione**, selezionare il nuovo server di processo che si desidera utilizzare e quindi selezionare le macchine virtuali che gestirà il nuovo server di processo. Fare clic sull'icona delle informazioni per ottenere informazioni sul server. Per informazioni su come rendere caricare decisioni, viene visualizzato lo spazio medio che è necessario per replicare ogni macchina virtuale selezionata nel nuovo server di processo. Fare clic sul segno di spunta per avviare la replica nel nuovo server di processo.

## <a name="vmware-account-permissions"></a>Autorizzazioni per l'account VMware

Il server di processo di individuare automaticamente macchine virtuali su un server vCenter. Per eseguire il rilevamento automatico è necessario [definire un ruolo (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) consentire il ripristino del sito accedere al server VMware. Si noti che se occorre eseguire la migrazione di macchine VMware in Azure e non necessarie per il failback da Azure, è possibile definire un ruolo di sola lettura è sufficiente. Le autorizzazioni di ruolo obbligatori sono riepilogate nella tabella seguente.

**Ruolo** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
Ruolo Azure_Site_Recovery | Individuazione di VMware VM |Assegnare i privilegi per il server centro v:<br/><br/>Archivio dati -> assegna spazio archivio dati Sfoglia, operazioni di basso livello di un file., file di installazione, aggiornamento macchina virtuale file<br/><br/>Rete -> Assegna di rete<br/><br/>Risorse -> macchina virtuale assegnare al pool di risorse, eseguire la migrazione spento macchina virtuale, eseguire la migrazione acceso macchina virtuale<br/><br/>Attività -> Crea attività, delle attività di aggiornamento<br/><br/>Macchina virtuale -> configurazione<br/><br/>Macchina virtuale -> interagire -> Rispondi alla domanda, dispositivo connessione., configurare CD media, Configura unità floppy, spegnere Power nel, installare gli strumenti di VMware<br/><br/>Macchina virtuale -> inventario -> Crea, registro, annullamento della registrazione<br/><br/>Macchina virtuale -> Provisioning -> Consenti macchina virtuale download, caricare file delle macchine virtuali Consenti<br/><br/>Macchina virtuale -> snapshot -> Rimuovi istantanee
ruolo di utente vCenter | Individuazione VMware VM il Failover senza arresto dell'origine macchine Virtuali | Assegnare i privilegi per il server centro v:<br/><br/>Oggetto Data Center –> propagare oggetto figlio, ruolo = sola lettura <br/><br/>L'utente viene assegnata a livello di Data Center e pertanto ha accesso a tutti gli oggetti nel centro dati.  Se si desidera limitare l'accesso, assegnare il ruolo di **non consentire l'accesso** con l'oggetto **propagare bambino** agli oggetti figlio (vSphere host, archivi dati, macchine virtuali e reti).
ruolo di utente vCenter | Failover e failback | Assegnare i privilegi per il server centro v:<br/><br/>Oggetto Data Center-propagazione oggetto figlio, ruolo = Azure_Site_Recovery<br/><br/>L'utente viene assegnata a livello di Data Center e pertanto ha accesso a tutti gli oggetti nel centro dati.  Se si desidera limitare l'accesso, assegnare il ruolo di **non consentire l'accesso** con la **propagazione oggetto figlio** all'oggetto figlio (vSphere host, archivi dati, macchine virtuali e reti).  
## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover.
- [Altre informazioni sul failback](site-recovery-failback-azure-to-vmware.md) per visualizzare l'errore su computer che eseguono in Azure nuovamente per l'ambiente locale.

## <a name="third-party-software-notices-and-information"></a>Note legali sul software di terze parti e informazioni

Non tradurre o Localize

Il software e firmware in esecuzione in Microsoft prodotto o servizio basato su o incorpora materiale da progetti elencati di seguito (collettivamente "codice terze parti").  Microsoft è l'autore non originale del codice di terze parti.  Le informazioni sul copyright originale e licenza, in cui Microsoft ha ricevuto il codice di terze parti, sono impostate via riportata di seguito.

Le informazioni nella sezione si riferisce componenti di terze parti codice dei progetti vengono elencate di seguito. Ad esempio licenze e le informazioni vengono fornite informativo.  Il codice di terze parti è da relicensed per l'utente da Microsoft in condizioni di licenza software Microsoft per il prodotto o servizio Microsoft.  

Le informazioni nella sezione B si riferisce componenti di codice di terze parti che vengono resi disponibili per l'utente da Microsoft sotto le condizioni di licenza originale.

Il file completo potrebbe disponibili nell' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft si riserva tutti i diritti non espressamente concessi, se da implicazioni, corrette o in caso contrario.
