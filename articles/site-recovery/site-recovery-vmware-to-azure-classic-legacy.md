<properties
    pageTitle="Replicare macchine virtuali VMware e server fisici su Azure con il ripristino del sito di Azure (legacy) | Microsoft Azure" 
    description="Viene descritto come replicare macchine virtuali locale e Windows/Linux server fisici in Azure utilizzando il ripristino del sito di Azure in una distribuzione legacy nel portale di classica." 
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

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Replicare macchine virtuali VMware e server fisici su Azure con il ripristino del sito di Azure tramite il portale classico (legacy)

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmware-to-azure.md)
- [Portale classica](site-recovery-vmware-to-azure-classic.md)
- [Portale classico (legacy)](site-recovery-vmware-to-azure-classic-legacy.md)


Introduzione al ripristino del sito Azure! In questo articolo vengono illustrati una distribuzione legacy della replica macchine virtuali VMware locale o server fisici Windows/Linux di Azure utilizzando il ripristino del sito di Azure nel portale di classica.

## <a name="overview"></a>Panoramica

Le organizzazioni devono una strategia BCDR che determina come App, carichi di lavoro e dati rimangono in esecuzione e disponibili durante il tempo di inattività pianificato e non pianificato e ripristino in condizioni di lavoro normali più presto possibile. La strategia BCDR deve mantenere i dati business attendibili e recuperabili e garantire che carichi di lavoro sempre disponibile quando si verifica danno.

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR da coordinazione replica di server fisici locale e macchine virtuali nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nella propria posizione principale, non si riesce sopra a posizione secondaria per mantenere le applicazioni e carichi di lavoro disponibili. Riescano a tornare alla propria posizione primaria restituisce al funzionamento normale. Altre informazioni, vedere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)


>[AZURE.WARNING] In questo articolo è **contenute istruzioni precedenti**. Non utilizzarlo per le distribuzioni di nuove. Se, tuttavia, [seguire queste istruzioni](site-recovery-vmware-to-azure.md) per distribuire il ripristino del sito nel portale di Azure o [utilizzare queste istruzioni](site-recovery-vmware-to-azure-classic.md) per configurare la distribuzione avanzata nel portale di classica. Se è già stato distribuito utilizzando il metodo descritto in questo articolo, è consigliabile eseguire la migrazione alla distribuzione avanzata nel portale di classica.


## <a name="migrate-to-the-enhanced-deployment"></a>Eseguire la migrazione alla distribuzione avanzata

In questa sezione è pertinente solo se è già stato distribuito il ripristino del sito con le istruzioni in questo articolo.

Per eseguire la migrazione di distribuzione esistente, che è necessario:

1. Distribuire nuovi componenti il ripristino del sito nel sito locale.
2. Configurare le credenziali per il rilevamento automatico delle macchine virtuali VMware nel nuovo server di configurazione.
3. Alla scoperta di server VMware con il nuovo server di configurazione.
3. Creare un nuovo gruppo di protezione con il nuovo server di configurazione.


Prima di iniziare:

- È consigliabile impostare una finestra di manutenzione per la migrazione.
- L'opzione di **Eseguire la migrazione di computer** è disponibile solo se si dispone di gruppi di protezione esistenti che sono stati creati durante una distribuzione legacy.
- Dopo aver completato la procedura di migrazione possono essere necessari 15 minuti o più per aggiornare le credenziali e per individuare e aggiornare macchine virtuali in modo che è possibile aggiungere a un gruppo di protezione. È possibile aggiornare manualmente anziché in attesa. 

Eseguire la migrazione come indicato di seguito:

1. Informazioni su [avanzato per la distribuzione nel portale di classica](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Esaminare avanzata [architettura](site-recovery-vmware-to-azure-classic.md#scenario-architecture)e [i prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).
2. Disinstallare il servizio di mobilità dal computer che si sta effettuando la replica. Verrà installata nei computer di una nuova versione del servizio quando vengono aggiunti al nuovo gruppo di protezione.
3. Scaricare una [chiave di registrazione archivio](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) ed [eseguire la procedura guidata configurazione unificata](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) per installare il server di configurazione, server process e componenti di server di destinazione principale. Ulteriori informazioni sulla [pianificazione delle capacità](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Configurare le credenziali](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) che il ripristino del sito consente di accedere a server VMware per individuare automaticamente macchine virtuali VMware. Informazioni sulle [autorizzazioni necessarie](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Aggiungere [server vCenter o vSphere host](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). È possibile richiedere 15 minuti per altre informazioni per i server all'interno del portale il ripristino del sito.
6. Creare un [nuovo gruppo di protezione](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Può richiedere fino a 15 minuti per il portale di aggiornare in modo che le macchine virtuali vengono rilevate e vengono visualizzate. Se si preferisce non attendere è possibile evidenziare il nome del server di gestione (non selezionarla) > **Aggiorna**.
7. In nuovo gruppo di protezione fare clic su **Computer eseguire la migrazione**.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. In **Selezionare computer** selezionare il gruppo di protezione da che si desidera eseguire la migrazione e i computer di che cui eseguire la migrazione.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. **Configurare** le impostazioni di destinazione specificare se si desidera utilizzare le stesse impostazioni per tutti i computer e selezionare il server di processo e account Azure dello spazio di archiviazione. Se non si dispone di un server di processo separato sarà l'indirizzo IP del server configurazione.


    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] [Migrazione degli account di archiviazione](../resource-group-move-resources.md) tra gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni non è supportata per gli account di archiviazione utilizzati per la distribuzione il ripristino del sito.

10. In **Impostazione account**, selezionare l'account creato per il server di processo di accedere al computer per inserire la nuova versione del servizio di mobilità.

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Il ripristino del sito verrà eseguita la migrazione dei dati replicati all'account di archiviazione Azure fornito dall'utente. Facoltativamente è possibile riutilizzare l'account di archiviazione che è utilizzata nella distribuzione legacy.
12. Al termine il processo macchine virtuali verranno sincronizzate automaticamente. Al termine della sincronizzazione è possibile eliminare le macchine virtuali dal gruppo di protezione precedenti.
13. Dopo avere eseguito la migrazione di tutti i computer è possibile eliminare il gruppo di protezione precedenti.
14. Ricordarsi di specificare le proprietà di failover per i computer e le impostazioni di rete Azure volta completata la sincronizzazione.
15. Se si dispone di piani di ripristino esistente, è possibile migrare all'installazione avanzata con l'opzione **Eseguire la migrazione di pianificazione di ripristino** . Si deve essere eseguita solo una volta migrati tutti i computer protetti. 

    ![Aggiungi account](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Dopo aver completato la migrazione continuare con l' [articolo avanzata](site-recovery-vmware-to-azure-classic.md). Il resto di questo articolo legacy non saranno più rilevante e non è necessario seguire più dei passaggi descritti in it * *.




## <a name="what-do-i-need"></a>Che cosa bisogna?

Questo diagramma mostra i componenti di distribuzione.

![Nuovo archivio](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Ecco cosa è necessario:

**Componente** | **Distribuzione** | **Dettagli**
--- | --- | ---
**Server di configurazione** | Una Azure standard A3 macchina virtuale nello stesso abbonamento il ripristino del sito. | Il server di configurazione coordinate comunicazioni tra computer protetto, il server di processo e i server di destinazione principale in Azure. Imposta la replica e il ripristino di coordinate in Azure in caso di failover.
**Server di destinazione principale** | Una macchina virtuale Azure, ovvero un server di Windows in base a un'immagine della raccolta Windows Server 2012 R2 (per proteggere il computer Windows) o come server Linux in base a un'immagine della raccolta OpenLogic CentOS 6.6 (per proteggere computer Linux).<br/><br/> Ridimensionamento delle tre opzioni disponibili: A4 Standard, D14 Standard e DS4 Standard.<br/><br/> Il server è connesso alla stessa rete Azure come il server di configurazione.<br/><br/> È possibile impostare il portale il ripristino del sito | Riceve e conserva i dati replicati dal computer protetto con allegati dischi rigidi virtuali creati in archiviazione blob nell'account di archiviazione Azure.<br/><br/> Selezionare DS4 Standard specificamente per la configurazione di protezione per carichi di lavoro che richiedono prestazioni coerenti e bassa latenza tramite Account di archiviazione Premium.
**Server di processo** | Un server virtuale o fisico locale che eseguono Windows Server 2012 R2<br/><br/> È consigliabile viene inserito nella stessa rete e segmento LAN come computer che si desidera proteggere, ma può essere eseguito su una rete diversa, purché computer protetto visibilità rete L3 ad esso.<br/><br/> Si è stata configurata e registrare nel server di configurazione del portale il ripristino del sito. | Computer protetto invia dati di replica nel server di processo locale. Ha un dati replica cache alla cache basata su disco che riceve. Esegue un numero di azioni su tali dati.<br/><br/> Ottimizza i dati per la memorizzazione nella cache, compressione e crittografia prima di inviarlo al server di destinazione principale.<br/><br/> Gestione installazione push del servizio mobilità.<br/><br/> Esegue il rilevamento automatico delle macchine virtuali VMware.
**Computer locale** | Locale macchine virtuali VMware o fisico server che eseguono Windows o Linux. | Configurare le impostazioni di replica che si applicano a uno o più computer. Si può avere esito negativo su un singolo computer o più comunemente più computer che vengono raccolte insieme in un piano di ripristino. 
**Servizio di mobilità** | Installato in ogni macchina virtuale o server fisico che si desidera proteggere<br/><br/> Installare manualmente o inserito e installati automaticamente dal server di processo quando si abilita la replica per un computer. | Il servizio mobilità invia dati al server di processo durante la replica iniziale (ripetizione). Dopo che il computer è stato protetto (termine ripetizione) il servizio mobilità acquisisce scrittura sul disco in memoria e li invia al server di processo. Applicationconsistency per server Windows viene eseguita mediante VSS.
**Azure archivio il ripristino del sito** | Creare un archivio il ripristino del sito con un abbonamento a Azure e registrare server nell'archivio di. | Archivio di coordinate e di orchestrare replica dei dati, failover e ripristino tra il sito locale e Azure.
**Meccanismo di replica** | **Su Internet**, ovvero comunica e replica i dati dal server locale protetto in Azure uso sicuro SSL/TLS canale tramite internet. Questo è l'opzione predefinita.<br/><br/> **VPN/ExpressRoute**, comunica e replica i dati tra computer server locale e Azure tramite una connessione VPN. È necessario configurare una connessione VPN da sito o una connessione ExpressRoute tra il sito locale e di rete Azure.<br/><br/> È possibile selezionare la modalità replicare durante la distribuzione il ripristino del sito. Non è possibile modificare il meccanismo dopo aver configurato senza impatto della replica della macchine esistenti. | Nessuna delle due opzioni è necessario aprire le porte di rete in ingresso sui computer protetto. Tutte le comunicazioni di rete viene avviata dal sito locale. 

## <a name="capacity-planning"></a>Pianificazione delle capacità

È necessario prendere in considerazione le principali aree:

- **Ambiente di origine**, ovvero il VMware infrastruttura, le impostazioni del computer di origine e requisiti.
- **I server dei componenti**, ovvero il processo server, server di configurazione e di destinazione principale server 

### <a name="considerations-for-the-source-environment"></a>Considerazioni per l'ambiente di origine

- **Dimensione massima del disco**, ovvero la dimensione massima corrente del disco che può essere collegato a una macchina virtuale è 1 TB. In questo modo la dimensione massima di un disco di origine che può essere replicata è limitata a 1 TB.
- **Dimensioni massime per ogni origine**, ovvero le dimensioni massime di una macchina singola origine sono 31 TB (con 31 dischi) e con un'istanza di D14 viene completato il provisioning per il server di destinazione principale. 
- **Il numero di origini per server di destinazione master**, ovvero più computer di origine possono essere protetti con un server di destinazione master singola. Tuttavia, una macchina singola origine non è protetto in più server di destinazione principale, in quanto come dischi replicano, in archiviazione blob Azure viene creato un disco rigido virtuale che riflette le dimensioni del disco e allegata come un disco dati al server di destinazione master.  
- **Massimo giornaliero modificare tariffa origine**, sono disponibili tre fattori da considerare quando si considera il tasso di modifica consigliati per origine. Per motivi di destinazione in base a due IOPS sono necessari nel disco di destinazione per ogni operazione sulla fonte. In questo modo la lettura di dati precedente e la scrittura dei nuovi dati terrà disco di destinazione. 
    - **Giornaliera modificare velocità supportata dal server di processo**, ovvero un computer di origine non è possibile estesi a più server di processo. Un server singolo processo può supportare fino a 1 TB di modifiche giornaliere. 1 TB è pertanto che i dati giornalieri massimo modificare tasso supportata per il computer di un'origine. 
    - **Velocità massima supportata dal disco di destinazione**, ovvero la varianza del valore massimo per il disco di origine non può essere più di 144 GB/giorno (con dimensione di scrittura 8 KB). Vedere la tabella nella sezione di destinazione principale per la velocità e IOPs della destinazione per vari scrivere le dimensioni. Questo numero deve essere diviso per due perché ogni origine IOP genera 2 IOPS nel disco di destinazione. Informazioni sulle [destinazioni prestazioni e scalabilità Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) durante la configurazione di destinazione per gli account di archiviazione premium.
    - **Velocità massima supportato da account di archiviazione**, ovvero una fonte non è possibile estesi a più account di archiviazione. Dato che un account di archiviazione utilizza un massimo di 20.000 richieste al secondo e che ogni origine IOP genera 2 IOPS nel server di destinazione master, è consigliabile che mantenere il numero di IOPS attraverso l'origine a 10.000. Informazioni sulle [destinazioni prestazioni e scalabilità Azure](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) quando si configura l'origine per gli account di archiviazione premium.

### <a name="considerations-for-component-servers"></a>Considerazioni per i server dei componenti

Tabella 1 sono riepilogate le dimensioni di macchina virtuale per la configurazione e server target master.

**Componente** | **Istanze di Azure distribuite** | **Core** | **Memoria** | **Dischi max** | **Dimensione del disco**
--- | --- | --- | --- | --- | ---
Server di configurazione | A3 standard | 4 | 7 GB | 8 | GB 1023
Server di destinazione principale | A4 standard | 8 | 14 GB | 16 | GB 1023
 | D14 standard | 16 | GB 112 | 32 | GB 1023
 | DS4 standard | 8 | 28 GB | 16 | GB 1023

**Tabella 1**

#### <a name="process-server-considerations"></a>Considerazioni sui processi server

In genere dimensioni del server di processo dipende la percentuale di modifiche giornaliera su tutti i carichi di lavoro protetti.


- È necessario calcolo sufficiente per eseguire attività, ad esempio la compressione all'interno del testo e la crittografia.
- Il server di processo utilizza cache basata su disco. Verificare che lo spazio consigliata della cache ed è disponibile in modo da agevolare le modifiche ai dati archiviate in caso di bottiglia di rete o un'interruzione del velocità effettiva del disco. 
- Assicurarsi che la larghezza di banda sufficiente in modo che il server di processo è possibile caricare i dati al server di destinazione master per fornire protezione dei dati continua. 

Tabella 2 offre un riepilogo delle linee guida del processo server.

**Modifica dei dati** | **CPU** | **Memoria** | **Dimensione della cache disco**| **Velocità effettiva del disco cache** | **Larghezza di banda in ingresso/uscita**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 vCPUs (2 sockets * 2 core @ 2,5 GHz) | 4 GB | 600 GB | 7 a 10 MB al secondo | 30 Mbps/21 MB/s
300 a 600 GB | 8 vCPUs (2 sockets * 4 core @ 2,5 GHz) | 6 GB | 600 GB | 11-15 MB al secondo | Mbps Mbps/42 60
600 GB a 1 TB | 12 vCPUs (2 sockets * 6 core @ 2,5 GHz) | 8 GB | 600 GB | da 16 a 20 MB al secondo | 100 Mbps Mbps/70
> 1 TB | Distribuire un altro server di processo | | | | 

**Tabella 2**

Dove: 

- In ingresso è download della larghezza di banda (intranet tra il server di origine e di processo).
- Uscita è caricamento della larghezza di banda (internet tra il server di processo di destinazione principale). I numeri di uscita presuppongono compressione server 30% processo Media.
- Per cache disco un disco OS separato di minimo 128 GB è consigliato per tutti i server di processo.
- Per velocità effettiva del disco cache dello spazio di archiviazione seguente è stato utilizzato per il benchmarking: 8 unità sa di 10 RPM con configurazione RAID 10.

#### <a name="configuration-server-considerations"></a>Considerazioni sui server di configurazione

Ogni server di configurazione supportino fino a 100 computer di origine con volumi 3-4. Se la distribuzione è più esteso è consigliabile che distribuire un altro server di configurazione. Per le proprietà di macchina virtuale predefinite del server di configurazione, vedere la tabella 1. 

#### <a name="master-target-server-and-storage-account-considerations"></a>Considerazioni relative a account server e lo spazio di archiviazione di destinazione principale

Spazio di archiviazione per i server target master include un disco del sistema operativo, un volume di conservazione e dischi di dati. L'unità di conservazione mantiene le registrazioni delle modifiche apportate disco per la durata della finestra di conservazione definita nel portale del ripristino del sito.  Fare riferimento alla tabella 1 per la macchina virtuale le proprietà del server di destinazione master. Tabella 3 mostra come vengono utilizzati i dischi della A4.

**Istanza** | **Disco rigido del sistema operativo** | **Criteri di conservazione** | **Dischi di dati**
--- | --- | --- | ---
 | | **Criteri di conservazione** | **Dischi di dati**
A4 standard | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 dischi (15 * 1023 GB)
D14 standard |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 31 dischi (15 * 1023 GB)
DS4 standard |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 dischi (15 * 1023 GB)

**Tabella 3**

Dipende dalla pianificazione per il server di destinazione master:

- Limitazioni azure le prestazioni e archiviazione
    - Il numero massimo di altamente utilizzati dischi per una macchina virtuale livello Standard, circa 40 (20.000/500 IOPS disco) in un account di archiviazione singola. Informazioni sulle [destinazioni scalabilità per lo spazio di archiviazione standard sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) e per [sccounts lo spazio di archiviazione premium](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-   Modifiche giornaliere 
-   Spazio di archiviazione di conservazione volume.

Si noti che:

- Una fonte non è possibile estesi a più account di archiviazione. Si applica al disco dati passare ad account archiviazione selezionata quando si configura la protezione. Il sistema operativo e dischi conservazione in genere passare a account di archiviazione automaticamente distribuito.
- Il volume di archiviazione di conservazione necessario dipende dal tasso cambia giornaliero e il numero di giorni di conservazione. L'archiviazione di conservazione richiesta per server di destinazione master = varianza totale dall'origine giornaliere * numero di giorni di conservazione. 
- I server target master ha un solo volume di conservazione. Il volume di conservazione verrà condivisi i dischi collegati al server di destinazione master. Per esempio:
    - Se esiste un computer di origine con 5 dischi e ogni disco genera 120 IOPS (8 KB dimensione) nel sito di origine, questa viene convertita in 240 IOPS disco (2 operazioni nel disco di destinazione per ogni origine IO). 240 IOPS è all'interno di Azure al limite di IOPS di 500.
    - Del volume di conservazione, questo nome diventa 120 * 5 = 600 secondo e questo può diventare un difficoltà si riscontrano. In questo scenario, una strategia efficace, è possibile aggiungere altri dischi al volume di conservazione e l'intervallo tra, come configurazione strisce RAID. Perché il IOPS vengono distribuite tra più unità, questo verrà migliorare le prestazioni. Il numero di unità da aggiungere al volume di conservazione sarà come indicato di seguito:
        - Totale IOPS dall'ambiente di origine / 500
        - Varianza totale giornaliere dall'ambiente di origine (non compressi) / 287 GB. GB 287 rappresenta la velocità massima supportata da un disco di destinazione al giorno. Questa metrica variano in base alla dimensione di scrittura con un fattore di 8K, poiché in questo caso 8K è la si presuppone che le dimensioni di scrittura. Ad esempio, se le dimensioni di scrittura sono 4 KB velocità sarà 287/2. E se le dimensioni di scrittura 16 KB velocità sarà possibile 287 * 2.
- Il numero di account di archiviazione necessario = origine totale IOPs/10000.


## <a name="before-you-start"></a>Prima di iniziare

**Componente** | **Requisiti** | **Dettagli**
--- | --- | --- 
**Account Azure** | È necessario un account di [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Spazio di archiviazione Azure** | È necessario un account di archiviazione Azure per archiviare i dati replicati<br/><br/> L'account deve essere un [Account di archiviazione ridondanti geografico Standard](../storage/storage-redundancy.md#geo-redundant-storage) o un [Account di archiviazione Premium](../storage/storage-premium-storage.md).<br/><br/> Deve nella stessa regione servizio il ripristino del sito di Azure ed essere associato all'abbonamento stesso. Non è supportato lo spostamento degli account di archiviazione creati per il [nuovo portale Azure](../storage/storage-create-storage-account.md) attraverso gruppi di risorse.<br/><br/> Per ulteriori informazioni [Introduzione a Microsoft Azure archiviazione](../storage/storage-introduction.md)
**Azure virtuali** | È necessario un virtuali Azure in cui verrà distribuiti il server di configurazione di destinazione principale. Dovrebbe essere nello stesso abbonamento e opzioni internazionali come archivio il ripristino del sito di Azure. Se si desidera replicare i dati tramite una connessione VPN o ExpressRoute la rete virtuale Azure deve essere connesso alla rete locale tramite una connessione ExpressRoute o una connessione VPN da sito.
**Risorse Azure** | Assicurarsi di avere risorse sufficienti Azure per distribuire tutti i componenti. Per saperne di più in [Azure abbonamento limiti](../azure-subscription-service-limits.md).
**Macchine virtuali di Azure** | Macchine virtuali che si desidera proteggere devono essere conformi con [Prerequisiti Azure](site-recovery-best-practices.md).<br/><br/> **Conteggio del disco**, ovvero un massimo di 31 dischi può essere supportato in un unico server protetto<br/><br/> **Le dimensioni del disco**, ovvero capacità disco singoli non devono essere superiore a 1023 GB<br/><br/> **Cluster**, ovvero non sono supportati server raggruppate<br/><br/> **Avvio**, ovvero Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) avvio non è supportato<br/><br/> **Volumi**, ovvero Bitlocker volumi crittografati non sono supportati<br/><br/> **I nomi dei server**, ovvero i nomi devono contenere tra 1 e 63 caratteri (lettere, numeri e trattini). Il nome deve iniziare con una lettera o un numero e terminano con una lettera o un numero. Dopo la protezione di un computer è possibile modificare il nome di Azure.
**Server di configurazione** | Verrà creato standard delle macchine virtuali A3 in base a un'immagine della raccolta siti Azure ripristino di Windows Server 2012 R2 nell'abbonamento per il server di configurazione. Viene creata come la prima istanza di un nuovo servizio cloud. Se si seleziona Internet pubblica come tipo di integrazione applicativa per il server di configurazione servizio cloud verrà creato con un indirizzo IP pubblico riservato.<br/><br/> Il percorso di installazione deve essere solo i caratteri in lingua inglese.
**Server di destinazione principale** | Azure macchina virtuale standard A4, D14 o DS4.<br/><br/> Il percorso di installazione deve essere solo i caratteri in lingua inglese. Ad esempio il percorso dovrebbe essere **/usr/local/ASR** per un server di destinazione principale in Linux.
**Server di processo** | È possibile distribuire il server di processo in fisici o computer virtuale che esegue Windows Server 2012 R2 con gli aggiornamenti più recenti. Installare in c: /.<br/><br/> È consigliabile che inserendo il server della stessa rete e subnet come computer di cui che si desidera proteggere.<br/><br/> Installare VMware vSphere CLI 5.5.0 nel server di processo. Il componente di VMware vSphere CLI è necessario sul server di processo per rilevare macchine virtuali gestite da un server vCenter o macchine virtuali in esecuzione su un host ESXi.<br/><br/> Il percorso di installazione deve essere solo i caratteri in lingua inglese.<br/><br/> RIF File System non è supportata.
**VMware** | Server di vCenter VMware gestione l'hypervisor vSphere VMware. Deve essere eseguita vCenter versione 5.1 o 5.5 con gli aggiornamenti più recenti.<br/><br/> Uno o più hypervisor vSphere contenente macchine virtuali VMware a cui si desidera proteggere. L'hypervisor deve essere in esecuzione ESX/ESXi versione 5.1 o 5.5 con gli aggiornamenti più recenti.<br/><br/> Macchine virtuali VMware necessario disporre di strumenti di VMware installato e in esecuzione. 
**Computer Windows** | Server fisici protetto o macchine virtuali VMware in esecuzione Windows contengono un numero di requisiti.<br/><br/> Un sistema operativo a 64 bit è supportata: **Windows Server 2012 R2**, **Windows Server 2012**, o **Windows Server 2008 R2 con al SP1 almeno**.<br/><br/> Il nome host, punti di montaggio, i nomi dei dispositivi, il percorso di sistema di Windows (ad esempio: C:\Windows) deve essere solo in lingua inglese.<br/><br/> Il sistema operativo deve essere installato sull'unità C:\.<br/><br/> Solo dischi di base sono supportati. Dischi dinamici non sono supportati.<br/><br/> Le regole del firewall nel computer protetto dovrebbero consentire loro di raggiungere il server di destinazione dalla configurazione e schema in Azure.p ><p>È necessario specificare un account di amministratore (deve essere un amministratore locale del computer Windows) per l'installazione push il servizio di mobilità su Windows Server. Se l'account specificato è un account di dominio non è necessario disattivare il controllo di accesso remoto degli utenti del computer locale. Per eseguire questo aggiungere la voce del Registro di sistema di LocalAccountTokenFilterPolicy DWORD con un valore di 1 in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Per aggiungere la voce del Registro di sistema da un cmd Apri CLI o powershell e immettere **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Altre informazioni](https://msdn.microsoft.com/library/aa826699.aspx) sul controllo di accesso.<br/><br/> Dopo il failover, se si desidera connettersi a macchine virtuali di Windows Azure con Desktop remoto assicurarsi che Desktop remoto sia abilitata per il computer locale. Se non ci si connette in rete VPN, le regole firewall devono consentire le connessioni Desktop remoto via internet.
**Computer Linux** | Un sistema operativo a 64 bit supportate: **Centos 6.4 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 esegue Red Hat compatibile kernel o estremamente affidabile e Enterprise Kernel versione 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Le regole del firewall nel computer protetto dovrebbero consentire loro di raggiungere la configurazione e i server di destinazione principale in Azure.<br/><br/> file /etc/hosts nel computer protetto devono contenere voci da associano il nome host locale a indirizzi IP associati a tutte le NIC <br/><br/> Se si desidera connettersi a una macchina virtuale Azure che eseguono Linux dopo failover utilizzando un Secure Shell client (ssh), assicurarsi che il servizio di Secure Shell sul computer protetto sia impostato per avviare automaticamente all'avvio di sistema e che le regole firewall consentano un ssh la connessione.<br/><br/> Nome host, punti di montaggio, i nomi dei dispositivi e percorsi di sistema Linux e nomi di file (ad esempio/ecc /; /usr) devono essere solo in lingua inglese.<br/><br/> Per attivare la protezione per i computer locale con lo spazio di archiviazione seguente:-<br>File di sistema: EXT3, ETX4, ReiserFS, XFS<br>Percorsi multipli dispositivo software mapping (percorsi multipli)<br>Gestione di volume: LVM2<br>Server fisici con lo spazio di archiviazione di HP CCISS controller non sono supportate.
**Terze parti** | Alcuni componenti di distribuzione in questo scenario dipendono dal software di terze parti per il corretto funzionamento. Per un elenco completo, vedere [informazioni e note legali sul software di terze parti](#third-party)


### <a name="network-connectivity"></a>Connettività di rete

Sono disponibili due opzioni quando si configura la connettività di rete tra il sito locale e la rete virtuale Azure in cui sono distribuiti i componenti dell'infrastruttura (server di configurazione, server di destinazione master). È necessario decidere quale opzione di connettività di rete utilizzare prima di distribuire il server di configurazione. È necessario scegliere questa impostazione in fase di distribuzione. Non può essere modificato in un secondo momento.

**Internet:** La comunicazione e la replica dei dati tra i server locale (server di processo, computer protetto) e i server dei componenti di Azure infrastruttura (server di configurazione, server di destinazione master) accade tramite una connessione SSL/TLS protetta locali per gli endpoint pubblici nei server di destinazione dalla configurazione e schema. (L'unica eccezione è la connessione tra il server di processo e quello di destinazione master sulla porta TCP 9080 che viene crittografata. Solo controllo relative al protocollo di replica per la configurazione di replica scambio di informazioni sulla connessione.)

![Diagramma distribuzione internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: la comunicazione e la replica dei dati tra i server locale (server di processo, computer protetto) e i server dei componenti di Azure infrastruttura (server di configurazione, server di destinazione master) si dispone di una connessione VPN tra la rete locale e la rete virtuale Azure distribuito in cui il server di configurazione e i server di destinazione principale. Assicurarsi che la rete locale sia connesso alla rete virtuale Azure tramite una connessione ExpressRoute o una connessione VPN da sito.

![Diagramma distribuzione VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>Passaggio 1: Creare un archivio

1. Accedere al [portale di gestione](https://portal.azure.com).


2. Espandere **I servizi di dati** > **Servizi di recupero** e fare clic su **Archivio di ripristino del sito**.


3. Fare clic su **Crea nuova** > **Creazione rapida**.

4. In **nome**immettere un nome descrittivo per identificare l'archivio.

5. Nell' **area geografica**, selezionare la località geografica per l'archivio. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](https://azure.microsoft.com/pricing/details/site-recovery/)

6. Fare clic su **Crea archivio**.

    ![Nuovo archivio](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Verificare se sulla barra di stato per verificare che l'archivio è stato creato correttamente. L'archivio verrà elencato come **attivo** nella pagina principale dei **Servizi di recupero** .

## <a name="step-2-deploy-a-configuration-server"></a>Passaggio 2: Distribuire un server di configurazione

### <a name="configure-server-settings"></a>Configurare le impostazioni del server

1. Nella pagina **Servizi di recupero** , fare clic su archivio per aprire la pagina avvio rapido. Guida introduttiva può essere aperti anche in qualsiasi momento tramite l'icona.

    ![Icona di avvio veloce](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. Nell'elenco a discesa selezionare **tra un sito locale con server VMware/fisici e Azure**.
3. In **Preparazione Target(Azure) risorse** fare clic su **Distribuire Server di configurazione**.

    ![Distribuire server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. Nella **Nuova configurazione Server dettagli** specificare:

    - Un nome per il server di configurazione e le credenziali per la connessione.
    - Nel tipo di connettività di rete elenco a discesa selezionare **Pubblica Internet** o **VPN**. Si noti che non è possibile modificare questa impostazione dopo essere stato applicato.
    - Selezionare la rete Azure in cui il server deve trovarsi. Se si usa effettuare VPN si sa Azure rete sia connesso alla rete locale come previsto. 
    - Specificare l'indirizzo IP interno e subnet assegnati al server. Si noti che i primi quattro indirizzi IP in qualsiasi subnet sono riservati per l'utilizzo di Azure interno. Usare altri indirizzi IP disponibili.
    
    ![Distribuire server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Quando si fa clic su **OK** una macchina virtuale di A3 standard in base a un'immagine della raccolta siti Azure ripristino di Windows Server 2012 R2 verrà creato nell'abbonamento per il server di configurazione. Viene creata come la prima istanza di un nuovo servizio cloud. Se si è scelto di connettersi tramite internet servizio cloud viene creato con un indirizzo IP pubblico riservato. È possibile monitorare lo stato di avanzamento nella scheda **processi** .

    ![Monitorare l'avanzamento](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  Se ci si connette tramite internet, dopo il server di configurazione è nota distribuito l'indirizzo IP pubblico assegnata a tale pagina **macchine virtuali** nel portale di Azure. Quindi su **endpoint** nota scheda la porta HTTPS pubblica mappati alla porta 443 di privato. Quando si registra la destinazione master e i server con il server di configurazione, sarà necessario queste informazioni in un secondo momento. Il server di configurazione viene distribuito con questi endpoint:

    - HTTPS: Una porta pubblica viene utilizzata per coordinare le comunicazioni tra computer server componente e Azure tramite internet. Privato porta 443 viene utilizzata per coordinare le comunicazioni tra computer server componente e Azure tramite VPN.
    - Personalizzato: Una porta pubblica viene utilizzata per la comunicazione strumento failback tramite internet. Porta privata 9443 viene utilizzata per la comunicazione strumento failback su VPN.
    - PowerShell: Porta privata 5986
    - Desktop remoto: porta privata 3389
    
    ![Punti finali macchine Virtuali](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] Non eliminare o modificare il numero di porta pubblico o privato di qualsiasi endpoint creati durante la distribuzione di configurazione server.

Il server di configurazione viene distribuito in un servizio cloud di Azure creati automaticamente con un indirizzo IP riservato. L'indirizzo riservato è necessaria per assicurare che l'indirizzo IP del servizio di configurazione server cloud rimane inalterato dopo il riavvio di macchine virtuali (incluso il server di configurazione) per il servizio cloud. L'indirizzo IP pubblico riservato dovranno essere manualmente riservati quando il server di configurazione è state rimosse o verrà rimosso riservato. Esiste un limite predefinito di 20 pubblico gli indirizzi IP prenotati per abbonamento. [Altre informazioni](../virtual-network/virtual-networks-reserved-private-ip.md) sugli indirizzi IP riservati. 

### <a name="register-the-configuration-server-in-the-vault"></a>Registrare il server di configurazione nell'archivio di

1. Nella pagina **Guida introduttiva** fare clic su **Risorse di destinazione preparare** > **scaricare una chiave di registrazione**. Il file di chiave viene generato automaticamente. È valida per 5 giorni dopo la generazione. Copiarlo nel server di configurazione.
2. In **macchine virtuali di** selezionare il server di configurazione dall'elenco di macchine virtuali. Aprire la scheda **Dashboard** e fare clic su **Connetti**. **Aprire** il file scaricato RDP agli utenti di accedere al server di configurazione utilizzando Desktop remoto. Se si usa VPN, utilizzare l'indirizzo IP interno, l'indirizzo specificato quando si distribuisce il server di configurazione, per una connessione Desktop remoto dal sito locale. La configurazione guidata Server di ripristino configurazione di Azure sito viene eseguita automaticamente quando si accede per la prima volta.

    ![Registrazione](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. **Installazione del Software di terze parti** fare clic sui **accetto** per scaricare e installare MySQL.

    ![Installazione di MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. In **Informazioni Server MySQL** creare le credenziali per accedere all'istanza del server MySQL.

    ![Credenziali MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. Nelle **Impostazioni di Internet** specificare come il server di configurazione si connetterà a internet. Si noti che:

    - Se si desidera utilizzare un proxy personalizzato, che è necessario configurarlo prima di installare il Provider.
    - Quando si fa clic su **Avanti** un test verrà eseguito per verificare la connessione proxy.
    - Se si utilizza un proxy personalizzato o il proxy predefinito richiede l'autenticazione che è necessario immettere i dettagli del proxy, inclusi l'indirizzo, porta e le credenziali.
    - L'URL seguente deve essere accessibile tramite il proxy:
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Se si dispone basate su indirizzo IP regole del firewall assicurano che le regole siano impostate per consentire la comunicazione dal server di configurazione per gli indirizzi IP descritto in [Intervalli di indirizzi IP del Data Center del Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) e HTTPS protocol (443). È necessario intervalli di indirizzi IP lista bianca dell'area Azure che si prevede di utilizzare e che usa ovest.

    ![Registrazione del proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. In **Impostazioni di localizzazione messaggio di errore del Provider** specificare in quali lingue messaggi di errore vengano visualizzati.

    ![Registrazione di messaggio di errore](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. Nella **Registrazione di ripristino di Azure sito** individuare e selezionare il file di chiave che sono stati copiati nel server.

    ![Registrazione del file chiave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. Nella pagina di completamento della creazione guidata selezionare queste opzioni:

    - Selezionare **Avvia finestra di dialogo Gestione Account** per specificare che deve aprire la finestra di dialogo Gestisci account dopo aver completato la procedura guidata.
    - Selezionare **Crea un'icona sul desktop per Cspsconfigtool** per aggiungere un collegamento sul desktop sul server di configurazione in modo che è possibile aprire la finestra di dialogo **Gestisci account** in qualsiasi momento senza dover eseguire nuovamente la procedura guidata.

    ![Completare la registrazione](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Fare clic su **Fine** per completare la procedura guidata. Viene generata una frase. Copiarlo in un percorso sicuro. È necessario eseguire l'autenticazione e registrare il processo e i server di destinazione master con il server di configurazione. Viene usato anche per assicurare l'integrità dei canali in communications server di configurazione. È possibile rigenerare la passphrase, ma sarà quindi necessario registrare nuovamente la destinazione master e diagrammi di processo server mediante la nuova passphrase.

    ![Passphrase](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Dopo la registrazione del server di configurazione verrà elencato nella pagina **Configurazione server** nell'archivio di.

### <a name="set-up-and-manage-accounts"></a>Configurare e gestire gli account

Durante la distribuzione il ripristino del sito richiede le credenziali per le azioni seguenti:

- Un account di VMware per thatSite ripristino possibile automaticamente macchine virtuali di individuazione sui server vCenter o vSphere host. 
- Quando si aggiunge macchine per la protezione, in modo che il ripristino del sito è possibile installare il servizio di mobilità su di essi.

Dopo aver registrato il server di configurazione è possibile aprire la finestra di dialogo **Gestisci account** per aggiungere e gestire gli account che devono essere utilizzati per le azioni. Esistono due modi per eseguire questa operazione:

- Aprire il collegamento che si è scelto di creati per la finestra di dialogo nell'ultima pagina del programma di installazione per il server della configurazione (cspsconfigtool).
- Aprire la finestra di dialogo di completare la configurazione dell'installazione di server.

1. In **Gestisci account** fare clic su **Aggiungi Account**. È anche possibile modificare ed eliminare gli account esistenti.

    ![Gestire gli account](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. In **Dettagli conto** specificare un nome di account da utilizzare in Azure e credenziali (nome di dominio/utente). 

    ![Gestire gli account](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Connettersi al server di configurazione 

Esistono due modi per connettersi al server della configurazione:

- Tramite una connessione di ExpressRoute o VPN a siti
- Via internet 

Si noti che:

- Una connessione a internet utilizza i punti finali della macchina virtuale in combinazione con l'indirizzo IP pubblico virtuale del server.
- Una connessione VPN utilizzato l'indirizzo IP interno del server con le porte privato endpoint.
- È una copia unica decisione per decidere se per la connessione (dati controllo e replica) dal server in locale ai server dei vari componenti (server di configurazione, server di destinazione master) in esecuzione in Azure una connessione VPN o su internet. Non è possibile modificare questa impostazione in un secondo momento. Se si è necessario ridistribuire lo scenario e riproteggere computer.  


## <a name="step-3-deploy-the-master-target-server"></a>Passaggio 3: Distribuire il server di destinazione principale

1. Fare clic su **Preparare risorse Target(Azure)** > **server di destinazione master Distribuisci**.
2. Specificare le credenziali e i dettagli del server di destinazione master. Il server verrà distribuito nella stessa rete Azure il server di configurazione. Quando si fa clic per completare una macchina virtuale Azure verrà creato con un'immagine della raccolta Windows o Linux.

    ![Impostazioni del server di destinazione](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Si noti che i primi quattro indirizzi IP in qualsiasi subnet sono riservati per l'utilizzo di Azure interno. Specificare altri indirizzi IP disponibili.

>[AZURE.NOTE] Selezionare DS4 Standard durante la configurazione di protezione per carichi di lavoro che richiedono coerente prestazioni i/o alta o bassa latenza per i/o carichi di lavoro in modo intensivo tramite [Account di archiviazione Premium](../storage/storage-premium-storage.md).


3. Windows server di destinazione macchine Virtuali viene creato con questi endpoint master. Si noti che vengono creati i punti finali pubblici solo se la connessione tramite internet.

    - Personalizzato: Porta pubblica utilizzata dal server di processo per inviare i dati di replica tramite internet. Porta privata 9443 viene utilizzata dal server di processo per inviare i dati di replica al server di destinazione master tramite VPN.
    - Personalizzato 1: Porta pubblica utilizzata dal server di processo per l'invio dei metadati tramite internet. Porta privata 9080 viene utilizzata dal server di processo per inviare i metadati al server di destinazione master tramite VPN.
    - PowerShell: Porta privata 5986
    - Desktop remoto: porta privata 3389

4. Viene creato un server di destinazione master Linux macchine Virtuali con questi endpoint. Si noti che vengono creati i punti finali pubblici solo se ci si connette tramite internet.

    - Personalizzato: Porta pubblica utilizzata dal server di processo per l'invio di dati di replica tramite internet. Porta privata 9443 viene utilizzata dal server di processo per inviare i dati di replica al server di destinazione master tramite VPN.
    - Personalizzato 1: Porta pubblica viene utilizzata il server di processo per l'invio dei metadati tramite internet. Porta privata 9080 viene utilizzata dal server di processo per inviare i metadati al server di destinazione master tramite VPN
    - SSH: Porta privata 22

    >[AZURE.WARNING] Non eliminare o modificare il numero di porta pubblico o privato di uno dei relativi endpoint creato durante la distribuzione di server di destinazione principale.

5. In attesa di **macchine virtuali** per la macchina virtuale iniziare.

    - Se si tratta di una nota di Windows server verso il basso i dettagli sul desktop remoti.
    - Se si tratta di un server Linux e ci si connette in rete VPN annotare l'indirizzo IP interno della macchina virtuale. Se ci si connette tramite internet annotare l'indirizzo IP pubblico.

6.  Accedere al server per completare l'installazione e registrare con il server di configurazione. 
7.  Se si esegue Windows:

    1. Avviare una connessione desktop remoto alla macchina virtuale. La prima volta che si effettua l'accesso uno script verrà eseguito in una finestra di PowerShell. Non chiuderlo. Dopo aver completato lo strumento di configurazione dell'agente Host viene aperto automaticamente per registrare il server.
    2. Nel **File di configurazione Host agente** specificare l'indirizzo IP interno del server di configurazione e la porta 443. Anche se non ci si connette in rete VPN perché la macchina virtuale è associata alla stessa rete Azure come il server di configurazione, è possibile utilizzare l'indirizzo interno e la porta 443 di privato. Lasciare **Utilizzare HTTPS** abilitato. Immettere la passphrase per il server di configurazione indicato in precedenza. Fare clic su **OK** per registrare il server. È possibile ignorare le opzioni di NAT. Non sono abituati.
    3. Se l'obiettivo di unità di conservazione stimata è più di 1 TB è possibile configurare il volume di conservazione (r) usando un disco virtuale e [spazi di archiviazione](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Server di destinazione principale di Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Se si esegue Linux:
    1. Assicurarsi di che avere installato la più recente Linux Integration Services (elenco) prima di installare il server di destinazione principale. È possibile trovare l'ultima versione di elenco insieme istruzioni su come installare [qui](https://www.microsoft.com/download/details.aspx?id=46842). Riavviare il computer dopo l'elenco di installazione.
    2. In **risorse di preparare l'ambiente di destinazione (Azure)** fare clic su **scaricare e installare software aggiuntivo (solo per il Server di destinazione Linux schema)**. Copiare il file scaricato macchina virtuale tramite un client sftp. In alternativa è possibile accedere al server di destinazione master linux distribuito e utilizzare *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* per scaricare il file.
    2. Accedere al server utilizzando un client Secure Shell. Se si è connessi alla rete Azure su VPN usare l'indirizzo IP interno. In caso contrario, utilizzare l'indirizzo IP esterno e l'endpoint pubblica SSH.
    3. Estrarre i file dal programma di installazione gzipped eseguendo: **catrame – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![server di destinazione master Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. Verificare che sia attiva nella directory in cui è stato estratto il contenuto del file.
    5. Copiare la passphrase server di configurazione in un file locale utilizzando il comando * *eco* `<passphrase>` * > passphrase.txt**
    6. Eseguire il comando "**sudo. installa -t entrambi - a -R ospitare /usr/local/ASR -d MasterTarget -i* `<Configuration server internal IP address>` * -p 443 -s y - c https -P passphrase.txt**".

    ![Registrare il server di destinazione](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Attendere alcuni minuti (10-15) e nella pagina verificare che il server di destinazione master sia elencato registrato nel **server** > scheda **Server dettagli sul** **Server di configurazione** . Se si esegue Linux e non ha registrato rieseguire l'host strumenti di configurazione dal /usr/local/ASR/Vx/bin/hostconfigcli. È necessario impostare le autorizzazioni di accesso eseguendo chmod come radice.

    ![Verificare il server di destinazione](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Può richiedere fino a 15 minuti dopo la registrazione è completata per il server di destinazione master da includere nel portale. Per aggiornare immediatamente, fare clic su **Aggiorna** nella pagina **Configurazione server** .

## <a name="step-4-deploy-the-on-premises-process-server"></a>Passaggio 4: Distribuire il server di processo locale

Prima di iniziare, è consigliabile configurare un indirizzo IP statico nel server di processo in modo che ha garantito sia persistente dopo il riavvio.

1. Fare clic su Quick Start > **installare processo Server in locale** > **scaricare e installare il server di processo**.

    ![Installare server di processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copiare il file zip scaricati nel server in cui si intende installare il server di processo. Il file zip contiene due file di installazione:

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. Decomprimere nell'archivio e copiare i file di installazione in un percorso nel server.
4. Eseguire il **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** installazione file e seguire le istruzioni. Consente di installare componenti di terze parti necessari per la distribuzione.
5. Eseguire quindi **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. Nella pagina **Modalità di Server** selezionare **Processo Server**.
7. Nella pagina **Dettagli sull'ambiente** eseguire le operazioni seguenti:


    - Se si desidera proteggere VMware virtuale computer, fare clic su **Sì**
    - Se si desidera proteggere server fisici, quindi non necessario vCLI VMware installati nel server processo. Fare clic su **No** e continuare.

8. Durante l'installazione di VMware vCLI, tenere presente quanto segue:

    - **È supportata solo VMware vSphere CLI 5.5.0**. Il server di processo non funziona con le altre versioni o aggiornamenti di vSphere CLI.
    - Scaricare vSphere CLI 5.5.0 da [qui.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - Se è stato installato vSphere CLI prima che si inizia al server di processo di installazione e configurazione non lo rileva, attendere alcuni minuti prima di provare di nuovo il programma di installazione. In questo modo che tutte le variabili di ambiente necessarie per il rilevamento CLI vSphere sono state inizializzate correttamente.

9.  Nella **Finestra selezione per Server** selezionare la scheda di rete che il server di processo deve utilizzare.

    ![Selezionare la scheda](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. In **informazioni Server di configurazione**:

    - Per l'indirizzo IP e porte, se ci si connette in rete VPN specificare l'indirizzo IP interno del server di configurazione e la porta 443. In caso contrario specificare l'indirizzo IP virtuale pubblico e mappate endpoint HTTP pubblico.
    - Digitare la passphrase del server di configurazione.
    - Deselezionare **firma software servizio mobilità verificare** se si desidera disattivare la verifica quando si utilizza push automatica per installare il servizio. Verifica della firma deve connettività internet dal server di processo.
    - Fare clic su **Avanti**.

    ![Eseguire la registrazione del server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. In **unità di installazione selezionare** selezionare un'unità cache. Il server di processo deve un'unità di cache con almeno 600 GB di spazio libero. Fare clic su **Installa**. 

    ![Eseguire la registrazione del server di configurazione](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Si noti che potrebbe essere necessario riavviare il server per completare l'installazione. In **Server di configurazione** > **Server dettagli** verificare che il server di processo viene visualizzata e viene registrato correttamente l'archivio.

>[AZURE.NOTE]Può richiedere fino a 15 minuti dopo la registrazione è completata per il server di processo vengono visualizzati come indicato nell'ambito del server di configurazione. Per aggiornare immediatamente, aggiornare il server di configurazione facendo clic sul pulsante Aggiorna nella parte inferiore della pagina Configurazione server
 
![Convalidare il server di processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Se si non disabilita la verifica della firma per il servizio di mobilità al momento della registrazione il server di processo è possibile eseguire in un secondo momento nel modo seguente:

1. Accedere al server di processo come amministratore e aprire il file C:\pushinstallsvc\pushinstaller.conf per la modifica. Nella sezione **[PushInstaller.transport]** aggiungere questa riga: **SignatureVerificationChecks = "0"**. Salvare e chiudere il file.
2. Riavviare il servizio InMage PushInstall.


## <a name="step-5-update-site-recovery-components"></a>Passaggio 5: Componenti il ripristino del sito di aggiornamento

Componenti di ripristino del sito vengono aggiornati nel tempo. Quando sono disponibili nuovi aggiornamenti è consigliabile installarli nell'ordine seguente:

1. Server di configurazione
2. Server di processo
3. Server di destinazione principale
4. Strumento failback (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Ottenere e installare gli aggiornamenti


1. È possibile ottenere gli aggiornamenti per la configurazione, grafici e server target master nel **Dashboard**di il ripristino del sito. Per l'installazione di Linux estrarre i file dal programma di installazione gzipped ed eseguire il comando "sudo. Installa" per installare l'aggiornamento.
2. [Scaricare](http://go.microsoft.com/fwlink/?LinkID=533813) l'aggiornamento più recente per tool(vContinuum) Failback.
3. Se si esegue macchine virtuali o server fisici che già installato il servizio di mobilità, è possibile ottenere gli aggiornamenti per il servizio come indicato di seguito:

    - **Opzione 1**: scaricare gli aggiornamenti:
        - [Windows Server (solo versione a 64 bit)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [CentOS 6.4,6.5,6.6 (solo versione a 64 bit)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle Enterprise Linux 6.4,6.5 (solo versione a 64 bit)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 (solo versione a 64 bit)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - Dopo l'aggiornamento del server di processo la versione aggiornata del servizio mobilità saranno disponibile nella cartella C:\pushinstallsvc\repository nel server di processo.
    - **Opzione 2**: se si dispone di un computer con una versione precedente del servizio mobilità installato, è possibile aggiornare automaticamente il servizio di mobilità del computer dal portale di gestione.

        1. Assicurarsi che il server di processo viene aggiornato.
        2. Assicurarsi che il computer protetto conformità con i [Prerequisiti](#install-the-mobility-service-automatically) per inserire automaticamente il servizio di Mobility, in modo che l'aggiornamento funziona come previsto.
        2. Selezionare il gruppo di protezione, evidenziare il computer protetto e fare clic su **servizio di aggiornamento mobilità**. Questo pulsante è disponibile solo se è presente una versione più recente del servizio di mobilità. 

            ![Selezionare il server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

In account selezionare specificare l'account di amministratore per essere utilizzato per aggiornare il servizio di mobilità sul server protetto. Fare clic su OK e attendere il completamento del processo attivato.


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Passaggio 6: Aggiungere vCenter server o vSphere host

1. Fare clic su **server** > **Configurazione server** > server di configurazione >**Aggiungi vCenter Server** per aggiungere un host server o vSphere vCenter.

    ![Selezionare il server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Specificare i dettagli per il server o host e selezionare il server di processo che verrà utilizzato per individuarlo.

    - Se il server vCenter non è in esecuzione sulla porta 443 predefinita specificare il numero di porta in cui viene eseguito il server vCenter.
    - Il server di processo deve essere sulla stessa rete l'host del server/vSphere vCenter e abbia VMware vSphere CLI 5.5.0 installato.

    ![impostazioni del server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Al termine dell'individuazione server vCenter verranno elencati sotto i dettagli di configurazione server.

    ![impostazioni del server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Se si usa un account di amministratore non aggiungere il server o host, assicurarsi che l'account con privilegi seguenti:

    - account vCenter deve avere Data Center, archivio dati, cartella, Host, rete, delle risorse, lo spazio di archiviazione visualizzazioni, macchina virtuale e privilegi Cambia distribuito vSphere abilitati.
    - vSphere ospitare account deve avere Data Center, archivio dati, cartella, Host, rete, risorsa, macchina virtuale e i privilegi di parametro distribuito vSphere abilitati



## <a name="step-7-create-a-protection-group"></a>Passaggio 7: Creare un gruppo di protezione

1. Aprire **Elementi protetti** > **Gruppo protezione** > **Crea gruppo protezione dati**.

    ![Crea gruppo protezione](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. Nella pagina **Specificare le impostazioni di protezione gruppo** specificare un nome per il gruppo e selezionare il server di configurazione in cui si desidera creare il gruppo.

    ![Impostazioni dei gruppi di protezione](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. Nella pagina **Specificare le impostazioni di replica** configurare le impostazioni di replica che verranno utilizzate per tutti i computer nel gruppo.

    ![Replica dei gruppi di protezione](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Impostazioni:
    - **La coerenza macchine Virtuali di più**: se si attivare questa opzione crea punti di ripristino coerente applicazione condivisa nei computer del gruppo di protezione. Questa impostazione è più rilevante quando tutti i computer del gruppo di protezione sono in esecuzione il carico di lavoro stesso. Tutti i computer verranno ripristinati allo stesso punto dati. Disponibile solo per i server di Windows.
    - **Soglia rilasciato**: verranno generati avvisi quando la replica di protezione dati continui rilasciato supera il valore di soglia rilasciato configurato.
    - **Punto di ripristino conservazione**: specifica la finestra di conservazione. Computer protetto possono essere recuperate in un punto qualsiasi all'interno di questa finestra.
    - **Applicazione coerente frequenza snapshot**: consente di specificare la frequenza verranno creati i punti di ripristino contenente snapshot coerenti con l'applicazione.

È possibile monitorare il gruppo di protezione che vengono creati nella pagina **Elementi protetti** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Passaggio 8: Configurare i computer che si desidera proteggere

È necessario installare il servizio di mobilità in macchine virtuali e server fisici che si desidera proteggere. È possibile eseguire in due modi:

- Push e installare automaticamente il servizio in ogni computer dal server di processo.
- Installare manualmente il servizio. 

### <a name="install-the-mobility-service-automatically"></a>Installare automaticamente il servizio di mobilità

Quando si aggiungono computer a un gruppo di protezione il servizio di mobilità viene inserito automaticamente e installato in ogni computer per il server di processo. 

**Push installare automaticamente il servizio mobilità sui server di Windows:** 

1. Installare gli aggiornamenti più recenti per il server di processo, come descritto [passaggio 5: installare gli aggiornamenti più recenti](#step-5-install-latest-updates)e assicurarsi che il server di processo sia disponibile. 
2. Verificare la connettività di rete tra il computer di origine e il server di processo, e che il computer di origine è accessibile dal server di processo.  
3. Configurare Windows firewall per consentire a **condivisione File e stampanti** e **Strumentazione gestione Windows**. In impostazioni di Windows Firewall, selezionare l'opzione "Consenti un'app o con Firewall" e selezionare le applicazioni, come illustrato nella figura seguente. Per i computer che appartengono a un dominio è possibile configurare i criteri del firewall con un oggetto Criteri di gruppo.

    ![Impostazioni del firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. L'account utilizzato per eseguire l'installazione push deve essere nel gruppo Administrators del computer in cui che si desidera proteggere. Queste credenziali vengono utilizzate solo per l'installazione push del servizio di mobilità ed è necessario specificare quando si aggiunge un computer a un gruppo di protezione.
5. Se l'account fornito non è un account di dominio è necessario disattivare il controllo di accesso remoto degli utenti del computer locale. Per eseguire questo aggiungere la voce del Registro di sistema di LocalAccountTokenFilterPolicy DWORD con un valore di 1 in HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Per aggiungere la voce del Registro di sistema da un cmd Apri CLI o powershell e immettere **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Push installare automaticamente il servizio mobilità server Linux:**

1. Installare gli aggiornamenti più recenti per il server di processo, come descritto [passaggio 5: installare gli aggiornamenti più recenti](#step-5-install-latest-updates)e assicurarsi che il server di processo sia disponibile.
2. Verificare la connettività di rete tra il computer di origine e il server di processo, e che il computer di origine è accessibile dal server di processo.  
3. Verificare che l'account è un utente radice nel server Linux di origine.
4. Assicurarsi che il file /etc/hosts nel server Linux di origine contiene le voci di cui eseguire il mapping del nome host locale a indirizzi IP associati a tutte le NIC.
5. Installare l'ultima openssh, openssh server, pacchetti openssl nel computer che si desidera proteggere.
6. Assicurarsi che SSH sia abilitato e in esecuzione su porta 22. 
7. Autenticazione SFTP sottosistema e la password nel file sshd_config come indicato di seguito: 

    - ) accedere come radice.
    - b) nel file di /etc/ssh/sshd_config, individuare la riga che inizia con **PasswordAuthentication**.
    - c) rimuovere il commento e modificare il valore "no" a "Sì".

        ![Linux mobilità](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d) individuare la riga che inizia con sottosistema e rimuovere il commento.
    
        ![Mobilità push Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. Assicurarsi che la variante di origine computer Linux è supportata. 
 
### <a name="install-the-mobility-service-manually"></a>Installare manualmente il servizio di mobilità

Pacchetti software usati per installare il servizio mobilità disponibili sul server di processo in C:\pushinstallsvc\repository. Accedere al server di processo e copiare il pacchetto di installazione appropriato per il computer di origine in base alla tabella riportata di seguito:-

| Sistema operativo di origine                               | Pacchetto di servizio mobilità nel server di processo                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (solo versione a 64 bit)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4, 6.5, 6.6 (solo versione a 64 bit)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (solo versione a 64 bit)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (solo versione a 64 bit)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**Per installare il servizio di mobilità manualmente in un server di Windows**, eseguire le operazioni seguenti:

1. Copiare il pacchetto di **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** il percorso della directory server processo elencato nella tabella sopra al computer di origine.
2. Installare il servizio mobilità eseguendo il file eseguibile sul computer di origine.
3. Seguire le istruzioni del programma di installazione.
4. Selezionare **servizio mobilità** equivalenti al ruolo e fare clic su **Avanti**.
    
    ![Installare il servizio mobilità](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Lasciare la directory di installazione come percorso di installazione predefinito e fare clic su **Installa**.
6. Nel **File di configurazione Host agente** specificare l'indirizzo IP e porte HTTPS del server di configurazione.

    - Se ci si connette tramite internet specificare l'indirizzo IP virtuale pubblico e pubblica endpoint HTTPS come la porta.
    - Se ci si connette in rete VPN specificare l'indirizzo IP interno e per la porta 443. Lascia **Utilizzare HTTPS** selezionata.

    ![Installare il servizio mobilità](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Specificare la passphrase server configurazione e fare clic su **OK** per registrare il servizio di mobilità con il server di configurazione.

**Per eseguire dalla riga di comando:**

1. Copiare il file "C:\connection.passphrase" sul server la passphrase tra il CX ed eseguire il comando. In questo esempio CX i 104.40.75.37 e la porta HTTPS è 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installare il servizio di mobilità manualmente in un server Linux**:

1. Copiare l'archivio tar appropriato in base alla tabella precedente, dal server di processo al computer di origine.
2. Aprire un'applicazione di shell ed estrarre l'archivio tar compressi in un percorso locale eseguendo`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio tar tramite l'immissione di *`echo <passphrase> >passphrase.txt`* dalla shell.
4. Installare il servizio mobilità immettendo *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Specificare l'indirizzo IP e porte:

    - Se si esegue la connessione al server di configurazione tramite internet specificare la configurazione server virtuale indirizzo IP pubblico e pubblica endpoint HTTPS in `<IP address>` e `<port>`.
    - Se ci si connette tramite una connessione VPN specificare l'indirizzo IP interno e 443.

**Per eseguire dalla riga di comando**:

1. Copiare il file "passphrase.txt" sul server la passphrase tra il CX ed eseguire questo comandi. In questo esempio CX i 104.40.75.37 e la porta HTTPS è 62519:

Per installare in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Per installare nel server di destinazione:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Quando si aggiungono computer a un gruppo di protezione già in esecuzione una versione appropriata del servizio di mobilità quindi l'installazione push viene ignorato.


## <a name="step-9-enable-protection"></a>Passaggio 9: Abilita protezione

Per abilitare la protezione aggiungere macchine virtuali e server fisici a un gruppo di protezione. Prima di iniziare, tenere presente che:

- Macchine virtuali vengono rilevate ogni 15 minuti e potrebbe richiedere fino a 15 minuti per poter comparire nel ripristino del sito di Azure dopo l'individuazione.
- Le modifiche dell'ambiente del computer virtuale (ad esempio installazione degli strumenti di VMware) anche possono richiedere fino a 15 minuti per l'aggiornamento nel ripristino del sito.
- È possibile archiviare l'ultima volta individuato il campo **contatto ultima** per l'host server/ESXi vCenter nella pagina **Configurazione server** .
- Se si dispone di un gruppo di protezione già stato creato e aggiungere un host di Server o ESXi vCenter successivamente, bastano quindici minuti per il portale il ripristino del sito di Azure aggiornare e per macchine virtuali venga elencato nella finestra di dialogo **aggiunta a un gruppo di protezione** .
- Se si preferisce proseguire con l'aggiunta di computer al gruppo di protezione senza attendere che l'individuazione pianificata, evidenziare il server di configurazione (non selezionarla) e fare clic sul pulsante **Aggiorna** .
- Quando si aggiunge macchine virtuali o computer fisico a un gruppo di protezione, il server di processo inserisce automaticamente e installa il servizio mobilità nel server di origine, se non è già installato it.
- Per l'inserimento automatico meccanismo per l'uso assicurarsi che è stato configurato il computer protetto come descritto nel passaggio precedente.

Aggiungere macchine come segue:

1. Fare clic su **elementi protetti** > **Gruppo protezione** > **macchine** > **aggiungere computer**. È buona norma è consigliabile che i gruppi di protezione dovrebbero rispecchiare i carichi di lavoro in modo che si aggiunge un'applicazione specifica al gruppo stesso computer.
2. In **macchine virtuali selezionare** se si sta proteggere server fisici, della procedura guidata **Aggiungere computer fisici** fornire l'indirizzo IP e un nome descrittivo. Selezionare quindi nei sistemi operativi.

    ![Aggiungere server V Center](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. In **Seleziona macchine virtuali** se si sta proteggere macchine virtuali VMware, selezionare un server vCenter che gestisce le macchine virtuali (o host EXSi in cui vengono eseguiti) e quindi selezionare il computer.

    ![Aggiungere server V Center](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. In **Specificare risorse di destinazione** selezionare il server di destinazione principale e lo spazio di archiviazione da utilizzare per la replica e selezionare se è necessario utilizzare le impostazioni per tutti i carichi di lavoro. Selezionare [Account di archiviazione Premium](../storage/storage-premium-storage.md) durante la configurazione di protezione per carichi di lavoro che richiedono elevate IO coerenti e bassa latenza per carichi di lavoro in modo intensivo IO. Se si desidera utilizzare un account di archiviazione Premium per i dischi carico di lavoro, è necessario utilizzare la destinazione di schema della serie DS. È possibile utilizzare lo spazio di archiviazione Premium dischi con destinazione schema di serie DS.

    >[AZURE.NOTE] Non è supportato lo spostamento degli account di archiviazione creati per il [nuovo portale Azure](../storage/storage-create-storage-account.md) attraverso gruppi di risorse.

    ![server vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. In **Impostazione account** selezionare l'account da usare per l'installazione del servizio di mobilità computer protetto. Le credenziali dell'account sono necessari per l'installazione automatica del servizio di mobilità. Se non è possibile selezionare un account assicurarsi configurare una come descritto nel passaggio 2. Si noti che l'account non è possibile accedere da Azure. Per Windows server l'account deve disporre dei privilegi di amministratore nel server di origine. Per Linux l'account deve essere radice.

    ![Credenziali Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Fare clic sul segno di spunta per completare l'aggiunta di computer al gruppo di protezione e avviare la replica iniziale per ogni computer. È possibile monitorare lo stato nella pagina **dei processi** .

    ![Aggiungere server V Center](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. È inoltre possibile monitorare lo stato di protezione facendo clic su **Elementi protetti** > nome del gruppo di protezione > **macchine virtuali** . Dopo il completamento della replica iniziale e i computer sono la sincronizzazione dei dati visualizzarle stato **protetta** .

    ![Processi macchina virtuale](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>Proprietà computer set protetto

1. Dopo una macchina stato **protetta** è possibile configurare le proprietà di failover. I dettagli del gruppo di protezione selezionare la macchina e aprire la scheda **Configura** .
2. È possibile modificare il nome che sarà assegnato al computer di in Azure dopo il failover e le dimensioni di Azure macchina virtuale. È inoltre possibile selezionare la rete Azure in cui verrà connesso il computer dopo il failover.

    > [AZURE.NOTE] [Migrazione delle reti](../resource-group-move-resources.md) attraverso gruppi di risorse all'interno della stessa sottoscrizione o sottoscrizioni non è supportata per le reti utilizzate per la distribuzione il ripristino del sito.

    ![Impostare le proprietà di macchina virtuale](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Si noti che:

- Il nome del computer Azure deve essere conforme ai requisiti di Azure.
- Per impostazione predefinita replicate macchine virtuali di Azure non sono connessi a una rete Azure. Se si desidera replicare macchine virtuali di comunicare assicurarsi che impostare la stessa rete Azure per poterli.
- Se si ridimensiona un volume su una macchina virtuale VMware server fisico rileva lo stato critico. Se è necessario modificare la dimensione, eseguire le operazioni seguenti:

    - a) modificare l'impostazione di dimensioni.
    - b) nella scheda **macchine virtuali** , selezionare la macchina virtuale e fare clic su **Rimuovi**.
    - c) in **Rimuovi macchina virtuale** selezionare l'opzione **Disattiva la protezione (usare per il ripristino di eseguire il drill- e volume ridimensionare)**. Questa opzione disattiva la protezione ma conserva i punti di ripristino in Azure.

        ![Impostare le proprietà di macchina virtuale](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d) riattivare la protezione per la macchina virtuale. Quando si riattiva la protezione con i dati per il volume ridimensionato saranno trasferiti in Azure.

    

## <a name="step-10-run-a-failover"></a>Passaggio 10: Eseguire caso di errore

Attualmente è possibile eseguire solo failover non previsti per le macchine virtuali VMware protette e server fisici. Tenere presente quanto segue:



- Prima di avviare caso di errore, verificare che il server di destinazione configurazione e schema è in esecuzione e integro. In caso contrario failover avrà esito negativo.
- Computer di origine non arrestare come parte di un failover non pianificato. Eseguire un failover non pianificato interrompe la replica dei dati per i server protetti. È necessario eliminare le macchine dal gruppo di protezione e aggiungerle di nuovo per avviare la protezione dei computer nuovo termine failover non pianificato.
- Se si vuole avere esito negativo su senza perdere dati, assicurarsi che le macchine virtuali del sito principale vengono disattivate prima di avviare il failover.

1. Nella **Ripristino plan di messaggistica unificata** con l'aggiunta di un piano di ripristino. Specificare i dettagli per il piano e selezionare **Azure** con quello di destinazione.

    ![Configurare il piano di ripristino](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. In **Selezionare macchina virtuale** selezionare un gruppo di protezione e quindi selezionare computer nel gruppo per aggiungere al piano di ripristino. [Per ulteriori](site-recovery-create-recovery-plans.md) informazioni sui piani di ripristino.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Se necessario è possibile personalizzare il piano per creare gruppi e sequenza l'ordine in quali macchine per il recupero piano è ha esito negativo su. È anche possibile aggiungere istruzioni per script e azioni manuale. Gli script quando si ripristina in Azure possono essere aggiunti tramite [Azure automazione runbook](site-recovery-runbook-automation.md).

4. Nella pagina **Ripristino plan di messaggistica unificata** selezionare il piano, fare clic su **Failover non pianificato**.
5. Nella finestra di **Conferma Failover** verificare la direzione di failover (per Azure) e selezionare il punto di ripristino venga reindirizzato a.
6. Attendere che il processo di failover per il completamento e quindi verificare che il failover funzioni correttamente e che le macchine virtuali replicate avviare correttamente in Azure.




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Passaggio 11: Fail indietro non è riuscita sul computer da Azure

[Ulteriori](site-recovery-failback-azure-to-vmware-classic-legacy.md) informazioni su come visualizzare l'errore sul computer con sistema operativo Azure indietro per l'ambiente locale.


## <a name="manage-your-process-servers"></a>Gestire i server di processo

Il server di processo invia dati di replica al server di destinazione master in Azure e rileva nuove macchine virtuali di VMware aggiunte a un server vCenter. Nei seguenti casi è consigliabile modificare il server di processo nella distribuzione:

- Se il server di processo corrente non è raggiungibile
- Se l'obiettivo di punto di ripristino (rilasciato) raggiunge un livello accettabile per l'organizzazione.

Se necessario è possibile spostare la replica di alcune o tutte le macchine virtuali VMware locale e server fisici in un server di processo diverso. Per esempio:

- **Errore**, ovvero se un server di processo ha esito negativo o non è disponibile, è possibile spostare la replica computer protetto a un altro server di processo. Metadati del computer di origine e computer di replica verranno spostati nel nuovo server di processo e sincronizzati di dati. Il nuovo server processo si connetterà automaticamente al server vCenter per eseguire il rilevamento automatico. È possibile monitorare lo stato dei server di processo nel dashboard del ripristino del sito.
- **Bilanciamento del carico per regolare rilasciato**, ovvero per migliorare il bilanciamento del carico è possibile selezionare un server di processo diverso nel portale del ripristino del sito e spostare replica di uno o più computer bilanciamento del carico manuale. In questo caso metadati dei computer di origine e di replica selezionato viene spostato nel nuovo server di processo. Il server di processo originale rimane connesso al server vCenter. 

### <a name="monitor-the-process-server"></a>Monitorare il server di processo

Se un server di processo in uno stato critico che verrà visualizzato un avviso di stato nel Dashboard di ripristino del sito. È possibile fare clic sullo stato per aprire la scheda eventi e quindi drill-down a processi specifici nella scheda processi. 

### <a name="modify-the-process-server-used-for-replication"></a>Modificare il server di processo utilizzato per la replica

1. Aprire **server** > **Configurazione server** > server di configurazione > **Dettagli sul Server**.
2. Fare clic su **Processo server** > **Server di processo di modifica** accanto al server che si desidera modificare.

    ![Modificare il Server di processo 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. **Modifica**del server Process > **Destinazione processo Server** selezionare il nuovo server che si desidera utilizzare e quindi selezionare le macchine virtuali che si desidera replicare nel nuovo server. Fare clic sull'icona di informazioni accanto al nome del server per informazioni dettagliate di spazio libero e memoria utilizzata. Per mettere caricare decisioni viene visualizzato lo spazio medio necessari per replicare ogni macchina virtuale selezionata nel nuovo server di processo.

    ![Modificare il Server di processo 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. Fare clic sul segno di spunta per avviare la replica nel nuovo server di processo. Si noti che, se si rimuove tutte le macchine virtuali da un server di processo che era fondamentale un avviso critico devono essere visualizzati non è più nel dashboard.


## <a name="third-party-software-notices-and-information"></a>Note legali sul software di terze parti e informazioni

Non tradurre o Localize

Il software e firmware in esecuzione in Microsoft prodotto o servizio basato su o incorpora materiale da progetti elencati di seguito (collettivamente "codice terze parti").  Microsoft è l'autore non originale del codice di terze parti.  Le informazioni sul copyright originale e licenza, in cui Microsoft ha ricevuto il codice di terze parti, sono impostate via riportata di seguito.

Le informazioni nella sezione si riferisce componenti di terze parti codice dei progetti vengono elencate di seguito. Ad esempio licenze e le informazioni vengono fornite informativo.  Il codice di terze parti è da relicensed per l'utente da Microsoft in condizioni di licenza software Microsoft per il prodotto o servizio Microsoft.  

Le informazioni nella sezione B si riferisce componenti di codice di terze parti che vengono resi disponibili per l'utente da Microsoft sotto le condizioni di licenza originale.

Il file completo potrebbe disponibili nell' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft si riserva tutti i diritti non espressamente concessi, se da implicazioni, corrette o in caso contrario.
