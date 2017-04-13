<properties
    pageTitle="Monitorare e risolvere i problemi di protezione per macchine virtuali e server fisici | Microsoft Auzre" 
    description="Il ripristino del sito Azure coordinate replica failover e ripristino di macchine virtuali in locale server Azure o un Data Center secondario. Utilizzare questo articolo per monitorare e risolvere i problemi di protezione VMM o un sito di Hyper-V." 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Monitorare e risolvere i problemi di protezione per macchine virtuali e server fisici

Il monitoraggio e la Guida alla risoluzione dei problemi consente di informazioni su verifica dell'integrità della replica e risoluzione dei problemi di tecniche per il ripristino del sito di Azure.

## <a name="understanding-the-components"></a>Informazioni sui componenti della

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>VMware/fisici la distribuzione del sito per la replica tra locale e Azure.
Per la configurazione DR tra computer VMware/fisici locale. Server di configurazione, schema destinazione e processo deve configurato. Durante l'attivazione di protezione per il server di origine il ripristino del sito Azure verrà installato servizio mobilità. Pubblicare interruzione locale dopo il server di origine non riesce a sopra Azure, clienti è necessario configurare un Server di processo in Azure e un valore di destinazione schema server locali per proteggere il server di origine alla ricompilato in locale. 

![Distribuzione di siti VMware/fisica per la replica tra locale e Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>VMM la distribuzione del sito per la replica tra siti locale.

Come parte dell'impostazione DR tra posizione due locale; Provider di ripristino del sito Azure deve essere scaricato e installato sul server VMM. Provider richiede connettività internet per garantire che tutte le operazioni attivate dal portale di Azure viene convertita in locale le operazioni come attivare la protezione, macchine virtuali di lato primaria arresto come parte di failover e così via.

![Distribuzione di siti VMM per la replica tra sito locale](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>VMM la distribuzione del sito per la replica tra locale e Azure.

Come parte dell'impostazione DR tra locale e Azure; Provider di ripristino del sito Azure deve essere scaricato e installato sul server VMM insieme agente di servizi di recupero Azure che deve essere installato su ogni host Hyper-V. Per ulteriori informazioni, vedere [Informazioni sui siti di protezione Azure](./site-recovery-understanding-site-to-azure-protection.md) .

![Distribuzione di VMM sito per la replica tra locale e Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>Distribuzione di Hyper-V sito per la replica tra locale e Azure

Questo è uguale a quella della distribuzione VMM: solo differenza è Provider e agente viene installato nell'host Hyper-V stesso. Per ulteriori informazioni, vedere [Informazioni sui siti di protezione Azure](./site-recovery-understanding-site-to-azure-protection.md) .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Eseguire il monitoraggio delle operazioni di configurazione, protezione e ripristino

Ogni operazione di ripristino ottiene controllare e revisioni della scheda "Processi". In caso di qualsiasi configurazione, protezione o errore di ripristino passare alla scheda processi e verificare se esistono eventuali errori.

![Eseguire il monitoraggio delle operazioni di configurazione, protezione e ripristino](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Dopo aver trovato gli errori sotto la visualizzazione di processi, selezionare il processo e fare clic su dettagli sull'errore per il processo.

![Eseguire il monitoraggio delle operazioni di configurazione, protezione e ripristino](media/site-recovery-monitoring-and-troubleshooting/image4.png)

I dettagli dell'errore per identificare possibili cause e consigli per il problema.

![Eseguire il monitoraggio delle operazioni di configurazione, protezione e ripristino](media/site-recovery-monitoring-and-troubleshooting/image5.png)

In questo caso sembra essere un'altra operazione che è in corso non riesce a causa di cui configurazione di protezione. Assicurarsi di risolvere il problema in base a recommendation – dopo aver di lavoro fare clic su RESART per avviare nuovamente l'operazione.

![Eseguire il monitoraggio delle operazioni di configurazione, protezione e ripristino](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Opzione per il riavvio non è disponibile per tutte le operazioni – per coloro che non è disponibile l'opzione RESTART passare all'oggetto e ripetere l'operazione nuovamente. In qualsiasi momento durante l'utilizzo del pulsante Annulla in corso, è possibile annullare ogni processo.

![Eseguire il monitoraggio delle operazioni di configurazione, protezione e ripristino](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Eseguire il monitoraggio dell'integrità della replica per macchina virtuale

Provider di ripristino centrale e il monitoraggio tramite il portale Azure per ognuna delle entità protetta remoto. Passare all'elemento protetto da tale posizione dopo selezionare cloud VMM o i gruppi di protezione. Scheda cloud VMM è valida solo per le distribuzioni VMM in base a e tutti gli altri scenari avere entità protetta in base alla tabulazione gruppi di protezione.

![Eseguire il monitoraggio dell'integrità della replica per macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Dopo aver di tale posizione selezionare l'entità protetto in cloud rispettivi o il gruppo di protezione. Dopo aver selezionato l'entità protetta tutti consentito operazioni visualizzate nel riquadro inferiore.

![Eseguire il monitoraggio dell'integrità della replica per macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Come indicato nel caso la macchina virtuale che integrità è fondamentale – è possibile fare clic sul pulsante Dettagli sull'errore nella parte inferiore per visualizzare l'errore. In base a "possibili cause" e "Recommendation" menzionati consente di risolvere il problema.

![Eseguire il monitoraggio dell'integrità della replica per macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Eseguire il monitoraggio dell'integrità della replica per macchina virtuale](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Nota: Se ci sono operazioni attive che sono in corso o non riuscito quindi passare alla visualizzazione processi come descritto in precedenza per visualizzare un messaggio di errore processo.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Risoluzione dei problemi di Hyper-V in locale

Connettersi alla console di gestione di Hyper-V in locale, selezionare la macchina virtuale e visualizzare lo stato di replica.

![Risoluzione dei problemi di Hyper-V in locale](media/site-recovery-monitoring-and-troubleshooting/image12.png)

In questo caso *Dell'integrità della replica* viene contrassegnato come critico – *Dell'integrità della replica di visualizzazione* per visualizzare i dettagli.

![Risoluzione dei problemi di Hyper-V in locale](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Nei casi in cui la replica è sospeso per la macchina virtuale, rapida selezionare *replica*->*replica curriculum*
![risoluzione dei problemi di Hyper-V in locale](media/site-recovery-monitoring-and-troubleshooting/image19.png)

In caso di macchina virtuale esegue la migrazione di un nuovo host Hyper-V (all'interno del cluster o un computer autonomo), che sono state configurate mediante Ripristino automatico di sistema, non influisce sulle replica per la macchina virtuale. Assicurarsi che il nuovo host Hyper-V soddisfa tutte le per-oggetti per e viene configurato tramite ripristino automatico di sistema.

### <a name="event-log"></a>Registro eventi

| Origini eventi                | Dettagli                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Registri/Microsoft/VirtualMachineManager/Server/amministratore dei servizi e applicazioni** (VMM Server)   |  In questo modo utile registrazione per la risoluzione dei problemi VMM diversi molti. |
| **Le applicazioni e servizi registri/MicrosoftAzureRecoveryServices/replica** (Host hyper-V)   | In questo modo utile registrazione per la risoluzione dei problemi di Microsoft Azure ripristino servizi Agent molti. <br/> ![Origine eventi per host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Le applicazioni e servizi sito registri/Microsoft/Azure ripristino/Provider/operativi** (Host hyper-V)   | In questo modo utile registrazione per la risoluzione dei problemi di servizio di recupero del sito di Microsoft Azure molti. <br/> ![Origine eventi per host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Registri/Microsoft/Windows/Hyper-V-VMM/amministratore dei servizi e applicazioni** (Host hyper-V) | In questo modo utile registrazione per la risoluzione dei problemi di gestione di molti Hyper-V macchina virtuale. <br/> ![Origine eventi per host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>Opzioni di registrazione di replica di Hyper-V

Tutti gli eventi relativi a Hyper-V Replica connessi Hyper-V-VMM\\amministratore log si trova in **registri applicazioni e servizi\\Microsoft\\Windows**. Inoltre, un log analitico può essere abilitato per Hyper-V-VMM. Per abilitare il registro, verificare prima di tutto i tipi di registri visibile nel Visualizzatore eventi. Aprire il Visualizzatore eventi, quindi nel **menu Visualizza**fare clic su **Mostra analitico e registri di Debug**.

![Risoluzione dei problemi di Hyper-V in locale](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Un log analitico è visibile in Hyper-V-VMM

![Risoluzione dei problemi di Hyper-V in locale](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Nel riquadro **Azioni** , fare clic su **Attiva registro**. Dopo aver abilitato, viene visualizzato nel **Monitor prestazioni** durante una sessione di traccia degli eventi si trova in **set di dati Raccoglitore.**

![Risoluzione dei problemi di Hyper-V in locale](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Per visualizzare le informazioni raccolte, prima di tutto interrompere la sessione di registrazione disabilitando il log e quindi salvare il registro e aprire nuovamente nel Visualizzatore eventi o usare altri strumenti per convertirla in base alle esigenze.



## <a name="reaching-out-for-microsoft-support"></a>Raggiungere per il supporto Microsoft

### <a name="log-collection"></a>Raccolta di log

Per la protezione del sito VMM, fare riferimento [ripristino Log insieme strumento supporto diagnostica piattaforma SDP ()](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) per raccogliere i registri necessari.

Per la protezione del sito di Hyper-V, scaricare lo [strumento](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) ed esecuzione nell'host Hyper-V per raccogliere i registri.

Per gli scenari VMware/fisici, fare riferimento [Azure sito ripristino Log insieme per la protezione del sito VMware e fisica](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) per raccogliere i registri necessari.

Strumento raccoglie i log in locale in una sottocartella denominata in modo casuale in **%LocalAppData%\ElevatedDiagnostics**

![Esempio di passaggi visualizzati dalla protezione del sito di Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Apertura di un ticket di supporto

Per generare ticket di supporto per ripristino automatico di sistema, raggiungere Azure supportano mediante l'URL in <http://aka.ms/getazuresupport>

## <a name="kb-articles"></a>Articoli della Knowledge Base

-   [Come conservare la lettera protette macchine virtuali che non è riuscita sopra o viene eseguita la migrazione di Azure](http://support.microsoft.com/kb/3031135)
-   [Come gestire locale per l'utilizzo della larghezza di banda di rete di protezione Azure](https://support.microsoft.com/kb/3056159)
-   [Ripristino automatico di sistema: errore "la risorsa non trovata" quando si tenta di attivare la protezione per una macchina virtuale](http://support.microsoft.com/kb/3010979)
-   [Comprendere e risolvere i problemi di guida duplicata Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Errori comuni di ripristino automatico di sistema e le relative soluzioni

Di seguito sono gli errori comuni che possono raggiunto con le relative soluzioni. Ogni dell'errore descritte in una pagina WIKI separata.

### <a name="general"></a>Generale
-   <span style="color:green;">Nuovo</span> Processi [non funziona con errore "un'operazione è in corso". Errore 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">Nuovo</span> Processi [non funziona con errore "Server non è connesso a Internet". Errore 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Programma di installazione
-   [Non è registrato il server VMM a causa di un errore interno. Fare riferimento alla visualizzazione processi nel portale di ripristino del sito per altri dettagli sull'errore. Eseguire il programma di registrare il server.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Non è possibile stabilire una connessione per l'archivio di Hyper-V ripristino Manager. Verificare le impostazioni del proxy o Riprova in seguito.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configurazione
-   [Impossibile creare il gruppo di protezione: errore durante il recupero dell'elenco dei server.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Host Hyper-V cluster contiene almeno una scheda di rete statica o nessuna scheda connessa è configurata per utilizzare DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM non dispone delle autorizzazioni per completare un'azione](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Non è possibile selezionare l'account di archiviazione all'interno della sottoscrizione durante la configurazione di protezione](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Protezione
- <span style="color:green;">Nuovo</span> Restituire [Attiva protezione di errore "la protezione non è stato essere configurata per la macchina virtuale". Errore 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">Nuovo</span> Restituire [Attiva protezione di errore "la protezione non riuscita sia abilitata per la macchina virtuale." Errore 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">Nuovo</span> Errore di migrazione live [23848 - macchina virtuale agirà da spostare con tipo Live. Questa operazione può interrompere lo stato di protezione di ripristino della macchina virtuale.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [Attivare la protezione riuscita dall'agente non è installato nel computer host](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Non è possibile trovare un host adatto per la macchina virtuale replica - a causa di risorse di elaborazione bassa](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Non è possibile trovare un host adatto per la macchina virtuale replica - a causa di alcuna rete logica collegato](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Non è possibile connettersi al computer host replica - non è possibile stabilire connessione](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>Ripristino
- VMM non è possibile completare l'operazione di host-
    -   [Impossibile sopra il punto di ripristino selezionata per la macchina virtuale: accesso negato.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Impossibile esito negativo su e il punto di ripristino selezionato macchina virtuale Hyper-V: operazione interrotta provare un punto di ripristino più recente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Una connessione con il server potrebbe non essere Fondazione (0x00002EFD)
        -   [Non è possibile abilitare la replica inversa per macchina virtuale Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Non è possibile abilitare la replica per macchina virtuale macchina virtuale Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Impossibile eseguire il commit failover per macchina virtuale](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [Il piano di ripristino contiene macchine virtuali che non sono pronte per failover pianificato](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [La macchina virtuale non è pronta per failover pianificato](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Macchine virtuali non è in esecuzione e non è acceso disattivato](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Dove si trova in uscita dell'operazione di banda in macchine virtuali e failover commit non è riuscito](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Eseguire test di Failover
    -   [Impossibile avviare failover Poiché failover test è in corso](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">Nuovo</span>  Failover timeout con 'PreFailoverWorkflow attività WaitForScriptExecutionTaskTimeout' a causa di impostazioni di configurazione il gruppo di sicurezza di rete associato a subnet a cui appartiene il computer o la macchina virtuale. Per informazioni dettagliate, fare riferimento a ['PreFailoverWorkflow attività WaitForScriptExecutionTaskTimeout'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) .


### <a name="configuration-server-process-server-master-target"></a>Configurazione Server, Server di processo, schema destinazione
Configurazione Server (CS), Server Process (PS), schema Targer (MT)
-   [Host ESXi in cui è ospitato PS/CS come una macchina virtuale viene interrotta con una viola schermata.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Desktop remoto risoluzione dei problemi dopo il failover
-   Molti clienti hanno che si verifichino problemi per connettersi al volume in macchine Virtuali di Azure. [Usa il documento sulla risoluzione dei problemi per RDP in macchina virtuale](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Aggiunta di un indirizzo IP pubblico su un computer virtuale di manager delle risorse
Se è disponibile il pulsante **Connetti** nel portale e non si è connessi a Azure tramite una strada o una connessione VPN da sito, è necessario creare e assegnare un indirizzo IP pubblico della macchina virtuale prima di poter usare RDP/SSH. Seguire la procedura seguente per aggiungere un indirizzo IP pubblico nell'interfaccia di rete del computer virtuale.  

![Impossibile aggiungere un indirizzo IP pubblico nell'interfaccia di rete del computer virtuale](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)