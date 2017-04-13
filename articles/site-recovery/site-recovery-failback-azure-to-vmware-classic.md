<properties 
   pageTitle="Esito negativo posteriore macchine virtuali di VMware e server fisici al sito locale | Microsoft Azure"
   description="Informazioni su errori al sito locale dopo il failover delle macchine virtuali VMware e server fisici in Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Macchine virtuali di VMware Fail indietro e server fisici al sito locale

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-failback-azure-to-vmware.md)
- [Portale classica Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portale classica Azure (Legacy)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



In questo articolo viene descritto come esito negativo indietro Azure macchine virtuali da Azure al sito locale. Quando si è pronti per avere esito negativo indietro macchine virtuali VMware o Windows/Linux server fisici dopo che riuscita sopra la locali del sito in Azure con questa [esercitazione](site-recovery-vmware-to-azure-classic.md), seguire le istruzioni fornite in questo articolo.



## <a name="overview"></a>Panoramica

Viene illustrata l'architettura di failback per questo scenario.

Usare questa architettura quando il server di processo è locale e si sta usando un ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Usare questa architettura quando il server di processo in Azure e si dispone di una rete VPN o una connessione ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Per visualizzare l'elenco completo delle porte e protocolli il failback architechture diagramma fare riferimento all'immagine riportata di seguito

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Ecco come funziona il failback:

- Dopo che è stata Impossibile su Azure, non si torna al sito locale in pochi passaggi:
    - **Passaggio 1**: riproteggere di macchine virtuali di Azure in modo che inizino replicare in macchine virtuali VMware in esecuzione nel sito locale. Attivazione reprotection Sposta la macchina virtuale in un gruppo di protezione failback creato automaticamente quando è stato creato originariamente il gruppo di protezione failover. Se è stato aggiunto al gruppo di protezione failover a un piano di ripristino quindi il gruppo di protezione failback automaticamente anche è stato aggiunto al piano.  Durante il riproteggere specificato come pianificare l'esito negativo indietro.
    - **Passaggio 2**: dopo nelle macchine virtuali Azure la replica al sito locale, eseguire una hanno esito negativo riesca proveniente da Azure.
    - **Passaggio 3**: dopo i dati non sono riuscita indietro si riproteggere macchine virtuali locale che non è stato, in modo che inizino replica di Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>Failback nella posizione originale o alternativi

Se ha esito negativo su un VM VMware alla stessa origine macchina virtuale potrebbe non riuscire se esiste ancora locale. In questo scenario solo le modifiche delta Impossibile eseguire nuovamente. Si noti che:

- Se ha esito negativo su server fisici failback sarà sempre a un nuovo VM VMware.
    - Prima che il computer fisico si noti che
    - Computer protetto viene restituito come una macchina virtuale quando non è possibile su proveniente da Azure VMware
    - Assicurarsi che alla scoperta di almeno uno schema destinazione server oltre necessari host ESX/ESXi a cui è necessario failback.
- Se non si riesce a macchina virtuale originale è necessario le operazioni seguenti:
    - Se la macchina virtuale viene gestita da un server vCenter host ESX della destinazione schema avrà accesso all'archivio dati macchine virtuali.
    - Se la macchina virtuale è su un host ESX ma non viene gestita da vCenter sul disco rigido della macchina virtuale deve essere in un archivio dati accessibile dall'host del MT.
    - Se la macchina virtuale è su un host ESX e non usa vCenter è necessario completare individuazione dell'host ESX di il MT prima riproteggere. Questa opzione se si sta non funziona server indietro fisici troppo.
    - (Se è presente la macchina virtuale locale) è anche possibile eliminarla prima di eseguire un failback. Quindi failback verrà creata una nuova macchina virtuale nello stesso host host ESX di destinazione principale.
    
- Quando verranno ripristinati failback in un percorso alternativo i dati per l'archivio dati stesso e lo stesso host ESX come quella usata dal server di destinazione master locale. 


## <a name="prerequisites"></a>Prerequisiti

- È necessario un ambiente VMware per avere esito negativo indietro macchine virtuali VMware e server fisici. Errore a un server fisico non sono supportate.
- Per avere esito negativo indietro siano stati creati una rete Azure quando si configura inizialmente la protezione. Failback richiede una connessione VPN o ExpressRoute dalla rete Azure in cui si trovano al sito locale macchine virtuali di Azure.
- Se le macchine virtuali di cui si desidera eseguire il alla vengono gestite da un server vCenter è necessario verificare che si dispone delle autorizzazioni necessarie per l'individuazione di macchine virtuali sui server vCenter. [Per ulteriori](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Se sono presente in una macchina virtuale istantanee reprotection avrà esito negativo. È possibile eliminare le istantanee o dischi. 
- Prima di eseguire il nuovo è necessario creare un numero di componenti:
    - **Creare un server di processo in Azure**. Si tratta di una macchina virtuale Azure che è necessario creare e continuare l'esecuzione durante il failback. È possibile eliminare il computer al termine failback.
    - **Creare un server di destinazione principale**: il server di destinazione master invia e riceve dati failback. Il server di gestione è stato creato in locale dispone di un server di destinazione master installato per impostazione predefinita. Tuttavia, in base al volume di traffico posteriore non riuscito potrebbe essere necessario creare un server di destinazione master separata per failback.
    - Se si desidera creare un server di destinazione master supplementari su Linux, è necessario configurare VM Linux prima di installare il server di destinazione master, come descritto di seguito.
- Configurazione server è locale necessari quando si esegue un failback. Durante l'opzione, la macchina virtuale deve essere presente nel database di server di configurazione, non funziona quali failback mancato eseguita correttamente. Assicurarsi quindi eseguire regolari backup pianificato del server di posta. In caso di problemi, sarà necessario ripristinare con lo stesso indirizzo IP in modo che funzionino failback.

## <a name="set-up-the-process-server-in-azure"></a>Configurare il server di processo in Azure

È necessario installare un server di processo in Azure in modo che le macchine virtuali Azure possibile inviare i dati al server di destinazione master locale. 

1.  Nel portale del ripristino del sito > **Configurazione server** selezionare questa opzione per aggiungere un nuovo processo server.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Specificare il nome del server processo e immettere un nome e la password utilizzati per connettere la macchina virtuale Azure come amministratore. Nella **Configurazione Server** selezionare il server di gestione locale e specificare la rete Azure in cui devono essere distribuiti server processo. Deve trattarsi di rete in cui si trovano le macchine virtuali di Azure. Specificare l'indirizzo IP dalla subnet selezionare e iniziare la distribuzione.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    Verrà attivato un processo di distribuire il server di processo

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Dopo che il server di processo viene distribuito in Azure è possibile accedere al suo utilizzando le credenziali specificate. La prima volta che ci si disconnette nella finestra di dialogo server processo verrà eseguito. Digitare l'indirizzo IP del server di gestione locale e il relativo passphrase. Lasciare la porta 443 predefinita. È anche possibile lasciare il valore predefinito 9443 porta replica dei dati a meno che non si specificamente modificato questa impostazione quando si configura il server di gestione locale. 

    >[AZURE.NOTE] Il server non sarà visibile in **proprietà macchine Virtuali**. È sufficiente visibile nella scheda **server** management server a cui è stata registrata. Possono essere necessari sul 10-15 minuti per il server di processo da visualizzare.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurare il server di destinazione master in locale

Server di destinazione master riceve i dati di failback. Un server di destinazione master viene installato automaticamente nel server di gestione locale, ma se si sta non funziona nuovamente una grande quantità di dati potrebbe essere necessario configurare un server di destinazione master aggiuntive. Eseguire questa operazione come indicato di seguito:

>[AZURE.NOTE] Se si desidera installare un server di destinazione master su Linux, seguire le istruzioni nella procedura successiva.

1. Se si installa il server di destinazione principale in Windows, aprire la pagina avvio rapido da macchine Virtuali in cui si sta eseguendo l'installazione di server di destinazione master e scaricare il file di installazione per la procedura guidata configurazione unificata di ripristino di Azure del sito.
2. Eseguire l'installazione, quindi selezionare **Aggiungi server processo aggiuntivi per ridimensionare la distribuzione**nella **prima di iniziare** .
3. Completare la procedura guidata nello stesso modo è stata quando è [impostato il server di gestione](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Nella pagina **Dettagli configurazione Server** specificare l'indirizzo IP del server di destinazione principale e una frase per accedere a macchina virtuale.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurare una VM Linux del server di destinazione principale
Per configurare il server di gestione in esecuzione il server di destinazione master come una VM Linux è necessario installare il cento) 6.6 S minime sistema operativo, recuperare l'ID SCSI per ogni disco rigido SCSI, installare alcuni pacchetti aggiuntivi e applicare alcune modifiche personalizzate.

#### <a name="install-centos-66"></a>Installare CentOS 6.6

1.  Installare il sistema operativo minimo 6.6 CentOS sul server di gestione macchine Virtuali. Mantenere il file ISO in un'unità DVD e avviare il sistema. Ignorare il test di elementi multimediali, selezionare la lingua inglese, selezionare i **Dispositivi di base dello spazio di archiviazione**, verificare che il disco rigido non contiene dati importanti e fare clic su **Sì**, annullare qualsiasi tipo di dati. Immettere il nome host del server di gestione e selezionare la scheda di rete server.  Nella finestra di dialogo **Sistema di modifica** selezionare** Connetti automaticamente** e aggiungere un indirizzo IP statico, rete e le impostazioni DNS. Specificare un fuso orario e una password principale per accedere al server di gestione. 
2.  Quando richiesto il tipo di installazione desiderato selezionare **Creare Layout personalizzati** come partizione. Dopo aver fatto clic **successivo** selezionare **libera** e fare clic su Crea. Creare **/**, **/var/crash** e **/ home partizioni** con **tipo ADFS:** **ext4**. Creare partizione Scambia come **tipo ADFS: scambiare**.
3.  Se si trovano dispositivi preesistenti che verrà visualizzato un messaggio di avviso. Fare clic su **formato** per formattare l'unità con le impostazioni di partizione. Fare clic su **scrivere modifica su disco** per applicare le modifiche di partizione.
4.  Selezionare **Installa caricatore di avvio** > **successiva** per installare il caricatore di avvio nella partizione radice.
5.  Al termine dell'installazione fare clic su **Riavvia**.


#### <a name="retrieve-the-scsi-ids"></a>Recuperare l'ID SCSI

1. Dopo l'installazione recuperare gli ID SCSI per ogni disco rigido SCSI nella macchina virtuale. Per eseguire questo arrestare il server di gestione delle macchine Virtuali, le proprietà di macchine Virtuali nella VMware pulsante destro del mouse sulla voce macchine Virtuali > **Modifica impostazioni** > **Opzioni**.
2. Fare clic **su Avanzate** > **articoli generale** e fare clic su **Parametri di configurazione**. Questa opzione sarà de-active quando il computer è in esecuzione. Per rendere attivo è necessario arrestare il computer.
3. Se la riga **disco. EnableUUID** esiste assicurarsi che il valore è impostato su **True** (maiuscole/minuscole). Se è già è possibile annullare e testare il comando SCSI all'interno di un sistema operativo guest dopo che viene avviato. 
4.  Se la riga non esistente fare clic su **Aggiungi riga** – e aggiungere con il valore **vero** . Non utilizzare le virgolette doppie.

#### <a name="install-additional-packages"></a>Installare pacchetti aggiuntivi

È necessario scaricare e installare alcuni pacchetti aggiuntivi. 

1.  Verificare che il server di destinazione principale è connesso a internet.
2.  Eseguire questo comando per scaricare e installare 15 pacchetti dal repository CentOS: **# slurp installazione – y xfsprogs perl lsscsi rsync wget kexec-degli strumenti**.
3.  Se il computer di origine è protezione esegue Linux con Reiser o XFS file system per il dispositivo principale o di avvio, allora è necessario scaricare e installare pacchetti aggiuntivi come indicato di seguito:

    - # <a name="cd-usrlocal"></a>percorso /usr/local CD
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>RPM-ivh kmod-reiserfs-0,0-1.el6.elrepo.x86_64.rpm reiserfs-utilità-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>RPM-ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Applicare le modifiche personalizzate

Eseguire le operazioni seguenti per applicare le modifiche personalizzate dopo aver completare i passaggi dopo l'installazione e installato i pacchetti di:

1.  Copiare la macchina virtuale RHEL 6-64 unificata agente di binario. Eseguire questo comando per untar binario: **catrame – zxvf <file name> **
2.  Eseguire questo comando per concedere le autorizzazioni: **# chmod 755./ApplyCustomChanges.sh**
3.  Eseguire lo script: **./ApplyCustomChanges.sh #**. È consigliabile eseguire il script solo una volta. Riavviare il server dopo lo script viene eseguito correttamente.


## <a name="run-the-failback"></a>Eseguire il failback

### <a name="reprotect-the-azure-vms"></a>Riproteggere macchine virtuali Azure

1.  Nel portale del ripristino del sito > scheda **macchine** selezionare la macchina virtuale che viene terminata e quindi scegliere **Proteggi nuovamente**.
2.  In **Server di destinazione Master** e **Processo Server** selezionare il server di destinazione master locale e il server di processo macchine Virtuali di Azure.
3.  Selezionare l'account che è configurato per la connessione a macchina virtuale.
4.  Selezionare la versione di failback del gruppo di protezione. Per esempio se la macchina virtuale protetto PG1, sarà necessario selezionare PG1_Failback.
5.  Se si desidera ripristinare in un percorso alternativo, selezionare l'unità di conservazione e l'archivio dati configurato per il server di destinazione principale. Quando non si torna al sito locale macchine virtuali VMware nel piano di protezione del failback utilizzerà l'archivio dati stesso come server di destinazione master. Se si desidera ripristinare la replica macchine Virtuali di Azure allo stesso locale macchine Virtuali la macchina virtuale locale deve essere già nell'archivio dati stesso come server di destinazione master. Se non c'è alcun macchine Virtuali locale durante reprotection verrà creato uno nuovo.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  Dopo aver fatto clic **OK** per iniziare un processo reprotection inizia replicare la macchina virtuale da Azure al sito locale. È possibile tenere traccia dello stato di avanzamento nella scheda **processi** .

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Eseguire caso di errore al sito locale

Dopo aver reprotection la macchina virtuale spostata nella versione failback del gruppo di protezione e verrà automaticamente aggiunti al piano di ripristino utilizzato per il failover Azure se presente.

1.  Nella pagina **Ripristino plan di messaggistica unificata** selezionare il piano di ripristino contenente il gruppo di failback e fare clic su **Failover** > **Failover non pianificato**.
2.  Nella finestra di **Conferma Failover** verificare la direzione di failover (da Azure) e selezionare il punto di ripristino che si desidera utilizzare per il failover (ultima versione). Se è abilitata **Macchine Virtuali con più** durante la configurazione delle proprietà della replica è possibile recuperare all'ultima versione dell'app o punto di ripristino coerente arresto anomalo. Fare clic sul segno di spunta per avviare il failover.
3.  Durante il failover il ripristino del sito verrà chiuso macchine virtuali di Azure. Dopo aver verificato che il failback verrà completata nel modo previsto è possibile verificare che le macchine virtuali Azure si è arrestate come previsto.

### <a name="reprotect-the--on-premises-site"></a>Riproteggere sito locale

Al termine del failback i dati saranno indietro nel sito locale, ma non protetta. Per avviare la replica in Azure nuovamente eseguire le operazioni seguenti:

1.  Nel portale del ripristino del sito > Seleziona scheda **macchine** macchine virtuali che non sono state eseguire il backup e quindi scegliere **Proteggi nuovamente**. 
2.  Dopo aver verificato che la replica Azure funziona come previsto, in Azure è possibile eliminare le macchine virtuali Azure (al momento non è in esecuzione) che sono state non è possibile tornare.


### <a name="common-issues-in-failback"></a>Problemi noti in failback

1. Se si esegue individuazione vCenter utente di sola lettura e proteggere le macchine virtuali, ha avuto esito positivo e failover funziona. Al momento del riproteggere avrà esito negativo dal momento che non possono essere rilevato archivi i dati. Come sintomo non vedrete archivi dati elencati durante nuovamente la protezione. Per risolvere questo problema, è possibile aggiornare le credenziali vCenter con account appropriato dotato di autorizzazioni e ripetere il processo. [Per saperne di più](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Quando si failback una VM Linux ed eseguire locale, si noterà che il pacchetto di gestione di rete viene disinstallato dal computer. In questo modo la macchina virtuale recuperata in Azure, viene rimosso il pacchetto di gestione di rete.
3. Quando una macchina virtuale è configurata con indirizzo IP statico e Impossibile su Azure, l'indirizzo IP viene acquisita tramite DHCP. Quando non si riesce sul nuovo in locale, la macchina virtuale continuerà a utilizzare DHCP per acquisire l'indirizzo IP. Sarà necessario manualmente login nel computer e impostare l'indirizzo IP all'indirizzo statico se necessario.
4. Se si utilizza ESXi 5.5 edizione gratuita o edizione gratuita Hypervisor vSphere 6, failover potrebbe avere esito positivo, ma failback non avrà esito positivo. Sarà necessario eseguire l'aggiornamento a una licenza di valutazione per abilitare il failback ned.

## <a name="failing-back-with-expressroute"></a>Errore indietro con ExpressRoute

Può eseguire nuovamente il una connessione VPN o Azure ExpressRoute. Se si desidera utilizzare ExpressRoute tenere presente quanto segue:

- ExpressRoute deve essere configurato sulla rete virtuale Azure a quale origine fail computer sopra e in quale macchine virtuali Azure si trovano dopo il failover.
- Dati viene replicati a un account di archiviazione Azure su un endpoint pubblico. È necessario configurare peering pubblica in ExpressRoute con i data center di destinazione per la replica il ripristino del sito da utilizzare ExpressRoute.



