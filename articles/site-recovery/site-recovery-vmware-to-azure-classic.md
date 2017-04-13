<properties
    pageTitle="Replicare macchine virtuali VMware e server fisici su Azure con il ripristino del sito di Azure | Microsoft Azure"
    description="In questo articolo viene descritto come distribuire Azure il ripristino del sito per la gestione di replica, failover e ripristino di Windows/Linux server fisici in Azure e macchine virtuali VMware locale."
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
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Replicare macchine virtuali VMware e server fisici su Azure con il ripristino del sito di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmware-to-azure.md)
- [Portale classica](site-recovery-vmware-to-azure-classic.md)
- [Portale classico (legacy)](site-recovery-vmware-to-azure-classic-legacy.md)


Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md).

## <a name="overview"></a>Panoramica

In questo articolo viene descritto come:

- **Macchine virtuali VMware replicare in Azure**, distribuire il ripristino del sito per coordinare la replica, failover e ripristino di macchine virtuali VMware locale allo spazio di archiviazione Azure.
- **Replicare server fisici in Azure**, ovvero distribuire Azure il ripristino di sito per coordinare la replica, failover e ripristino di locale Windows e Linux server fisici in Azure.

>[AZURE.NOTE] In questo articolo viene descritto come replicare Azure. Se si desidera replicare macchine virtuali VMware o server fisici Windows/Linux in un Data Center secondario, seguire le istruzioni fornite in [questo articolo](site-recovery-vmware-to-vmware.md).

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.

## <a name="enhanced-deployment"></a>Avanzato per la distribuzione

In questo articolo include sono contenute istruzioni per una distribuzione avanzata nel portale di Azure classica. È consigliabile che utilizzare questa versione di tutte le distribuzioni di nuove. Se è già stato distribuito utilizza la versione precedente di legacy è consigliabile eseguire la migrazione alla nuova versione. Leggere [altre](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) informazioni sulla migrazione.

Distribuzione avanzata è un aggiornamento principale. Di seguito è riportato un riepilogo delle modifiche apportate che sono state apportate:

- **Nessuna infrastruttura macchine virtuali di Azure**: replica i dati direttamente a un account di archiviazione Azure. Inoltre la replica e failover non è necessario configurare un'infrastruttura di macchine virtuali (server di configurazione, server di destinazione master) sono necessarie nella distribuzione legacy.  
- **Installazione unificato**: un'unica installazione offre semplicità di configurazione e scalabilità per i componenti locale.
- **Distribuzione protetta**: tutto il traffico crittografato e le comunicazioni di gestione di replica vengono inviate tramite HTTPS 443.
- **Punti di ripristino**: supporto per il ripristino applicazione coerente e un arresto anomalo punti per gli ambienti Windows e Linux e supporta sia singolo macchine Virtuali e multi-SV configurazioni coerenti.
- **Eseguire test di failover**: supporto per il controllo test non dannosi a Azure, senza impatto produzione o sospendere la replica.
- **Failover non pianificato**: il supporto per il controllo non pianificato a Azure con un'opzione avanzata arrestare macchine virtuali automaticamente prima del failover.
- **Failback**: failback integrato che consente di replicare solo le modifiche delta al sito locale.
- **vSphere 6.0**: un supporto limitato per le distribuzioni VMware Vsphere 6.0.


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Come il ripristino del sito consente di proteggere macchine virtuali e server fisici?


- Gli amministratori di VMware possono configurare protezione fuori sede in Azure di carichi di lavoro di business e le applicazioni in macchine virtuali VMware. Server Manager possibile replicare server di Windows e Linux locale fisica in Azure.
- Console di ripristino sito Azure offre una posizione centralizzata per l'installazione semplice e gestione di replica, failover e processi di ripristino.
- Se si replicano macchine virtuali VMware gestiti da un server vCenter, il ripristino del sito di individuare automaticamente tali macchine virtuali. Se computer fanno parte di un host ESXi il ripristino del sito consente di individuare macchine virtuali nell'host.
- Eseguire semplici failover dall'infrastruttura locale Azure e failback (ripristino) comuni ai server VMware VM nel sito locale.
- Configurare i piani di ripristino che raggruppano i carichi di lavoro di applicazione a più livelli in più computer. Può avere esito negativo su tali piani e il ripristino del sito fornisce la coerenza macchine Virtuali con più in modo che i computer che eseguono la stessa carichi di lavoro possono essere recuperati insieme a un punto dati coerenti.


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

Componenti scenario:

- **Un server di gestione locale**: il server di gestione vengono eseguiti i componenti il ripristino del sito:
    - **Server di configurazione**: coordinare la comunicazione e gestire i processi di replica e ripristino di dati.
    - **Server di processo**: funge da gateway replica. Riceve dati dal computer di origine protetta, Ottimizza con la memorizzazione nella cache, compressione e la crittografia e invia dati replica allo spazio di archiviazione Azure. Inoltre, gestisce installazione push del servizio di mobilità ai computer protetto ed esegue il rilevamento automatico delle macchine virtuali VMware.
    - **Server di destinazione master**: gestisce i dati di replica durante failback comuni.
    È inoltre possibile distribuire un server di gestione che funge da un server di processo solo per ridimensionare la distribuzione.
- **Servizio di mobilità**: questo componente viene distribuito in tutti i computer (VMware VM o server fisico) che si desidera replicare in Azure. Acquisisce scrive dati nel computer e li inoltra al server di processo.
- **Azure**: non è necessario creare macchine virtuali Azure per gestire la replica e failover. Il servizio il ripristino del sito gestisce la gestione dei dati e dati replica direttamente allo spazio di archiviazione Azure. Replicato macchine virtuali di Azure sono caricare automaticamente un solo quando si verifica il controllo a Azure. Tuttavia, se si vuole avere esito negativo proveniente da Azure al sito locale sarà necessario configurare una macchina virtuale Azure funga da un server di processo.


L'immagine mostra come questi componenti interagiscono.

![architettura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/fisici a Azure** (creata dall'Henry Robalino)


## <a name="capacity-planning"></a>Pianificazione delle capacità

Quando si intende capacità, cosa è necessario considerare:

- **Ambiente di origine**, ovvero la pianificazione della capacità o i requisiti macchina VMware infrastruttura e la fonte.
- **Il server di gestione**, pianificazione per il server di gestione locale che eseguono componenti il ripristino del sito.
- **Larghezza di banda di rete dall'origine alla destinazione**-strategia di larghezza di banda necessario per la replica tra origine e di Azure

### <a name="source-environment-considerations"></a>Considerazioni relative all'ambiente di origine

- **Massimo giornaliero modificare tariffe**, ovvero un computer protetto è possibile utilizzare solo un server di processo e un server singolo processo può gestire fino a 2 TB di modifica dei dati al giorno. In questo modo 2 TB è che la data giornaliera massima modifica frequenza con cui è supportato per un computer protetto.
- **Velocità massima**, ovvero una macchina replicata a cui può appartenere a un account di archiviazione in Azure. Un account di archiviazione standard può gestire un massimo di 20.000 richieste al secondo ed è consigliabile mantenere il numero di IOPS in un computer di origine a 20.000. Per esempio se si dispone di un computer di origine con dei ogni 5 dischi genera 120 IOPS (8 KB dimensione) nel sito di origine verranno essere all'interno di Azure al limite di IOPS di 500. Il numero di account di archiviazione necessario = origine totale IOPs/20000.


### <a name="management-server-considerations"></a>Considerazioni sui server di gestione

Il server di gestione esegue componenti il ripristino del sito che gestiscono l'ottimizzazione dei dati, replica e gestione. Sarà in grado di gestire la capacità di frequenza modifica giornaliera attraverso tutte carichi di lavoro in esecuzione nel computer protetto e con larghezza di banda sufficiente replicare continuamente i dati allo spazio di archiviazione Azure. In particolare:

- Il server di processo riceve replica dati dal computer protetto e ottimizza con la memorizzazione nella cache, compressione e crittografia prima di inviare a Azure. Il server di gestione deve disporre di risorse sufficienti per eseguire queste attività.
- Il server di processo utilizza cache basata su disco. È consigliabile un disco separato della cache di 600 GB o più per gestire le modifiche ai dati archiviate in caso di bottiglia di rete o un'interruzione del. Durante la distribuzione è possibile configurare la cache in un'unità che contiene almeno 5 GB di spazio di archiviazione disponibile ma 600 GB viene descritto come minimo.
- È buona norma è consigliabile che il server di gestione trovarsi nella stessa rete e segmento LAN come computer di cui si desidera proteggere. Può trovarsi in una rete diversa ma computer che si desidera proteggere dovrebbe essere visibilità rete L3 ad esso.

Nella tabella seguente vengono riepilogati i consigli dimensioni per il server di gestione.

**Server di gestione delle CPU** | **Memoria** | **Dimensione della cache disco** | **Modifica dei dati** | **Computer protetto**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 core @ 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | Distribuire un server di gestione con queste impostazioni replicare macchine meno di 100.
12 vCPUs (2 sockets * 6 core @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Distribuire un server di gestione con queste impostazioni per la replica tra 100-150 computer.
16 vCPUs (2 sockets * 8 core @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Distribuire un server di gestione con queste impostazioni per la replica tra 150-200 computer.
Distribuire un altro server di processo | | | > 2 TB | Distribuire server processo aggiuntivi se si sta replicare più di 200 computer o se i dati giornalieri cambiano tasso supera i 2 TB.

Dove:

- Ogni computer di origine è configurato con 3 dischi di 100 GB.
- È stato usato benchmark lo spazio di archiviazione di 8 unità sa di 10 K RPM con RAID 10 per le misurazioni disco cache.

### <a name="network-bandwidth-from-source-to-target"></a>Larghezza di banda di rete dall'origine di destinazione
Verificare che il calcolo della larghezza di banda necessari per la replica iniziale e la replica delta tramite lo [strumento di pianificazione delle capacità](site-recovery-capacity-planner.md)

#### <a name="throttling-bandwidth-used-for-replication"></a>La limitazione della larghezza di banda utilizzata per la replica

VMware replicate in Azure del traffico attraverso un server di processo specifico. È possibile limitare la larghezza di banda è disponibile per la replica il ripristino del sito come indicato di seguito:

1. Aprire Microsoft Azure Backup MMC snap-in sul server di gestione principale o su un server di gestione con ulteriori viene completato il provisioning processo server. Per impostazione predefinita, una scelta rapida per Microsoft Azure Backup viene creato sul desktop o possano trovarlo nella: c:\Programmi\Microsoft c:\Programmi\Microsoft Azure ripristino servizi Agent\bin\wabadmin.
2. In snap-in fare clic su **Modifica proprietà**.

    ![Limitare larghezza di banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Nella scheda **Throttling** specificare la larghezza di banda che può essere utilizzato per la replica il ripristino del sito e la programmazione applicabile.

    ![Limitare larghezza di banda](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Facoltativamente è possibile impostare la limitazione per l'utilizzo di PowerShell. Ecco un esempio:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Ottimizzare l'utilizzo della larghezza di banda
Per aumentare la larghezza di banda utilizzata per la replica per il ripristino del sito di Azure è necessario modificare una chiave del Registro di sistema.

La seguente chiave controlla il numero di thread per la replica disco utilizzati durante la replica

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 In una rete "overprovisioned", deve essere modificate da valori predefiniti è questa chiave del Registro di sistema. È supportato un massimo di 32.  


[Ulteriori](site-recovery-capacity-planner.md) informazioni sulla pianificazione delle capacità dettagliate.

### <a name="additional-process-servers"></a>Server di processo aggiuntivi

Se è necessario proteggere più di 200 computer o modifiche giornaliere è superiore a tale 2 TB è possibile aggiungere altri server per gestire il carico. Per ridimensionare è possibile:

- Aumentare il numero di server di gestione. Ad esempio è possibile proteggere fino a 400 macchine con due server di gestione.
- Aggiunta di server aggiuntivi process e utilizzarli per gestire il traffico anziché (o in aggiunta a) il server di gestione.

La tabella seguente descrive uno scenario in cui:

- Impostare il server di gestione originale da utilizzare come server di configurazione.
- Impostare un server di processo aggiuntivi.
- Configurare protette macchine virtuali per utilizzare il server di processo aggiuntivi.
- Ogni computer protetto origine è configurato con tre dischi di 100 GB.

**Server di gestione originale**<br/><br/>(server configurazione) | **Server di processo aggiuntivi**| **Dimensione della cache disco** | **Modifica dei dati** | **Computer protetto**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 core @ 2,5 GHz), 16 GB di memoria | 4 vCPUs (2 sockets * 2 core @ 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | È possibile replicare macchine minore o uguale a 85.
8 vCPUs (2 sockets * 4 core @ 2,5 GHz), 16 GB di memoria | 8 vCPUs (2 sockets * 4 core @ 2,5 GHz), 12 GB di memoria | 600 GB | 250 GB a 1 TB | È possibile replicare tra 85 150 computer.
12 vCPUs (2 sockets * 6 core @ 2,5 GHz), 18 GB di memoria | 12 vCPUs (2 sockets * 6 core @ 2,5 GHz) 24 GB di memoria | 1 TB | 1 TB a 2 TB | È possibile replicare tra 225 150 computer.


Il modo in cui si modifica la scala dei server verrà dipendono la preferenza per una scala di backup o ridimensionare il modello.  È scalare distribuendo alcuni gestione high-end e i server o scalabilità distribuendo più server con meno risorse. Ad esempio: se è necessario proteggere 220 computer è possibile eseguire una delle operazioni seguenti:

- Configurare il server di gestione originale con 12vCPU e 18 GB di memoria, un server di processo aggiuntivi con 12vCPU, 24 GB di memoria e configurare i computer protetto da utilizzare solo il server di processo aggiuntivi.
- In alternativa potrebbe configurare due server di gestione (8vCPU x 2, 16 GB di RAM) e due server di processo aggiuntivi (1x 8vCPU) e 4vCPU x1 per gestire i computer (220) 135 + 85 e configurare i computer protetto da utilizzare solo il server di processo aggiuntivi.


[Seguire queste istruzioni](#deploy-additional-process-servers) per configurare un server di processo aggiuntivi.

## <a name="before-you-start-deployment"></a>Prima di iniziare la distribuzione

Le tabelle riepilogano i prerequisiti per la distribuzione di questo scenario.


### <a name="azure-prerequisites"></a>Prerequisiti Azure

**Prerequisito** | **Dettagli**
--- | ---
**Account Azure**| È necessario un account di [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**Spazio di archiviazione Azure** | È necessario un account di archiviazione Azure per archiviare i dati replicati. Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure disattivate backup in caso di failover. <br/><br/>È necessario un [account di archiviazione ridondanti geografico standard](../storage/storage-redundancy.md#geo-redundant-storage). L'account deve essere nella stessa regione servizio il ripristino del sito ed essere associato all'abbonamento stesso. Si noti che la replica per gli account di archiviazione premium non è attualmente supportati e non deve essere utilizzato.<br/><br/>Non è supportato lo spostamento degli account di archiviazione creati per il [nuovo portale Azure](../storage/storage-create-storage-account.md) attraverso gruppi di risorse. [Ulteriori informazioni sui](../storage/storage-introduction.md) Spazio di archiviazione Azure.<br/><br/>
**Rete Azure** | È necessario un virtuali Azure che macchine virtuali di Azure si connetterà in caso di failover. La rete virtuale Azure deve essere nella stessa regione come archivio il ripristino del sito.<br/><br/>L'esito negativo dopo il controllo a Azure è sarà necessaria una connessione VPN connessione (Azure ExpressRoute) configurare o dalla rete Azure al sito locale.


### <a name="on-premises-prerequisites"></a>Prerequisiti locale

**Prerequisito** | **Dettagli**
--- | ---
**Server di gestione** | È necessario un server di Windows 2012 R2 locale in esecuzione in una macchina virtuale o server fisico. Tutti i componenti del ripristino del sito locale installati nel server di gestione<br/><br/> Si consiglia di distribuire il server come una VM VMware disponibilità. Failback al sito locale da Azure è sempre possibile in macchine virtuali VMware indipendentemente dal fatto che non è riuscita in macchine virtuali o server fisici. Se il server di gestione non configurato come un VM VMware è necessario configurare un server di destinazione master separato come una VM VMware per ricevere il traffico failback.<br/><br/>Il server non deve essere un Controller di dominio.<br/><br/>Il server deve disporre di un indirizzo IP statico.<br/><br/>Il nome host del server deve essere 15 caratteri o meno.<br/><br/>Impostazioni locali del sistema operativo dovrebbero essere solo in lingua inglese.<br/><br/>Il server di gestione richiede l'accesso a internet.<br/><br/>È necessario accedere in uscita dal server nel modo seguente: accesso temporaneo su HTTP 80 durante l'installazione dei componenti il ripristino del sito (per scaricare MySQL); Accesso in uscita in corso in HTTPS 443 per la gestione di replica; Accesso in uscita in corso nel 9443 HTTPS per il traffico di replica (questa porta può essere modificata)<br/><br/> Verificare che siano accessibili dal server di gestione questi URL: <br/>- \*. hypervrecoverymanager.windowsazure.com<br/>- \*. accesscontrol.windows.net<br/>- \*. backup.windowsazure.com<br/>- \*. blob.core.windows.net<br/>- \*. store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Se si dispone di regole firewall basato su indirizzo IP sul server, assicurarsi che le regole di consentano la comunicazione con Azure. È necessario consentire di [Intervalli di indirizzi IP del Data Center del Azure](https://www.microsoft.com/download/details.aspx?id=41653) e la porta HTTPS (443). È necessario anche lista bianca intervalli di indirizzi IP per l'area geografica Azure dell'abbonamento e per Usa ovest. L' URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") è per il download MySQL.
**VMware vCenter/ESXi host**: | È necessario uno o più vMware vSphere ESX/ESXi hypervisor gestione macchine virtuali VMware, esecuzione ESX/ESXi versione 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti.<br/><br/> È consigliabile che distribuire un server di vCenter VMware per gestire l'host ESXi. Deve essere eseguita vCenter versione 6.0 o 5.5 con gli aggiornamenti più recenti.<br/><br/>Tenere presente che il ripristino del sito non supporta vCenter nuove caratteristiche vSphere 6.0 incrociato come vCenter vMotion, volumi virtuali e lo spazio di archiviazione DRS. Supporto per il ripristino del sito è limitato alle funzionalità che sono anche disponibili nella versione 5.5.
**Computer protetto**: | **AZURE**<br/><br/>Computer che si desidera proteggere devono essere conformi con [Azure prerequisiti](site-recovery-best-practices.md#azure-virtual-machine-requirements) per la creazione di macchine virtuali di Azure.<br><br/>Se si desidera connettersi alle macchine virtuali di Azure dopo il failover è necessario attivare le connessioni Desktop remoto nel firewall locale.<br/><br/>Disco singoli computer protetto non deve essere superiore a 1023 GB. Una macchina virtuale possono essere presenti fino a 64 dischi (in questo modo fino a 64 TB). Se si dispone di dischi superiori a 1 TB valutare la possibilità di utilizzo della replica di database, ad esempio SQL Server sempre attive o protezione di dati Oracle.<br/><br/>Minimo 2 GB di spazio disponibile su unità di installazione per l'installazione di componenti.<br/><br/>Condiviso guest disco cluster non sono supportati. Se si dispone di una distribuzione cluster si consiglia di utilizzare replica di database, ad esempio SQL Server sempre attive o protezione di dati Oracle.<br/><br/>Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) avvio non è supportato.<br/><br/>Nomi dei computer deve contenere tra 1 e 63 caratteri (lettere, numeri e trattini). Il nome deve iniziare con una lettera o un numero e terminano con una lettera o un numero. Dopo la protezione di un computer è possibile modificare il nome di Azure.<br/><br/>**Macchine virtuali VMware**<br/><br>È necessario installare VMware vSphere 6.0 PowerCLI. sul server di gestione (configurazione server).<br/><br/>Macchine virtuali VMware che si desidera proteggere necessario disporre di strumenti di VMware installato e in esecuzione.<br/><br/>Se l'origine macchine Virtuali include il raggruppamento NIC viene convertita in una singola scheda dopo il controllo a Azure.<br/><br/>Se macchine virtuali protette dispone di un disco iSCSI il ripristino del sito converte il disco di iSCSI macchine Virtuali protetto in un file disco rigido virtuale a quando la macchina virtuale ha esito negativo su in Azure. Destinazione iSCSI può essere raggiunta in macchine Virtuali di Azure verrà connettersi alla destinazione iSCSI e vedere essenzialmente a due dischi: il disco disco rigido virtuale nella macchina virtuale Azure e disco iSCSI di origine. In questo caso è necessario disconnettere la destinazione iSCSI che viene visualizzato l'errore su macchine Virtuali di Azure.<br/><br/>[Altre informazioni](#vmware-permissions-for-vcenter-access) sulle autorizzazioni utente VMware necessarie per il ripristino del sito.<br/><br/> **WINDOWS SERVER computer (VMware VM o server fisico)**<br/><br/>Il server deve essere in esecuzione un sistema operativo a 64 bit supportato: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con in almeno SP1.<br/><br/>Il sistema operativo deve essere installato sull'unità C:\ e il disco del sistema operativo deve essere un disco di base di Windows (OS non devono essere installato su un disco dinamico di Windows).<br/><br/>Per i computer Windows Server 2008 R2 è necessario disporre di .NET Framework 3.5.1 installato.<br/><br/>È necessario specificare un account di amministratore (deve essere un amministratore locale del computer Windows) per l'installazione push il servizio di mobilità su Windows Server. Se l'account specificato è un account di dominio non è necessario disattivare il controllo di accesso remoto degli utenti del computer locale. [Altre informazioni](#install-the-mobility-service-with-push-installation).<br/><br/>Il ripristino del sito supporta macchine virtuali con disco RDM.  Durante il failback il ripristino del sito riutilizza disco RDM se è disponibile su disco macchine Virtuali e RDM origine originale. Se non sono disponibili, durante il failback il ripristino del sito verrà creato un nuovo file VMDK per ogni disco.<br/><br/>**COMPUTER LINUX**<br/><br/>È necessario un sistema operativo a 64 bit supportate: Red Hat Enterprise Linux 6,7; Centos 6.5, 6.6,6.7; Oracle Enterprise Linux 6.4, 6.5 esegue kernel compatibile Red Hat o estremamente affidabile e Enterprise Kernel versione 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>file /etc/hosts nel computer protetto devono contenere voci da associano il nome host locale a indirizzi IP associati a tutte le schede di rete. <br/><br/>Se si desidera connettersi a una macchina virtuale Azure che eseguono Linux dopo failover utilizzando un Secure Shell client (ssh), assicurarsi che il servizio di Secure Shell sul computer protetto sia impostato per avviare automaticamente all'avvio di sistema e che le regole firewall consentano un ssh la connessione.<br/><br/>Protezione può essere attivata solo per i computer Linux con lo spazio di archiviazione seguente: File di sistema (EXT3, ETX4, ReiserFS XFS); Percorsi multipli software dispositivo mapping (percorsi multipli)); Gestione di volume: (LVM2). Server fisici con lo spazio di archiviazione di HP CCISS controller non sono supportate. Il file System ReiserFS è supportata solo su SUSE Linux Enterprise Server 11 SP3.<br/><br/>Il ripristino del sito supporta macchine virtuali con disco RDM.  Durante l'opzione per Linux, il ripristino del sito non riutilizzare il disco RDM. Crea invece un nuovo file VMDK per ogni disco RDM corrispondente.

Solo per Linux VM - assicurarsi che sia impostata l'impostazione disk.enableUUID=true nei parametri di configurazione di macchine Virtuali di VMware. Se la riga corrente non è presente, aggiungerla. È necessario per fornire un UUID coerente per il file VMDK in modo che installa correttamente. Si noti inoltre che senza questa impostazione, failback causerà un download completo anche se la macchina virtuale è disponibile in-Importo Amm.to Aggiunta di questa impostazione assicurarsi che solo le modifiche delta vengono trasferite durante failback.

## <a name="step-1-create-a-vault"></a>Passaggio 1: Creare un archivio

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).
2. Espandere **I servizi di dati** > **Servizi di recupero** e fare clic su **Archivio di ripristino del sito**.
3. Fare clic su **Crea nuova** > **Creazione rapida**.
4. In **nome**immettere un nome descrittivo per identificare l'archivio.
5. Nell' **area geografica**, selezionare la località geografica per l'archivio. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Fare clic su **Crea archivio**.
    ![Nuovo archivio](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Verificare se sulla barra di stato per verificare che l'archivio è stato creato correttamente. L'archivio verrà elencato come **attivo** nella pagina principale dei **Servizi di recupero** .

## <a name="step-2-set-up-an-azure-network"></a>Passaggio 2: Configurare una rete Azure

Configurare una rete Azure in modo che verranno connessi a una rete macchine virtuali di Azure dopo il failover e poter failback al sito locale come previsto.

1. Nel portale di Azure > **Crea rete virtuale** specificare il nome di rete. Nome di intervallo e subnet di indirizzi IP.
2. È necessario aggiungere la rete VPN/ExpressRoute se si vuole semplicemente failback. VPN/ExpressRoute possono essere aggiunti alla rete anche dopo aver failover.

[Per ulteriori](../virtual-network/virtual-networks-overview.md) informazioni sulle reti Azure.

> [AZURE.NOTE] [Migrazione delle reti](../resource-group-move-resources.md) attraverso gruppi di risorse all'interno della stessa sottoscrizione o sottoscrizioni non è supportata per le reti utilizzate per la distribuzione il ripristino del sito.

## <a name="step-3-install-the-vmware-components"></a>Passaggio 3: Installare componenti VMware

Se si desidera replicare VMware virtuale macchine installare i seguenti componenti di VMware sul server di gestione:

1. [Scaricare](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e installare VMware vSphere 6.0 PowerCLI.
2. Riavviare il server.


## <a name="step-4-download-a-vault-registration-key"></a>Passaggio 4: Scaricare una chiave di registrazione archivio

1. La gestione del server aprire la console di ripristino del sito in Azure. Nella pagina **Servizi di recupero** fare clic su archivio per aprire la pagina avvio rapido. Guida introduttiva può essere aperti anche in qualsiasi momento tramite l'icona.

    ![Icona di avvio veloce](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Nella pagina **Guida introduttiva** fare clic su **Risorse di destinazione preparare** > **scaricare una chiave di registrazione**. Il file di registrazione viene generato automaticamente. È valida per 5 giorni dopo la generazione.


## <a name="step-5-install-the-management-server"></a>Passaggio 5: Installare il server di gestione
> [AZURE.TIP] Verificare che siano accessibili dal server di gestione questi URL:
>
- *. hypervrecoverymanager.windowsazure.com
- *. accesscontrol.windows.net
- *. backup.windowsazure.com
- *. blob.core.windows.net
- *. store.core.windows.net
- https://dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Nella pagina **Guida introduttiva** scaricare il file di installazione unificata nel server.

2. Eseguire il file di installazione per avviare l'installazione della procedura guidata configurazione del sito ripristino unificata.

3.  Nella **prima di iniziare** selezionare **Installa il server di configurazione processo**.

    ![Prima di iniziare](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. In **Licenza Software di terze parti** fare clic sui **accetto** per scaricare e installare MySQL. 

    ![Terzo = software di terze parti](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. In **registrazione** individuare e selezionare la chiave di registrazione che è stato scaricato dall'archivio.

    ![Registrazione](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. Nelle **Impostazioni di Internet** specificare come si connetterà il ripristino del sito di Azure Provider eseguito sul server di configurazione tramite internet.

    - Se si desidera connettersi con i proxy che è attualmente configurato nel computer selezionare **Connetti con le impostazioni del proxy esistente**.
    - Se si desidera che il Provider di connettersi direttamente selezionare **Connetti direttamente senza un proxy**.
    - Se il proxy esistente richiede l'autenticazione o che si desidera utilizzare un proxy personalizzato per la connessione al Provider, selezionare **Connetti con le impostazioni del proxy personalizzato**.
        - Se si utilizza un proxy personalizzato è necessario specificare l'indirizzo, porta e le credenziali
        - Se si usa un proxy deve è già stato consentito URL seguenti:
            - *. hypervrecoverymanager.windowsazure.com;    
            - *. accesscontrol.windows.net; 
            - *. backup.windowsazure.com; 
            - *. blob.core.windows.net; 
            - *. store.core.windows.net
            

    ![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. In **Controllo prerequisiti** installazione viene eseguito un controllo per assicurarsi che è possibile eseguire l'installazione. 

    
    ![Prerequisiti](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Se viene visualizzato un avviso sulle **selezionare Sincronizza ora generale** verificare che l'ora dell'orologio di sistema (impostazioni di**Data e ora** ) è diverso da quello di fuso orario.

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. Configurazione di **MySQL** creare le credenziali per l'accesso all'istanza di server MySQL che verrà installato.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. Nella pagina **Dettagli sull'ambiente** selezione se si intende replicare macchine virtuali VMware. Se si è il programma di installazione verifica che sia installato 6.0 PowerCLI.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. Nella pagina selezione **Percorso di installazione** nel punto in cui si desidera installare i file binari e archiviare la cache. È possibile selezionare un'unità con almeno 5 GB di spazio di archiviazione disponibile, ma è consigliabile un'unità di cache con almeno 600 GB di spazio libero.

    ![Percorso di installazione](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. Selezione di **Rete** specificare comunicare ascoltatore (scheda di rete e la porta SSL) in cui il server di configurazione verrà inviare e ricevere replica dati. È possibile modificare l'impostazione predefinita porta (9443). Oltre a questa porta porta 443 verrà utilizzata da un server web che coordina operazioni di replica. 443 non devono essere utilizzato per la ricezione di traffico.


    ![Selezione di rete](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  In **Riepilogo** esaminare le informazioni e fare clic su **Installa**. Al termine dell'installazione viene generata una frase. Sarà necessario quando si abilita la replica quindi copiarlo e mantenerlo in un percorso sicuro.

    ![Riepilogo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  Esaminare le informazioni di **Riepilogo** .

    ![Riepilogo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Proxy dell'agente di Microsoft Azure ripristino del servizio deve essere il programma di installazione.
>Al termine dell'installazione avviare un'applicazione denominata "Microsoft Azure ripristino servizi Shell" dal menu Start di Windows. Nella finestra di comando visualizzata eseguire il seguente insieme di comandi per configurare le impostazioni del server proxy.
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>Eseguire l'installazione dalla riga di comando

È anche possibile eseguire la procedura guidata unificata dalla riga di comando, come indicato di seguito:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Dove:

- / ServerMode: obbligatorio. Specifica se l'installazione è necessario installare il server di configurazione e processo o solo il server di processo (usato per installare processo aggiuntivi server). Valori di input: CS, Mr
- InstallDrive: obbligatorio. Specifica la cartella in cui sono installati.
- / MySQLCredFilePath. Obbligatorio. Specifica il percorso di un file in cui le credenziali del server MySQL brano. È possibile ottenere il modello per creare il file.
- / VaultCredFilePath. Obbligatorio. Percorso del file archivio credenziali
- / EnvType. Obbligatorio. Tipo di installazione. Valori: VMware, NonVMware
- / PSIP e /CSIP. Obbligatorio. Indirizzo IP del server di processo e configurazione del server.
- / PassphraseFilePath. Obbligatorio. Percorso del file passphrase.
- / ByPassProxy. Facoltativo. Specifica che il server di gestione si connette a Azure senza un proxy.
- / ProxySettingsFilePath. Facoltativo. Specifica le impostazioni per un proxy personalizzato (proxy predefinito nel server che richiede l'autenticazione) o proxy personalizzato




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Passaggio 6: Impostare le credenziali per il server vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

Il server di processo di individuare automaticamente macchine virtuali VMware gestiti da un server vCenter. Per il rilevamento automatico il ripristino del sito è necessario un account e le credenziali che possono accedere al server vCenter. Non è rilevante se si sta replicare solo server fisici.

Eseguire questa operazione come indicato di seguito:

1. Nel vCenter server consente di creare un ruolo (**Azure_Site_Recovery**) livello vCenter con le [autorizzazioni necessarie](#vmware-permissions-for-vcenter-access).
2. Assegnare il ruolo di **Azure_Site_Recovery** a un utente vCenter.

    >[AZURE.NOTE] Un account utente vCenter che contiene il ruolo di sola lettura è possibile eseguire failover senza arrestare macchine origine protetta. Se si desidera arrestare tali macchine è necessario il ruolo Azure_Site_Recovery. Si noti che se si sta solo la migrazione macchine virtuali da VMware in Azure e non sia necessario failback il ruolo di sola lettura sarà sufficiente.

3. Per aggiungere l'account aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella \home\svsystems\bin [percorso di installazione].
2. Nella scheda **Gestisci account** , fare clic su **Aggiungi Account**.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. In **Dettagli conto** aggiungere le credenziali che possono essere utilizzate per accedere al server vCenter. Si noti che possono essere più di 15 minuti per il nome dell'account all'interno del portale. Per aggiornare immediatamente, fare clic su Aggiorna nella scheda **Server di configurazione** .

    ![Dettagli](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Passaggio 7: Aggiungere vCenter server ed ESXi host

Se si sta replicare macchine virtuali VMware è necessario aggiungere un server vCenter (o host ESXi).

1. Nei **server** > **Server di configurazione** della scheda, selezionare il server di configurazione > **Aggiungi vCenter server**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Aggiungere il server vCenter o ESXi host dettagli, il nome dell'account specificato per l'accesso al server vCenter nel passaggio precedente e il server di processo che verrà utilizzato per individuare le macchine virtuali VMware che vengono gestite dal server vCenter. Si noti che i server vCenter ESXi host devono trovarsi nella stessa rete del server in cui è installato il server di processo.

    >[AZURE.NOTE] Se si sta aggiungendo il server di vCenter o ESXi host con un account che non dispone di privilegi di amministratore sul server vCenter o host, quindi verificare che la vCenter o per gli account ESXi sono questi privilegi abilitati: Data Center, archivio dati, cartelle, Jost, rete, risorsa, virtuale computer, vSphere distribuito cambia. Il server di vCenter deve inoltre i privilegi di visualizzazioni di spazio di archiviazione.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Al termine individuazione server vCenter verranno elencati nella scheda **Server di configurazione** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>Passaggio 8: Creare un gruppo di protezione

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Gruppi di protezione sono raggruppamenti logici di macchine virtuali o server fisici che si desidera proteggere utilizzando le stesse impostazioni di protezione. Si applicano impostazioni di protezione a un gruppo di protezione e vengano applicate a tutti i computer di macchine virtuali/fisici aggiunti al gruppo.

1. Aprire **Elementi protetti** > **Protezione gruppo** e fare clic per aggiungere un gruppo di protezione.

    ![Crea gruppo protezione](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Nella pagina **Specificare le impostazioni di protezione gruppo** specificare un nome per il gruppo e **da** selezionare il server di configurazione in cui si desidera creare il gruppo. **Destinazione** è Azure.

    ![Impostazioni dei gruppi di protezione](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Nella pagina **Specificare le impostazioni di replica** configurare le impostazioni di replica che verranno utilizzate per tutti i computer nel gruppo.

    ![Replica dei gruppi di protezione](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **La coerenza macchine Virtuali di più**: se si attivare questa opzione crea punti di ripristino coerente applicazione condivisa nei computer del gruppo di protezione. Questa impostazione è più rilevante quando tutti i computer del gruppo di protezione sono in esecuzione il carico di lavoro stesso. Tutti i computer verranno ripristinati allo stesso punto dati. Questa funzione è disponibile se si sta replicare macchine virtuali VMware o server fisici Windows/Linux.
    - **Soglia rilasciato**: imposta la rilasciato. Verranno generati avvisi quando la replica di protezione dati continui supera il valore di soglia rilasciato configurato.
    - **Punto di ripristino conservazione**: specifica la finestra di conservazione. Computer protetto possono essere recuperate in un punto qualsiasi all'interno di questa finestra.
    - **Applicazione coerente frequenza snapshot**: consente di specificare la frequenza verranno creati i punti di ripristino contenente snapshot coerenti con l'applicazione.

Quando si fa clic sul segno di spunta verrà creato un gruppo di protezione con il nome specificato. In Addition un secondo gruppo di protezione viene creato con il nome < protezione-gruppo-nome-Failback). In questo gruppo di protezione viene utilizzato se non si riesce al sito locale dopo il controllo a Azure. È possibile monitorare i gruppi di protezione che vengono creati nella pagina **Elementi protetti** .

## <a name="step-9-install-the-mobility-service"></a>Passaggio 9: Installare il servizio di mobilità

Il primo passaggio per consentire la protezione per macchine virtuali e server fisici è per installare il servizio di mobilità. È possibile eseguire in due modi:

- Push e installare automaticamente il servizio in ogni computer dal server di processo. Si noti che quando si aggiungono computer a un gruppo di protezione e che sia già incluso in esecuzione una versione appropriata dell'installazione push servizio mobilità non verificarsi.
- Installare automaticamente il servizio usando il metodo push dell'organizzazione, ad esempio Windows Server Update Services o System Center Configuration Manager. Assicurarsi che è stato configurato il server di gestione prima di eseguire la verifica.
- Installare manualmente in ogni computer che si desidera proteggere. zza SA configurato il server di gestione prima di eseguire la verifica.


### <a name="install-the-mobility-service-with-push-installation"></a>Installare il servizio di mobilità con l'installazione push

Quando si aggiungono computer a un gruppo di protezione il servizio di mobilità viene inserito automaticamente e installato in ogni computer per il server di processo.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparare l'ambiente per push automatica nel computer Windows

Ecco come preparare il computer Windows in modo che il servizio di mobilità può essere installato automaticamente dal server di processo.

1.  Creare un account che può essere usato dal server di processo di accedere al computer. L'account deve disporre dei privilegi di amministratore (locale o dominio). Si noti che le credenziali vengono utilizzate solo per l'installazione push del servizio di mobilità.

    >[AZURE.NOTE] Se non si usa un account di dominio che è necessario disattivare il controllo di accesso remoto degli utenti del computer locale. A tale scopo, aggiungere la voce DWORD LocalAccountTokenFilterPolicy con il valore 1 nel registro in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System in. Per aggiungere il Registro di sistema voce da CLI comando Apri o tramite PowerShell immettere **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  In Windows Firewall del computer si desidera proteggere, selezionare **Consenti un'app o con Firewall** e attivare **condivisione File e stampanti** e **Strumentazione gestione Windows**. Per i computer che appartengono a un dominio è possibile configurare i criteri del firewall con un oggetto Criteri di gruppo.

    ![Impostazioni del firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Aggiungere l'account che è stato creato:

    - Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella \home\svsystems\bin [percorso di installazione].
    - Nella scheda **Gestisci account** , fare clic su **Aggiungi Account**.
    - Aggiungere l'account che è stato creato. Dopo avere aggiunto l'account è necessario fornire le credenziali quando si aggiunge un computer a un gruppo di protezione.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparare l'ambiente per push automatica nel server Linux

1.  Assicurarsi che il computer Linux che si desidera proteggere è supportato come descritto in [locale prerequisiti](#on-premises-prerequisites). Assicurarsi che è presente la connettività di rete tra il computer in cui che si desidera proteggere e il server di gestione che esegue il server di processo.

2.  Creare un account che può essere usato dal server di processo di accedere al computer. L'account deve essere un utente radice nel server Linux di origine. Si noti che le credenziali vengono utilizzate solo per l'installazione push del servizio di mobilità.

    - Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella \home\svsystems\bin [percorso di installazione].
    - Nella scheda **Gestisci account** , fare clic su **Aggiungi Account**.
    - Aggiungere l'account che è stato creato. Dopo avere aggiunto l'account è necessario fornire le credenziali quando si aggiunge un computer a un gruppo di protezione.

3.  Verificare che il file /etc/hosts nel server Linux di origine contiene le voci che mappano hostname locale agli indirizzi IP associati a tutte le schede di rete.
4.  Installare l'ultima openssh, openssh server, pacchetti openssl nel computer che si desidera proteggere.
5.  Assicurarsi che SSH sia abilitato e in esecuzione su porta 22.
6.  Autenticazione SFTP sottosistema e la password nel file sshd_config come indicato di seguito:

    - Eseguire l'accesso come radice.
    - Nel file di /etc/ssh/sshd_config, individuare la riga che inizia con PasswordAuthentication.
    - Rimuovere il commento e modificare il valore da **alcuna** su **Sì**.
    - Individuare la riga che inizia con **sottosistema** e rimuovere il commento.

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Installare manualmente il servizio di mobilità

I programmi di installazione sono disponibili in c:\Programmi\Microsoft file (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Sistema operativo di origine | File di installazione del servizio di mobilità
--- | ---
Windows Server (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (solo versione a 64 bit) | Microsoft ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Installare manualmente in Windows server


1. Scaricare ed eseguire il programma di installazione pertinenti.
2. Nella **prima di iniziare **selezionare **mobilità servizio**.

    ![Servizio di mobilità](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Dettagli della **configurazione Server** specificare l'indirizzo IP del server di gestione e la passphrase generati durante l'installazione di componenti del server di gestione. È possibile recuperare la passphrase eseguendo: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** sul server di gestione.

    ![Servizio di mobilità](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Lasciare il percorso predefinito nel **Percorso di installazione** e fare clic su **Avanti** per iniziare l'installazione.
5. Monitorare l'installazione in **Corso l'installazione** e, se richiesto, riavviare il computer.

È inoltre possibile installare dalla riga di comando:

UnifiedAgent.exe [/ ruolo < agente/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

Dove:

- / Ruolo: obbligatorio. Specifica se il servizio mobilità deve essere installato.
- / InstallLocation: obbligatorio. Specifica la posizione installare il servizio.
- / PassphraseFilePath: obbligatorio. Specifica la passphrase server di configurazione.
- / LogFilePath: obbligatorio. Specifica posizione file di configurazione registro

#### <a name="uninstall-mobility-service-manually"></a>Disinstallazione manuale di servizio mobilità

È possibile disinstallare servizio mobilità mediante l'aggiunta di installazione applicazioni dal Pannello di controllo o riga di comando.

Il comando per disinstallare il servizio di mobilità tramite riga di comando

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>Modificare l'indirizzo IP del server di gestione

Dopo avere eseguito la procedura guidata è possibile modificare l'indirizzo IP del server di gestione come indicato di seguito:

1. Aprire hostconfig.exe file (disponibile sul desktop).
2. Nella scheda **globale** è possibile modificare l'indirizzo IP del server di gestione.

    >[AZURE.NOTE] È necessario modificare solo l'indirizzo IP del server di gestione. Il numero di porta per le comunicazioni server di gestione deve essere 443 e utilizzare HTTPS deve essere abilitata. La passphrase non deve essere modificata.

    ![Indirizzo IP del server Management](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>Installare manualmente in un server Linux:

1. Copiare l'archivio tar appropriate in base alla tabella sopra al computer Linux che si desidera proteggere.
2. Aprire un'applicazione di shell ed estrarre l'archivio tar compressi in un percorso locale eseguendo:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio tar. Per eseguire questa operazione copiare la passphrase C:\ProgramData\Microsoft Azure sito Recovery\private\connection.passphrase sul server di gestione e salvarlo in passphrase.txt eseguendo *`echo <passphrase> >passphrase.txt`* nella shell.
4. Installare il servizio mobilità immettendo *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Specificare l'indirizzo IP interno del server di gestione e verificare che sia selezionata la porta 443.

**È inoltre possibile installare dalla riga di comando**:

1. Copiare la passphrase \InMage Systems\private\connection file c:\Programmi\Microsoft (x86) sul server di gestione e salvarlo come "passphrase.txt" sul server di gestione. Eseguire questi comandi. In questo esempio l'indirizzo IP del server management 104.40.75.37 e la porta HTTPS deve essere 443:

Per installare in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Per installare nel server di destinazione master:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Passaggio 10: Attivare la protezione per un computer

Per abilitare la protezione aggiungere macchine virtuali e server fisici a un gruppo di protezione. Prima di iniziare, tenere presente quanto segue se si sta proteggere macchine virtuali VMware:

- Macchine virtuali VMware vengono rilevate ogni 15 minuti e possono essere più di 15 minuti per poter comparire nel portale di ripristino di sito dopo l'individuazione.
- Le modifiche dell'ambiente del computer virtuale (ad esempio installazione degli strumenti di VMware) anche possono richiedere più di 15 minuti per l'aggiornamento nel ripristino del sito.
- È possibile cercare l'ultima volta individuato macchine virtuali VMware nel campo **contatto ultima** per l'host server/ESXi vCenter nella scheda **Server di configurazione** .
- Se si dispone di un gruppo di protezione già stato creato e aggiungere un host di Server o ESXi vCenter successivamente, potrebbe richiedere più di 15 minuti per il portale il ripristino del sito di Azure aggiornare e per macchine virtuali venga elencato nella finestra di dialogo **aggiunta a un gruppo di protezione** .
- Se si preferisce proseguire con l'aggiunta di computer al gruppo di protezione senza attendere che l'individuazione pianificata, evidenziare il server di configurazione (non selezionarla) e fare clic sul pulsante **Aggiorna** .

Si noti inoltre che:

- È consigliabile progettare i gruppi di protezione in modo speculare i carichi di lavoro. Aggiungere, ad esempio computer che eseguono una specifica applicazione allo stesso gruppo.
- Quando si aggiungono computer a un gruppo di protezione, inserisce automaticamente il server di processo e installa il servizio mobilità se non è già installato. Si noti che è necessario che il meccanismo push preparare come descritto nel passaggio precedente.


Aggiungere computer a un gruppo di protezione:

1. Fare clic su **elementi protetti** > **Gruppo protezione** > **computer** > aggiungere computer. \As una procedura consigliata
2. In **Seleziona macchine virtuali** se si sta proteggere macchine virtuali VMware, selezionare un server vCenter che gestisce le macchine virtuali (o host EXSi in cui vengono eseguiti) e quindi selezionare il computer.

    ![Attivare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  In **macchine virtuali selezionare** se si sta proteggere server fisici, della procedura guidata **Aggiungere computer fisici** fornire l'indirizzo IP e un nome descrittivo. Selezionare quindi nei sistemi operativi.

    ![Attivare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. In **Specificare risorse di destinazione** selezionare l'account di archiviazione in uso per la replica e selezionare se è necessario utilizzare le impostazioni per tutti i carichi di lavoro. Si noti che gli account di archiviazione premium non sono attualmente supportati.

    >[AZURE.NOTE] 1 non è supportato lo spostamento degli account di archiviazione creati per il [nuovo portale Azure](../storage/storage-create-storage-account.md) attraverso gruppi di risorse.                           2.[migrazione degli account di archiviazione](../resource-group-move-resources.md) tra gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni non è supportata per gli account di archiviazione utilizzati per la distribuzione il ripristino del sito.

    ![Attivare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. In **Impostazione account** selezionare l'account è [configurato](#install-the-mobility-service-with-push-installation) per l'utilizzo per l'installazione automatica del servizio di mobilità.

    ![Attivare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Fare clic sul segno di spunta per completare l'aggiunta di computer al gruppo di protezione e avviare la replica iniziale per ogni computer.

    >[AZURE.NOTE] Se l'installazione push è stata preparata mobilità servizio viene installato automaticamente nei computer che non dispongono di vengono sommati al gruppo di protezione. Dopo il servizio di installazione di un processo di protezione viene avviato e non riesce. Dopo l'errore è necessario riavviare manualmente tutti i computer in cui sono stato installato il servizio di mobilità. Dopo il riavvio inizia nuovamente il processo di protezione e si verifica della replica iniziale.

È possibile monitorare lo stato nella pagina **dei processi** .

![Attivare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Inoltre, è possibile monitorare lo stato di protezione in **Elementi protetti** > <protection group name> > **macchine virtuali**. Dopo il completamento della replica iniziale e sincronizzazione dei dati, lo stato del computer diventa** protetto**.

![Attivare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>Passaggio 11: Set protetta proprietà computer

1. Dopo una macchina stato **protetta** è possibile configurare le proprietà di failover. I dettagli del gruppo di protezione selezionare la macchina e aprire la scheda **Configura** .
2. Il ripristino del sito automaticamente suggerisce le proprietà per la macchina virtuale Azure e rileva che Impostazioni rete locale.

    ![Impostare le proprietà di macchina virtuale](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. È possibile modificare queste impostazioni:

    -  **Nome macchine Virtuali di Azure**: questo è il nome che sarà assegnato al computer di in Azure dopo il failover. Il nome deve essere conforme ai requisiti di Azure.

    -  **Dimensione memoria virtuale di Azure**: il numero di schede di rete è dovuto la dimensione specificata per la macchina virtuale di destinazione. [Per ulteriori](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) sulle schede e le dimensioni massime. Si noti che:

        - Se si modifica la dimensione per una macchina virtuale e salvare le impostazioni, il numero della scheda di rete verrà modificate quando si apre la scheda **Configura** successivo. Il numero di schede di rete di macchine virtuali di destinazione è minimo del numero di schede di rete macchina virtuale di origine e numero massimo di schede di rete supportate per la dimensione della macchina virtuale scelta.
            - Se il numero di schede di rete del computer di origine è minore o uguale al numero di schede consentiti per la dimensione di computer di destinazione, la destinazione avrà lo stesso numero di schede come origine.
            - Se il numero di schede per la macchina virtuale di origine è maggiore del numero consentito per verrà usata le dimensioni di destinazione, quindi la dimensione massima di destinazione.
            - Se, ad esempio un computer di origine con due schede di rete e le dimensioni del computer di destinazione supporta quattro, il computer di destinazione sarà disponibili due schede. Se il computer di origine con due schede, ma le dimensioni di destinazione supportati supporta solo uno computer di destinazione avrà solo una scheda.
        - Se la macchina virtuale ha più schede di rete tutte le schede devono connessi alla rete Azure stesso.
    - **Rete Azure**: È necessario specificare una rete Azure macchine virtuali di Azure verrà connesso dopo il failover. Se non si specifica un macchine virtuali di Azure non connesso a una rete. Inoltre è necessario specificare una rete Azure se si desidera failback da Azure al sito locale. Failback richiede una connessione VPN tra una rete Azure e una rete locale.
    - **Indirizzo IP di Azure/subnet**: per ogni scheda di rete è selezionare subnet al quale si connetterà la macchina virtuale Azure. Si noti che:
        - Se la scheda di rete del computer di origine è configurata per l'utilizzo di un indirizzo IP statico è possibile specificare un indirizzo IP statico per la macchina virtuale Azure. Se non si fornisce un indirizzo IP statico verrà allocato qualsiasi indirizzo IP disponibile. Se si specifica l'indirizzo IP di destinazione ma è già in uso da un'altra macchine Virtuali di Azure failover avrà esito negativo. Se la scheda di rete del computer di origine è configurata per l'utilizzo di DHCP è necessario DHCP come impostazione per Azure.

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Passaggio 12: Creare un piano di ripristino ed eseguire caso di errore

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

È possibile eseguire caso di errore per un singolo computer o avere esito negativo su più macchine virtuali di cui eseguire la stessa attività o eseguire il carico di lavoro stesso. L'esito negativo su più computer nello stesso momento che aggiungerle a un piano di ripristino.

### <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Nella pagina **Ripristino plan di messaggistica unificata** fare clic su **Aggiungi piano di ripristino** e aggiungere un piano di ripristino. Specificare i dettagli per il piano e selezionare **Azure** con quello di destinazione.

    ![Configurare il piano di ripristino](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. In **Selezionare macchina virtuale** selezionare un gruppo di protezione e quindi selezionare computer nel gruppo per aggiungere al piano di ripristino.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

È possibile personalizzare il piano per creare gruppi e sequenza l'ordine in cui vengono eseguite macchine nel piano del ripristino sopra. È anche possibile aggiungere script e istruzioni per le azioni manuale. Script possono essere creati manualmente o mediante il dal [Runbook automazione Azure](site-recovery-runbook-automation.md). [Altre informazioni](site-recovery-create-recovery-plans.md) sulla personalizzazione dei piani di ripristino.

## <a name="run-a-failover"></a>Eseguire caso di errore

Prima di eseguire caso di errore si noti che:


- Assicurarsi che il server di gestione è in esecuzione e disponibile, in caso contrario failover avrà esito negativo.
- Se si esegue una nota failover non pianificato che:

    - Se possibile sarà necessario arrestare macchine principale prima di eseguire un failover non pianificato. In questo modo che non si dispone di origine e di replica computer che eseguono nello stesso momento. Se si sta replicare macchine virtuali VMware quindi quando si esegue un failover non pianificato è possibile specificare che il ripristino del sito da semplificare tentativo di arrestare il computer di origine. In base allo stato del sito principale questo potrebbe o potrebbe non funzionare. Se si sta replica server fisici che non offre questa opzione, il ripristino del sito.
    - Quando si esegue un failover non pianificato interrompe la replica dei dati dal computer principale in modo che non è possibile trasferire qualsiasi delta dati dopo l'avvio di un failover non pianificato.

- Se si desidera connettersi al computer virtuale di replica in Azure dopo il failover, abilitare connessione Desktop remoto sul computer di origine prima di eseguire il failover e consentire la connessione RDP attraverso il firewall. È anche necessario consentire RDP endpoint pubblico della macchina virtuale Azure dopo il failover. Seguire queste [procedure consigliate](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) per garantire il corretto funzionamento RDP dopo caso di errore.

>[AZURE.NOTE] Per ottenere prestazioni ottimali in caso di errore in Azure, assicurarsi di avere installato l'agente di Azure nel computer protetto. Questo è utile in avvio più veloce e utile anche in diagnosi in caso di problemi. Agente di Linux può essere trovato [qui](https://github.com/Azure/WALinuxAgent) - e Windows sono reperibili agente [qui](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>Eseguire test caso di errore

Eseguire test caso di errore per simulare i processi di failover e ripristino di una rete isolata che non influisce sull'ambiente di produzione e continua in base al regolare della replica nel modo consueto. Test failover avvia sulla fonte ed è possibile eseguire in due modi:

- **Non specificare una rete Azure**: se si esegue caso di errore test senza una rete il test è sufficiente controllerà che macchine virtuali di avviare e vengono visualizzati correttamente in Azure. Macchine virtuali di non essere connessi a una rete Azure dopo il failover.
- **Specificare una rete Azure**: questo tipo di failover verifica che l'intero ambiente di replica proviene massimo come previsto e di Azure macchine virtuali connessi alla rete specificata.


1. Nella pagina **Ripristino plan di messaggistica unificata** selezionare il piano, fare clic su **Test Failover**.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. In **Confermare Failover Test** selezionare **Nessuno** per indicare che non si desidera utilizzare una rete Azure per failover test oppure selezionare la rete in cui verrà connesso il test macchine virtuali dopo il failover. Fare clic sul segno di spunta per avviare il failover.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Monitorare l'avanzamento di failover nella scheda **processi** .

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Dopo il failover anche dovrebbe essere possibile vedere replica Azure computer vengono visualizzati nel portale di Azure > **macchine virtuali**. Se si desidera avviare una connessione RDP verso la macchina virtuale Azure è necessario aprire la porta 3389 sull'endpoint macchine Virtuali.

5. Dopo aver finito quando failover raggiunge completare test fase, fare clic su Test completo alla fine. Nelle note per registrare e salvare le osservazioni associate failover test.

6. Fare clic su Pulisci automaticamente l'ambiente di testing **failover test è stata completata** . A questo punto failover test verrà visualizzato uno stato di **completamento** . Gli elementi o macchine virtuali create automaticamente durante il failover test vengono eliminate. Si noti che, se continua più di due settimane in caso di errore di test è completato dalla stessa.



### <a name="run-an-unplanned-failover"></a>Eseguire un failover non pianificato

Failover non pianificato viene avviato da Azure e può essere eseguito anche se il sito principale non è disponibile.


1. Nella pagina **Ripristino plan di messaggistica unificata** selezionare il piano, fare clic su **Failover** > **Failover non pianificato**.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Se si sta replicare macchine virtuali VMware è possibile selezionare per provare a chiudere macchine virtuali locale. Si tratta di usufruendo e failover continuerà se l'impegno ha esito positivo o meno. Se non è riuscita dettagli sugli errori verranno visualizzati nella scheda **processi **> **Processi Failover non pianificati**.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] Questa opzione non è disponibile se si sta replicare server fisici. È necessario provare a chiudere quelli manualmente se possibile.

3. Nella finestra di **Conferma Failover** verificare la direzione di failover (da Azure) e selezionare il punto di ripristino che si desidera utilizzare per il failover. Se è abilitata macchine Virtuali con più durante la configurazione delle proprietà della replica è possibile recuperare al punto di ripristino dell'applicazione o un arresto anomalo coerente più recente. È inoltre possibile selezionare **punto di ripristino personalizzati** per recuperare a un punto precedente nel tempo. Fare clic sul segno di spunta per avviare il failover.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Attendere il completamento del processo failover non pianificato. È possibile monitorare lo stato di avanzamento failover nella scheda **processi** . Si noti che, anche se si verificano errori durante failover non pianificato il piano di ripristino esegue fino al completamento. È inoltre dovrebbe essere possibile vedere replica Azure computer vengono visualizzate in macchine virtuali nel portale di Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Connettersi a replicato macchine virtuali di Azure dopo il failover

Per connettersi a replicato macchine virtuali in Azure una volta failover qui è necessario:

1. Connessione Desktop remoto deve essere attivata nel computer principale.
2. Windows Firewall nel computer principale per consentire RDP.
3. Dopo il failover è necessario aggiungere RDP all'endpoint pubblico per Azure macchina virtuale.

[Per ulteriori](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) sulla configurazione specifica.


## <a name="deploy-additional-process-servers"></a>Distribuire server processo aggiuntivi

Se è necessario ridimensionare esterna per una distribuzione oltre 200 computer di origine o il tasso di varianza del giorno totale supera i 2 TB, è necessario server processo aggiuntivi per gestire il volume di traffico. Per impostare un server di processo aggiuntivi verificare i requisiti di [server aggiuntivi processo](#additional-process-servers) e quindi seguire le istruzioni per configurare il server di processo. Dopo aver configurato il server è possibile configurare i computer di origine per utilizzarlo.

### <a name="set-up-an-additional-process-server"></a>Impostare un server di processo aggiuntivi

È possibile impostare un server di processo aggiuntivi come indicato di seguito:

- Eseguire la procedura guidata unificata per configurare un server di gestione come server di processo.
- Se si desidera gestire la replica dei dati utilizzando il nuovo server processo è necessario eseguire la migrazione dei computer protetti per eseguire questa operazione.

### <a name="install-the-process-server"></a>Installare il server di processo

1. Nella pagina Guida introduttiva scaricare il file di installazione unificata per l'installazione del componente il ripristino del sito. Eseguire l'installazione.
2. Nella **prima di iniziare a** selezionare **Aggiungi processo aggiuntivi server scalabilità distribuzione**.

    ![Aggiungere il server di processo](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Completare la procedura guidata nello stesso modo è stata quando è [impostato](#step-5-install-the-management-server) il primo server di gestione.

4. Dettagli della **configurazione Server** specificare l'indirizzo IP del server di gestione in cui è installato il server di configurazione e la passphrase. Sul server di gestione eseguire ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** per ottenere la passphrase.

    ![Aggiungere il server di processo](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Eseguire la migrazione di computer per usare il nuovo server di processo

1. Aprire **Configurazione server** > **Server** > nome del server di gestione originale > **Dettagli sul Server**.

    ![Aggiornare il server di processo](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Nell'elenco **Processo server** fare clic su **Modifica processo Server** accanto al server che si desidera modificare.

    ![Aggiornare il server di processo](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. **Modifica**del server Process > **Destinazione processo Server** selezionare il server di gestione di nuovo e quindi selezionare le macchine virtuali che gestirà il nuovo server di processo. Fare clic sull'icona delle informazioni per ottenere informazioni sul server. Per mettere caricare decisioni viene visualizzato lo spazio medio che è necessario per replicare ogni macchina virtuale selezionata nel nuovo server di processo. Fare clic sul segno di spunta per avviare la replica nel nuovo server di processo.

    ![Aggiornare il server di processo](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>VMware le autorizzazioni per l'accesso vCenter

Il server di processo di individuare automaticamente macchine virtuali su un server vCenter. Per eseguire il rilevamento automatico è necessario definire un ruolo (Azure_Site_Recovery) livello vCenter per consentire il ripristino del sito accedere al server vCenter. Si noti che se occorre eseguire la migrazione di macchine VMware in Azure e non necessarie per il failback da Azure, è possibile definire un ruolo di sola lettura è sufficiente. Impostare le autorizzazioni come descritto in [passaggio 6: configurare le credenziali per il server vCenter](#step-6-set-up-credentials-for-the-vcenter-server) nella tabella seguente sono riepilogate le autorizzazioni di ruolo.

**Ruolo** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
Ruolo Azure_Site_Recovery | Individuazione di VMware VM |Assegnare i privilegi per il server centro v:<br/><br/>Archivio dati -> assegna spazio archivio dati Sfoglia, operazioni di basso livello di un file., file di installazione, aggiornamento macchina virtuale file<br/><br/>Rete -> Assegna di rete<br/><br/>Risorse -> macchina virtuale assegnare al pool di risorse, eseguire la migrazione spento macchina virtuale, eseguire la migrazione acceso macchina virtuale<br/><br/>Attività -> Crea attività, delle attività di aggiornamento<br/><br/>Macchina virtuale -> configurazione<br/><br/>Macchina virtuale -> interagire -> Rispondi alla domanda, dispositivo connessione., configurare CD media, Configura unità floppy, spegnere Power nel, installare gli strumenti di VMware<br/><br/>Macchina virtuale -> inventario -> Crea, registro, annullamento della registrazione<br/><br/>Macchina virtuale -> Provisioning -> Consenti macchina virtuale download, caricare file delle macchine virtuali Consenti<br/><br/>Macchina virtuale -> snapshot -> Rimuovi istantanee
ruolo di utente vCenter | Individuazione VMware VM il Failover senza arresto dell'origine macchine Virtuali | Assegnare i privilegi per il server centro v:<br/><br/>Oggetto Data Center –> propagare oggetto figlio, ruolo = sola lettura <br/><br/>L'utente viene assegnata a livello di Data Center e pertanto ha accesso a tutti gli oggetti nel centro dati.  Se si desidera limitare l'accesso, assegnare il ruolo di **non consentire l'accesso** con l'oggetto **propagare bambino** agli oggetti figlio (ESX host, archivi dati, macchine virtuali e reti).
ruolo di utente vCenter | Failover e failback | Assegnare i privilegi per il server centro v:<br/><br/>Oggetto Data Center-propagazione oggetto figlio, ruolo = Azure_Site_Recovery<br/><br/>L'utente viene assegnata a livello di Data Center e pertanto ha accesso a tutti gli oggetti nel centro dati.  Se si desidera limitare l'accesso, assegnare il ruolo di **non consentire l'accesso **con la **propagazione oggetto figlio** all'oggetto figlio (ESX host, archivi dati, macchine virtuali e reti).  



## <a name="third-party-software-notices-and-information"></a>Note legali sul software di terze parti e informazioni

Non tradurre o Localize

Il software e firmware in esecuzione in Microsoft prodotto o servizio basato su o incorpora materiale da progetti elencati di seguito (collettivamente "codice terze parti").  Microsoft è l'autore non originale del codice di terze parti.  Le informazioni sul copyright originale e licenza, in cui Microsoft ha ricevuto il codice di terze parti, sono impostate via riportata di seguito.

Le informazioni nella sezione si riferisce componenti di terze parti codice dei progetti vengono elencate di seguito. Ad esempio licenze e le informazioni vengono fornite informativo.  Il codice di terze parti è da relicensed per l'utente da Microsoft in condizioni di licenza software Microsoft per il prodotto o servizio Microsoft.  

Le informazioni nella sezione B si riferisce componenti di codice di terze parti che vengono resi disponibili per l'utente da Microsoft sotto le condizioni di licenza originale.

Il file completo potrebbe disponibili nell' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft si riserva tutti i diritti non espressamente concessi, se da implicazioni, corrette o in caso contrario.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sul failback](site-recovery-failback-azure-to-vmware-classic.md) per visualizzare l'errore su computer che eseguono in Azure nuovamente per l'ambiente locale.
