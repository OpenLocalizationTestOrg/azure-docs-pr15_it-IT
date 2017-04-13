<properties
    pageTitle="Replicare macchine virtuali di Hyper-V in cloud VMM Azure con il ripristino del sito portale di Azure | Microsoft Azure"
    description="Descrive come distribuire Azure il ripristino del sito per la gestione di replica, failover e ripristino delle macchine virtuali di Hyper-V in cloud VMM in Azure tramite il portale di Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Replicare macchine virtuali di Hyper-V in cloud VMM Azure utilizzando il ripristino del sito di Azure con il portale di Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmm-to-azure.md)
- [Azure classica](site-recovery-vmm-to-azure-classic.md)
- [Gestione risorse di PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell classica](site-recovery-deploy-with-powershell.md)

Introduzione al ripristino del sito Azure! Usare questo articolo se si desidera replicare macchine virtuali di Hyper-V locale gestito in aree System Center Virtual Machine Manager (VMM) in Azure utilizzando il ripristino del sito di Azure nel portale di Azure.

> [AZURE.NOTE]Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model
> ) per la creazione e utilizzo delle risorse: Gestione risorse di Azure e classica. Azure include inoltre due portali: portale classico Azure che supporta il modello di distribuzione classica e il portale Azure con supporto per entrambi i modelli di distribuzione.


Azure il ripristino del sito nel portale di Azure offre diverse nuove caratteristiche:

- Nel portale di Azure servizi Azure eseguire il Backup e il ripristino del sito di Azure vengono combinati in un singolo archivio di servizi di recupero, in modo che è possibile configurare e gestire la continuità aziendale e il ripristino di emergenza (BCDR) da un'unica posizione. Un dashboard unificato consente di controllare e gestire le operazioni in più siti locali e cloud pubblica Azure.
- Gli utenti con abbonamenti Azure viene completato il provisioning con il programma Cloud soluzione Provider (CSP) ora possono gestire operazioni di ripristino del sito nel portale di Azure.
- Il ripristino del sito nel portale di Azure possibile replicare macchine per gli account di archiviazione di Manager delle risorse di Azure. In caso di failover, il ripristino del sito crea macchine virtuali basate su Manager delle risorse in Azure.
- Il ripristino del sito continua per il supporto della replica per gli account di archiviazione classica. In caso di failover, il ripristino del sito consente di creare macchine virtuali utilizzando il modello classico.


Dopo aver letto in questo articolo, pubblicare eventuali commenti nella parte inferiore di commenti Disqus. Porre domande tecniche nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Panoramica

Le organizzazioni devono una strategia BCDR che determina come App, carichi di lavoro e dati rimangono in esecuzione e disponibili durante il tempo di inattività pianificato e non pianificato e ripristino in condizioni di lavoro normali più presto possibile. La strategia BCDR deve mantenere i dati business attendibili e recuperabili e garantire che carichi di lavoro sempre disponibile quando si verifica danno.

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR dalla orchestrazione replica di server fisici locale e macchine virtuali nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nella propria posizione principale, non si riesce sopra a posizione secondaria per mantenere le applicazioni e carichi di lavoro disponibili. Riescano a tornare alla propria posizione primaria restituisce al funzionamento normale. Altre informazioni, vedere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

Questo articolo fornisce tutte le informazioni che necessarie per replicare locale macchine virtuali di Hyper-V nel cloud VMM in Azure. Include una panoramica dell'architettura, informazioni e procedure di distribuzione per la configurazione di Azure, server locali, le impostazioni di replica e pianificazione della capacità di pianificazione. Dopo aver configurato l'infrastruttura è possibile abilitare la replica nei computer che si desidera proteggere e verificare che failover funzioni.


## <a name="business-advantages"></a>Vantaggi di Business

- Il ripristino del sito offre una protezione fuori sede per carichi di lavoro di business e le applicazioni in macchine virtuali di Hyper-V.
- Il portale di servizi di recupero rappresenta una posizione centralizzata per configurare, gestire e monitorare replica, failover e ripristino.
- Facilmente, è possibile eseguire failover dall'infrastruttura locale Azure e failback (ripristino) comuni ai server host Hyper-V nel sito locale.
- È possibile configurare i piani di ripristino con più computer in modo da carichi di lavoro di applicazione a più livelli esito negativo su insieme.


## <a name="scenario-architecture"></a>Architettura di uno scenario


Questi sono i componenti di uno scenario:

- **Server VMM**: un server VMM locale con una o più aree.
- **Host Hyper-V o cluster**: server host Hyper-V o cluster gestito nel cloud VMM.
- **Provider di ripristino del sito di Azure e agente di servizi di recupero**: durante la distribuzione è installare il Provider di ripristino del sito di Azure sul server VMM e l'agente di Microsoft Azure ripristino servizi nel server host Hyper-V. Il Provider di sul server VMM comunica con il ripristino del sito tramite HTTPS 443 replicare orchestrazione. L'agente sul server host Hyper-V replica i dati allo spazio di archiviazione Azure su HTTPS 443 per impostazione predefinita.
- **Azure**: È necessario un abbonamento a Azure, un account di archiviazione Azure per archiviare replicato i dati e una rete virtuale Azure in modo da macchine virtuali di Azure sono connessi a una rete dopo il failover.

![Topologia E2A](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Prerequisiti Azure

Ecco cosa occorre in Azure per distribuire questo scenario.

**Prerequisito** | **Dettagli**
--- | ---
**Account Azure**| È necessario un account di [Microsoft Azure](http://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**Spazio di archiviazione Azure** | È necessario un account di archiviazione di Azure standard per archiviare i dati replicati. È possibile usare un account di archiviazione LRS o GRS. È consigliabile GRS in modo da dati flessibili se si verifica un'interruzione internazionali oppure se non è possibile ripristinare l'area principale. [Altre informazioni](../storage/storage-redundancy.md). L'account deve essere nella stessa regione archivio di servizi di recupero.<br/><br/>Spazio di archiviazione Premium non sono supportata.<br/><br/> Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure vengono create in caso di failover. <br/><br/> [Ulteriori informazioni sui](../storage/storage-introduction.md) Spazio di archiviazione Azure.
**Rete Azure** | È necessario un virtuali Azure in grado di connettersi macchine virtuali di Azure in caso di failover. La rete deve essere nella stessa regione archivio di servizi di recupero.

## <a name="on-premises-prerequisites"></a>Prerequisiti locale

Ecco cosa occorre locale

**Prerequisito** | **Dettagli**
--- | ---
**VMM**| Uno o più server VMM in esecuzione su System Center 2012 R2. Ogni server VMM deve disporre di una o più aree configurati. Deve contenere un cloud:<br/><br/> Uno o più gruppi host VMM.<br/><br/> Uno o più server host Hyper-V o cluster di ogni gruppo host.<br/><br/>[Altre informazioni](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) sull'impostazione cloud VMM.
**Hyper-V** | Server host Hyper-V deve essere in esecuzione almeno **Windows Server 2012 R2** con ruolo Hyper-V o **Microsoft Hyper-V Server 2012 R2** e sono stati installati gli aggiornamenti più recenti.<br/><br/> Un server di Hyper-V deve contenere uno o più macchine virtuali.<br/><br/> Un cluster che include macchine virtuali da replicare o server host Hyper-V devono essere gestiti in un'area VMM.<br/><br/>Server Hyper-V devono essere connessi a Internet, direttamente o tramite un proxy.<br/><br/>Server di Hyper-V devono presentare le correzioni indicate nell'articolo [2961977](https://support.microsoft.com/kb/2961977) installato.<br/><br/>Server host Hyper-V necessario l'accesso a internet per la replica di dati di Azure.
**Provider e agente** | Durante la distribuzione di Azure sito ripristino si installano il Provider di ripristino del sito di Azure sul server VMM e l'agente di servizi di recupero host Hyper-V. Il Provider e l'agente necessario connettersi a Azure tramite internet direttamente o tramite un proxy. Si noti che un proxy di HTTPS non sono supportato. Il server proxy nel server VMM e host Hyper-V devono consentire l'accesso a: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blob.core.windows.net <br/><br/> *. store.core.windows.net<br/><br/>Se si dispone di regole firewall basato su indirizzo IP sul server VMM, assicurarsi che le regole di consentano la comunicazione con Azure. È necessario consentire di [Intervalli di indirizzi IP del Data Center del Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/><br/>Consentire a intervalli di indirizzi IP per l'area geografica Azure dell'abbonamento e per Usa ovest.<br/><br/>Inoltre. il server proxy sul server VMM acceda https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>Computer protetto prerequisiti


**Prerequisito** | **Dettagli**
--- | ---
**Macchine virtuali protette** | Prima di eseguire il su una macchina virtuale, assicurarsi che il nome assegnato a macchina virtuale Azure conforme ai [Prerequisiti Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). È possibile modificare il nome dopo aver attivato la replica per la macchina virtuale. <br/><br/> Disco singoli computer protetto non deve essere superiore a 1023 GB. Una macchina virtuale possono essere presenti fino a 16 dischi (in questo modo fino a 16 TB).<br/><br/> Condiviso guest disco cluster non sono supportati.<br/><br/> Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) avvio non è supportato.<br/><br/> Se l'origine macchine Virtuali include il raggruppamento NIC viene convertita in una singola scheda dopo il controllo a Azure.<br/><br/>Protezione macchine virtuali in esecuzione Linux con un indirizzo IP statico non è supportata.

## <a name="prepare-for-deployment"></a>Prepararsi per la distribuzione

Per prepararsi per la distribuzione che è necessario:

1. [Configurare una rete Azure](#set-up-an-azure-network) in cui macchine virtuali di Azure sarà trovare dopo il failover.
2. [Configurare un account Azure dello spazio di archiviazione](#set-up-an-azure-storage-account) per i dati replicati.
4. [Preparare il server VMM](#prepare-the-vmm-server) per la distribuzione il ripristino del sito.
5. [Preparazione per la connessione di rete](#prepare-for-network-mapping). Configurare le reti, in modo che è possibile configurare la connessione di rete durante la distribuzione il ripristino del sito.

### <a name="set-up-an-azure-network"></a>Configurare una rete Azure

È necessario un network Azure in modo che le macchine virtuali Azure verranno create dopo failover si connetterà a tale.

- La rete dovrebbe essere nella stessa regione quello in cui si distribuisce l'archivio di servizi di recupero.
- A seconda del modello di risorsa che si desidera utilizzare per non riuscita su macchine virtuali di Azure, è necessario configurare la rete Azure in [modalità di gestione risorse](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o [classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- È consigliabile che configurare la rete prima di iniziare. In caso contrario, è necessario eseguire durante la distribuzione il ripristino del sito.

> [AZURE.NOTE] [Migrazione delle reti](../resource-group-move-resources.md) attraverso gruppi di risorse all'interno della stessa sottoscrizione o sottoscrizioni non è supportata per le reti utilizzate per la distribuzione il ripristino del sito.


### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione Azure

- È necessario un account di archiviazione di Azure standard per contenere dati replicati in Azure. L'account deve essere nella stessa regione archivio di servizi di recupero.
- A seconda del modello di risorsa che si desidera utilizzare per non riuscita su macchine virtuali di Azure, impostare un account in [modalità di gestione risorse](../storage/storage-create-storage-account.md) o [modalità classica](../storage/storage-create-storage-account-classic-portal.md).
- È consigliabile impostare un account prima di iniziare. In caso contrario, è necessario eseguire durante la distribuzione il ripristino del sito.

> [AZURE.NOTE] [Migrazione degli account di archiviazione](../resource-group-move-resources.md) tra gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni non è supportata per gli account di archiviazione utilizzati per la distribuzione il ripristino del sito.

### <a name="prepare-the-vmm-server"></a>Preparare il server VMM

- Assicurarsi che il server VMM conformità con i [Prerequisiti](#on-premises-prerequisites).
- Durante la distribuzione il ripristino del sito, è possibile specificare che tutte le aree in un server VMM devono essere disponibile nel portale di Azure. Se si vuole solo aree specifiche all'interno del portale, è possibile attivare questa impostazione nel cloud nella console di amministrazione VMM.


### <a name="prepare-for-network-mapping"></a>Prepararsi per la corrispondenza di rete

È necessario configurare la connessione di rete durante la distribuzione il ripristino del sito. Rete il mapping tra origine VMM VM reti e destinazione Azure reti per poter effettuare le operazioni seguenti:

- Computer che hanno esito negativo sulla stessa rete può connettersi tra loro, anche se si sta non nello stesso modo o lo stesso piano di ripristino.
- Se un gateway di rete è impostato sulla destinazione della rete Azure, Azure macchine virtuali di connettersi a macchine virtuali locale.
- Per configurare la rete mappatura riguarda le azioni preparare l'ambiente:

    - Assicurarsi di macchine virtuali nel server di origine Hyper-V host connessi a una rete VMM VM. La rete deve essere collegata a una rete logica associato nel cloud.
    - Una rete Azure come descritto [sopra](#set-up-an-azure-network)

- [Altre informazioni](site-recovery-network-mapping.md) sulle modalità di connessione di rete.


## <a name="create-a-recovery-services-vault"></a>Creare un archivio di servizi di recupero

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Management** > **servizi di recupero**. In alternativa è possibile fare clic su **Sfoglia** > **Servizi di recupero** archivi > **Aggiungi**.

    ![Nuovo archivio](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. In **nome**specificare un nome descrittivo per identificare l'archivio. Se si dispone di più di una sottoscrizione, selezionare una di esse.
4. [Creare un gruppo di risorse](../resource-group-template-deploy-portal.md), oppure selezionarne uno esistente. Specificare un'area di Azure. Computer verranno replicati in quest'area. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se si vuole accedere rapidamente l'archivio nel dashboard, fare clic su **Aggiungi a dashboard** > **Crea archivio**.

    ![Nuovo archivio](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

Nuovo archivio viene visualizzato nel **Dashboard** > **tutte le risorse**, quindi nella finestra principale blade **archivi di servizi di recupero** .

## <a name="getting-started"></a>Guida introduttiva

Il ripristino del sito offre un'introduzione ottimizzata che consente di distribuire più presto possibile. Guida introduttiva di verificare i prerequisiti e illustra le ripristino del sito distribuzione passaggi da eseguire nell'ordine corretto.

In Guida introduttiva si seleziona il tipo di computer che si desidera replicare e in cui si desidera replicare. Configurare i server in locale, gli account di archiviazione Azure e reti. Creare criteri di replica e pianificazione delle capacità. Dopo aver configurato l'infrastruttura si abilita la macchine virtuali replica. È possibile eseguire failover per computer specifici o creare piani di ripristino per avere esito negativo su più dispositivi.

Iniziare introduzione scegliendo la modalità con cui si desidera distribuire il ripristino del sito. Le modifiche flusso introduzione leggermente in base ai propri requisiti di replica.



## <a name="step-1-choose-your-protection-goals"></a>Passaggio 1: Scegliere i propri obiettivi di protezione

Selezionare gli elementi da replicare e in cui si desidera replicare.

1. In e il **ripristino servizi archivi** , selezionare l'insieme di credenziali e fare clic su **Impostazioni**.
2. In **Guida introduttiva** fare clic sul **Ripristino del sito** > **passaggio 1: preparare infrastruttura** > **obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. In **obiettivo di protezione** selezionare **Per Azure**e selezionare **Sì, con Hyper-V**. Selezionare **Sì** per confermare che si sta utilizzando VMM per gestire host Hyper-V e il sito di ripristino. Fare clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>Passaggio 2: Configurare l'ambiente di origine

Installare il Provider di ripristino del sito di Azure sul server VMM e registrare il server nell'archivio di. Installare l'agente di servizi di ripristino di Windows Azure in host Hyper-V.

1. Fare clic su **passaggio 2: preparare infrastruttura** > **origine**.

    ![Impostare un'origine](./media/site-recovery-vmm-to-azure/set-source1.png)

2. **Preparare l'ambiente** origine fare clic su **+ VMM** per aggiungere un server VMM.

    ![Impostare un'origine](./media/site-recovery-vmm-to-azure/set-source2.png)

3. Nel controllo blade **Aggiungi Server** **System Center VMM server** visualizzato nel **tipo di Server** e che il server VMM soddisfi i [requisiti di URL e i prerequisiti](#on-premises-prerequisites).
4. Scaricare il file di installazione di Provider di ripristino di Azure del sito.
5. Scaricare la chiave di registrazione. È necessario quando si esegue l'installazione. La chiave è valida per cinque giorni dopo aver generato.

    ![Impostare un'origine](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Installare il Provider di ripristino del sito di Azure sul server VMM.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurare il Provider di ripristino del sito di Azure

1.  Eseguire il Provider di file di installazione.
2. In **Microsoft Update** è possibile scegliere di aggiornamenti in modo che gli aggiornamenti di Provider installati secondo i criteri di Microsoft Update.
3. L' **installazione**, accettare o modificare il percorso di installazione di Provider predefinito e fare clic su **Installa**.

    ![Percorso di installazione](./media/site-recovery-vmm-to-azure/provider2.png)

4. Al termine dell'installazione fare clic su **Registra** per registrare il server VMM nell'archivio di.
5. Nella pagina **Impostazioni di archivio** , fare clic su **Sfoglia** per selezionare il file di chiave archivio. Specificare la sottoscrizione il ripristino del sito di Azure e nome dell'archivio.

    ![Registrazione server](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. In **Connessione a Internet**, specificare come Provider eseguito sul server VMM si connetterà il ripristino del sito tramite internet.

    - Se si desidera che il Provider di connettersi direttamente selezionare **Connetti direttamente il ripristino del sito di Azure senza un proxy**.
    - Se i proxy esistente richiede l'autenticazione o che si desidera utilizzare un'istruzione select proxy personalizzato **connessione per il ripristino del sito di Azure tramite un server proxy**.
    - Se si utilizza un proxy personalizzato, specificare l'indirizzo, porta e le credenziali.
    - Se si usa un proxy che deve è già stato consentito URL descritto in [Prerequisiti](#on-premises-prerequisites).
    - Se si utilizza un proxy personalizzato verrà creato un account RunAs VMM (DRAProxyAccount) automaticamente con le credenziali proxy specificato. Configurare il server proxy in modo che l'account è possibile autenticare correttamente. Le impostazioni dell'account RunAs VMM possono essere modificate nella console di VMM. In **Impostazioni**, espandere **sicurezza** > **Gli account**e quindi modificare la password per DRAProxyAccount. È necessario riavviare il servizio VMM in modo che questa impostazione è effettiva.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Accettare o modificare la posizione di un certificato SSL che viene generato automaticamente per la crittografia dei dati. Questo certificato viene utilizzato se si abilita la crittografia dei dati per un'area protetta da Azure nel portale del ripristino del sito di Azure. Proteggere il certificato. Quando si esegue caso di errore in Azure sarà necessaria per decrittografare, se è abilitata la crittografia dei dati.


8. In **nome Server**, specificare un nome descrittivo per identificare il server VMM nell'archivio di. In una configurazione cluster, specificare il nome di ruolo cluster VMM.
9. Abilitare **i metadati cloud di sincronizzazione** se si desidera sincronizzare i metadati per tutte le aree sul server VMM con l'archivio. Questa azione è sufficiente si verifichi una volta in ogni server. Se non si desidera sincronizzare tutte le aree, è possibile lasciare deselezionato questa impostazione e sincronizzare ogni cloud singolarmente nelle proprietà cloud nella console di VMM. Fare clic su **Registra** per completare il processo.

    ![Registrazione server](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. Avvia registrazione. Al termine della registrazione, il server è visualizzato nella **Impostazioni** > **server** blade nell'archivio di.


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Installazione di riga di comando per il Provider di ripristino del sito di Azure

È possibile installare il Provider di ripristino del sito di Azure dalla riga di comando. Questo metodo può essere utilizzato per installare il Provider su Server Core per Windows Server 2012 R2.

1. Scaricare la chiave di file e la registrazione di installazione del Provider in una cartella. Ad esempio C:\ASR.
2. Dal prompt dei comandi con privilegi elevati eseguire questi comandi per estrarre il programma di installazione di Provider:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Eseguire questo comando per installare i componenti:

            C:\ASR> setupdr.exe /i

4. Eseguire questi comandi per registrare il server nell'archivio di:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Dove:

- **/Credentials**: parametro obbligatorio che specifica la posizione del file di chiave di registrazione.  
- **/FriendlyName**: parametro obbligatorio per il nome del server host Hyper-V che viene visualizzata nel portale del ripristino del sito di Azure.
- - **/EncryptionEnabled**: parametro facoltativo quando si attiva la replica macchine virtuali di Hyper-V in VMM nuvole in Azure. Specificare se si desidera crittografare macchine virtuali di Azure (a crittografia resto). Assicurarsi che il nome del file con estensione **pfx** . La crittografia è disattivata per impostazione predefinita.
- **/ProxyAddress**: parametro facoltativo che consente di specificare l'indirizzo del server proxy.
- **/ProxyPort**: parametro facoltativo che specifica la porta del server proxy.
- **/proxyUsername**: parametro facoltativo che specifica il nome utente proxy (se proxy richiede l'autenticazione).
- **/proxyPassword**: parametro facoltativo che specifica la password per l'autenticazione con il server proxy (se il proxy richiede l'autenticazione).


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installare l'agente di servizi di ripristino di Windows Azure in host Hyper-V

1. Dopo aver configurato il Provider, è necessario scaricare il file di installazione per l'agente di servizi di ripristino di Windows Azure. Eseguire l'installazione su ciascun server Hyper-V nel cloud VMM.

    ![Siti di Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Nella pagina **Controllo prerequisiti** , fare clic su **Avanti**. Eventuali prerequisiti mancanti verranno installati automaticamente.

    ![Agente di servizi di recupero prerequisiti](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. Nella pagina **Impostazioni di installazione** , accettare o modificare il percorso di installazione e la posizione della cache. È possibile configurare la cache in un'unità che contiene almeno 5 GB di spazio di archiviazione disponibile, ma è consigliabile un'unità di cache con più di 600 GB di spazio libero. Fare clic su **Installa**.
4. Una volta completata l'installazione, fare clic su **Chiudi** per completare.

    ![Registrare MARS agente](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Installazione di riga di comando per l'agente di servizi di recupero del sito di Windows Azure

È possibile installare l'agente di servizi di recupero di Microsoft Azure dalla riga di comando utilizzando il comando seguente:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configurare l'accesso proxy internet per il ripristino del sito da host Hyper-V

L'agente di servizi di recupero in esecuzione in host Hyper-V acceda internet Azure per la replica macchine Virtuali. Se si accede a internet tramite un proxy, configurarlo come indicato di seguito:

1. Aprire lo snap-in Microsoft Azure Backup MMC nell'host Hyper-V. Per impostazione predefinita è disponibile sul desktop o in c:\Programmi\Microsoft c:\Programmi\Microsoft Azure ripristino servizi Agent\bin\wabadmin una scelta rapida per Microsoft Azure Backup.
2. In snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Configurazione Proxy** specificare informazioni sul server proxy.

    ![Registrare MARS agente](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Assicurarsi che l'agente possa raggiungere gli URL descritti i [Prerequisiti](#on-premises-prerequisites).


## <a name="step-3-set-up-the-target-environment"></a>Passaggio 3: Configurare l'ambiente di destinazione

Specificare l'account di archiviazione Azure da utilizzare per la replica e la rete Azure a cui si connetterà macchine virtuali di Azure dopo il failover.

1.  Fare clic su **Prepara infrastruttura** > **destinazione** e selezionare l'abbonamento Azure che si desidera utilizzare.
2.  Specificare il modello di distribuzione che si desidera utilizzare per le macchine virtuali dopo il failover.
3.  Il ripristino del sito verifica la presenza di uno o più account di archiviazione di Azure compatibile e reti.

    ![Spazio di archiviazione](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Se non sono state create un account di archiviazione e si desidera creare una gestione di risorse, fare clic su **+ account di archiviazione** per eseguire tale all'interno del testo.  In e il **conto dello spazio di archiviazione crea** specificare un nome dell'account, tipo, sottoscrizione e posizione. L'account deve essere nello stesso percorso archivio di servizi di recupero.

    ![Spazio di archiviazione](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Si noti che:

    - Se si desidera creare un account di archiviazione utilizzando il modello classico è nel portale di Azure. [Ulteriori informazioni](../storage/storage-create-storage-account-classic-portal.md)
    - Se si usa un account di archiviazione premium per i dati replicati, è necessario configurare un account di spazio di archiviazione aggiuntivo standard per l'archiviazione dei registri di replica che acquisire le modifiche ai dati in locale.

4.  Se non è stata creata una rete Azure e si desidera creare una gestione risorse fare clic su **+ rete** per eseguire tale all'interno del testo. Nella e **Crea rete virtuale** specificare un nome di rete, indirizzi di intervalli, dettagli subnet, sottoscrizione e posizione. La rete deve essere nello stesso percorso archivio di servizi di recupero.

    ![Rete](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Se si desidera creare una rete utilizzando il modello classico è necessario farlo nel portale di Azure. [Altre informazioni](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurare il mapping di rete

- [Lettura](#prepare-for-network-mapping) indica una rapida panoramica delle quali connessione di rete. [Leggere questo](site-recovery-network-mapping.md) per una spiegazione più dettagliata.
- Verificare che macchine virtuali sul server VMM sono connessi a una rete macchine Virtuali e che sia stato creato almeno una rete virtuale Azure. Più reti macchine Virtuali possono essere associate a un'unica rete Azure.

Configurare il mapping come indicato di seguito:

1. In **Impostazioni** > **Infrastruttura di ripristino del sito** > **i mapping di rete** > **Rete Mapping**, fare clic sull'icona **+ mappatura di rete** .

    ![Connessione di rete](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Il **mapping di rete Aggiungi** scegliere origine VMM il server **Azure** con quello di destinazione.
3. Verificare l'abbonamento e il modello di distribuzione dopo il failover.
4. In **rete di origine**, selezionare la rete di macchine Virtuali locale di origine da mappare dall'elenco associato al server VMM.
5. **Rete di destinazione**, selezionare la rete Azure in cui replica macchine virtuali di Azure sarà trovare quando vengono creati. Fare clic su **OK**.

    ![Connessione di rete](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Ecco cosa accade quando si inizia a connessione di rete:

- Macchine virtuali esistenti della rete macchine Virtuali di origine si è connessi alla rete di destinazione quando si inizia a mapping. Nuove macchine virtuali connessione alla rete macchine Virtuali origine sono connessi alla rete Azure mappata quando si verifica della replica.
- Se si modifica una connessione di rete esistente, verrà connesso macchine virtuali di replica utilizzando le nuove impostazioni.
- Se la rete di destinazione dispone di più subnet e una di queste subnet ha lo stesso nome subnet in cui si trova la macchina virtuale di origine, quindi la macchina virtuale duplicata verrà connesso subnet destinazione dopo il failover.
- Se non vi è alcuna subnet di destinazione con un nome corrispondente, la macchina virtuale verrà connesso prima subnet nella rete.



## <a name="step-4-set-up-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica


1. Per creare un nuovo criterio di replica, fare clic su **Prepara infrastruttura** > **Le impostazioni di replica** > **+ Crea e associa**.

    ![Rete](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. In **Crea e criteri di associazione**, specificare un nome di criteri.
3. Nella casella **Frequenza copia**, specificare la frequenza con cui si desidera replicare dati delta dopo la replica iniziale (ogni 30 secondi, 5 o 15 minuti).
4. In **criteri di conservazione punto di ripristino**, specificare in ore il periodo di tempo la finestra di conservazione per ogni punto di ripristino. Computer protetto possono essere recuperate in un punto qualsiasi all'interno di una finestra.
6. **Frequenza snapshot coerente App**, specificare la frequenza (1-12 ore) punti di ripristino contenente snapshot coerenti applicazione sarà creata. Hyper-V vengono utilizzati due tipi di snapshot, ovvero uno snapshot standard che fornisce uno snapshot della macchina virtuale intero incrementale e uno snapshot coerente applicazione che contiene uno snapshot nel momento dei dati dell'applicazione macchina virtuale. Snapshot coerenti applicazione utilizzare Servizio copia ombreggiatura del Volume (VSS) per garantire che le applicazioni in uno stato coerente quando dello snapshot. Si noti che se si abilita snapshot coerenti con l'applicazione, influisce negativamente sulle prestazioni delle applicazioni in esecuzione in macchine virtuali di origine. Assicurarsi che il valore impostato sia minore del numero di punti di ripristino aggiuntive che configurare.
3. **Ora di inizio della replica iniziale**, indicano quando avviare la replica iniziale. La replica avviene sulla larghezza di banda internet in modo che è possibile pianificare di fuori dell'orario occupato.
5. **Crittografare i dati archiviati in Azure**, specificare se crittografare dati resto in archiviazione Azure. Fare clic su **OK**.

    ![Criteri di replica](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Quando si crea un nuovo criterio automaticamente associata con il cloud VMM. Fare clic su **OK**. È possibile associare aggiuntive cloud VMM (e macchine virtuali nel loro) con il criterio di replica nelle **Impostazioni** > **replica** > nome del criterio > **Associare Cloud VMM**.

    ![Criteri di replica](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Passaggio 5: Pianificazione delle capacità

Dopo aver creato il basic infrastruttura configurare è possibile considerare la pianificazione della capacità e stabilire la necessità di risorse aggiuntive.

Il ripristino del sito fornisce una pianificazione capacità che ne faciliti allocare risorse per l'ambiente di origine, componenti di ripristino del sito, rete e lo spazio di archiviazione. È possibile eseguire la pianificazione in modalità rapida per le stime in base a un numero medio di macchine virtuali, dischi e lo spazio di archiviazione o in visualizzazione dettagliata in cui verrà input figure a livello di carico di lavoro. Prima di iniziare è necessario:

- Raccogliere informazioni sull'ambiente di replica, tra cui macchine virtuali, dischi per macchine virtuali e lo spazio di archiviazione per disco.
- Valutare le modifiche (varianza) giornaliere che saranno disponibili per i dati replicati. È possibile utilizzare la [pianificazione delle capacità per Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) per eseguire questa operazione.

1.  Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Leggere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2.  Al termine selezionare **Sì** nel **è stato eseguito la pianificazione delle capacità**?

    ![Pianificazione delle capacità](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerazioni sulla larghezza di banda di rete

È possibile utilizzare lo strumento di pianificazione delle capacità per calcolare la larghezza di banda che necessaria per la replica (replica iniziale e quindi delta). Per controllare la quantità della larghezza di banda della replica è necessario tra alcune opzioni:

- **Banda**: Hyper-V che replica in un sito secondario del traffico attraverso un host Hyper-V specifico. È possibile limitare la larghezza di banda sul server host.
- **Modificare la larghezza di banda**: È possibile determinare la larghezza di banda per la replica tramite un paio di chiavi del Registro di sistema.

#### <a name="throttle-bandwidth"></a>Limitare larghezza di banda

1. Aprire lo snap-in Microsoft Azure Backup MMC sul server host Hyper-V. Per impostazione predefinita è disponibile sul desktop o in c:\Programmi\Microsoft c:\Programmi\Microsoft Azure ripristino servizi Agent\bin\wabadmin una scelta rapida per Microsoft Azure Backup.
2. In snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Throttling** selezionare **Abilita l'utilizzo della larghezza di banda internet la limitazione per le operazioni di backup**e impostare i limiti per il lavoro e non-lavoro ore. Gli intervalli validi sono compresi 512kbps e 102 Mbps al secondo.

    ![Limitare larghezza di banda](./media/site-recovery-vmm-to-azure/throttle2.png)

È anche possibile utilizzare il cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) per impostare la limitazione. Ecco un esempio:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set OBMachineSetting NoThrottle** non indica che nessuna limitazione richiesta.


#### <a name="influence-network-bandwidth"></a>Influenza della larghezza di banda di rete

Il valore del Registro di sistema **UploadThreadsPerVM** controlla il numero di thread utilizzati per il trasferimento di dati (replica iniziale o delta) di un disco. Un valore superiore aumenta la larghezza di banda di rete utilizzato per la replica. Il valore del Registro di sistema **DownloadThreadsPerVM** specifica il numero di thread utilizzato per il trasferimento di dati durante il failback.

1. Nel Registro di sistema passare **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

    - Modificare il valore **UploadThreadsPerVM** (o creare la chiave se non esiste) per il controllo thread utilizzati per la replica su disco.
    - Modificare il valore **DownloadThreadsPerVM** (o creare la chiave se non esiste) per il controllo thread utilizzati per il traffico failback comuni.
2. Il valore predefinito è "4". In una rete "overprovisioned", queste chiavi del Registro di sistema devono essere modificate dai valori predefiniti. Il valore massimo è 32. Monitorare il traffico per ottimizzare il valore.

## <a name="step-6-enable-replication"></a>Passaggio 6: La replica Abilita

A questo punto abilitare replica come indicato di seguito:

1. Fare clic su **passaggio 2: replicare applicazione** > **origine**. Dopo aver attivato la replica per la prima volta che si fa clic su **+ replicare** nell'archivio di abilitare la replica per computer aggiuntivi.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. In e **l'origine** > selezionare il server VMM e nel cloud in cui si trovano host Hyper-V. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. In **destinazione** selezionare l'abbonamento, failover post al modello di distribuzione e l'account di archiviazione che si usa per i dati replicati.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Selezionare l'account di archiviazione che si desidera utilizzare. Se si vuole usare un account di archiviazione diverso rispetto a quelle ottenute è necessario è possibile [crearne uno](#set-up-an-azure-storage-account). Per creare un archivio account utilizzando il modello di Manager delle risorse, fare clic su **Crea nuovo**. Se si desidera creare un account di archiviazione utilizzando il modello classico è eseguire tale [nel portale di Azure](../storage/storage-create-storage-account-classic-portal.md). Fare clic su **OK**.
5. Selezionare la rete Azure e subnet a cui si connetterà macchine virtuali di Azure quando si sta caricare un dopo il failover. Selezionare **Configura adesso per i computer selezionato** per applicare le impostazioni di rete per tutti i computer che si seleziona per la protezione. Selezionare **Configura in seguito** per selezionare la rete Azure in base al computer. Se si desidera utilizzare una rete diversa da quelle è necessario è possibile [crearne uno](#set-up-an-azure-network). Per creare una rete utilizzando il modello di Manager delle risorse, fare clic su **Crea nuovo**. Se si desidera creare una rete utilizzando il modello classico è necessario eseguire tale [nel portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet. Fare clic su **OK**.
6. In **macchine virtuali di** > **Selezionare macchine virtuali** fare clic su e selezionare ogni computer che si desidera replicare. È possibile selezionare solo computer per il quale è possibile abilitare la replica. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. Nella **finestra delle proprietà** > **configurare le proprietà**, selezionare il sistema operativo per macchine virtuali selezionate e il disco del sistema operativo. Fare clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. In **impostazioni di replica** > **configurare le impostazioni della replica**, selezionare il criterio di replica da applicare per le macchine virtuali di protette. Fare clic su **OK**. È possibile modificare il criterio di replica nelle **Impostazioni** > **criteri di replica** > nome del criterio > **Modifica impostazioni**. Le modifiche applicate vengono utilizzate per macchine già replica e nuovo.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/enable-replication7.png)

È possibile tenere traccia dello stato del processo di **Attivazione della protezione tramite** **le impostazioni di** > **processi** > **processi di ripristino del sito**. Viene eseguito il processo di **Protezione finalizzare** il computer è pronto per il failover.

### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà di macchine Virtuali

È consigliabile verificare la proprietà del computer di origine. Tenere presente che il nome di macchine Virtuali di Azure deve essere conforme ai [requisiti di Azure macchina virtuale](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Fare clic su **Impostazioni** > **Elementi protetti** > **Elementi replicato** > e selezionare il computer per visualizzarne i dettagli.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. Nella **finestra delle proprietà** per visualizzare informazioni di replica e failover per la macchina virtuale.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. Nel **calcolo e rete** > **calcolare proprietà** è possibile specificare le dimensioni di nome e la destinazione macchine Virtuali di Azure. Modificare il nome per soddisfare i [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) , se necessario. È anche possibile visualizzare e modificare le informazioni sulla rete di destinazione, subnet e indirizzo IP che è stata assegnata a macchina virtuale Azure. Tenere presente quanto segue:

    - È possibile impostare l'indirizzo IP di destinazione. Se non si fornisce un indirizzo eseguito il computer utilizzerà DHCP. Se si imposta un indirizzo che non è disponibile in caso di failover, non viene eseguito il failover. Se l'indirizzo è disponibile nella rete failover test, è possibile utilizzare lo stesso indirizzo IP di destinazione per failover test.
    - Il numero di schede di rete è dovuto la dimensione specificata per la macchina virtuale di destinazione, come indicato di seguito:

        - Se il numero di schede di rete del computer di origine è minore o uguale al numero di schede consentiti per la dimensione di computer di destinazione, la destinazione avrà lo stesso numero di schede come origine.
        - Se il numero di schede per la macchina virtuale di origine è maggiore del numero consentito per la dimensione di destinazione viene utilizzato il valore massimo dimensioni di destinazione.
        - Se, ad esempio un computer di origine con due schede di rete e le dimensioni del computer di destinazione supporta quattro, il computer di destinazione sarà disponibili due schede. Se il computer di origine con due schede, ma le dimensioni di destinazione supportati supporta solo uno computer di destinazione avrà solo una scheda.     
        - Se la macchina virtuale ha più schede di rete che tutti si connetterà alla stessa rete.

    ![Abilitare la replica](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  In **dischi** è possibile vedere il sistema operativo e dischi di dati in macchine Virtuali da replicare.



## <a name="step-7-test-your-deployment"></a>Passaggio 7: Testare la distribuzione

Per verificare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale o un piano di ripristino che contiene uno o più macchine virtuali.


### <a name="prepare-for-failover"></a>Preparare l'ambiente per failover

- Per eseguire test caso di errore è consigliabile creare una nuova rete Azure che è isolati dalla rete produzione Azure (questo è il comportamento predefinito quando si crea una nuova rete in Azure). [Altre informazioni](site-recovery-failover.md#run-a-test-failover) sull'esecuzione di test failover.
- Per ottenere le massime prestazioni quando non si riesce sopra a Azure, installare l'agente di Azure nel computer protetto. Fa avvio più veloce e utile per la risoluzione dei problemi. Installare l'agente di [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) o [Linux](https://github.com/Azure/WALinuxAgent) .
- Per testare completamente la distribuzione è necessario un'infrastruttura per il computer replicato a funzionare come previsto. Se si desidera verificare Active Directory e DNS è possibile creare una macchina virtuale come controller di dominio con il sistema DNS e questo replicare Azure utilizzando il ripristino del sito di Azure. Leggere in altre [Considerazioni sul failover test per Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Se si desidera eseguire un failover non pianificato anziché caso di errore test tenere presente quanto segue:

    - Se possibile sarà necessario arrestare macchine principale prima di eseguire un failover non pianificato. In questo modo che non si dispone di origine e di replica computer che eseguono nello stesso momento.
    - Quando si esegue un failover non pianificato interrompe la replica dei dati dal computer principale in modo che non è possibile trasferire qualsiasi delta dati dopo l'avvio di un failover non pianificato. Anche se si esegue un failover non pianificato su un piano di ripristino verrà eseguito fino al completamento, anche se si verifica un errore.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Prepararsi a cui connettersi macchine virtuali di Azure dopo il failover

Se si desidera connettersi alle macchine virtuali di Azure utilizzando RDP dopo il failover, assicurarsi di eseguire le operazioni seguenti:

**Nel computer locale prima del failover**:

- Per l'accesso tramite internet abilitare RDP, assicurarsi che le regole di porte TCP e UDP vengono aggiunte per il **pubblico**e assicurarsi che sia consentito RDP in **Windows Firewall** -> **consentiti App e funzionalità** per tutti i profili.
- Per l'accesso tramite una connessione da sito abilitare RDP nel computer e assicurarsi che sia consentito RDP in **Windows Firewall** -> **consentiti App e funzionalità** per le reti di **dominio** e **Private** .
- Installare l' [agente di macchine Virtuali di Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) nel computer locale.
- Assicurarsi che i criteri SAN del sistema operativo sia impostato su OnlineAll. [Ulteriori informazioni]( https://support.microsoft.com/kb/3031135)
- Disattivare il servizio IPSec prima di eseguire il failover.

**In the Azure macchine Virtuali dopo failover**:

- Aggiungere un endpoint pubblico per il protocollo RDP (porta 3389) e specificare le credenziali per l'accesso.
- Assicurarsi che non si dispone di alcun criterio di dominio che impedisca la connessione a un computer virtuale tramite un indirizzo pubblico.
- Provare a connettersi. Se non è possibile connettersi, verificare che la macchina virtuale sia in esecuzione. Per ulteriori suggerimenti sulla risoluzione dei problemi, leggere questo [articolo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Se si desidera accedere a un sistema operativo Linux dopo failover utilizzando un Secure Shell client (ssh) macchine Virtuali di Azure, eseguire le operazioni seguenti:

**Nel computer locale prima del failover**:

- Assicurarsi che il servizio Secure Shell nella macchina virtuale Azure è impostato per l'avvio automatico all'avvio di sistema.
- Verificare che le regole del firewall consentano una connessione SSH.

**In the Azure macchine Virtuali dopo failover**:

- Le regole gruppo di sicurezza di rete su non riuscito su macchine Virtuali e subnet Azure a cui è connesso necessario consentire le connessioni in ingresso alla porta SSH.
- Per consentire le connessioni in ingresso sulla porta SSH (porta TCP 22 per impostazione predefinita), è necessario creare un endpoint di pubblico.
- Se si accede a macchina virtuale tramite una connessione VPN (strada o da un sito VPN) il client può essere utilizzato per direttamente connettere la macchina virtuale su SSH.


### <a name="run-a-test-failover"></a>Eseguire test caso di errore

Per eseguire test failover eseguire le operazioni seguenti:

1. L'esito negativo su una singola macchina virtuale nelle **Impostazioni** > **Replicato elementi**, fare clic su macchina virtuale > **+ Failover Test**.
2. L'esito negativo su un piano di ripristino, in **Impostazioni** > di**Ripristino plan di messaggistica unificata**rapida il piano > **Failover Test**. Per creare un pianificazione di ripristino [seguire queste istruzioni](site-recovery-create-recovery-plans.md).

3. **Test** failover selezionare la rete Azure a cui si connettono macchine virtuali di Azure dopo che si verifica il failover.
4. Fare clic su **OK** per avviare il failover. È possibile tenere traccia dello stato facendo clic su Virtual Machine per visualizzare le relative proprietà o nel processo di **Test Failover** nelle **Impostazioni** > **processi di ripristino del sito**.
5. Quando il failover raggiunge la fase di **test completata** , eseguire le operazioni seguenti:

    1. Visualizzare la macchina virtuale duplicata nel portale di Azure. Verificare che la macchina virtuale viene avviato correttamente.
    2. Se si è impostato in macchine virtuali di accesso alla rete locale è possibile avviare una connessione Desktop remoto alla macchina virtuale.
    3. Fare clic su **completa il test** per terminare.
    4. Fare clic su **note** per registrare e salvare le osservazioni associate failover test.
    5. Fare clic su **failover test è stata completata**. Pulire l'ambiente di testing per automaticamente spegnere ed eliminare la macchina virtuale di test.
    6. In questa fase vengono eliminate eventuali elementi o macchine virtuali create automaticamente dal ripristino del sito durante il failover di test. Eventuali elementi aggiuntivi creati per il failover test non vengono eliminati.

    > [AZURE.NOTE] Se continua più di due settimane in caso di errore di test è completata obbligatoriamente.

6. Dopo il failover anche dovrebbe essere possibile vedere replica Azure computer vengono visualizzati nel portale di Azure > **macchine virtuali**. È necessario assicurarsi che la macchina virtuale è la dimensione appropriata, che si è connesso alla rete appropriato e che sia in esecuzione.
7. Se si [preparato per le connessioni dopo failover](#prepare-to-connect-to-Azure-VMs-after-failover) dovrebbe essere possibile connettere la macchina virtuale Azure.


## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Ecco come è possibile monitorare le impostazioni di configurazione, stato e dell'integrità per la distribuzione il ripristino del sito:

1. Fare clic sul nome dell'archivio per accedere al dashboard di **Nozioni di base** . In questo dashboard è possibile processi il ripristino del sito, lo stato della replica, piani di ripristino, lo stato dei server e gli eventi.  È possibile personalizzare Essentials per mostrare le sezioni e i layout che risultano particolarmente utili per l'utente, tra cui lo stato degli altri archivi di Backup e ripristino del sito.

    ![Nozioni di base](./media/site-recovery-vmm-to-azure/essentials.png)

2. Nella sezione **integrità** è possibile monitorare sito server, ovvero VMM o configurazione server in cui si verificano problemi e gli eventi generati per il ripristino del sito ultime 24 ore.
3. È possibile gestire e il monitoraggio della replica di **Elementi replicato**, **Ripristino plan di messaggistica unificata**e riquadri di **Processi di ripristino del sito** . È possibile drill-down dei processi nelle **Impostazioni** -> **processi** -> **Processi di ripristino del sito**.


## <a name="next-steps"></a>Passaggi successivi

Dopo la distribuzione sia configurata e in esecuzione, [altre informazioni](site-recovery-failover.md) sui diversi tipi di failover.
