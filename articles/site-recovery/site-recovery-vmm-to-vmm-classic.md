<properties
    pageTitle="Replicare macchine virtuali di Hyper-V in cloud VMM in un sito secondario VMM | Microsoft Azure"
    description="In questo articolo viene descritto come replicare macchine virtuali di Hyper-V nel cloud VMM in un sito secondario di VMM con il ripristino del sito di Azure."
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

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Replicare macchine virtuali di Hyper-V in cloud VMM in un sito secondario di VMM

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmm-to-vmm.md)
- [Portale classica](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Manager delle risorse](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

## <a name="overview"></a>Panoramica

In questo articolo viene descritto come replicare macchine virtuali di Hyper-V in server host Hyper-V gestite nella cloud VMM sito secondario VMM utilizzando il ripristino del sito di Azure.

L'articolo include i prerequisiti, viene illustrato come configurare un archivio il ripristino del sito, installare il Provider di ripristino del sito di Azure in origine e destinazione server VMM, registrare i server nell'archivio di, configurare le impostazioni di protezione per cloud VMM e quindi attivare la protezione per macchine virtuali di Hyper-V. Completare il processo verificando failover per assicurarsi che tutto funzioni come previsto.

Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.

## <a name="architecture"></a>Architettura

Nell'immagine seguente mostra i canali di comunicazione diverse porte utilizzate per il ripristino del sito di Azure per la gestione e la replica

![Topologia E2E](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>Prima di iniziare

Verificare di che avere inserito i prerequisiti di:

**Prerequisiti** | **Dettagli**
--- | ---
**Azure**| È necessario un account di [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.
**VMM** | È necessario almeno un server VMM.<br/><br/>Il server VMM deve essere in esecuzione almeno System Center 2012 SP1 con gli ultimi aggiornamenti cumulativi.<br/><br/>Se si desidera configurare la protezione con un singolo server VMM, è necessario almeno due aree configurati sul server.<br/><br/>Se si desidera distribuire la protezione con due server VMM, ogni server deve contenere almeno un cloud configurato sul server VMM principale che si desidera proteggere e un'area configurato nel server VMM secondario che si desidera utilizzare per la protezione e ripristino<br/><br/>Tutte le aree VMM è necessario impostare il profilo di funzionalità di Hyper-V.<br/><br/>Nel cloud di origine che si desidera proteggere deve contenere uno o più gruppi di host VMM.<br/><br/>Altre informazioni sull'impostazione cloud VMM in [procedura dettagliata: creazione di cloud privati con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) nel blog di Keith Mayer.
**Hyper-V** | È necessario uno o più server host Hyper-V in gruppi di host VMM primari e secondari e uno o più macchine virtuali di ogni server di host Hyper-V.<br/><br/>Server di Hyper-V host e di destinazione deve essere in esecuzione almeno Windows Server 2012 con il ruolo Hyper-V e sono stati installati gli aggiornamenti più recenti.<br/><br/>Qualsiasi server Hyper-V contenente macchine virtuali che si desidera proteggere deve trovarsi in un'area VMM.<br/><br/>Se si esegue Hyper-V in un cluster, tenere presente che gestore cluster non viene creata automaticamente se si dispone di un cluster basato su indirizzo IP statico. È necessario configurare manualmente il gestore di cluster. [Altre informazioni](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) in post di blog del Ezio Finn.
**Connessione di rete** | È possibile configurare la connessione di rete per assicurarsi che replicate macchine virtuali in modo ottimale posizionate in server host Hyper-V secondari dopo failover e che possono connettersi alle reti macchine Virtuali appropriate. Se non Configura connessione di rete, replica macchine virtuali non connesso a qualsiasi rete dopo il failover.<br/><br/>Per configurare la connessione di rete durante l'installazione, verificare che le macchine virtuali nel server di host di origine Hyper-V sono connessi a una rete VMM VM. La rete deve essere collegata a una rete logica associata al cloud. < br /<br/>Cloud destinazione nel server VMM secondario utilizzate per il ripristino deve avere una rete macchine Virtuali corrispondente configurata e, a sua volta dovranno essere collegati a una rete logica corrispondente associata con il cloud di destinazione.<br/><br/>[Altre informazioni](site-recovery-network-mapping.md) sulla connessione di rete.
**Mapping di spazio di archiviazione** | Per impostazione predefinita quando si replica una macchina virtuale su un server di host Hyper-V origine a un server di host Hyper-V di destinazione, replicati dati vengono archiviati nella posizione predefinita indicato per l'host Hyper-V di destinazione nella gestione di Hyper-V. Per un maggiore controllo memorizzazione dei dati replicati, è possibile configurare il mapping di spazio di archiviazione<br/><br/> Per configurare il mapping di spazio di archiviazione, è necessario configurare la classificazione di spazio di archiviazione dell'origine e destinazione server VMM prima di iniziare la distribuzione. [Altre informazioni](site-recovery-storage-mapping.md).


## <a name="step-1-create-a-site-recovery-vault"></a>Passaggio 1: Creare un archivio il ripristino del sito

1. Accedere al [Portale di gestione](https://portal.azure.com) dal server VMM che si desidera registrare.

2. Espandere **I servizi di dati** > **Servizi di recupero** e fare clic su **Archivio di ripristino del sito**.

3. Fare clic su **Crea nuova** > **Creazione rapida**.

4. In **nome**immettere un nome descrittivo per identificare l'archivio.

5. **Nell'area** selezionare la località geografica per l'archivio. Per verificare se sono supportate aree vedere disponibilità geografici in [Azure sito ripristino prezzi dettagli](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Fare clic su **Crea archivio**.

    ![Creare un archivio](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

Controllare nella barra di stato che l'archivio è stato creato. L'archivio verrà elencato come **attivo** nella pagina principale dei servizi di recupero.

## <a name="step-2-generate-a-vault-registration-key"></a>Passaggio 2: Generare una chiave di registrazione archivio

Generare una chiave di registrazione nell'archivio di. Dopo aver scaricato il Provider di ripristino del sito di Azure e installarlo nel server VMM, è necessario utilizzare questo tasto per registrare il server VMM nell'archivio di.

1. Nella pagina **Servizi di recupero** , fare clic su archivio per aprire la pagina avvio rapido. Guida introduttiva può essere aperti anche in qualsiasi momento tramite l'icona.

    ![Icona di avvio veloce](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. Nell'elenco a discesa selezionare **tra due siti VMM locale**.
3. **Preparare il server VMM**, fare clic su **Genera un file chiave registrazione**. Il file di chiave viene generato automaticamente e valido per 5 giorni dopo la generazione. Se non si accede al portale Azure dal server VMM è necessario copiare il file nel server.

    ![Chiave di registrazione](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Passaggio 3: Installare il Provider di ripristino del sito Azure

4. Nella pagina **Guida introduttiva** **preparare VMM server**, fare clic su **Scarica Microsoft Azure sito ripristino Provider per l'installazione nel server VMM** per ottenere l'ultima versione di file di installazione del Provider.

2. Eseguire il file sul server VMM di origine.

    >[AZURE.NOTE] Se VMM viene distribuito in un cluster e si sta eseguendo l'installazione di Provider per la prima volta installarlo in un nodo attivo e completare l'installazione per registrare il server VMM nell'archivio di. Installare il Provider sugli altri nodi. Tenere presente che se si esegue l'aggiornamento del Provider è necessario eseguire l'aggiornamento in tutti i nodi perché che devono essere eseguiti tutti la stessa versione di Provider.

3. Il programma di installazione non alcuni **pre-Requisiti verificare** e le richieste di rappresenti l'autorizzazione per arrestare il servizio VMM per iniziare l'installazione di Provider. Al termine dell'installazione, il servizio VMM verrà riavviato automaticamente. Se sta eseguendo l'installazione in un cluster VMM verrà richiesto di interrompere il ruolo di Cluster.

4. In **Microsoft Update** è possibile scegliere di aggiornamenti. Con questa impostazione abilitata Provider verranno installati in base a criteri di Microsoft Update.

    ![Aggiornamenti di Microsoft](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. Il percorso di installazione è impostato su ** <SystemDrive>\Programmi\Microsoft System Center 2012 R2\Virtual computer Manager\bin**. Fare clic sul pulsante Installa per avviare l'installazione del Provider.

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. Dopo aver installato il provider di servizi fare clic su **Registra** per registrare il server nell'archivio di.

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
9. In **nome archivio**di verificare il nome dell'archivio in cui verrà registrato il server. Fare clic su *Avanti*.

    ![Registrazione server](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. In **Connessione a Internet** specificare la modalità di connessione Provider eseguito sul server VMM a Internet. Selezionare **Connetti con le impostazioni del proxy esistente** per utilizzare le impostazioni di connessione Internet predefinito configurate sul server.

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

13.  Fare clic su **Avanti** per completare il processo. Dopo la registrazione, recupero dei metadati dal server VMM per il ripristino del sito di Azure. Il server viene visualizzato nel **Server VMM** > **server** nell'archivio di.

    ![Server](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>Installazione della riga di comando

Il Provider di ripristino del sito di Azure può essere installato anche dalla riga di comando. Questo metodo può essere utilizzato per installare il provider su un Server CORE per Windows Server 2012 R2.

1. Scaricare la chiave di file e la registrazione di installazione del Provider in una cartella. Ad esempio C:\ASR.
2. Arrestare il servizio di gestione di macchina virtuale System Center
3. Estrarre il programma di installazione di Provider eseguendo questi comandi dal prompt dei comandi con privilegi di **amministratore** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installare il provider eseguendo:

        C:\ASR> setupdr.exe /i

5. Registrare il provider eseguendo:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Dove si trovano i parametri:

 - **/Credentials**: parametro obbligatorio che specifica la posizione in cui si trova il file di chiave di registrazione  
 - **/FriendlyName**: parametro obbligatorio per il nome del server host Hyper-V che viene visualizzata nel portale del ripristino del sito di Azure.
 - **/EncryptionEnabled**: parametro facoltativo che è necessario utilizzare solo in VMM per uno Scenario di Azure se è necessario la crittografia dei macchine virtuali in inattivi in Azure. Assicurarsi che il nome del file che è fornire un estensione **pfx** .
 - **/ProxyAddress**: parametro facoltativo che consente di specificare l'indirizzo del server proxy.
 - **/ProxyPort**: parametro facoltativo che specifica la porta del server proxy.
 - **/proxyUsername**: parametro facoltativo che specifica il nome utente Proxy (se proxy richiede l'autenticazione).
 - **/proxyPassword**: parametro facoltativo che specifica la Password per l'autenticazione con il server proxy (se proxy richiede l'autenticazione).  

## <a name="step-4-configure-cloud-protection-settings"></a>Passaggio 4: Configurare cloud impostazioni di protezione

Se sono registrate server VMM, è possibile configurare le impostazioni di protezione cloud. Se l'opzione **Sincronizza dati cloud con l'archivio** viene abilitata al momento dell'installazione il Provider così tutte le aree sul server VMM verranno visualizzati nella scheda **Elementi protetti** nell'archivio di. Se non è stato è possibile sincronizzare un'area specifica con il ripristino del sito di Azure nella scheda **Generale** della pagina proprietà cloud nella console di VMM.

![Cloud pubblicati](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. Nella pagina Guida introduttiva, fare clic su **configurare la protezione per cloud VMM**.
2. Nella scheda **Cloud VMM** selezionare l'area che si desidera configurare e passare alla scheda **configurazione** .
3. Nella finestra di **destinazione**, selezionare **VMM**.
4. In **percorso di destinazione**, selezionare il server VMM Site che gestisce l'area che si desidera utilizzare per il ripristino.
4. Nel **cloud di destinazione**, selezionare l'area di destinazione che si desidera utilizzare per il failover macchine virtuali nel cloud origine. Si noti che:

    - È consigliabile selezionare un'area di destinazione che soddisfi i requisiti di ripristino per macchine virtuali che è possibile proteggere.
    - Una cloud può appartenere solo a una coppia di cloud singola, ovvero come primaria o un'area di destinazione.

5. Nella casella **Frequenza copia**, specificare la frequenza dati devono essere sincronizzati tra posizioni di origine e destinazione 5he. Si noti che questa impostazione è rilevante solo quando l'host di Hyper-V è in esecuzione Windows Server 2012 R2. Per altri server viene utilizzata un'impostazione di cinque minuti.
6. In **punti di ripristino aggiuntive**, specificare se si desidera creare punti di ripristino aggiuntive. Il valore predefinito zero indica che solo il punto di ripristino più recente per una macchina virtuale principale è archiviato in un server di host duplicata. Si noti che per consentire più punti di ripristino richiede ulteriore spazio di archiviazione per le istantanee vengono memorizzati in ogni fase di ripristino. Per impostazione predefinita, i punti di ripristino vengono creati ogni ora, in modo che ogni punto di ripristino contiene patrimonio di un'ora di dati. Il valore di punto di ripristino che assegna per la macchina virtuale nella console di VMM non può essere minore del valore che si assegna la console di ripristino sito Azure.
7. **Frequenza degli snapshot coerenti con l'applicazione**, specificare la frequenza di creare snapshot coerenti con l'applicazione. Hyper-V vengono utilizzati due tipi di snapshot, ovvero uno snapshot standard che fornisce uno snapshot della macchina virtuale intero incrementale e uno snapshot coerente applicazione che contiene uno snapshot nel momento dei dati dell'applicazione macchina virtuale. Snapshot coerenti applicazione utilizzare Servizio copia ombreggiatura del Volume (VSS) per garantire che le applicazioni in uno stato coerente quando dello snapshot. Si noti che se si abilita snapshot coerenti con l'applicazione, influisce negativamente sulle prestazioni delle applicazioni in esecuzione in macchine virtuali di origine. Assicurarsi che il valore impostato sia minore del numero di punti di ripristino aggiuntive che configurare.

    ![Configurare le impostazioni di protezione](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. Nella casella **compressione di trasferimento dei dati**, specificare se devono essere compresso replicato i dati trasferiti.
9. In **autenticazione**, specificare la modalità di autenticazione il traffico tra i server di host Hyper-V principale e ripristino. Selezionare HTTPS a meno che non si dispone di un ambiente Kerberos configurato di lavoro. Il ripristino del sito Azure configura automaticamente i certificati di autenticazione HTTPS. È richiesta alcuna configurazione manuale. Se si seleziona Kerberos, un ticket Kerberos verrà utilizzato per l'autenticazione dei server host. Per impostazione predefinita, porta 8083 e 8084 (per i certificati) verrà aperta in Windows Firewall nei server che host Hyper-V. Si noti che questa impostazione si applica solo alle server host Hyper-V in esecuzione su Windows Server 2012 R2.
10. In **porta**, modificare il numero di porta sul quale il computer host di origine e destinazione in attesa per il traffico di replica. Ad esempio, è possibile modificare l'impostazione se si vuole applicare larghezza di banda di rete qualità del servizio (QoS) la limitazione per il traffico di replica. Verificare che la porta non è utilizzata da qualsiasi altra applicazione e che sia aperto nelle impostazioni del firewall.
11. **Metodo di replica**, specificare come devono essere gestita la replica iniziale dei dati di origine in posizioni di destinazione, prima dell'avvio di replica normale:

    - **Rete**-la copia dei dati in rete può essere richiede molto tempo e risorse. È consigliabile usare questa opzione se nel cloud contiene macchine virtuali con relativamente dischi rigidi virtuali e il sito principale è connesso al sito secondario tramite una connessione con la larghezza di banda. È possibile specificare che la copia deve iniziare immediatamente o selezionare un tempo. Se si utilizza la replica di rete, è consigliabile pianificarne durante le ore.
    - **Non in linea**, questo metodo specifica che verrà eseguita la replica iniziale utilizzando i supporti esterni. È utile se si desidera evitare la riduzione delle prestazioni di rete o per aree geografiche remoti. Per utilizzare questo metodo è specificare il percorso di esportazione nel cloud di origine e il percorso di importazione del provider di destinazione. Quando si abilita la protezione per una macchina virtuale, il disco rigido virtuale viene copiato nel percorso di esportazione specificato. Si inviarlo al sito di destinazione e copiare il percorso di importazione. Il sistema copia le informazioni importate in macchine virtuali duplicata.

12. Selezionare **Elimina Replica Virtual Machine** per specificare che la macchina virtuale replica deve essere eliminata se si sospende proteggere la macchina virtuale selezionando l'opzione **Elimina protezione per la macchina virtuale** nella scheda macchine virtuali di proprietà cloud. Con questa impostazione è abilitata, quando si disattiva la protezione la macchina virtuale è stata rimossa dal ripristino del sito di Azure, le impostazioni del ripristino del sito per la macchina virtuale vengono rimosse nella console di VMM e la replica viene eliminata.

    ![Configurare le impostazioni di protezione](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

Dopo aver salvato le impostazioni di un processo verrà creato e può essere monitorato nella scheda **processi** . Tutti i server host Hyper-V nel cloud origine VMM saranno configurati per la replica. Nella scheda **Configura** , è possono modificare le impostazioni di cloud. Se si desidera modificare nel cloud percorso o di destinazione di destinazione è necessario rimuovere la configurazione di cloud e quindi riconfigurare nel cloud.

### <a name="prepare-for-offline-initial-replication"></a>Prepararsi per la replica iniziale offline

È necessario eseguire le azioni seguenti per preparare l'ambiente per offline replica iniziale:

- Nel server di origine, è necessario specificare un percorso da cui verrà eseguita l'esportazione dei dati. Assegnare il controllo completo per le autorizzazioni di condivisione e NTFS al servizio VMM sul percorso di esportazione. Nel server di destinazione, è necessario specificare un percorso da cui verrà eseguita l'importazione dei dati. Assegnare le stesse autorizzazioni su questo percorso di importazione.
- Se il percorso di importazione o esportazione è condiviso, assegnare l'appartenenza ai gruppi amministratore, Power User, operatore di stampa o operatore Server per l'account di servizio VMM nel computer remoto in cui il condiviso si trova.
- Se si utilizza un account Esegui come aggiungere host, percorsi importazione / esportazione assegnare lettura e scrittura agli account Esegui come VMM.
- Le azioni di importazione / esportazione non dovrebbero trovarsi in qualsiasi computer utilizzato come server host Hyper-V, perché loopback configurazione non è supportata da Hyper-V.
- In Active Directory, su ogni Hyper-V server host contenente macchine virtuali che si desidera proteggere, abilitare e configurare vincolato delega per considerare attendibile il computer remoto in cui i percorsi di importazione / esportazione si trovano, come indicato di seguito:
    1. Sul controller di dominio, aprire **Active Directory Users e computer**.
    2. Nella struttura della console fare clic su **NomeDominio** > **computer**.
    3. Pulsante destro del mouse sul nome del server host Hyper-V > **proprietà**.
    4. Nella scheda **delega** fare clic su T**ruggine questo computer per la delega solo ai servizi specificati**.
    5. Fare clic su **Usa Kerberos**.
    6. Fare clic su **Aggiungi** > **utenti e computer**.
    7. Digitare il nome del computer che ospita il percorso di esportazione > **OK**. Nell'elenco di servizi disponibili, tenere premuto il tasto CTRL e fare clic su **cifs** > **OK**. Ripetere la procedura per il nome del computer che ospita il percorso di importazione. Ripetere l'operazione per server host Hyper-V aggiuntivi.

## <a name="step-5-configure-network-mapping"></a>Passaggio 5: Configurare il mapping di rete
1. Nella pagina Guida introduttiva, fare clic su **mappa reti**.
2. Selezionare il server VMM di origine da cui si desidera eseguire il mapping di reti, quindi il server VMM di destinazione a cui eseguire il mapping le reti. L'elenco delle reti di origine e le reti di destinazione. Viene visualizzato un valore vuoto per le reti che non sono attualmente mappate.
3. Selezionare una rete nella **rete aziendale in origine** > **mappa**. Il servizio rileva le reti macchine Virtuali nel server di destinazione e li visualizza. Fare clic sull'icona di informazioni accanto ai nomi di rete di origine e destinazione per visualizzare le subnet per ogni rete.

    ![Configurare il mapping di rete](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. Nella finestra di dialogo selezionare una delle reti macchine Virtuali dal server VMM di destinazione.

    ![Selezionare una rete di destinazione](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. Quando si seleziona una rete di destinazione, vengono visualizzate le aree protette che utilizzano la rete di origine. Vengono visualizzate anche le reti di destinazione disponibili associati nuvole utilizzati per la protezione. È consigliabile selezionare una rete di destinazione in cui è disponibile per tutte le aree in uso per la protezione. Oppure, è anche possibile passare al Server VMM e modificare le proprietà del cloud per aggiungere la rete logica corrispondente alla rete macchine virtuali che si desidera scegliere.
6. Fare clic sul segno di spunta per completare il processo di mapping. Un processo inizia a tenere traccia dell'avanzamento mapping. È possibile visualizzare nella scheda **processi** .


## <a name="step-6-configure-storage-mapping"></a>Passaggio 6: Configurare il mapping di spazio di archiviazione
Per impostazione predefinita quando si replica una macchina virtuale su un server di host Hyper-V origine a un server di host Hyper-V di destinazione, replicati dati vengono archiviati nella posizione predefinita indicato per l'host Hyper-V di destinazione nella gestione di Hyper-V. Per un maggiore controllo memorizzazione dei dati replicati, è possibile configurare i mapping di spazio di archiviazione come indicato di seguito:



1. Definire classificazioni di spazio di archiviazione sul server VMM di origine e destinazione. [Altre informazioni](https://technet.microsoft.com/library/gg610685.aspx). Classificazioni devono essere disponibile per i server di host Hyper-V in aree di origine e destinazione. Classificazioni non è necessario avere lo stesso tipo di spazio di archiviazione. Ad esempio è possibile mappare una classificazione di origine che contiene le azioni di piccole e medie imprese da una classificazione di destinazione che contiene CSVs.
2. Dopo la classificazione sono attive è possibile creare i mapping. A tale scopo, nella pagina **Guida introduttiva** > **mappa lo spazio di archiviazione**.
3. Fare clic sulla scheda **archiviazione** > **mappa classificazioni di spazio di archiviazione**.
4. Nella scheda **mappare classificazioni di spazio di archiviazione** selezionare classificazioni sulla fonte e VMM server target. Salvare le impostazioni.

    ![Selezionare una rete di destinazione](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>Passaggio 7: Attivare la protezione macchina virtuale
Dopo aver server, nuvole e reti sono configurate correttamente, è possibile abilitare la protezione per macchine virtuali nel cloud.

1. Nella scheda **macchine virtuali** nel cloud in cui si trova la macchina virtuale, fare clic su **Attiva protezione** > **Aggiungi macchine virtuali**.
2. Nell'elenco di macchine virtuali nel cloud, selezionare quello che si desidera proteggere.

    ![Attivare la protezione macchina virtuale](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. Verificare l'avanzamento dell'azione Attiva protezione nella scheda **processi** , tra cui la replica iniziale. Dopo l'esecuzione del processo di protezione finalizzare la macchina virtuale è pronta per failover. Dopo che è abilitata la protezione e vengono replicate macchine virtuali, sarà possibile visualizzarle in Azure.

    ![Processo di protezione macchina virtuale](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] È anche possibile abilitare la protezione per macchine virtuali nella console di VMM. Fare clic su **Attiva protezione** sulla barra degli strumenti nella scheda **Il ripristino del sito di Azure** nelle proprietà macchina virtuale.

### <a name="on-board-existing-virtual-machines"></a>Bordo macchine virtuali esistenti

Se si dispone di macchine virtuali esistenti in VMM che replica con Hyper-V Replica è necessario incorporata loro per la protezione il ripristino del sito di Azure come indicato di seguito:

1. Verificare di che avere nuvole primari e secondari. Assicurarsi che il server di Hyper-V che ospita la macchina virtuale esistente si trova nel cloud principale e che il server di Hyper-V che ospita la macchina virtuale replica si trova nel cloud secondario. Assicurarsi che è ora configurato le impostazioni di protezione per il cloud. Le impostazioni devono corrispondere a quelle attualmente configurato per Replica Hyper-V. In caso contrario replica macchina virtuale potrebbe non funzionare come previsto.
2. Quindi attivare la protezione per la macchina virtuale principale. Azure il ripristino del sito e VMM garantisce che viene rilevata la stessa host duplicata e macchina virtuale e il ripristino del sito di Azure verranno riutilizzare e ristabilire la replica utilizzando le impostazioni configurate durante la configurazione di cloud.


## <a name="test-your-deployment"></a>Testare la distribuzione

Per testare la distribuzione è possibile eseguire test caso di errore per una singola macchina virtuale, o creare un piano di ripristino composta da più macchine virtuali ed eseguire caso di errore per il piano di test.  Test failover consente di simulare il meccanismo failover e ripristino di una rete isolata.

### <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

1. Nella scheda **Ripristino plan di messaggistica unificata** , fare clic su **Creazione del piano di ripristino**.
2. Specificare un nome per il piano di ripristino e server VMM di origine e destinazione. Il server di origine deve avere macchine virtuali abilitati per failover e ripristino. Selezionare **Hyper-V** per visualizzare solo i nuvole configurati per la replica di Hyper-V.

    ![Creare piano di ripristino](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. In **Selezione macchina virtuale**, selezionare gruppi di replica. Tutte le macchine virtuali associate al gruppo di replica verrà selezionate e aggiunta al piano di ripristino. Queste macchine virtuali vengono aggiunti al gruppo predefinito piano ripristino, gruppo 1. Se necessario, è possibile aggiungere più gruppi. Si noti che, dopo macchine virtuali di replica verrà avviato secondo l'ordine dei gruppi di piano di ripristino.

    ![Aggiungere macchine virtuali](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

Dopo il ripristino piano è stata creata, viene visualizzato nell'elenco nella scheda **Ripristino plan di messaggistica unificata** .

###<a name="run-a-test-failover"></a>Eseguire test caso di errore

1. Nella scheda **Ripristino plan di messaggistica unificata** , selezionare il piano, fare clic su **Test Failover**.
2. Nella pagina **Confermare Failover Test** selezionare **Nessuno**. Si noti che, se questa opzione è attivato il volume macchine virtuali di replica non connesso a una rete. Il test verrà eseguito che la macchina virtuale ha esito negativo su come previsto, ma non testare l'ambiente di rete di replica. Esaminare come [eseguire test caso di errore](site-recovery-failover.md#run-a-test-failover) per ulteriori informazioni su come utilizzare le opzioni di rete diverse.
3. Computer virtuale di test verrà creato nello stesso host host in cui si trova la macchina virtuale duplicata. Viene aggiunta alla stessa area in cui si trova la macchina virtuale duplicata.

### <a name="run-a-recovery-plan"></a>Eseguire un piano di ripristino
Dopo la replica la macchina virtuale replica potrebbero non ha un indirizzo IP che corrisponde all'indirizzo IP del computer virtuale principale. Macchine virtuali aggiorneranno i server DNS che utilizzano dopo l'avvio. È anche possibile aggiungere uno script per aggiornare il Server DNS per garantire un aggiornamento tempestivo.

#### <a name="script-to-retrieve-the-ip-address"></a>Script per recuperare l'indirizzo IP
Eseguire questo script di esempio per recuperare l'indirizzo IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>Script per aggiornare il DNS
Eseguire questo script di esempio per aggiornare il DNS, specificando l'indirizzo IP recuperata utilizzando lo script di esempio precedente.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>Informativa sulla privacy per il ripristino del sito

Questa sezione vengono fornite ulteriori informazioni sulla privacy per il ripristino del sito di Microsoft Azure servizio (""). Per visualizzare l'informativa sulla privacy per i servizi di Microsoft Azure, vedere l' [Informativa sulla Privacy di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Funzionalità: la registrazione**

- **Risultato**: Registra server con il servizio in modo che è possibile proteggere macchine virtuali
- **Informazioni raccolte**: dopo la registrazione del servizio raccoglie elabora e trasmette le informazioni relative al certificato gestione dal server VMM designato per fornire il ripristino di emergenza utilizzando il nome del server VMM e il nome di nuvole macchina virtuale nel server VMM.
- **Uso delle informazioni**:
    - Certificato di gestione, ovvero viene utilizzato per identificare e autenticazione del server VMM registrato per l'accesso al servizio. Il servizio utilizza la chiave pubblica del certificato per proteggere un token che solo il server VMM registrato può accedere a. Il server è necessario utilizzare questo token per accedere alle caratteristiche di servizio.
    - Nome del server VMM, ovvero VMM il nome del server è necessario identificare e comunicare con il server VMM appropriato in cui si trovano le aree.
    - Cloud nomi dal server VMM, ovvero il nome di area è necessario quando si usa il cloud servizio associazione/unpairing funzionalità descritte di seguito. Quando si desidera associare il cloud da un centro dati primario con un altro cloud nell'interfaccia di recupero dati, i nomi di tutte le aree dal centro dati ripristino vengono visualizzati.

- **Scelta**: queste informazioni sono un elemento essenziale del processo di registrazione servizio perché può essere utile è e il servizio per identificare il server VMM per il quale si desidera proteggere il ripristino del sito di Azure, nonché per identificare il server VMM registrato corretto. Se non si desidera inviare queste informazioni a un servizio, non utilizzare questo servizio. Se si registra il server e quindi in un secondo momento da annullarne la registrazione, è possibile eseguire questa operazione eliminando le informazioni sul server VMM dal portale di servizio (che è il portale di Azure).

**Caratteristica: Attivare la protezione il ripristino del sito di Azure**

- **Risultato**: il Provider di ripristino del sito Azure installato sul server VMM è il canale di comunicazione con il servizio. Il provider di servizi è una raccolta di collegamento dinamico (DLL) ospitata nel processo di VMM. Dopo aver installato il provider di servizi, la funzionalità di "Data Center ripristino" Ottiene abilitata nella console di amministrazione VMM. Le macchine virtuali di nuove o esistente in un'area è possibile abilitare una proprietà denominata "Data Center ripristino" per proteggere la macchina virtuale. Una volta impostata questa proprietà, il Provider invia il nome e l'ID della macchina virtuale al servizio. Per Windows Server 2012 o Windows Server 2012 R2 Hyper-V tecnologia di replica è attivata la protezione virtuale. I dati di macchina virtuale Ottiene replicati da un host Hyper-V a un'altra (in genere si trova in un data center diversi "ripristino").

- **Raccolta delle informazioni**: il servizio raccoglie, elabora e trasmette basato su metadati per la macchina virtuale che include il nome, ID, virtuali e il nome del cloud a cui appartiene.

- **Uso delle informazioni**: il servizio utilizza delle suddette informazioni per compilare le informazioni di macchina virtuale nel portale del servizio.

- **Scelta**: questo è un elemento essenziale del servizio e non può essere disattivato. Se non si vuole queste informazioni inviate al servizio, non abilitare protezione il ripristino del sito di Azure per le macchine virtuali. Si noti che tutti i dati inviati dal Provider del servizio sia stato inviato tramite HTTPS.

**Caratteristica: Piano di ripristino**

- **Risultato**: questa caratteristica consente di creare un piano di orchestrazione per il centro di dati "ripristino". È possibile definire l'ordine in cui deve essere avviato macchine virtuali o un gruppo di macchine virtuali nel sito di ripristino. È inoltre possibile specificare qualsiasi script automatizzati per essere eseguita o qualsiasi manuale azione da eseguire, in fase di ripristino per ogni macchina virtuale. Failover (vedere la sezione successiva) in genere è attivato a livello di pianificazione di ripristino per il ripristino coordinato.

- **Raccolta delle informazioni**: il servizio raccoglie, elabora e trasmette metadati per il piano di ripristino, inclusi i metadati macchina virtuale e i metadati di qualsiasi script di automazione e note di azione manuale.

- **Uso delle informazioni**: I metadati descritti in precedenza vengono utilizzati per creare il piano di ripristino nel portale del servizio.

- **Scelta**: questo è un elemento essenziale del servizio e non può essere disattivato. Se non si vuole queste informazioni inviate al servizio, non creare piani di ripristino per il servizio.

**Caratteristica: Connessione di rete**

- **Risultato**: questa caratteristica consente di eseguire il mapping di informazioni relative alla rete dall'interfaccia di dati primario per il centro di dati di ripristino. Quando le macchine virtuali vengono recuperate nel sito di ripristino, questo mapping utile per stabilire la connettività di rete per poter.

- **Informazioni raccolte**: come parte della funzionalità di mapping di rete, il servizio raccoglie, elabora e trasmette i metadati delle reti logiche per ogni sito (principale e Data Center).

- **Uso delle informazioni**: il servizio utilizza i metadati per popolare il portale di servizio in cui è possibile mappare le informazioni di rete.

- **Scelta**: questo è un elemento essenziale del servizio e non può essere disattivato. Se non si vuole queste informazioni inviate al servizio, non usare la funzionalità di mapping di rete.

**Caratteristica: Failover - test pianificati, non pianificato**

- **Risultato**: questa caratteristica consente di failover di una macchina virtuale da un centro di dati gestiti VMM a un altro centro di dati gestiti VMM. L'azione failover viene attivata dall'utente nel loro portale del servizio. Motivi per caso di errore possono includono un evento non pianificato (ad esempio nel caso dei disaster0 naturale; un evento pianificato (ad esempio Data Center bilanciamento del carico); test caso di errore (ad esempio una prova piano ripristino).

Il Provider sul server VMM riceve inoltre una notifica dell'evento dal servizio e viene eseguita un'azione failover nell'host Hyper-V tramite le interfacce VMM. Effettivo failover della macchina virtuale da un host Hyper-V a un'altra (in genere in esecuzione in un data center diversi "ripristino") viene gestito da della tecnologia di replica di Windows Server 2012 o Windows Server 2012 R2 Hyper-V. Dopo avere inserito il failover, il Provider installato sul server VMM del centro dati "ripristino" Invia le informazioni di successo al servizio.

- **Raccolta delle informazioni**: il servizio utilizza delle suddette informazioni per popolare lo stato delle informazioni di azione failover nel portale del servizio.

- **Uso delle informazioni**: il servizio le informazioni vengono usate in precedenza come indicato di seguito:

    - Certificato di gestione, ovvero viene utilizzato per identificare e autenticazione del server VMM registrato per l'accesso al servizio. Il servizio utilizza la chiave pubblica del certificato per proteggere un token che solo il server VMM registrato può accedere a. Il server è necessario utilizzare questo token per accedere alle caratteristiche di servizio.
    - Nome del server VMM, ovvero VMM il nome del server è necessario identificare e comunicare con il server VMM appropriato in cui si trovano le aree.
    - Cloud nomi dal server VMM, ovvero il nome di area è necessario quando si usa il cloud servizio associazione/unpairing funzionalità descritte di seguito. Quando si desidera associare il cloud da un centro dati primario con un altro cloud nell'interfaccia di recupero dati, i nomi di tutte le aree dal centro dati ripristino vengono visualizzati.

- **Scelta**: questo è un elemento essenziale del servizio e non può essere disattivato. Se non si vuole queste informazioni inviate al servizio, non usare questo servizio.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere eseguito caso di errore di prova per controllare che l'ambiente funziona come previsto, [informazioni sui](site-recovery-failover.md) diversi tipi di failover.
