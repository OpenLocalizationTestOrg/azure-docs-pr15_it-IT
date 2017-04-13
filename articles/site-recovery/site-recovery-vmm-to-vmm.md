<properties
    pageTitle="Replicare macchine virtuali di Hyper-V in cloud VMM in un sito secondario di VMM tramite il portale di Azure | Microsoft Azure"
    description="Descrive come distribuire Azure il ripristino del sito per la gestione di replica, failover e ripristino delle macchine virtuali di Hyper-V in cloud VMM a un sito secondario di VMM tramite il portale di Azure."
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
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Replicare macchine virtuali di Hyper-V in cloud VMM in un sito secondario di VMM tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmm-to-vmm.md)
- [Portale classica](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Manager delle risorse](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Introduzione al ripristino del sito Azure! Usare questo articolo se si desidera replicare macchine virtuali di Hyper-V locale gestito in aree System Center Virtual Machine Manager (VMM) a un sito secondario. In questo articolo viene descritto come configurare la replica utilizzando il ripristino del sito di Azure nel portale di Azure.

> [AZURE.NOTE] Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse: Gestione risorse di Azure e classica. Azure include inoltre due portali: portale classico Azure che supporta il modello di distribuzione classica e il portale Azure con supporto per entrambi i modelli di distribuzione.


Azure il ripristino del sito nel portale di Azure offre diverse nuove caratteristiche:

- Nel Azure portale, il Backup di Azure e il ripristino del sito di Azure servizi vengono combinato in un singolo archivio di servizi di recupero, in modo che è possibile configurare e gestire la continuità aziendale e il ripristino di emergenza (BCDR) da un'unica posizione. Un dashboard unificato consente di controllare e gestire le operazioni in più siti locali e cloud pubblica Azure.
- Gli utenti con abbonamenti Azure viene completato il provisioning con il programma Cloud soluzione Provider (CSP) ora possono gestire operazioni di ripristino del sito nel portale di Azure.


Dopo aver letto in questo articolo, pubblicare eventuali commenti nella parte inferiore di commenti Disqus. Porre domande tecniche nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Panoramica

Le organizzazioni devono una strategia BCDR che determina come App, carichi di lavoro e dati rimangono in esecuzione e disponibili durante il tempo di inattività pianificato e non pianificato e ripristino in condizioni di lavoro normali più presto possibile. La strategia BCDR deve mantenere i dati business attendibili e recuperabili e garantire che carichi di lavoro sempre disponibile quando si verifica danno.

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR da coordinazione replica di server fisici locale e macchine virtuali nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nella propria posizione principale, non si riesce sopra a posizione secondaria per mantenere le applicazioni e carichi di lavoro disponibili. Riescano a tornare alla propria posizione primaria restituisce al funzionamento normale. Altre informazioni, vedere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

Questo articolo fornisce tutte le informazioni che necessarie per replicare locale macchine virtuali di Hyper-V nel cloud VMM in un sito secondario di VMM. Include una panoramica dell'architettura, informazioni e procedure di distribuzione per la configurazione dei server locali, le impostazioni di replica e pianificazione della capacità di pianificazione. Dopo aver configurato l'infrastruttura è possibile abilitare la replica nei computer che si desidera proteggere e verificare che failover funzioni.

## <a name="business-advantages"></a>Vantaggi di Business

- Il ripristino del sito offre una protezione per carichi di lavoro di business e le applicazioni in macchine virtuali di Hyper-V mediante la loro replica a un server di Hyper-V secondario.
- Il portale di servizi di recupero rappresenta una posizione centralizzata per configurare, gestire e monitorare replica, failover e ripristino.
- È possibile eseguire failover eseguire con facilità dall'infrastruttura locale principale per il sito secondario e failback (ripristino) del sito secondario all'oggetto primario.
- È possibile configurare i piani di ripristino con più computer in modo da carichi di lavoro di applicazione a più livelli esito negativo su insieme.

## <a name="scenario-architecture"></a>Architettura di uno scenario

Questi sono i componenti di uno scenario:

- **Sito principale**: nel sito principale, sono uno o più server di host Hyper-V in esecuzione macchine virtuali di origine che si desidera replicare. Questi server host primario si trovano in un cloud privato VMM.
- **Sito secondario**: sito secondario, sono disponibili uno o più server host Hyper-V in esecuzione macchine virtuali di destinazione a cui è replicare macchine virtuali principale. Questi server host si trovano in un cloud privato VMM. Il server primario (se è presente un singolo server VMM) o su un server VMM secondario, può essere nel cloud.
- **Provider**: durante la distribuzione il ripristino del sito, installare il Provider di ripristino del sito di Azure nei server VMM e registrare tali server in un archivio di servizi di recupero. Il Provider eseguito sul server VMM comunica con il ripristino del sito tramite HTTPS 443 replicare orchestrazione. Replica di dati tra server host Hyper-V primario e secondario. Dati replicati rimangono all'interno di reti e siti locale e non vengono inviati a Azure. Altre informazioni sulla [privacy](#privacy-information-for-site-recovery).

![Topologia E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Panoramica sulla privacy dei dati

In questa tabella sono riepilogate archiviazione dei dati in questo scenario:
****
Azione | **Dettagli** | **Dati raccolti** | **Utilizzo** | **Obbligatorio**
--- | --- | --- | --- | ---
**Registrazione** | È possibile registrare un server VMM un archivio di servizi di recupero. Se in seguito si desidera annullare la registrazione di un server, è possibile eliminare le informazioni sul server dal portale di Azure. | Dopo aver registrato un server VMM il ripristino del sito raccoglie, processi e viene trasferita metadati sul server di VMM e i nomi degli cloud VMM rilevato per il ripristino del sito. | I dati vengono utilizzati per identificare e comunicare con il server VMM appropriato e configurare le impostazioni per cloud VMM appropriato. | Questa caratteristica è necessaria. Se non si desidera inviare queste informazioni per il ripristino del sito è consigliabile utilizzare il servizio il ripristino del sito.
**Abilitare la replica** | Il Provider di ripristino del sito di Azure installato sul server VMM e il canale di comunicazione con il servizio il ripristino del sito. Il provider di servizi è una raccolta di collegamento dinamico (DLL) ospitata nel processo di VMM. Dopo aver installato il provider di servizi, la funzionalità di "Data Center ripristino" Ottiene abilitata nella console di amministrazione VMM. Macchine virtuali di nuove ed esistenti è possono abilitare questa caratteristica attivare la protezione per una macchina virtuale. | Con questo insieme di proprietà, il Provider invia il nome e l'ID della macchina virtuale per il ripristino del sito.  La replica è attivata per Windows Server 2012 o Windows Server 2012 R2 Hyper-V Replica. I dati di macchina virtuale Ottiene replicati da un host Hyper-V a un'altra (in genere si trova in un data center diversi "ripristino"). | Il ripristino del sito Usa i metadati per compilare le informazioni di macchine Virtuali nel portale di Azure. | Questa caratteristica è essenziale del servizio e non può essere disattivata. Se non si desidera inviare queste informazioni non abilitare protezione il ripristino del sito per macchine virtuali. Si noti che tutti i dati inviati dal Provider per il ripristino del sito è stato inviato tramite HTTPS.
**Piano di ripristino** | Piani di ripristino consentono di creare un piano di orchestrazione per il centro di dati di ripristino. È possibile definire l'ordine in cui deve essere avviato macchine virtuali o un gruppo di macchine virtuali nel sito di ripristino. È anche possibile specificare qualsiasi script automatizzati per essere eseguita o qualsiasi manuale azione da eseguire, al momento di ripristino per ogni macchina virtuale. Failover viene in genere attivato livello di piano di ripristino per il ripristino coordinato. | Il ripristino del sito raccoglie, elabora e trasmette metadati per il piano di ripristino, inclusi i metadati macchina virtuale e i metadati di qualsiasi script di automazione e note di azione manuale. | I metadati vengono utilizzati per creare il piano di ripristino nel portale di Azure. | Questa caratteristica è essenziale del servizio e non può essere disattivata. Se non si desidera inviare queste informazioni per il ripristino del sito, non creare piani di ripristino.
**Connessione di rete** | Mappe informazioni relative alla rete dall'interfaccia di dati primario per il centro di dati di ripristino. Quando vengono recuperate macchine virtuali nel sito di ripristino, connessione di rete consente di stabilire la connettività di rete. | Il ripristino del sito raccoglie, elabora e trasmette i metadati delle reti logiche per ogni sito (principale e Data Center). | I metadati viene utilizzato per popolare le impostazioni di rete in modo che è possibile mappare le informazioni di rete. | Questa caratteristica è essenziale del servizio e non può essere disattivata. Se non si desidera inviare queste informazioni per il ripristino del sito, non utilizzare il mapping di rete.
**Failover (pianificato/pianificato/prova)** | Failover ha esito negativo su macchine virtuali da un centro di dati gestiti VMM a un'altra. L'azione failover attivato manualmente nel portale di Azure. | Il Provider sul server VMM riceve una notifica dell'evento failover tramite il ripristino del sito e viene eseguita un'azione failover nell'host Hyper-V tramite le interfacce VMM. Failover effettivo di una macchina virtuale è stata inviata da un host Hyper-V in un'altra e gestito da Windows Server 2012 o Windows Server 2012 R2 Hyper-V Replica. Dopo avere inserito failover, il Provider nel server VMM nell'interfaccia di recupero dati invia informazioni esito positivo per il ripristino del sito. | Il ripristino del sito utilizza le informazioni inviate a popolare lo stato delle informazioni di azione failover nel portale di Azure. | Questa caratteristica è essenziale del servizio e non può essere disattivata. Se non si desidera inviare queste informazioni per il ripristino del sito, non usare failover.


## <a name="azure-prerequisites"></a>Prerequisiti Azure

Ecco cosa occorre in Azure per distribuire questo scenario:

**Prerequisiti** | **Dettagli**
--- | ---
**Azure**| È necessario un account di [Microsoft Azure](http://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.


## <a name="on-premises-prerequisites"></a>Prerequisiti locale

Ecco cosa occorre nei siti principali e secondarie locale per distribuire questo scenario:

**Prerequisiti** | **Dettagli**
--- | ---
**VMM** | È consigliabile che distribuire un server VMM nel sito principale e un server VMM il sito secondario.<br/><br/> È inoltre possibile [replicare tra aree in un unico server VMM](site-recovery-single-vmm.md). Per eseguire questa operazione è necessario almeno due aree configurati sul server VMM.<br/><br/> Server VMM deve essere in esecuzione almeno System Center 2012 SP1 con gli aggiornamenti più recenti.<br/><br/> Ogni server VMM deve avere uno o più aree configurati e tutte le aree è necessario impostare il profilo della capacità di Hyper-V. <br/><br/>Nuvole devono contenere uno o più gruppi di host VMM.<br/><br/>Altre informazioni sull'impostazione cloud VMM nella [configurazione dell'infrastruttura cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), e [procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Server VMM necessario l'accesso a internet.
**Hyper-V** | Server Hyper-V deve essere in esecuzione almeno Windows Server 2012 con il ruolo Hyper-V e sono stati installati gli aggiornamenti più recenti.<br/><br/> Un server di Hyper-V deve contenere uno o più macchine virtuali.<br/><br/>  Server host Hyper-V devono essere distribuiti nei gruppi host nel cloud VMM primario e secondario.<br/><br/> Se si esegue Hyper-V in un cluster in Windows Server 2012 R2 è necessario installare [l'aggiornamento 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se in esecuzione Hyper-V in un cluster in Windows Server 2012, tenere presente che gestore cluster non viene creata automaticamente se si dispone di un cluster basato su indirizzo IP statico. È necessario configurare manualmente il gestore di cluster. [Per ulteriori](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Provider** | Durante la distribuzione il ripristino del sito, installare il Provider di ripristino del sito di Azure server VMM. Il provider di servizi comunica con il ripristino del sito tramite HTTPS 443 per poter gestire le replica. Replica di dati tra il server di Hyper-V primario e secondario attraverso la rete LAN o una connessione VPN.<br/><br/> Il Provider eseguito sul server VMM richiede l'accesso a questi URL: *. hypervrecoverymanager.windowsazure.com; *. AccessControl.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Inoltre consentire la comunicazione firewall dal server VMM [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/download/confirmation.aspx?id=41653) e per consentire il protocollo HTTPS (443).

## <a name="prepare-for-deployment"></a>Prepararsi per la distribuzione

Per prepararsi per la distribuzione che è necessario:

1. [Preparare il server VMM](#prepare-the-vmm-server) per la distribuzione il ripristino del sito.
2. [Preparazione per la connessione di rete](#prepare-for-network-mapping). Configurare le reti, in modo che è possibile configurare una connessione di rete.

### <a name="prepare-the-vmm-server"></a>Preparare il server VMM

Assicurarsi che il server VMM conformità con i [Prerequisiti](#on-premises-prerequisites) e sono accessibili degli URL elencati.


### <a name="prepare-for-network-mapping"></a>Prepararsi per la corrispondenza di rete

Rete mappe di mapping tra le reti VMM VM nei server che VMM primario e secondario per:

- Posizionare in modo ottimale macchine virtuali di replica su host Hyper-V secondario dopo il failover.
- Connettere macchine virtuali di replica reti macchine Virtuali appropriate.
- Se non Configura rete mapping replica macchine virtuali non connesso a qualsiasi rete dopo il failover.
- Se si desidera configurare la rete mapping durante il ripristino del sito distribuzione qui è è necessario:

    - Assicurarsi di macchine virtuali nel server di origine Hyper-V host connessi a una rete VMM VM. La rete deve essere collegata a una rete logica associato nel cloud.
    - Verificare che il cloud secondario utilizzate per il ripristino sia assegnata una rete macchine Virtuali corrispondente configurato. La rete macchine Virtuali deve essere collegata a una rete logica associato con il cloud secondario.

- [Altre informazioni](site-recovery-network-mapping.md) sulle modalità di connessione di rete.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Prepararsi per la distribuzione con un singolo server VMM

Se è presente un singolo server VMM è possibile replicare macchine virtuali nell'host Hyper-V nel cloud VMM in [Azure](site-recovery-vmm-to-azure.md) o una cloud VMM secondario. È consigliabile che la prima opzione perché la replica tra aree non è semplice, ma se è necessario eseguire questa operazione qui riguarda le azioni da eseguire:

1. **Configurare VMM in una macchina virtuale Hyper-V**. Quando si esegue questa operazione è consigliabile che si colocate l'istanza di SQL Server utilizzata da VMM nella stessa macchina virtuale. Risparmiare tempo durante la sola macchine Virtuali sono necessario creare. Se si desidera utilizzare remota istanza di SQL Server e un'interruzione si verifica, è necessario ripristinare l'istanza prima di poter ripristinare VMM.
2. **Accertarsi che il server VMM ha almeno due aree configurati**. Un'area conterrà macchine virtuali da replicare e altri cloud verrà utilizzata come posizione secondaria. [Prerequisiti](#on-premises-prerequisites)devono essere conformi agli cloud che contiene le macchine virtuali che si desidera proteggere.
3. Configurare il ripristino del sito come descritto in questo articolo. Creare e registrare il server VMM nell'archivio di impostare un criterio di replica e abilitare la replica. È necessario specificare che la replica iniziale entrerà in vigore in rete.
4. Quando si configura connessione di rete è eseguire il mapping di rete macchina virtuale per il cloud principale alla rete macchine Virtuali per il cloud secondario.
5. Nella console di gestione di Hyper-V, abilitare Replica Hyper-V nell'host Hyper-V contenente VM VMM e abilitare la replica nella macchina virtuale. Assicurarsi che non si aggiunge la macchina virtuale VMM ad aree che sono protetti tramite il ripristino del sito, per garantire che le impostazioni di Replica Hyper-V non prevalere il ripristino del sito.
6. Se si creano i piani di ripristino per failover utilizzare lo stesso server VMM per origine e destinazione.
7. Per avere esito negativo su e recuperare come indicato di seguito:

    - Manualmente esito negativo su VM VMM per il sito secondario con Hyper-V Manager pianificato in caso di errore.
    - Esito negativo su macchine virtuali.
    - Dopo principale VMM VM è stata recuperata, accesso al portale di Azure -> servizi di recupero archivio ed eseguire un failover non pianificato delle macchine virtuali dal sito secondario al sito principale.
    - Dopo avere inserito failover non pianificato, tutte le risorse sarà possibile accedervi dal sito principale.


### <a name="create-a-recovery-services-vault"></a>Creare un archivio di servizi di recupero

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Management** > **servizi di recupero**. In alternativa è possibile fare clic su **Sfoglia** > **Servizi di recupero** archivi > **Aggiungi**.

    ![Nuovo archivio](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. Nella **casella Nome** specificare un nome descrittivo per identificare l'archivio. Se si dispone di più di una sottoscrizione, selezionare una di esse.
4. [Creare un nuovo gruppo di risorse](../resource-group-template-deploy-portal.md) o selezionarne uno esistente e specificare un'area di Azure. Computer verranno replicati in quest'area. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se si desidera accedere rapidamente l'archivio nel dashboard fare clic su **Aggiungi a dashboard** > **Crea archivio**.

    ![Nuovo archivio](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

Nuovo archivio verrà visualizzato nel **Dashboard** > **tutte le risorse**, quindi nella finestra principale blade **archivi di servizi di recupero** .




## <a name="getting-started"></a>Guida introduttiva

Il ripristino del sito offre un'introduzione ottimizzata che consente di distribuire più presto possibile. Guida introduttiva di verificare i prerequisiti e illustra le ripristino del sito distribuzione passaggi da eseguire nell'ordine corretto.

In Guida introduttiva si seleziona il tipo di computer che si desidera replicare e in cui si desidera replicare. Impostare i server in locale, creare criteri di replica e pianificazione delle capacità. Dopo aver configurato l'infrastruttura si abilita la macchine virtuali replica. È possibile eseguire failover per computer specifici o creare piani di ripristino per avere esito negativo su più dispositivi.

Iniziare introduzione scegliendo la modalità con cui si desidera distribuire il ripristino del sito. Le modifiche flusso introduzione leggermente in base ai propri requisiti di replica.

## <a name="step-1-choose-your-protection-goal"></a>Passaggio 1: Scegliere gli obiettivi di protezione

Selezionare gli elementi da replicare e in cui si desidera replicare.

1. In e il **ripristino servizi archivi** selezionare l'insieme di credenziali e fare clic su **Impostazioni**.
2. In **Impostazioni** > **Introduzione** fare clic sul **Ripristino del sito** > **passaggio 1: preparare infrastruttura** > **obiettivo di protezione**.
3. In **obiettivo di protezione** selezionare questa opzione **per il sito di ripristino**e selezionare **Sì, con Hyper-V**.
4. Selezionare **Sì** per indicare che si sta utilizzando VMM per gestire l'host Hyper-V e selezionare **Sì** se si dispone di un server VMM secondario. Se si distribuisce replica tra le aree in un unico server VMM fare clic su **No**. Fare clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>Passaggio 2: Configurare l'ambiente di origine

Installare il Provider di ripristino del sito di Azure nel server VMM e registrare server nell'archivio di.

1. Fare clic su **passaggio 2: preparare infrastruttura** > **origine**.

    ![Impostare un'origine](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. **Preparare l'ambiente** origine fare clic su **+ VMM** per aggiungere un server VMM.

    ![Impostare un'origine](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. Nel controllo blade **Aggiungi Server** **System Center VMM server** visualizzato nel **tipo di Server** e che il server VMM soddisfi i [requisiti di URL e i prerequisiti](#on-premises-prerequisites).
4. Scaricare il file di installazione di Provider di ripristino di Azure del sito.
5. Scaricare la chiave di registrazione. È necessario quando si esegue l'installazione. La chiave è valida per 5 giorni dopo aver generato.

    ![Impostare un'origine](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Installare il Provider di ripristino del sito di Azure sul server VMM.

> [AZURE.NOTE] Non è necessario installare in modo esplicito alcuna nel server host Hyper-V.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurare il Provider di ripristino del sito di Azure

1. Eseguire il Provider di file di installazione in ogni server VMM. Se VMM viene distribuito in un cluster e si sta eseguendo l'installazione di Provider per la prima volta installarlo in un nodo attivo e completare l'installazione per registrare il server VMM nell'archivio di. Installare il Provider sugli altri nodi. Nodi cluster deve essere eseguita la stessa versione del Provider.
2. Il programma di installazione esegue alcuni controlli prerequirement e le richieste di autorizzazione per arrestare il servizio VMM. Al termine dell'installazione, il servizio VMM verrà riavviato automaticamente. Se sta eseguendo l'installazione in un cluster VMM verrà richiesto di interrompere il ruolo di Cluster.

2.  In **Microsoft Update** è possibile scegliere di aggiornamenti in modo che gli aggiornamenti di Provider installati secondo i criteri di Microsoft Update.
3. **Installazione di** accettare o modificare il percorso di installazione di Provider predefinito e fare clic su **Installa**.

    ![Percorso di installazione](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Al termine dell'installazione fare clic su **Registra** per registrare il server nell'archivio di.

    ![Percorso di installazione](./media/site-recovery-vmm-to-vmm/provider-register.png)

9. In **nome archivio**di verificare il nome dell'archivio in cui verrà registrato il server. Fare clic su *Avanti*.

    ![Registrazione server](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. In **Connessione a Internet** specificare la modalità di connessione a Internet Provider eseguito sul server VMM. Selezionare **Connetti con le impostazioni del proxy esistente** per utilizzare le impostazioni di connessione Internet predefinito configurate sul server.

    ![Impostazioni di Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Se si desidera utilizzare un proxy personalizzato, che è necessario configurarlo prima di installare il Provider. Configurare le impostazioni del proxy personalizzato verrà eseguito un test per verificare la connessione proxy.
    - Se si utilizza un proxy personalizzato o il proxy predefinito richiede l'autenticazione che è necessario immettere i dettagli del proxy, inclusi l'indirizzo proxy e la porta.
    - URL seguenti devono essere accessibili dal Server VMM e host Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Consentire agli indirizzi IP descritti in [Intervalli di indirizzi IP del Data Center del Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS protocol (443). È necessario intervalli di indirizzi IP lista bianca dell'area Azure che si prevede di utilizzare e che usa ovest.
    - Se si utilizza un proxy personalizzato verrà creato un account RunAs VMM (DRAProxyAccount) automaticamente con le credenziali proxy specificato. Configurare il server proxy in modo che l'account è possibile autenticare correttamente. Le impostazioni dell'account RunAs VMM possono essere modificate nella console di VMM. A tale scopo, aprire l'area di lavoro di **Impostazioni** , espandere **protezione**, fare clic su **Esecuzione come account**e quindi modificare la password per DRAProxyAccount. È necessario riavviare il servizio VMM in modo che questa impostazione è effettiva.


8. Nella **Chiave di registrazione**, selezionare la chiave scaricati dal ripristino del sito di Azure e copiati nel server VMM.


10.  L'impostazione di crittografia viene utilizzato solo quando si attiva la replica macchine virtuali di Hyper-V nel cloud VMM in Azure. Se sta replicate in un sito secondario non è usata.

11.  In **nome Server**, specificare un nome descrittivo per identificare il server VMM nell'archivio di. In una configurazione cluster specificare il nome del ruolo di cluster VMM.
12.  Nella pagina selezione **Sincronizza cloud metadati** se si vuole sincronizzare metadati per tutte le aree sul server VMM con l'archivio. Questa azione è sufficiente si verifichi una volta in ogni server. Se non si desidera sincronizzare tutte le aree, è possibile lasciare deselezionato questa impostazione e sincronizzare ogni cloud singolarmente nelle proprietà cloud nella console di VMM.

13.  Fare clic su **Avanti** per completare il processo. Dopo la registrazione, recupero dei metadati dal server VMM per il ripristino del sito di Azure. Il server è visualizzato nella scheda **Server VMM** nella pagina **server** nell'archivio di.

    ![Server](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Dopo che il server sia disponibile nella console di ripristino del sito di **origine** > **Prepara origine** selezionare il server VMM e nel cloud in cui si trova l'host Hyper-V. Fare clic su **OK**.

#### <a name="command-line-installation"></a>Installazione della riga di comando

È possibile installare il Provider di ripristino del sito di Azure dalla riga di comando. Questo metodo può essere utilizzato per installare il Provider su Server Core per Windows Server 2012 R2.

1. Scaricare la chiave di file e la registrazione di installazione del Provider in una cartella. Ad esempio C:\ASR.
2. Arrestare il servizio di gestione di macchina virtuale System Center.
3. Dal prompt dei comandi con privilegi elevati eseguire questi comandi per estrarre il programma di installazione di Provider:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Eseguire questo comando per installare il Provider:

        C:\ASR> setupdr.exe /i

5. Eseguire questi comandi per registrare il server nell'archivio di:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Dove si trovano i parametri:

 - **/Credentials**: parametro obbligatorio che specifica la posizione in cui si trova il file di chiave di registrazione  
 - **/FriendlyName**: parametro obbligatorio per il nome del server host Hyper-V che viene visualizzata nel portale del ripristino del sito di Azure.
 - **/EncryptionEnabled**: parametro facoltativo che si utilizza solo quando la replica da VMM in Azure.
 - **/ProxyAddress**: parametro facoltativo che consente di specificare l'indirizzo del server proxy.
 - **/ProxyPort**: parametro facoltativo che specifica la porta del server proxy.
 - **/proxyUsername**: parametro facoltativo che specifica il nome utente Proxy (se proxy richiede l'autenticazione).
 - **/proxyPassword**: parametro facoltativo che specifica la Password per l'autenticazione con il server proxy (se proxy richiede l'autenticazione).  

## <a name="step-3-set-up-the-target-environment"></a>Passaggio 3: Configurare l'ambiente di destinazione

Selezionare il server VMM di destinazione e cloud.

1. Fare clic su **Prepara infrastruttura** > **destinazione** e selezionare il server VMM destinazione che si desidera utilizzare.
2.  Verranno visualizzati nuvole nel server che vengono sincronizzate con il ripristino del sito. Selezionare l'area di destinazione.

    ![Destinazione](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica

1. Per creare una nuova replica criteri, fare clic su **Prepara infrastruttura** > **Le impostazioni di replica** > **+ Crea e associa**.

    ![Rete](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. **Creare** e associati criteri specificare un nome di criteri. Il tipo di origine e destinazione deve essere **Hyper-V**.
3. Nella **versione di host Hyper-V** selezionare il sistema operativo è in esecuzione nell'host.

    > [AZURE.NOTE] Cloud VMM può contenere host Hyper-V in esecuzione diverse versioni (supportate) di Windows Server, ma un criterio di replica è ospita applicato lo stesso sistema operativo. Se è già in esecuzione più versioni di un sistema operativo host quindi creare criteri di replica distinta.

4. In **tipo di autenticazione** e la **porta di autenticazione** specificare la modalità di autenticazione il traffico tra i server di host Hyper-V principale e ripristino. Selezionare il **certificato** a meno che non si dispone di un ambiente Kerberos di lavoro. Il ripristino del sito Azure configura automaticamente i certificati di autenticazione HTTPS. Non è necessario eseguire alcuna manualmente. Per impostazione predefinita, porta 8083 e 8084 (per i certificati) verrà aperta in Windows Firewall nei server che host Hyper-V. Se si seleziona **Kerberos**, un ticket Kerberos verrà utilizzato per l'autenticazione dei server host. Si noti che questa impostazione si applica solo alle server host Hyper-V in esecuzione su Windows Server 2012 R2.
3. Nella casella **Copia frequenza** specificare la frequenza con cui si desidera replicare dati delta dopo la replica iniziale (ogni 30 secondi, 5 o 15 minuti).
4. In **criteri di conservazione punto di ripristino**, specificare in ore il periodo di tempo la finestra di conservazione per ogni punto di ripristino. Computer protetto possono essere recuperate in un punto qualsiasi all'interno di una finestra.
6. Frequenza **snapshot coerente App** specificare la frequenza (1-12 ore) punti di ripristino contenente snapshot coerenti applicazione sarà creata. Hyper-V vengono utilizzati due tipi di snapshot, ovvero uno snapshot standard che fornisce uno snapshot della macchina virtuale intero incrementale e uno snapshot coerente applicazione che contiene uno snapshot nel momento dei dati dell'applicazione macchina virtuale. Snapshot coerenti applicazione utilizzare Servizio copia ombreggiatura del Volume (VSS) per garantire che le applicazioni in uno stato coerente quando dello snapshot. Si noti che se si abilita snapshot coerenti con l'applicazione, influisce negativamente sulle prestazioni delle applicazioni in esecuzione in macchine virtuali di origine. Assicurarsi che il valore impostato sia minore del numero di punti di ripristino aggiuntive che configurare.
7. Nella casella **dati Trasferisci compressione** specificare se devono essere compresso replicato i dati trasferiti.
8. Selezionare **Elimina replica macchine Virtuali** per specificare che la macchina virtuale replica deve essere eliminata se si disattiva la protezione per l'origine macchine Virtuali. Se si abilita questa impostazione, quando si disattiva la protezione per l'origine macchine Virtuali che è stata rimossa dalla console di ripristino del sito, le impostazioni del ripristino del sito per VMM vengono rimossi dalla console di VMM e la replica viene eliminata.
3. In **metodo di replica iniziale** se si sta replicare in rete specificare se avviare la replica iniziale o pianificarne. Per salvare la larghezza di banda di rete è consigliabile pianificare di fuori dell'orario occupato. Fare clic su **OK**.

    ![Criteri di replica](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Quando si crea un nuovo criterio automaticamente associata con il cloud VMM. In **criteri di replica** fare clic su **OK**. È possibile associare aggiuntive cloud VMM (e macchine virtuali nel loro) con il criterio di replica nelle **Impostazioni** > **replica** > nome del criterio > **Associare Cloud VMM**.

    ![Criteri di replica](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Prepararsi per la replica iniziale offline

È possibile eseguire la replica non in linea per la copia iniziale dei dati. È possibile preparare questo come indicato di seguito:

- Nel server di origine, è necessario specificare un percorso da cui verrà eseguita l'esportazione dei dati. Assegnare il controllo completo per le autorizzazioni di condivisione e NTFS al servizio VMM sul percorso di esportazione. Nel server di destinazione, è necessario specificare un percorso da cui verrà eseguita l'importazione dei dati. Assegnare le stesse autorizzazioni su questo percorso di importazione.
- Se il percorso di importazione o esportazione è condiviso, assegnare l'appartenenza ai gruppi amministratore, Power User, operatore di stampa o operatore Server per l'account di servizio VMM nel computer remoto in cui il condiviso si trova.
- Se si utilizza un account Esegui come aggiungere host, percorsi importazione / esportazione assegnare lettura e scrittura agli account Esegui come VMM.
- Le azioni di importazione / esportazione non dovrebbero trovarsi in qualsiasi computer utilizzato come server host Hyper-V, perché loopback configurazione non è supportata da Hyper-V.
- In Active Directory, su ogni Hyper-V server host contenente macchine virtuali che si desidera proteggere, abilitare e configurare vincolato delega per considerare attendibile il computer remoto in cui i percorsi di importazione / esportazione si trovano, come indicato di seguito:
    1. Sul controller di dominio, aprire **Active Directory Users e computer**.
    2. Nella struttura della console fare clic su **NomeDominio** > **computer**.
    3. Pulsante destro del mouse sul nome del server host Hyper-V > **proprietà**.
    4. Nella scheda **delega** fare clic su **computer attendibile per la delega solo ai servizi specificati**.
    5. Fare clic su **Usa Kerberos**.
    6. Fare clic su **Aggiungi** > **utenti e computer**.
    7. Digitare il nome del computer che ospita il percorso di esportazione > **OK**. Nell'elenco di servizi disponibili, tenere premuto il tasto CTRL e fare clic su **cifs** > **OK**. Ripetere la procedura per il nome del computer che ospita il percorso di importazione. Ripetere l'operazione per server host Hyper-V aggiuntivi.


### <a name="configure-network-mapping"></a>Configurare il mapping di rete

Configurare la connessione di rete tra le reti di origine e destinazione.

- [Lettura](#prepare-for-network-mapping) per una rapida panoramica delle quali connessione di rete funziona. In aggiunta [leggere](site-recovery-network-mapping.md) per una spiegazione più dettagliata.
- Verificare di macchine virtuali di server VMM connessi a una rete macchine Virtuali.

Configurare il mapping come indicato di seguito:

1. **Impostazioni** > **Infrastruttura di ripristino del sito** > **Rete Mapping** > **i mapping di rete** fare clic su **+ mappatura di rete**.

    ![Connessione di rete](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Nella scheda **connessione di rete Aggiungi** selezionare l'origine e destinazione server VMM. Reti macchine Virtuali associate con il server VMM vengono recuperate.
3. **Rete di origine**, selezionare la rete che si desidera utilizzare nell'elenco delle reti macchine Virtuali associato al server VMM principale.
6. Rete di **destinazione** selezionare la rete che si desidera utilizzare nel server VMM secondario. Fare clic su **OK**.

    ![Connessione di rete](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Ecco cosa accade quando si inizia a connessione di rete:

- Qualsiasi macchine virtuali di replica esistenti che corrispondono alla rete macchine Virtuali origine verrà connesso alla rete macchine Virtuali di destinazione.
- Nuove macchine virtuali connessi alla rete macchine Virtuali di origine verranno connessi alla rete mappato destinazione dopo la replica.
- Se si modifica un mapping con una nuova rete esistente, verrà connesso macchine virtuali di replica utilizzando le nuove impostazioni.
- Se la rete di destinazione dispone di più subnet e una di queste subnet ha lo stesso nome subnet in cui si trova la macchina virtuale di origine, quindi la macchina virtuale duplicata verrà connesso subnet destinazione dopo il failover. Se non vi è alcuna subnet di destinazione con un nome corrispondente, la macchina virtuale verrà connesso prima subnet nella rete.

### <a name="configure-storage-mapping"></a>Configurare il mapping di spazio di archiviazione
Per impostazione predefinita quando si replica una macchina virtuale su un server di host Hyper-V origine a un server di host Hyper-V di destinazione, replicati dati vengono archiviati nella posizione predefinita indicato per l'host Hyper-V di destinazione nella gestione di Hyper-V. Per un maggiore controllo memorizzazione dei dati replicati, è possibile configurare il mapping di spazio di archiviazione<br/><br/> Per configurare il mapping di spazio di archiviazione, è necessario configurare la classificazione di spazio di archiviazione dell'origine e destinazione server VMM prima di iniziare la distribuzione. Mapping di spazio di archiviazione tramite nuovo portale Azure attualmente non è supportato. Tuttavia, può essere attivato tramite Powershell. [Altre informazioni](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Passaggio 5: Pianificazione delle capacità

Dopo aver creato il basic infrastruttura configurare è possibile considerare la pianificazione della capacità e stabilire la necessità di risorse aggiuntive.

Il ripristino del sito fornisce una pianificazione capacità basati su Excel per informazioni su come assegnare risorse per l'ambiente di origine, componenti di ripristino del sito, rete e lo spazio di archiviazione. È possibile eseguire la pianificazione in modalità rapida per le stime in base a un numero medio di macchine virtuali, dischi e lo spazio di archiviazione o in visualizzazione dettagliata in cui verrà input figure a livello di carico di lavoro. Prima di iniziare è necessario:

- Raccogliere informazioni sull'ambiente di replica, tra cui macchine virtuali, dischi per macchine virtuali e lo spazio di archiviazione per disco.
- Valutare le modifiche (varianza) giornaliere che saranno disponibili per i dati delta replicato. È possibile utilizzare la [pianificazione delle capacità per Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) per eseguire questa operazione.

1.  Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Leggere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2.  Al termine selezionare **Sì** nel **è stato eseguito la pianificazione delle capacità**?

    ![Pianificazione delle capacità](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Controllo della larghezza di banda

Dopo aver raccolto le informazioni di replica delta in tempo reale con la capacità di pianificazione Hyper-V Replica, lo strumento di pianificazione capacità basati su Excel consente di calcolare la larghezza di banda che necessaria per la replica (iniziale e delta). Per controllare la quantità di larghezza di banda utilizzata per la replica è possibile configurare i criteri di NetQos utilizzando criteri di gruppo o Windows PowerShell. Esistono vari modi, è possibile eseguire questa operazione:

**PowerShell** | **Dettagli**
--- | ---
**New - NetQosPolicy "QoS alla subnet di destinazione"** | Regolare il traffico da un host Hyper-V in esecuzione Windows Server 2012 R2 a una subnet secondaria. Utilizzare le subnet principali e secondarie sono diverse.
**New - NetQosPolicy "QoS alla porta di destinazione"** | Regolare il traffico da un host Hyper-V in esecuzione Windows Server 2012 R2 a una porta di destinazione.
**Nuovo - NetQosPolicy "Il traffico limitazione da VMM"** | Regolare il traffico da vmms.exe. Rallentamento il traffico di Hyper-V sia Live migrazione. È possibile associare protocolli IP e porte per perfezionare.

È possibile usare le impostazioni della larghezza di banda lo spessore o limitare il traffico, bit per secondario. Se si usa un cluster è necessario eseguire questa operazione in tutti i nodi di cluster. Per ulteriori informazioni, vedere:


- Blog di Thomas Maurer per [La limitazione per il traffico di Replica Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Informazioni aggiuntive sui [cmdlet New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx).


## <a name="step-6-enable-replication"></a>Passaggio 6: La replica Abilita

A questo punto abilitare replica come indicato di seguito:

1. Fare clic su **passaggio 2: replicare applicazione** > **origine**. Dopo aver attivato la replica per la prima volta, si fa clic su **+ replicare** nell'archivio di abilitare la replica per computer aggiuntivi.

    ![Abilitare la replica](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. In e **l'origine** > selezionare server VMM e nel cloud in cui si trovano host Hyper-V da replicare. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. Verificare il server VMM secondario e cloud nella e **destinazione** .
4. In **macchine virtuali di** selezionare le macchine virtuali che si desidera proteggere dall'elenco.

    ![Attivare la protezione macchina virtuale](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

È possibile tenere traccia dello stato dell'azione **Attiva protezione** nelle impostazioni > **processi** > **processi di ripristino del sito**. Dopo l'esecuzione del processo **Di protezione finalizzare** la macchina virtuale è pronta per failover.


>[AZURE.NOTE] È anche possibile abilitare la protezione per macchine virtuali nella console di VMM. Fare clic su **Attiva protezione** sulla barra degli strumenti nelle proprietà macchina virtuale > scheda **Il ripristino del sito di Azure** .

Dopo aver attivato la replica è possibile visualizzare le proprietà per macchine Virtuali nelle **Impostazioni** > **Elementi replicato** > nome macchina virtuale. Nel dashboard di **Nozioni di base** è possibile visualizzare informazioni sui criteri di replica per la macchina virtuale e il relativo stato. Per ulteriori informazioni, fare clic su **proprietà** .

### <a name="onboard-existing-virtual-machines"></a>Integrata macchine virtuali esistenti

Se si dispone di macchine virtuali esistenti in VMM che replica con Replica Hyper-V è possibile integrata di protezione il ripristino del sito di Azure come descritto di seguito:

1. Assicurarsi che il server di Hyper-V che ospita la macchina virtuale esistente si trova nel cloud principale e che il server di Hyper-V che ospita la macchina virtuale replica si trova nel cloud secondario.
2. Assicurarsi che un criterio di replica sia configurato per il cloud VMM principale.
2. Attivare la replica per la macchina virtuale principale. Azure il ripristino del sito e VMM garantisce che viene rilevata la stessa host duplicata e macchina virtuale e il ripristino del sito di Azure verranno riutilizzare e ristabilire la replica utilizzando le impostazioni specificate.


## <a name="step-7-test-your-deployment"></a>Passaggio 7: Testare la distribuzione

Per testare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale o creare un piano di ripristino che contiene uno o più macchine virtuali.

### <a name="prepare-for-failover"></a>Preparare l'ambiente per failover

- Per testare completamente la distribuzione è necessario un'infrastruttura per il computer replicato a funzionare come previsto. Se si desidera verificare Active Directory e DNS è possibile creare una macchina virtuale come controller di dominio con il sistema DNS e questo replicare Azure utilizzando il ripristino del sito di Azure. Leggere in altre [Considerazioni sul failover test per Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Le istruzioni fornite in questo articolo viene descritto come eseguire test caso di errore con nessuna rete. Questa opzione verrà verificare che la macchina virtuale ha esito negativo su ma non è possibile verificare le impostazioni di rete per la macchina virtuale. [Altre informazioni](site-recovery-failover.md#run-a-test-failover) sulle altre opzioni.
- Se si desidera eseguire un failover non pianificato anziché caso di errore test tenere presente quanto segue:

    - Se possibile sarà necessario arrestare macchine principale prima di eseguire un failover non pianificato. In questo modo che non si dispone di origine e di replica computer che eseguono nello stesso momento.
    - Quando si esegue un failover non pianificato interrompe la replica dei dati dal computer principale in modo che non è possibile trasferire qualsiasi delta dati dopo l'avvio di un failover non pianificato. Anche se si esegue un failover non pianificato su un piano di ripristino verrà eseguito fino al completamento, anche se si verifica un errore.




### <a name="run-a-test-failover"></a>Eseguire test caso di errore

1. L'esito negativo su una singola macchina virtuale nelle **Impostazioni** > **Replicato elementi**, fare clic su macchina virtuale > **+ Failover Test**.

    ![Test failover](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. L'esito negativo su un piano di ripristino, in **Impostazioni** > di**Ripristino plan di messaggistica unificata**rapida il piano > **Failover Test**. Per creare un pianificazione di ripristino [seguire queste istruzioni](site-recovery-create-recovery-plans.md).
2. **Test Failover**, selezionare **Nessuno**. Con questa opzione è verificare che la macchina virtuale ha esito negativo su come previsto, ma la macchina virtuale replicata non connesso a una rete.

    ![Test failover](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Fare clic su **OK** per avviare il failover. È possibile tenere traccia dello stato facendo clic su Virtual Machine per visualizzare le relative proprietà o nel processo di **Test Failover** nelle **Impostazioni** > **processi** > **processi di ripristino del sito**.
4. Quando il processo di failover raggiunge la fase di **test completata** , eseguire le operazioni seguenti:

    -  Visualizzare la replica macchine Virtuali nel cloud VMM secondario.
    -  Fare clic su **completa il test** per completare il processo failover test.
    -  Fare clic su **note** per registrare e salvare le osservazioni associate failover test.

5. Computer virtuale di test verrà creato nello stesso host host in cui si trova la macchina virtuale duplicata. Viene aggiunta alla stessa area in cui si trova la macchina virtuale duplicata.
6. Dopo aver verificato che iniziano macchine virtuali completata fare clic su **failover test è stata completata**. In questa fase vengono eliminati tutti gli elementi creati automaticamente dal ripristino del sito durante il failover di test.  

    > [AZURE.NOTE] Se continua più di due settimane in caso di errore di test è completata obbligatoriamente.



### <a name="update-dns-with-the-replica-vm-ip-address"></a>Aggiornare il DNS con l'indirizzo IP VM replica

Dopo il failover replica macchina virtuale potrebbe non essere lo stesso indirizzo IP del computer virtuale principale.

- Macchine virtuali aggiorneranno i server DNS che utilizzano dopo l'avvio.
- È inoltre possibile aggiornare manualmente DNS come indicato di seguito:

#### <a name="retrieve-the-ip-address"></a>Recuperare l'indirizzo IP

Eseguire questo script di esempio per recuperare l'indirizzo IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Aggiornare il DNS

Eseguire questo script di esempio per aggiornare il DNS, specificando l'indirizzo IP recuperata utilizzando lo script di esempio precedente.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Passaggi successivi

Dopo la distribuzione sia configurata e in esecuzione, [altre informazioni](site-recovery-failover.md) sui diversi tipi di failover.
