<properties
    pageTitle="Replicare macchine virtuali di Hyper-V in cloud VMM Azure | Microsoft Azure"
    description="In questo articolo viene descritto come replicare macchine virtuali di Hyper-V in host Hyper-V nel cloud System Center VMM in Azure."
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
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Replicare macchine virtuali di Hyper-V in cloud VMM Azure

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Manager delle risorse](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portale classica](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - classico](site-recovery-deploy-with-powershell.md)



Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md).

## <a name="overview"></a>Panoramica

In questo articolo viene descritto come distribuire il ripristino del sito per replicare macchine virtuali di Hyper-V Server Hyper-V host che si trovano nel cloud privati VMM in Azure.

L'articolo include i prerequisiti per lo scenario e viene illustrato come configurare un archivio il ripristino del sito, ottenere il Provider di ripristino del sito di Azure installato sul server VMM origine, registrare il server nell'archivio di, aggiungere un account di archiviazione Azure, installare l'agente di servizi di ripristino di Windows Azure nel server host Hyper-V, configurare le impostazioni di protezione per cloud VMM applicati a tutte le macchine virtuali protette e quindi attivare la protezione per tali macchine virtuali. Completare il processo verificando failover per assicurarsi che tutto funzioni come previsto.

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.

## <a name="architecture"></a>Architettura

![Architettura](./media/site-recovery-vmm-to-azure-classic/topology.png)

- Il Provider di ripristino del sito di Azure installato VMM durante l'installazione il ripristino del sito e il server VMM registrato nell'archivio del ripristino del sito. Il provider di servizi comunica con il ripristino del sito per gestire orchestrazione replica.
- L'agente di servizi di ripristino di Windows Azure viene installato sul server host Hyper-V durante la distribuzione il ripristino del sito. Gestisce la replica dei dati per lo spazio di archiviazione Azure.


## <a name="azure-prerequisites"></a>Prerequisiti Azure

Ecco cosa serve in Azure.

**Prerequisito** | **Dettagli**
--- | ---
**Account Azure**| È necessario un account di [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**Spazio di archiviazione Azure** | È necessario un account di archiviazione Azure per archiviare i dati replicati. Replicato dati vengono archiviati in archiviazione Azure e macchine virtuali di Azure disattivate backup in caso di failover. <br/><br/>È necessario un [account di archiviazione ridondanti geografico standard](../storage/storage-redundancy.md#geo-redundant-storage). L'account deve nella stessa regione servizio il ripristino del sito ed essere associato all'abbonamento stesso. Si noti che la replica per gli account di archiviazione premium non è attualmente supportati e non deve essere utilizzato.<br/><br/>[Ulteriori informazioni sui](../storage/storage-introduction.md) Spazio di archiviazione Azure.
**Rete Azure** | È necessario un virtuali Azure che macchine virtuali di Azure si connetterà in caso di failover. La rete virtuale Azure deve essere nella stessa regione come archivio il ripristino del sito.

## <a name="on-premises-prerequisites"></a>Prerequisiti locale

Ecco cosa serve locale.

**Prerequisito** | **Dettagli**
--- | ---
**VMM** | È necessario almeno un server VMM distribuito come server fisico o virtuale autonomo o come un cluster virtuale. <br/><br/>Il server VMM deve essere in esecuzione System Center 2012 R2 con gli ultimi aggiornamenti cumulativi.<br/><br/>È necessario almeno un cloud configurato sul server VMM.<br/><br/>Nel cloud di origine che si desidera proteggere deve contenere uno o più gruppi di host VMM.<br/><br/>Altre informazioni sull'impostazione cloud VMM in [procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) nel blog di Keith Mayer.
**Hyper-V** | È necessario uno o più server host Hyper-V o cluster nel cloud VMM. Il server host deve disporre e uno o più macchine virtuali. <br/><br/>Il server di Hyper-V deve essere in esecuzione almeno **Windows Server 2012 R2** con il ruolo Hyper-V o **Microsoft Hyper-V Server 2012 R2** e sono stati installati gli aggiornamenti più recenti.<br/><br/>Qualsiasi server Hyper-V contenente macchine virtuali che si desidera proteggere deve trovarsi in un'area VMM.<br/><br/>Se si esegue Hyper-V in una nota cluster tale gestore cluster non viene creati automaticamente se si dispone di un cluster basato su indirizzo IP statico. È necessario configurare manualmente il gestore di cluster. [Altre informazioni](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) in post di blog del Ezio Finn.
**Computer protetto** | Macchine virtuali che si desidera proteggere devono soddisfare i [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## <a name="network-mapping-prerequisites"></a>Prerequisiti di mapping di rete
Quando si proteggere macchine virtuali nelle mappe di rete Azure mapping tra le reti macchine Virtuali nel server VMM di origine e destinazione Azure reti per abilitare le operazioni seguenti:

- Tutti i computer quali failover sulla stessa rete è possibile connettersi a altro, indipendentemente dalla quale piano di ripristino si trovano in.
- Se un gateway di rete è impostata sulla destinazione della rete Azure, macchine virtuali di connettersi a altre macchine virtuali locale.
- Se non Configura rete mapping solo macchine virtuali che non tramite lo stesso piano di ripristino saranno in grado di connettersi tra loro dopo il controllo a Azure.

Se si desidera distribuire connessione di rete è necessario le operazioni seguenti:

- Macchine virtuali che si desidera proteggere nel server VMM di origine deve essere connesso a una rete macchine Virtuali. La rete deve essere collegata a una rete logica associato nel cloud.
- Una rete Azure a cui è possibile connettersi replicate macchine virtuali dopo il failover. Selezionare la rete in fase di failover. La rete dovrebbe essere nella stessa regione come abbonamento il ripristino del sito di Azure.

Preparare l'ambiente per il mapping di rete come indicato di seguito:

1. [Ulteriori informazioni sui](site-recovery-network-mapping.md) requisiti per il mapping rete.
2. Preparare reti macchine Virtuali in VMM:

    - [Impostare le reti logiche](https://technet.microsoft.com/library/jj721568.aspx).
    - [Configurare le reti macchine Virtuali](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Passaggio 1: Creare un archivio il ripristino del sito

1. Accedere al [Portale di gestione](https://portal.azure.com) dal server VMM che si desidera registrare.
2. Fare clic su **servizi di dati** > **servizi di recupero** > **archivio di ripristino del sito**.
3. Fare clic su **Crea nuova** > **Creazione rapida**.
4. In **nome**immettere un nome descrittivo per identificare l'archivio.
5. Nell' **area geografica**, selezionare la località geografica per l'archivio. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Fare clic su **Crea archivio**.

    ![Nuovo archivio](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Verificare se sulla barra di stato per verificare che l'archivio è stato creato correttamente. L'archivio verrà elencato come **attivo** nella pagina principale dei servizi di recupero.

## <a name="step-2-generate-a-vault-registration-key"></a>Passaggio 2: Generare una chiave di registrazione archivio

Generare una chiave di registrazione nell'archivio di. Dopo aver scaricato il Provider di ripristino del sito di Azure e installarlo nel server VMM, è necessario utilizzare questo tasto per registrare il server VMM nell'archivio di.

1. Nella pagina **Servizi di recupero** , fare clic su archivio per aprire la pagina avvio rapido. Guida introduttiva può essere aperti anche in qualsiasi momento tramite l'icona.

    ![Icona di avvio veloce](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. Nell'elenco a discesa selezionare **tra un sito VMM locale e Microsoft Azure**.
3. **Preparare il server VMM**, fare clic su file di **chiave di registrazione genera** . Il file di chiave viene generato automaticamente e valido per 5 giorni dopo la generazione. Se non si accede al portale Azure dal server VMM è necessario copiare il file nel server.

    ![Chiave di registrazione](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Passaggio 3: Installare il Provider di ripristino del sito Azure

1. In **Avvio rapido** > **preparare VMM server**, fare clic su **Scarica Microsoft Azure sito ripristino Provider per l'installazione nel server VMM** per ottenere l'ultima versione di file di installazione del Provider.
2. Eseguire il file sul server VMM di origine.

    >[AZURE.NOTE] Se VMM viene distribuito in un cluster e si sta eseguendo l'installazione di Provider per la prima volta installarlo in un nodo attivo e completare l'installazione per registrare il server VMM nell'archivio di. Installare il Provider sugli altri nodi. Si noti che se si esegue l'aggiornamento del Provider è necessario eseguire l'aggiornamento in tutti i nodi perché che devono essere eseguiti tutti la stessa versione di Provider.

3. Il programma di installazione non un controllo prerequirements e le richieste di autorizzazione per arrestare il servizio VMM per iniziare l'installazione di Provider. Al termine dell'installazione, il servizio VMM verrà riavviato automaticamente. Se sta eseguendo l'installazione in un cluster VMM verrà richiesto di interrompere il ruolo di Cluster.

4. In **Microsoft Update** è possibile scegliere di aggiornamenti. Con questa impostazione abilitata Provider verranno installati in base a criteri di Microsoft Update.

    ![Aggiornamenti di Microsoft](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  Il percorso di installazione per il Provider è impostato su ** <SystemDrive>\Programmi\Microsoft System Center 2012 R2\Virtual computer Manager\bin**. Fare clic su **Installa**.

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. Dopo aver installato il provider di servizi fare clic su **Registra** per registrare il server nell'archivio di.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. In **nome archivio**di verificare il nome dell'archivio in cui verrà registrato il server. Fare clic su *Avanti*.

    ![Registrazione server](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. In **Connessione a Internet** specificare la modalità di connessione Provider eseguito sul server VMM a Internet. Selezionare **Connetti con le impostazioni del proxy esistente** per utilizzare le impostazioni di connessione Internet predefinito configurate sul server.

    ![Impostazioni di Internet](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

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

    ![LastPage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Dopo la registrazione, recupero dei metadati dal server VMM per il ripristino del sito di Azure. Il server è visualizzato nella scheda **Server VMM** nella pagina **server** nell'archivio di.

### <a name="command-line-installation"></a>Installazione della riga di comando

Il Provider di ripristino del sito di Azure può essere installato utilizzando la seguente riga di comando. Questo metodo può essere utilizzato per installare il provider su un Server Core per Windows Server 2012 R2.

1. Scaricare la chiave di file e la registrazione di installazione del Provider in una cartella. Ad esempio: C:\ASR.
2. Arrestare il servizio System Center Virtual Machine Manager
3. Da un prompt dei comandi con privilegi elevato estrarre il programma di installazione di Provider con questi comandi:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installare il provider come indicato di seguito:

        C:\ASR> setupdr.exe /i

5. Registrare il Provider come indicato di seguito:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Parametri in cui sono i seguenti:

 - **/Credentials** : parametro obbligatorio che specifica la posizione in cui si trova il file di chiave di registrazione  
 - **/FriendlyName** : parametro obbligatorio per il nome del server host Hyper-V che viene visualizzata nel portale del ripristino del sito di Azure.
 - **/EncryptionEnabled** : parametro facoltativo per specificare se si vuole crittografia macchine virtuali di Azure (a crittografia resto). Il nome del file deve avere estensione **pfx** .
 - **/ProxyAddress** : parametro facoltativo che consente di specificare l'indirizzo del server proxy.
 - **/ProxyPort** : parametro facoltativo che specifica la porta del server proxy.
 - **/proxyUsername** : parametro facoltativo che specifica il nome utente proxy.
 - **/proxyPassword** : parametro facoltativo che specifica la password del proxy.  


## <a name="step-4-create-an-azure-storage-account"></a>Passaggio 4: Creare un account di archiviazione Azure

1. Se non si dispone di un account di archiviazione Azure fare clic su **Aggiungi un Account di archiviazione di Azure** per creare un account.
2. Creare un account con attivata la replica geografico. Deve nella stessa regione servizio il ripristino del sito di Azure ed essere associato all'abbonamento stesso.

    ![Account di archiviazione](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] [Migrazione degli account di archiviazione](../resource-group-move-resources.md) tra gruppi di risorse all'interno della stessa sottoscrizione o tra le sottoscrizioni non è supportata per gli account di archiviazione utilizzati per la distribuzione il ripristino del sito.

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Passaggio 5: Installare l'agente di servizi di recupero Azure

Installare l'agente di servizi di ripristino di Windows Azure in ogni server host Hyper-V nel cloud VMM.

1. Fare clic su **Guida introduttiva** > **agente servizi di recupero di scaricare Azure sito e installare su host** per ottenere la versione più recente del file di installazione agente.

    ![Installare l'agente di servizi di recupero dati](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. Eseguire il file di installazione in ogni server host Hyper-V.
3. Nella pagina **Controllo prerequisiti** fare clic su **Avanti**. Eventuali prerequisiti mancanti verranno installati automaticamente.

    ![Agente di servizi di recupero prerequisiti](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. Nella pagina **Impostazioni di installazione** specificare nel punto in cui si desidera installare l'agente e selezionare il percorso della cache in cui verranno installato metadati di backup. Fare clic su **Installa**.
5. Al termine dell'installazione fare clic su **Chiudi** per completare la procedura guidata.

    ![Registrare MARS agente](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Installazione della riga di comando

È anche possibile installare l'agente di servizi di recupero di Microsoft Azure dalla riga di comando questo comando:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Passaggio 6: Configurare cloud impostazioni di protezione

Dopo aver registrato il server VMM, è possibile configurare le impostazioni di protezione cloud. L'opzione **Sincronizza dati cloud con l'archivio** viene abilitata quando è stato installato il Provider in modo che tutte le aree sul server VMM verranno visualizzato nella scheda <b>Elementi protetti</b> nell'archivio di.

![Cloud pubblicati](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Nella pagina Guida introduttiva, fare clic su **configurare la protezione per cloud VMM**.
2. Nella scheda **Elementi protetta** , fare clic su cloud che si desidera configurare e passare alla scheda **configurazione** .
3. Nella finestra di **destinazione** selezionare **Azure**.
4. In **Account di archiviazione** selezionare l'account di archiviazione Azure che utilizzare per la replica.
5. Impostare **Crittografa memorizzati dati** **disattivato**. Questa impostazione specifica che i dati devono essere crittografati replicato tra il sito locale e Azure.
6. Nella casella **Copia frequenza** lasciare l'impostazione predefinita. Questo valore indica la frequenza di sincronizzazione dei dati tra le posizioni di origine e destinazione.
7. In **punti di ripristino Mantieni per**lasciare l'impostazione predefinita. Con un valore predefinito pari a zero, solo il punto di ripristino più recente per una macchina virtuale principale è archiviato in un server di host duplicata.
8. **Frequenza degli snapshot coerenti con l'applicazione**, lasciare l'impostazione predefinita. Questo valore indica la frequenza di creare snapshot. Snapshot usare servizio Copia ombreggiatura del Volume (VSS) per garantire che le applicazioni in uno stato coerente quando dello snapshot.  Se si imposta un valore, verificare che il valore è minore rispetto al numero di punti di ripristino aggiuntive che configurare.
9. In **ora di inizio della replica**specificare quando deve iniziare la replica iniziale dei dati di Azure. Verrà utilizzato il fuso orario del server di host Hyper-V. È consigliabile pianificare la replica iniziale durante le ore.

    ![Impostazioni di replica cloud](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Dopo aver salvato le impostazioni di un processo verrà creato e può essere monitorato nella scheda **processi** . Tutti i server host Hyper-V nel cloud origine VMM saranno configurati per la replica.

Dopo il salvataggio, è possono modificare le impostazioni di cloud nella scheda **Configura** . Per modificare il percorso di destinazione o di un account di archiviazione di destinazione, è necessario rimuovere la configurazione di cloud e quindi riconfigurare nel cloud. Si noti che se si cambia l'account di archiviazione la modifica viene applicata solo per macchine virtuali abilitati per la protezione dopo l'account di archiviazione è stato modificato. Macchine virtuali esistenti non vengono migrate nel nuovo account di archiviazione.

## <a name="step-7-configure-network-mapping"></a>Passaggio 7: Configurare il mapping di rete
Prima di iniziare il mapping di rete verificare che macchine virtuali sul server VMM origine connessi a una rete macchine Virtuali. Inoltre creare uno o più reti virtuali Azure. Si noti che è possono eseguire il mapping di più reti macchine Virtuali a un'unica rete Azure.

1. Nella pagina Guida introduttiva, fare clic su **mappa reti**.
2. Nella scheda **reti** nella **posizione di origine**, selezionare il server VMM di origine. In **percorso di destinazione** selezionare Azure.
3. Nelle reti di **origine** in un elenco delle reti macchine Virtuali associato al server VMM. Nelle reti di **destinazione** vengono visualizzate le reti Azure associate all'abbonamento.
4. Selezionare la rete macchine Virtuali di origine e fare clic su **mappa**.
5. Nella pagina **Selezionare una rete di destinazione** , selezionare la destinazione della rete Azure che si desidera utilizzare.
6. Fare clic sul segno di spunta per completare il processo di mapping.

    ![Impostazioni di replica cloud](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Dopo aver salvato le impostazioni di un processo inizia a tenere traccia dello stato di avanzamento mapping e può essere controllata nella scheda processi. Qualsiasi macchine virtuali di replica esistenti che corrispondono alla rete macchine Virtuali di origine verranno connessi alla destinazione Azure reti. Nuove macchine virtuali connessi alla rete macchine Virtuali di origine verrà connesso alla rete Azure mappata dopo la replica. Se si modifica un mapping con una nuova rete esistente, verrà connesso macchine virtuali di replica utilizzando le nuove impostazioni.

Si noti che, se la rete di destinazione ha più subnet e una di queste subnet ha lo stesso nome subnet in cui si trova la macchina virtuale di origine e quindi la macchina virtuale duplicata verrà connesso a tale subnet destinazione dopo il failover. Se non vi è alcuna subnet di destinazione con un nome corrispondente, la macchina virtuale verrà connesso prima subnet nella rete.

> [AZURE.NOTE] [Migrazione delle reti](../resource-group-move-resources.md) attraverso gruppi di risorse all'interno della stessa sottoscrizione o sottoscrizioni non è supportata per le reti utilizzate per la distribuzione il ripristino del sito.

## <a name="step-8-enable-protection-for-virtual-machines"></a>Passaggio 8: Attivare la protezione per macchine virtuali

Dopo aver server, nuvole e reti sono configurate correttamente, è possibile abilitare la protezione per macchine virtuali nel cloud. Tenere presente quanto segue:

- Macchine virtuali deve soddisfare [requisiti Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Per abilitare la protezione del sistema operativo e il sistema operativo disco devono essere impostate per la macchina virtuale. Quando si crea una macchina virtuale in VMM tramite un modello di macchina virtuale è possibile impostare la proprietà. È anche possibile impostare queste proprietà per macchine virtuali esistenti nella scheda **Generale** e **Configurazione Hardware** delle proprietà macchina virtuale. Se non si imposta queste proprietà in VMM sarà possibile come configurarli nel portale del ripristino del sito di Azure.

    ![Creare macchina virtuale](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Modificare le proprietà di macchina virtuale](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. Per abilitare la protezione, nella scheda **macchine virtuali** nel cloud in cui si trova, la macchina virtuale fare clic su **Attiva protezione** > **Aggiungi macchine virtuali**.
2. Nell'elenco di macchine virtuali nel cloud, selezionare quello che si desidera proteggere.

    ![Attivare la protezione macchina virtuale](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Verificare l'avanzamento dell'azione **Attiva protezione** nella scheda **processi** , tra cui la replica iniziale. Dopo l'esecuzione del processo **Di protezione finalizzare** la macchina virtuale è pronta per failover. Dopo che è abilitata la protezione e vengono replicate macchine virtuali, sarà possibile visualizzarle in Azure.


    ![Processo di protezione macchina virtuale](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. Verificare le proprietà di macchina virtuale e apportare le modifiche desiderate.

    ![Verificare le macchine virtuali](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. Nella scheda **Configura** le proprietà di macchina virtuale seguenti proprietà di rete può essere modificata.





- **Numero di schede di rete del computer virtuale di destinazione** , il numero di schede di rete è dovuta la dimensione specificata per la macchina virtuale di destinazione. Controllare [specifiche dimensioni macchina virtuale](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) per il numero di schede supportate per la dimensione macchina virtuale. Quando si modifica la dimensione per una macchina virtuale e salvare le impostazioni, il numero della scheda di rete verrà modificate quando si apre pagina **Configura** alla successiva. Il numero di schede di rete di macchine virtuali di destinazione è il numero minimo di schede di rete macchina virtuale di origine e il numero massimo di schede di rete supportate per la dimensione della macchina virtuale scelta, come indicato di seguito:

    - Se il numero di schede di rete del computer di origine è minore o uguale al numero di schede consentiti per la dimensione di computer di destinazione, la destinazione avrà lo stesso numero di schede come origine.
    - Se il numero di schede per la macchina virtuale di origine è maggiore del numero consentito per verrà usata le dimensioni di destinazione, quindi la dimensione massima di destinazione.
    - Ad esempio, se un computer di origine con due schede di rete e le dimensioni del computer di destinazione supporta quattro, il computer di destinazione avrà due schede. Se il computer di origine con due schede, ma le dimensioni di destinazione supportati supporta solo uno computer di destinazione avrà solo una scheda.    

- **Rete di computer virtuale di destinazione** - la rete a cui la macchina virtuale si connette a dipende dalla connessione di rete della rete del computer virtuale di origine. Se la macchina virtuale di origine ha più di una scheda di rete e le reti di origine sono mappate a reti diverse in relazione alla destinazione, è necessario scegliere tra una delle reti di destinazione.
- **Subnet di ogni scheda di rete** - per ogni scheda di rete è possibile selezionare subnet a cui il volume su macchina virtuale cui connettersi.
- **Indirizzo IP di destinazione** - se la scheda di rete del computer virtuale di origine è configurata per l'utilizzo di un indirizzo IP statico, è possibile specificare l'indirizzo IP per la macchina virtuale di destinazione. Utilizzare questa caratteristica mantenere l'indirizzo IP di una macchina virtuale di origine dopo caso di errore. Se non viene specificato alcun indirizzo IP qualsiasi indirizzo IP disponibile viene assegnato alla scheda di rete in fase di failover. Se l'indirizzo IP di destinazione viene specificato, ma è già utilizzato da un'altra macchina virtuale in esecuzione in Azure failover avrà esito negativo.  

    ![Modificare le proprietà di rete](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] Macchine virtuali Linux con indirizzo IP statico non sono supportate.

## <a name="test-the-deployment"></a>Verificare la distribuzione

Per testare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale, o creare un piano di ripristino composta da più macchine virtuali ed eseguire caso di errore per il piano di test.  

Test failover consente di simulare il meccanismo failover e ripristino di una rete isolata. Si noti che:

- Se si desidera connettere la macchina virtuale in Azure con Desktop remoto dopo il failover, abilitare connessione Desktop remoto sul computer virtuale prima di eseguire il test failover.
- Dopo il failover si userà un indirizzo IP pubblico per connettersi alla macchina virtuale di Azure con Desktop remoto. Se si desidera eseguire questa operazione, assicurarsi che non si dispone di alcun criterio di dominio che impedisca la connessione a un computer virtuale tramite un indirizzo pubblico.

>[AZURE.NOTE] Per ottenere prestazioni ottimali in caso di errore in Azure, assicurarsi di avere installato l'agente di Azure nel computer protetto. Questo è utile in avvio più veloce e utile anche in diagnosi in caso di problemi. Agente di Linux può essere trovato [qui](https://github.com/Azure/WALinuxAgent) - e Windows sono reperibili agente [qui](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Nella scheda **Ripristino plan di messaggistica unificata** , aggiungere un nuovo piano. Specificare un nome, **VMM** in **tipo di origine**e il server VMM origine nell' **origine**, la destinazione sarà Azure.

    ![Creare piano di ripristino](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. Nella pagina **Seleziona macchine virtuali** selezionare macchine virtuali da aggiungere al piano di ripristino. Queste macchine virtuali vengono aggiunti al gruppo predefinito piano ripristino, gruppo 1. Un massimo di 100 macchine virtuali in un piano di ripristino singolo sono stati testati.

- Se si desidera verificare la proprietà macchina virtuale prima di aggiungerle al piano, fare clic su macchina virtuale nella pagina delle proprietà dell'area in cui si trova. È anche possibile configurare le proprietà di macchina virtuale nella console di VMM.
- Tutte le macchine virtuali che vengono visualizzate disabilitate per la protezione. L'elenco include entrambe le macchine virtuali abilitati per la protezione e replica iniziale è stata completata e quelli che sono state abilitate per la protezione con iniziale della replica in sospeso. Solo macchine virtuali con replica iniziale completata può eseguire il come parte di un piano di ripristino.

    ![Creare piano di ripristino](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Dopo aver creato un piano di ripristino viene visualizzata nella scheda **Ripristino plan di messaggistica unificata** . È anche possibile aggiungere [automazione Azure runbook](site-recovery-runbook-automation.md) al piano di ripristino per automatizzare le azioni durante il failover.

### <a name="run-a-test-failover"></a>Eseguire test caso di errore

Esistono due modi per eseguire test caso di errore in Azure.

- **Il test di failover senza una rete Azure**, questo tipo di test failover verifica che la macchina virtuale insorge correttamente in Azure. La macchina virtuale non connesso a una rete Azure dopo il failover.
- **Il test di failover con una rete Azure**, verrà connesso questo tipo di verifica del failover che l'intero ambiente di replica proviene massimo come previsto e che non è riuscita in macchine virtuali Azure rete di destinazione specificato. Per la gestione di subnet, per verificare failover alla subnet del computer virtuale di test sarà necessario individuare il in base alla subnet della macchina virtuale duplicata. Questo è diverso da regolare della replica quando subnet di una macchina virtuale replica è in base alla subnet del computer virtuale di origine.

Se si desidera eseguire test caso di errore per una macchina virtuale abilitata per la protezione Azure senza specificare una rete di destinazione Azure non è necessario prepararsi nulla. Per eseguire test caso di errore con un valore di destinazione Azure rete è necessario creare una nuova rete Azure che è isolati dalla rete di produzione Azure (il comportamento predefinito quando si crea una nuova rete in Azure). Esaminare come [eseguire test caso di errore](site-recovery-failover.md#run-a-test-failover) per altri dettagli.


È anche necessario configurare l'infrastruttura per la macchina virtuale replicata a funzionare come previsto. Ad esempio una macchina virtuale Controller di dominio e DNS possono essere replicata in Azure utilizzando il ripristino del sito di Azure e possono essere creata nella rete di test tramite Test Failover. Osservare sezione [test considerazioni sul failover per active directory](site-recovery-active-directory.md#considerations-for-test-failover) per altri dettagli.

Per eseguire un failover test, effettuare le operazioni seguenti:

1. Nella scheda **Ripristino plan di messaggistica unificata** , selezionare il piano, fare clic su **Test Failover**.
2. Nella pagina **Confermare Failover Test** selezionare **Nessuno** o a una rete Azure specifica.  Si noti che se si seleziona Nessuno failover test controllerà che la macchina virtuale replicate correttamente in Azure ma non controlla la configurazione di rete di replica.

    ![Nessuna rete](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. Se la crittografia dei dati è abilitato per il cloud, selezionare il certificato emesso durante l'installazione del Provider sul server VMM quando è attivata l'opzione per abilitare la crittografia dei dati per una cloud **Chiave di crittografia** .
4. Nella scheda **processi** è possibile avanzamento failover. È anche possibile visualizzare la replica di test macchina virtuale nel portale di Azure. Se si è impostato in macchine virtuali di accesso alla rete locale è possibile avviare una connessione Desktop remoto alla macchina virtuale.
5. Quando il failover raggiunge la fase di **test completata** , fare clic su **Test completo** per completare il processo failover test. È possibile drill-down a della scheda **processo** per tenere traccia dello stato e lo stato di avanzamento failover ed eseguire le azioni che sono necessari.
6. Dopo il failover sarà possibile visualizzare la replica di test macchina virtuale nel portale di Azure. Se si è impostato in macchine virtuali di accesso alla rete locale è possibile avviare una connessione Desktop remoto alla macchina virtuale. Eseguire le operazioni seguenti:

    1. Verificare che le macchine virtuali avviato correttamente.
    2. Se si desidera connettere la macchina virtuale in Azure con Desktop remoto dopo il failover, abilitare connessione Desktop remoto sul computer virtuale prima di eseguire il test failover. È anche necessario aggiungere un endpoint RDP sul computer virtuale. È possibile sfruttare un [Azure automazione runbook](site-recovery-runbook-automation.md) a tale scopo.
    3. Dopo il failover se si utilizza un indirizzo IP pubblico per connettersi alla macchina virtuale di Azure con Desktop remoto, assicurarsi che non si ha alcun criterio di dominio che impedisca la connessione a un computer virtuale tramite un indirizzo pubblico.

7.  Al termine della verifica eseguire le operazioni seguenti:
    - Fare clic su **failover test è stata completata**. Pulire l'ambiente di testing per automaticamente spegnere ed eliminare le macchine virtuali di test.
    - Fare clic su **note** per registrare e salvare le osservazioni associate failover test.

>

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [configurazione di piani di ripristino](site-recovery-create-recovery-plans.md) e [failover](site-recovery-failover.md).
