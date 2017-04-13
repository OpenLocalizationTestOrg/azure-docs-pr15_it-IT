<properties
    pageTitle="Replicare macchine virtuali di Hyper-V (senza VMM) Azure utilizzando il ripristino del sito di Azure con il portale di Azure | Microsoft Azure"
    description="Descrive come distribuire Azure il ripristino del sito per la gestione di replica, failover e ripristino delle macchine virtuali di Hyper-V in locale che non sono gestiti da VMM in Azure tramite il portale di Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="raynew"/>


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Replicare macchine virtuali di Hyper-V (senza VMM) Azure utilizzando il ripristino del sito di Azure con il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-hyper-v-site-to-azure.md)
- [Azure classica](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell - Manager delle risorse](site-recovery-deploy-with-powershell-resource-manager.md)



Introduzione al ripristino del sito Azure! Usare questo articolo se si desidera replicare locale Hyper-V virtuale computer che **non sono** gestiti in aree System Center macchine virtuali Manager (VMM) in Azure. In questo articolo viene descritto come configurare la replica utilizzando il ripristino del sito di Azure nel portale di Azure.

> [AZURE.NOTE] Azure include due diversi [modelli di distribuzione](../resource-manager-deployment-model.md) per la creazione e utilizzo delle risorse: Gestione risorse di Azure e classica. Azure include inoltre due portali: portale classico Azure che supporta il modello di distribuzione classica e il portale Azure con supporto per entrambi i modelli di distribuzione.

> Il ripristino del sito Azure supporta il ripristino e migrazione di macchine virtuali di Hyper-V in Azure. La procedura descritta in questo articolo si applica allo stesso modo durante la configurazione della replica di Azure di emergenza o per la migrazione macchine virtuali di Azure

Azure il ripristino del sito nel portale di Azure sono disponibili numerose nuove funzionalità:

- Nel Azure portale, Azure Backup e il ripristino del sito di Azure servizi vengono combinato in un singolo archivio di servizi di recupero in modo che è possibile configurare e gestire la continuità aziendale e il ripristino di emergenza (BCDR) da un'unica posizione. Un dashboard unificato consente di controllare e gestire le operazioni in più siti locali e cloud pubblica Azure.
- Gli utenti con abbonamenti Azure viene completato il provisioning con il programma Cloud soluzione Provider (CSP) ora possono gestire operazioni di ripristino del sito nel portale di Azure.
- Il ripristino del sito nel portale di Azure possibile replicare macchine per gli account di archiviazione di Manager delle risorse. In caso di failover, il ripristino del sito crea macchine virtuali basate su Manager delle risorse in Azure.
- Il ripristino del sito continua per il supporto della replica per gli account di archiviazione classica e failover delle macchine virtuali utilizzando il modello di distribuzione classica.


Dopo aver letto il feedback nella parte inferiore nella sezione commenti Disqus post in questo articolo. Porre domande tecniche nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Panoramica


Le organizzazioni devono una strategia BCDR che determina come App, carichi di lavoro e dati rimangono in esecuzione e disponibili durante il tempo di inattività pianificato e non pianificato e ripristino in condizioni di lavoro normali più presto possibile. La strategia BCDR verrà mantenere i dati business attendibili e recuperabili e assicurarsi che carichi di lavoro siano sempre disponibili quando si verifica di emergenza.

Il ripristino del sito è un servizio Azure contribuisce alla strategia BCDR da coordinazione replica di server fisici locale e macchine virtuali nel cloud (Azure) o a un Data Center secondario. Quando si verificano interruzioni nella propria posizione principale, può eseguire il in posizione secondaria per mantenere le applicazioni e carichi di lavoro disponibili. Potrebbe non riuscire alla propria posizione principale quando viene restituito al funzionamento normale. Altre informazioni, vedere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

Questo articolo fornisce tutte le informazioni necessarie per replicare locale Hyper-V virtuale computer che **non sono** gestiti in aree System Center macchine virtuali Manager (VMM) in Azure. Include una panoramica dell'architettura, informazioni e procedure di distribuzione per la configurazione dei server locali, Azure, un criterio di replica e pianificazione della capacità di pianificazione. Dopo aver configurato l'infrastruttura è possibile abilitare la replica nei computer che si desidera proteggere ed eseguire test caso di errore per convalidare la configurazione. È possibile anche eseguire la migrazione nelle macchine virtuali di Azure eseguendo innanzitutto pianificato in caso di errore e quindi eseguire la migrazione.

## <a name="business-advantages"></a>Vantaggi di Business

- Fornisce failover (Azure) fuori sede per carichi di lavoro di business e le applicazioni in macchine virtuali Hyper-V.
- Fornisce un'unica console di servizi di recupero per semplicità di configurazione e la gestione dei processi di replica, failover e ripristino.
- Consente di semplice failover infrastruttura locale eseguire Azure e fail (ripristino)-back comuni al sito locale.
- È possibile configurare i piani di ripristino con più computer, in modo da carichi di lavoro di applicazione a più livelli esito negativo su insieme.

## <a name="scenario-architecture"></a>Architettura di uno scenario

Questi sono i componenti di uno scenario:

- **Host Hyper-V o cluster**: locale Hyper-V host server o cluster. Host Hyper-V in esecuzione macchine virtuali che si desidera proteggere vengono raccolte nei siti di Hyper-V logici durante la distribuzione il ripristino del sito.
- **Provider di ripristino del sito di Azure e agente di servizi di recupero**: durante la distribuzione di installare il Provider di ripristino del sito di Azure e l'agente di servizi di recupero di Microsoft Azure sui server di host di Hyper-V. Il provider di servizi comunica con il ripristino del sito di Azure tramite HTTPS 443 replicare orchestrazione. L'agente sul server host Hyper-V replica i dati allo spazio di archiviazione Azure su HTTPS 443 per impostazione predefinita.
- **Azure**: È necessario un abbonamento a Azure, un account di archiviazione Azure per archiviare replicato i dati e una rete virtuale Azure in modo da macchine virtuali di Azure sono connessi a una rete dopo il failover.

![Architettura di siti di Hyper-V](./media/site-recovery-hyper-v-site-to-azure/architecture.png)



## <a name="azure-prerequisites"></a>Prerequisiti Azure

Ecco cosa occorre in Azure per distribuire questo scenario.

**Prerequisito** | **Dettagli**
--- | ---
**Account Azure**| È necessario un account di [Microsoft Azure](http://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**Spazio di archiviazione Azure** | È necessario un account di archiviazione standard. È possibile usare un account di archiviazione LRS o GRS. È consigliabile GRS in modo da dati flessibili se si verifica un'interruzione internazionali oppure se non è possibile ripristinare l'area principale. [Altre informazioni](../storage/storage-redundancy.md). L'account deve essere nella stessa regione archivio di servizi di recupero.<br/><br/> Spazio di archiviazione Premium non sono supportata.<br/><br/> Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure vengono create in caso di failover.<br/><br/> [Ulteriori informazioni sui](../storage/storage-introduction.md) Spazio di archiviazione Azure.
**Rete Azure** | È necessario un virtuali Azure che macchine virtuali di Azure si connetterà in caso di failover. La rete virtuale Azure deve essere nella stessa regione archivio di servizi di recupero.

## <a name="on-premises-prerequisites"></a>Prerequisiti locale

Ecco cosa serve locale.

**Prerequisito** | **Dettagli**
--- | ---
**Hyper-V** | Uno o più locale server che eseguono **Windows Server 2012 R2** con più recenti aggiornamenti e il ruolo di Hyper-V abilitato o **Microsoft Hyper-V Server 2012 R2**.<br/><br/>Il server di Hyper-V deve contenere uno o più macchine virtuali.<br/><br/>Server Hyper-V devono essere connessi a Internet, direttamente o tramite un proxy.<br/><br/>Server Hyper-V deve avere correzioni per [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") installato.
**Provider e agente** | Durante la distribuzione di Azure sito ripristino è necessario installare il Provider di ripristino del sito di Azure. L'installazione di Provider verrà inoltre installato l'agente di servizi di recupero Azure ogni server Hyper-V in esecuzione macchine virtuali che si desidera proteggere. Tutti i server di Hyper-V in un archivio il ripristino del sito devono avere le stesse versioni del Provider e agente.<br/><br/>È necessario che il Provider di connettersi per il ripristino del sito di Azure tramite Internet. È possibile inviare il traffico direttamente o tramite un proxy. Si noti che proxy HTTPS in base a non è supportato. Il server proxy, consentire l'accesso a: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blog.core.windows.net <br/><br/> *Store.Core.Windows.NET <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>Se si dispone di regole firewall basato su indirizzo IP sul server, assicurarsi che le regole di consentano la comunicazione con Azure. È necessario consentire di [Intervalli di indirizzi IP del Data Center del Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).<br/><br/>Consentire a intervalli di indirizzi IP per l'area geografica Azure dell'abbonamento e per Usa ovest.

## <a name="protected-machine-prerequisites"></a>Computer protetto prerequisiti


**Prerequisito** | **Dettagli**
--- | ---
**Macchine virtuali protette** | Prima di eseguire il su una macchina virtuale è necessario assicurarsi che il nome assegnato a macchina virtuale Azure conforme ai [Prerequisiti Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). È possibile modificare il nome dopo aver attivato la replica per la macchina virtuale.<br/><br/> Disco singoli computer protetto non deve essere superiore a 1023 GB. Una macchina virtuale possono essere presenti fino a 16 dischi (in questo modo fino a 16 TB).<br/><br/> Condiviso guest disco cluster non sono supportati.<br/><br/> Se l'origine macchine Virtuali include il raggruppamento NIC viene convertita in una singola scheda dopo il controllo a Azure.<br/><br/>Protezione macchine virtuali in esecuzione Linux con un indirizzo IP statico non è supportata.

## <a name="prepare-for-deployment"></a>Prepararsi per la distribuzione

Per prepararsi per la distribuzione che è necessario:

1. [Configurare una rete Azure](#set-up-an-azure-network) in cui macchine virtuali di Azure sarà trovare quando vengono creati dopo il failover.
2. [Configurare un account Azure dello spazio di archiviazione](#set-up-an-azure-storage-account) per i dati replicati.
3. [Preparare l'ambiente Hyper-V host](#prepare-the-hyper-v-hosts) per assicurarsi che possono accedere gli URL necessari.

### <a name="set-up-an-azure-network"></a>Configurare una rete Azure

Configurare una rete Azure. È necessario questo in modo che le macchine virtuali Azure creati dopo failover connessi a una rete.

- La rete dovrebbe essere nella stessa regione quello in cui verrà distribuito nell'archivio di servizi di recupero.
- A seconda del modello di risorsa che si desidera utilizzare per non riuscita su macchine virtuali di Azure, è necessario configurare la rete Azure in [modalità di gestione risorse](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o [classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- È consigliabile che configurare la rete prima di iniziare. In caso contrario, è necessario eseguire durante la distribuzione il ripristino del sito.

> [AZURE.NOTE] [Migrazione delle reti](../resource-group-move-resources.md) attraverso gruppi di risorse all'interno della stessa sottoscrizione o sottoscrizioni non è supportata per le reti utilizzate per la distribuzione il ripristino del sito.

### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione Azure

- È necessario un account di archiviazione di Azure standard per contenere dati replicati in Azure.
- A seconda del modello di risorsa che si desidera utilizzare per non riuscita su macchine virtuali di Azure, è necessario impostare un account in [modalità di gestione risorse](../storage/storage-create-storage-account.md) o [modalità classica](../storage/storage-create-storage-account-classic-portal.md).
- È consigliabile impostare un account di archiviazione prima di iniziare. In caso contrario, è necessario eseguire durante la distribuzione il ripristino del sito. Gli account devono essere presenti nell'area stesso come archivio di servizi di recupero.

> [AZURE.NOTE] [Migrazione degli account di archiviazione](../resource-group-move-resources.md) tra gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni non è supportata per gli account di archiviazione utilizzati per la distribuzione il ripristino del sito.

### <a name="prepare-the-hyper-v-hosts"></a>Preparare l'ambiente host Hyper-V

- Assicurarsi che l'host Hyper-V rispettino i [Prerequisiti](#on-premises-prerequisites).

### <a name="create-a-recovery-services-vault"></a>Creare un archivio di servizi di recupero

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** > **Management** > **eseguire il Backup e il ripristino del sito (OMS)**. In alternativa è possibile fare clic su **Sfoglia** > **Servizi di recupero** archivi > **Aggiungi**.

    ![Nuovo archivio](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. Nella **casella Nome** specificare un nome descrittivo per identificare l'archivio. Se si dispone di più di una sottoscrizione, selezionare una di esse.
4. [Creare un nuovo gruppo di risorse](../resource-group-template-deploy-portal.md) o selezionarne uno esistente e specificare un'area di Azure. Computer verranno replicati in quest'area. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se si desidera accedere rapidamente l'archivio nel dashboard fare clic su **Aggiungi al dashboard** e quindi fare clic su **Crea archivio**.

    ![Nuovo archivio](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

Nuovo archivio verrà visualizzato nel **Dashboard** > **tutte le risorse**, quindi nella finestra principale blade **archivi di servizi di recupero** .

## <a name="getting-started"></a>Guida introduttiva

Il ripristino del sito offre un'introduzione ottimizzata che consente di distribuire più presto possibile. Guida introduttiva di verificare i prerequisiti e illustra le ripristino del sito distribuzione passaggi da eseguire nell'ordine corretto.

In Guida introduttiva si seleziona il tipo di computer che si desidera replicare e in cui si desidera replicare. Configurare i server in locale, gli account di archiviazione Azure e reti. Creare criteri di replica e pianificazione delle capacità. Dopo aver configurato l'infrastruttura si abilita la macchine virtuali replica. È possibile eseguire failover per computer specifici o creare piani di ripristino per avere esito negativo su più dispositivi.

Iniziare introduzione scegliendo la modalità con cui si desidera distribuire il ripristino del sito. Le modifiche flusso introduzione leggermente in base ai propri requisiti di replica.



## <a name="step-1-choose-your-protection-goals"></a>Passaggio 1: Scegliere i propri obiettivi di protezione

Selezionare gli elementi da replicare e in cui si desidera replicare.

1. In e il **ripristino servizi archivi** selezionare l'insieme di credenziali e fare clic su **Impostazioni**.
2. In **Impostazioni** > **Introduzione** fare clic sul **Ripristino del sito** > **passaggio 1: preparare infrastruttura** > **obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. In **obiettivo di protezione** selezionare **Per Azure**e selezionare **Sì, con Hyper-V**. Selezionare **No** per confermare che non si usa VMM. Fare clic su **OK**.

    ![Scegliere gli obiettivi](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Passaggio 2: Configurare l'ambiente di origine

Configurare il sito di Hyper-V, installare il Provider di ripristino del sito di Azure e agente di servizi di ripristino di Windows Azure in host Hyper-V e registrare gli host nell'archivio di.


1. Fare clic su **passaggio 2: preparare infrastruttura** > **origine**. Per aggiungere un nuovo sito di Hyper-V come contenitore per l'host Hyper-V o gruppi, fare clic su **+ Hyper-V sito**.

    ![Impostare un'origine](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. In e il **sito di creare Hyper-V** specificare un nome per il sito. Fare clic su **OK**. Selezionare il sito che appena creato.

    ![Impostare un'origine](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Fare clic su **+ Hyper-V Server** per aggiungere un server per il sito.
4. Nella casella **Aggiungi Server** > **tipo di Server** verificare che vengano visualizzati i **server di Hyper-V** . Assicurarsi che il server di Hyper-V che si desidera aggiungere conformità con i [Prerequisiti](#on-premises-prerequisites) ed è in grado di accedere a URL specificato.
4. Scaricare il file di installazione di Provider di ripristino di Azure del sito. Si eseguirà il file per installare il Provider e l'agente di servizi di recupero su ogni host Hyper-V.
5. Scaricare la chiave di registrazione. È necessario questo quando si esegue l'installazione. La chiave è valida per 5 giorni dopo aver generato.

    ![Impostare un'origine](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Eseguire il Provider di file di installazione in ogni host che è stato aggiunto al sito di Hyper-V. Se sta eseguendo l'installazione in un cluster di Hyper-V, eseguire l'installazione su ciascun nodo. Installazione e la registrazione di ogni nodi Cluster Hyper-V rimangono che macchine virtuali protette anche se si esegue la migrazione tra nodi.

### <a name="install-the-provider-and-agent"></a>Installare il Provider e l'agente

1. Eseguire il Provider di file di installazione.
2. In **Microsoft Update** è possibile scegliere di aggiornamenti in modo che gli aggiornamenti di Provider installati secondo i criteri di Microsoft Update.
3. **Installazione di** accettare o modificare il percorso di installazione di Provider predefinito e fare clic su **Installa**.
4. Nella pagina **Impostazioni di archivio** , fare clic su **Sfoglia** per selezionare il file di archivio chiave che è stato scaricato. Specificare la sottoscrizione il ripristino del sito di Azure, nome dell'archivio e il sito di Hyper-V a cui appartiene il server di Hyper-V.

    ![Registrazione server](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5 nelle **Impostazioni del Proxy** specificare come si connetterà il Provider installati nel server per il ripristino del sito di Azure tramite internet.

- Se si desidera che il Provider di connettersi direttamente selezionare **Connetti direttamente senza un proxy**.
- Se si desidera connettersi con i proxy che è attualmente configurato nel server selezionare **Connetti con le impostazioni del proxy esistente**.
- Se i proxy esistente richiede l'autenticazione o si desidera utilizzare un proxy personalizzato per l'istruzione select di connessione Provider **connettersi con le impostazioni del proxy personalizzato**.
- Se si utilizza un proxy personalizzato è necessario specificare l'indirizzo, porta e le credenziali
- Se si usa un proxy, assicurarsi che gli URL descritti i [Prerequisiti](#on-premises-prerequisites) sono consentiti attraversata.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6 al termine dell'installazione fare clic su **Registra** per registrare il server nell'archivio di.

![Percorso di installazione](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7 termine della registrazione dei metadati da Hyper-V vengono recuperati i server per il ripristino del sito di Azure e il server è visualizzato in **Impostazioni** > **Infrastruttura di ripristino del sito** > **Host Hyper-V** blade.


### <a name="command-line-installation"></a>Installazione della riga di comando

Il Provider di ripristino del sito di Azure e l'agente può essere installati anche utilizzando la seguente riga di comando. Questo metodo può essere utilizzato per installare il provider su un Server Core per Windows Server 2012 R2.

1. Scaricare la chiave di file e la registrazione di installazione del Provider in una cartella. Ad esempio C:\ASR.
2. Dal prompt dei comandi con privilegi elevati eseguire questi comandi per estrarre il programma di installazione di Provider:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Eseguire questo comando per installare i componenti:

            C:\ASR> setupdr.exe /i

4. Eseguire questi comandi per registrare il server nell'archivio di: CD c:\Programmi\Microsoft c:\Programmi\Microsoft Azure sito ripristino Provider\
            C:\Programmi\Microsoft c:\Programmi\Microsoft sito Azure ripristino Provider\> DRConfigurator.exe /r /Friendlyname <friendly name of the server> /delle credenziali<path of the credentials file>

<br/>
Dove:

- **/Credentials** : parametro obbligatorio che specifica la posizione in cui si trova il file di chiave di registrazione  
- **/FriendlyName** : parametro obbligatorio per il nome del server host Hyper-V che viene visualizzata nel portale del ripristino del sito di Azure.
- **/ProxyAddress** : parametro facoltativo che consente di specificare l'indirizzo del server proxy.
- **/ProxyPort** : parametro facoltativo che specifica la porta del server proxy.
- **/proxyUsername** : parametro facoltativo che specifica il nome utente Proxy (se proxy richiede l'autenticazione).
- **/proxyPassword** : parametro facoltativo che specifica la Password per l'autenticazione con il server proxy (se proxy richiede l'autenticazione).


## <a name="step-3-set-up-the-target-environment"></a>Passaggio 3: Configurare l'ambiente di destinazione

Specificare l'account di archiviazione Azure da utilizzare per la replica e la rete Azure a cui si connetterà macchine virtuali di Azure dopo il failover.

1.  Fare clic su **Prepara infrastruttura** > **destinazione** e selezionare l'abbonamento Azure che si desidera utilizzare.
2.  Specificare il modello di distribuzione che si desidera utilizzare per le macchine virtuali dopo il failover.
3.  Il ripristino del sito verifica la presenza di uno o più account di archiviazione di Azure compatibile e reti.

    ![Spazio di archiviazione](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.  Se non sono state create un account di archiviazione e si desidera creare una gestione risorse fare clic su account **+ spazio di archiviazione** per eseguire tale all'interno del testo. In e il **conto dello spazio di archiviazione crea** specificare un nome dell'account, tipo, sottoscrizione e posizione. L'account deve essere nello stesso percorso archivio di servizi di recupero.

    ![Spazio di archiviazione](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

    Se si desidera creare un account di archiviazione utilizzando il modello classico è necessario eseguire tale [nel portale di Azure](../storage/storage-create-storage-account-classic-portal.md).

5.  Se non è stata creata una rete Azure e si desidera creare una gestione risorse fare clic su **+ rete** per eseguire tale all'interno del testo. Nella e **Crea rete virtuale** specificare un nome di rete, indirizzi di intervalli, dettagli subnet, sottoscrizione e posizione. La rete deve essere nello stesso percorso archivio di servizi di recupero.

    ![Rete](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

    Se si desidera creare una rete utilizzando il modello classico è necessario eseguire tale [nel portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).


## <a name="step-4-set-up-replication-settings"></a>Passaggio 4: Configurare le impostazioni di replica

1. Per creare una nuova replica criteri, fare clic su **Prepara infrastruttura** > **Le impostazioni di replica** > **+ Crea e associa**.

    ![Rete](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. **Creare** e associati criteri specificare un nome di criteri.
3. Nella casella **Copia frequenza** specificare la frequenza con cui si desidera replicare dati delta dopo la replica iniziale (ogni 30 secondi, 5 o 15 minuti).
4. In **criteri di conservazione punto di ripristino**, specificare in ore il periodo di tempo la finestra di conservazione per ogni punto di ripristino. Computer protetto possono essere recuperate in un punto qualsiasi all'interno di una finestra.
6. Frequenza **snapshot coerente App** specificare la frequenza (1-12 ore) punti di ripristino contenente snapshot coerenti applicazione sarà creata. Hyper-V vengono utilizzati due tipi di snapshot, ovvero uno snapshot standard che fornisce uno snapshot della macchina virtuale intero incrementale e uno snapshot coerente applicazione che contiene uno snapshot nel momento dei dati dell'applicazione macchina virtuale. Snapshot coerenti applicazione utilizzare Servizio copia ombreggiatura del Volume (VSS) per garantire che le applicazioni in uno stato coerente quando dello snapshot. Si noti che se si abilita snapshot coerenti con l'applicazione, influisce negativamente sulle prestazioni delle applicazioni in esecuzione in macchine virtuali di origine. Assicurarsi che il valore impostato sia minore del numero di punti di ripristino aggiuntive che configurare.
3. Nella **finestra di avvio della replica iniziale** specificare quando avviare la replica iniziale. La replica avviene sulla larghezza di banda internet in modo che è possibile pianificare di fuori dell'orario occupato. Fare clic su **OK**.

    ![Criteri di replica](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Quando si crea un nuovo criterio ha automaticamente associato con il sito di Hyper-V. Fare clic su **OK**. È possibile associare un sito di Hyper-V (e macchine virtuali al suo interno) a più criteri di replica nelle **Impostazioni** > **replica** > nome del criterio > **Associa sito Hyper-V**.

## <a name="step-5-capacity-planning"></a>Passaggio 5: Pianificazione delle capacità

Dopo aver creato il basic infrastruttura configurare è possibile considerare la pianificazione della capacità e stabilire la necessità di risorse aggiuntive.

Il ripristino del sito fornisce una pianificazione capacità che ne faciliti allocare risorse per l'ambiente di origine, componenti di ripristino del sito, rete e lo spazio di archiviazione. È possibile eseguire la pianificazione in modalità rapida per le stime in base a un numero medio di macchine virtuali, dischi e lo spazio di archiviazione o in visualizzazione dettagliata in cui verrà input figure a livello di carico di lavoro. Prima di iniziare è necessario:

- Raccogliere informazioni sull'ambiente di replica, tra cui macchine virtuali, dischi per macchine virtuali e lo spazio di archiviazione per disco.
- Valutare le modifiche (varianza) giornaliere che saranno disponibili per i dati replicati. È possibile utilizzare la [Pianificazione delle capacità per Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) per eseguire questa operazione.

1.  Fare clic su **Download** per scaricare lo strumento e quindi eseguirlo. [Leggere l'articolo](site-recovery-capacity-planner.md) fornito con lo strumento.
2.  Al termine selezionare **Sì** nel **è stato eseguito la pianificazione delle capacità**?

    ![Pianificazione delle capacità](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerazioni sulla larghezza di banda di rete

È possibile utilizzare lo strumento di pianificazione delle capacità per calcolare la larghezza di banda che necessaria per la replica (replica iniziale e quindi delta). Per controllare la quantità della larghezza di banda della replica è necessario tra alcune opzioni:

- **Banda**: Hyper-V che replica in Azure del traffico attraverso un host Hyper-V specifico. È possibile limitare la larghezza di banda sul server host.
- **Modificare la larghezza di banda**: È possibile determinare la larghezza di banda per la replica tramite un paio di chiavi del Registro di sistema.

#### <a name="throttle-bandwidth"></a>Limitare larghezza di banda

1. Aprire lo snap-in Microsoft Azure Backup MMC sul server host Hyper-V. Per impostazione predefinita è disponibile sul desktop o in c:\Programmi\Microsoft c:\Programmi\Microsoft Azure ripristino servizi Agent\bin\wabadmin una scelta rapida per Microsoft Azure Backup.
2. In snap-in fare clic su **Modifica proprietà**.
3. Nella scheda **Throttling** selezionare **Abilita l'utilizzo della larghezza di banda internet la limitazione per le operazioni di backup**e impostare i limiti per il lavoro e non-lavoro ore. Gli intervalli validi sono compresi 512kbps e 102 Mbps al secondo.

    ![Limitare larghezza di banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

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

## <a name="step-6-enable-replication"></a>Passaggio 6: La replica Abilita

A questo punto abilitare replica come indicato di seguito:

1. Fare clic su **passaggio 2: replicare applicazione** > **origine**. Dopo aver attivato la replica per la prima volta che si sarà fare clic su **+ replicare** nell'archivio di abilitare la replica per computer aggiuntivi.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. In e **l'origine** > selezionare il sito di Hyper-V. Fare clic su **OK**.
3. In **destinazione** selezionare l'abbonamento di archivio e il modello di failover da usare in Azure (gestione classic o delle risorse) dopo il failover.
4. Selezionare l'account di archiviazione che si desidera utilizzare. Se si vuole usare un account di archiviazione diverso rispetto a quelle ottenute è necessario è possibile [crearne uno](#set-up-an-azure-storage-account). Per creare un archivio account utilizzando il modello di Manager delle risorse, fare clic su **Crea nuovo**. Se si desidera creare un account di archiviazione utilizzando il modello classico è necessario eseguire tale [nel portale di Azure](../storage/storage-create-storage-account-classic-portal.md). Fare clic su **OK**.
5.  Selezionare la rete Azure e subnet a cui si connetterà macchine virtuali di Azure quando si sta caricare un dopo il failover. Selezionare **Configura adesso per i computer selezionato** per applicare le impostazioni di rete per tutti i computer che si seleziona per la protezione. Selezionare **Configura in seguito** per selezionare la rete Azure in base al computer. Se si desidera utilizzare una rete diversa da quelle è necessario è possibile [crearne uno](#set-up-an-azure-network). Per creare una rete utilizzando il modello di Manager delle risorse, fare clic su **Crea nuovo**. Se si desidera creare una rete utilizzando il modello classico è necessario eseguire tale [nel portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. In **macchine virtuali di** > **Selezionare macchine virtuali** fare clic su e selezionare ogni computer che si desidera replicare. È possibile selezionare solo computer per il quale è possibile abilitare la replica. Fare clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. Nella **finestra delle proprietà** > **configurare le proprietà**, selezionare il sistema operativo per macchine virtuali selezionate e il disco del sistema operativo. Verificare che le macchine Virtuali di Azure nome (destinazione) conforme ai [requisiti di Azure macchina virtuale](site-recovery-best-practices.md#azure-virtual-machine-requirements) e modificarlo se necessario. Fare clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. In **impostazioni di replica** > **configurare le impostazioni della replica**, selezionare il criterio di replica da applicare per le macchine virtuali di protette. Fare clic su **OK**. È possibile modificare il criterio di replica nelle **Impostazioni** > **criteri di replica** > nome del criterio > **Modifica impostazioni**. Le modifiche applicate verranno utilizzate per macchine già replica e nuovo.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

È possibile tenere traccia dello stato del processo di **Attivazione della protezione tramite** **le impostazioni di** > **processi** > **processi di ripristino del sito**. Viene eseguito il processo di **Protezione finalizzare** il computer è pronto per il failover.

### <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà di macchine Virtuali

È consigliabile verificare la proprietà del computer di origine.

1. Fare clic su **Impostazioni** > **Elementi protetti** > **Elementi replicato** > e selezionare il computer.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. Nella **finestra delle proprietà** per visualizzare informazioni di replica e failover per la macchina virtuale.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. Nel **calcolo e rete** > **calcolare proprietà** è possibile specificare le dimensioni di nome e la destinazione macchine Virtuali di Azure. Modificare il nome per soddisfare i requisiti di Azure, se necessario. È anche possibile visualizzare e modificare le informazioni sulla rete di destinazione, subnet e indirizzo IP assegnato a macchina virtuale Azure. Tenere presente quanto segue:

    - È possibile impostare l'indirizzo IP di destinazione. Se non si fornisce un indirizzo eseguito il computer utilizzerà DHCP. Se si imposta un indirizzo che non è disponibile in caso di failover, non viene eseguito il failover. Se l'indirizzo è disponibile nella rete failover test, è possibile utilizzare lo stesso indirizzo IP di destinazione per failover test.
    - Il numero di schede di rete è dovuto la dimensione specificata per la macchina virtuale di destinazione, come indicato di seguito:

        - Se il numero di schede di rete del computer di origine è minore o uguale al numero di schede consentiti per la dimensione di computer di destinazione, la destinazione avrà lo stesso numero di schede come origine.
        - Se il numero di schede per la macchina virtuale di origine è maggiore del numero consentito per verrà usata le dimensioni di destinazione, quindi la dimensione massima di destinazione.
        - Se, ad esempio un computer di origine con due schede di rete e le dimensioni del computer di destinazione supporta quattro, il computer di destinazione sarà disponibili due schede. Se il computer di origine con due schede, ma le dimensioni di destinazione supportati supporta solo uno computer di destinazione avrà solo una scheda.     
        - Se la macchina virtuale ha più schede di rete che tutti si connetterà alla stessa rete.

    ![Abilitare la replica](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.  In **dischi** è possibile vedere il sistema operativo e dischi di dati in macchine Virtuali da replicare.


## <a name="step-7-test-the-deployment"></a>Passaggio 7: Verificare la distribuzione

Per verificare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale o un piano di ripristino che contiene uno o più macchine virtuali.


### <a name="prepare-for-test-failover"></a>Preparare l'ambiente per test failover

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

- Aggiungere un indirizzo IP pubblico per la scheda NIC associata la macchina virtuale Azure per consentire RDP.
- Assicurarsi che non si dispone di alcun criterio di dominio che impedisca la connessione a un computer virtuale tramite un indirizzo pubblico.
- Provare a connettersi. Se non è possibile connettersi verificare che la macchina virtuale sia in esecuzione. Per ulteriori suggerimenti sulla risoluzione dei problemi, leggere questo [articolo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Se si desidera accedere a un sistema operativo Linux dopo failover utilizzando un Secure Shell client (ssh) macchine Virtuali di Azure, eseguire le operazioni seguenti:

**Nel computer locale prima del failover**:

- Assicurarsi che il servizio Secure Shell nella macchina virtuale Azure è impostato per l'avvio automatico all'avvio di sistema.
- Verificare che le regole del firewall consentano una connessione SSH.

**In the Azure macchine Virtuali dopo failover**:

- Le regole gruppo di sicurezza di rete su non riuscito su macchine Virtuali e subnet Azure a cui è connesso necessario consentire le connessioni in ingresso alla porta SSH.
- Per consentire le connessioni in ingresso sulla porta SSH (porta TCP 22 per impostazione predefinita), è necessario creare un endpoint di pubblico.
- Se si accede a macchina virtuale tramite una connessione VPN (strada o VPN da sito) il cliente può utilizzato per direttamente connettere la macchina virtuale su SSH.

### <a name="run-a-test-failover"></a>Eseguire test caso di errore

Per eseguire test failover eseguire le operazioni seguenti:

1. L'esito negativo su una singola macchina virtuale nelle **Impostazioni** > **Replicato elementi**, fare clic su macchina virtuale > **+ Failover Test**.

    ![Test failover](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. L'esito negativo su un piano di ripristino, in **Impostazioni** > di**Ripristino plan di messaggistica unificata**rapida il piano > **Failover Test**. Per creare un pianificazione di ripristino [seguire queste istruzioni](site-recovery-create-recovery-plans.md).

3. **Test** failover selezionare la rete Azure a cui verrà connesso macchine virtuali di Azure dopo che si verifica il failover.

    ![Test failover](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

4. Fare clic su **OK** per avviare il failover. È possibile tenere traccia dello stato facendo clic su Virtual Machine per aprire relative proprietà o il processo di **Test Failover** nelle **Impostazioni** > **processi di ripristino del sito**.
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


## <a name="failover"></a>Failover

Al termine della replica iniziale per i computer, è possibile richiamare failover in base alle esigenze. Il ripristino del sito supporta diversi tipi di failover - Test failover, failover pianificato e failover non pianificato.
[Altre informazioni](site-recovery-failover.md) sui diversi tipi di failover e descrizioni dettagliate dei quando e come eseguire ognuna di esse.

> [AZURE.NOTE] Se si intende eseguire la migrazione di macchine virtuali di Azure, si consiglia di utilizzare un' [operazione Failover pianificato](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary) per eseguire la migrazione di macchine virtuali di Azure. Dopo l'applicazione di migrazione viene convalidata in Azure tramite test failover, utilizzare la procedura indicata in [Migrazione completa](#Complete-migration-of-your-virtual-machines-to-Azure) per eseguire la migrazione di macchine virtuali. Non è necessario eseguire un Commit o Elimina. Migrazione completa viene completata la migrazione, rimuove la protezione per la macchina virtuale e interrompe la fatturazione il ripristino del sito di Azure per il computer.


###<a name="run-an-unplanned-failover"></a>Eseguire un Failover non pianificato

Questa scelta quando un sito principale diventa inaccessibile a causa di un incidente imprevisto, ad esempio un risparmio energia elettrica o attacchi virus. Questa procedura viene descritto come eseguire un 'failover non pianificato"per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina virtuale nella scheda macchine virtuali. Prima di iniziare, verificare che tutte le macchine virtuali di cui su che si vuole avere esito negativo stata completata la replica iniziale.

1. Selezionare **ripristino plan di messaggistica unificata > recoveryplan_name**.
2. Scegliere e il piano ripristino **Failover non pianificato**.
3. Nella pagina **Failover non pianificato** , scegliere il percorso di origine e destinazione.
4. Selezionare **arrestare macchine virtuali e sincronizzare i dati più recenti** per specificare che il ripristino del sito arrestare la macchine virtuali protette e sincronizzare i dati in modo che la versione più recente dei dati verrà eseguita su.
5. Dopo il failover macchine virtuali sono in un commit in sospeso.  Fare clic su **Commit** per eseguire il commit il failover.

[Ulteriori informazioni](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>Completamento della migrazione delle macchine virtuali di Azure


>[AZURE.NOTE] I passaggi seguenti si applicano solo se si esegue la migrazione di macchine virtuali di Azure

1. Eseguire failover pianificato come indicato [di seguito](site-recovery-failover.md)
2. In **Impostazioni > replicato elementi**del mouse la macchina virtuale e scegliere **Migrazione completa**

    ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

2. Fare clic su **OK** per completare la migrazione. È possibile avanzamento facendo clic su Virtual Machine per visualizzare le relative proprietà o utilizzando il processo di migrazione completa in **Impostazioni > processi di ripristino sito**.


## <a name="monitor-your-deployment"></a>Monitorare la distribuzione

Ecco come è possibile monitorare le impostazioni di configurazione, stato e dell'integrità per la distribuzione il ripristino del sito:

1. Fare clic sul nome dell'archivio per accedere al dashboard di **Nozioni di base** . In questo dashboard è possibile processi il ripristino del sito, lo stato della replica, piani di ripristino, lo stato dei server e gli eventi.  È possibile personalizzare Essentials per mostrare le sezioni e i layout che risultano particolarmente utili per l'utente, tra cui lo stato degli altri archivi di Backup e ripristino del sito.

    ![Nozioni di base](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. Nella sezione **integrità** è possibile monitorare server del sito in cui si verificano problemi e gli eventi generati per il ripristino del sito ultime 24 ore.
3. È possibile gestire e il monitoraggio della replica di **Elementi replicato**, **Ripristino plan di messaggistica unificata**e riquadri di **Processi di ripristino del sito** . È possibile drill-down dei processi nelle **Impostazioni** -> **processi** -> **Processi di ripristino del sito**.
