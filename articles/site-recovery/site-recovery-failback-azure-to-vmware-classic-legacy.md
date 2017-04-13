<properties 
   pageTitle="Esito negativo posteriore macchine virtuali di VMware e server fisici comuni per VMware (legacy) | Microsoft Azure" 
   description="In questo articolo viene descritto come esito negativo nuovamente una macchina virtuale VMware che replicata in Azure con il ripristino del sito di Azure." 
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
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Esito negativo posteriore macchine virtuali di VMware e server fisici comuni per VMware con il ripristino del sito di Azure (legacy)

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-failback-azure-to-vmware.md)
- [Portale classica Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portale classica Azure (Legacy)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md)

## <a name="overview"></a>Panoramica

In questo articolo viene descritto come esito negativo posteriore macchine virtuali di VMware e server fisici Windows/Linux da Azure al sito locale dopo che è stato replicato nel sito locale in Azure.

>[AZURE.NOTE] In questo articolo viene descritto lo scenario legacy. Si devono utilizzare le istruzioni in questo articolo solo se replicate in Azure [queste](site-recovery-vmware-to-azure-classic-legacy.md)istruzioni precedenti. Se si impostare la replica utilizzo [avanzato per la distribuzione](site-recovery-vmware-to-azure-classic-legacy.md) , seguire le istruzioni in [questo articolo](site-recovery-failback-azure-to-vmware-classic.md) per avere esito negativo indietro. 


## <a name="architecture"></a>Architettura

Il diagramma rappresenta lo scenario failover e failback. Le linee blu sono le connessioni usate durante il failover. Le linee rosse sono le connessioni usate durante failback. Le linee con frecce accedere tramite internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>Prima di iniziare 

- È necessario che non sopra le macchine virtuali VMware o server fisici e deve essere in esecuzione in Azure.
- Si noti che è possibile solo avere esito negativo posteriore macchine virtuali di VMware e server fisici Windows/Linux da Azure in macchine virtuali VMware nel sito principale locale.  Se si sta problemi nuovamente un computer fisico, il controllo a Azure viene convertito in una macchina virtuale Azure e failback VMware viene convertito in una VM VMware.

Ecco come si configura il failback:

1. **Impostare i componenti di failback**: È necessario configurare un server vContinuum in locale e scegliere il server di configurazione macchine Virtuali di Azure. È necessario anche configurare un server di processo come una macchina virtuale Azure per inviare dati server di destinazione master locale. Registrare il server di processo con il server di configurazione gestite il failover. Installare un server di destinazione master locale. Se è necessario un server di destinazione principale di Windows è impostata automaticamente durante l'installazione di vContinuum. Se è necessario Linux è necessario configurarlo manualmente in un server diverso.
2. **Abilitare la protezione e failback**: dopo aver configurato i componenti, in vContinuum è necessario abilitare la protezione per non riuscita su macchine virtuali di Azure. È necessario eseguire un controllo di conformità in macchine virtuali e caso di errore da Azure al sito locale. Al termine failback si riproteggere computer locale in modo che inizino replica di Azure.



## <a name="step-1-install-vcontinuum-on-premises"></a>Passaggio 1: Installare vContinuum locale

È necessario installare un server vContinuum in locale e scegliere il server di configurazione.

1.  [Scaricare vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Scaricare la versione [vContinuum aggiornare](http://go.microsoft.com/fwlink/?LinkID=533813) .
3. Installare l'ultima versione di vContinuum. Nella pagina di **benvenuto** fare clic su **Avanti**.
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  La prima pagina della procedura guidata specificare l'indirizzo IP del server CX e la porta del server CX. Selezionare **utilizza HTTPS**.

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Individuare il server di configurazione indirizzo IP nella scheda **Dashboard** del server di configurazione macchina virtuale in Azure.
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Trovare il server di configurazione porta pubblica HTTPS nella scheda **endpoint** del server di configurazione macchina virtuale in Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Nella pagina **Dettagli Passphrase CS** specificare la passphrase annotato verso il basso al momento della registrazione il server di configurazione. Se non si ricorda archiviarlo **c:\\file di programma (x86)\\InMage sistemi\\privato\\connection.passphrase** sul server di configurazione macchina virtuale.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Nella pagina **Seleziona destinazione posizione** specifica in cui si vuole installare il server vContinuum e fare clic su **Installa**.

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Al termine dell'installazione, è possibile avviare vContinuum.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>Passaggio 2: Installare un server di processo in Azure 

È necessario installare un server di processo in Azure in modo che le macchine virtuali di Azure inviare i dati a un server di destinazione master locale. 

1.  Nella pagina **Configurazione server** Azure, selezionare questa opzione per aggiungere un nuovo processo server.

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Specificare un server di processo nome e un nome e una password per connettere la macchina virtuale come amministratore. Selezionare il server di configurazione a cui si desidera registrare il server di processo. Deve essere lo stesso server che si usano per proteggere e avere esito negativo su macchine virtuali.
3.  Specificare la rete Azure in cui devono essere distribuiti server processo. Dovrebbe essere della stessa rete del server di configurazione. Specificare una subnet indirizzo selezionato IP univoca e iniziare la distribuzione.

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Un processo viene attivato per distribuire il server di processo.

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Dopo che il server di processo viene distribuito in Azure è possibile accedere al server utilizzando le credenziali specificate. Registrare il server di processo nello stesso modo che è stato registrato il server di processo locale. 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Server registrati durante il failback non sarà visibile in proprietà macchine Virtuali nel ripristino del sito. Visibili solo nella scheda **server** del server di configurazione in cui è registrate. Possono essere necessari circa 10-15 minuti finché non viene visualizzato il server di processo nella scheda.


## <a name="step-3-install-a-master-target-server-on-premises"></a>Passaggio 3: Installare un server di destinazione master locale

A seconda del sistema operativo macchine virtuali di origine, è necessario installare Linux o un server di destinazione master Windows locale.

### <a name="deploy-a-windows-master-target-server"></a>Distribuire un server di destinazione principale di Windows

Un valore di destinazione master Windows già viene fornito con il programma di installazione di vContinuum. Quando si installa il vContinuum, un server master è distribuito nello stesso computer e registrato per il server di configurazione.

1.  Per iniziare la distribuzione, creare un oggetto vuoto computer locale nell'host ESX in cui si desidera ripristinare le macchine virtuali comuni.

2.  Assicurarsi che siano almeno due dischi collegati a macchina virtuale: una viene utilizzata per il sistema operativo e l'altra viene utilizzata per l'unità di conservazione.

3.  Installare il sistema operativo.

4.  Installare il vContinuum nel server. Inoltre è stata completata l'installazione del server di destinazione master.

### <a name="deploy-a-linux-master-target-server"></a>Distribuire un server di destinazione master Linux

1.  Per iniziare la distribuzione, creare un oggetto vuoto computer locale nell'host ESX in cui si desidera ripristinare le macchine virtuali comuni.

2.  Assicurarsi che siano almeno due dischi collegati a macchina virtuale: una viene utilizzata per il sistema operativo e l'altra viene utilizzata per l'unità di conservazione.

3.  Installare il sistema operativo Linux. Il sistema di destinazione master Linux non utilizzare LVM per gli spazi di archiviazione radice o criteri di conservazione. Un server di destinazione master Linux è configurato per evitare l'individuazione di partizioni/dischi LVM per impostazione predefinita.
4.  Partizioni che è possibile creare:

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Eseguire il post sotto procedura di installazione prima di iniziare l'installazione di destinazione principale.


#### <a name="post-os-installation-steps"></a>Procedura di installazione post del sistema operativo

Per ottenere l'ID SCSI per ogni disco rigido SCSI in un computer virtuale Linux, attivare il parametro "disco. EnableUUID = TRUE "come indicato di seguito:

1. Arrestare la macchina virtuale.
2. Pulsante destro del mouse sulla voce macchine Virtuali nel riquadro sinistro > **Modifica impostazioni**.
3. Fare clic sulla scheda **Opzioni** . Selezionare **Avanzate\>articoli generale** > **Parametri di configurazione**. L'opzione **Parametri di configurazione** è disponibile solo quando si chiude il computer.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Controlla se una riga con **disco. EnableUUID** esistente. Se è impostato su **False** a e quindi impostarla su **True (non maiuscole/minuscole).** Se esiste ed è impostato su true, fare clic su **Annulla** e testare il comando SCSI all'interno del sistema operativo guest dopo che viene avviato. Se non esiste fare clic su **Aggiungi riga**.
5. Aggiungere disco. EnableUUID nella colonna **nome** . Impostare il valore è TRUE. Non aggiungere i valori indicati insieme le virgolette doppie.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>Scaricare e installare pacchetti aggiuntivi

Nota: Assicurarsi che il sistema è disponibile la connettività internet prima di scaricare e installare pacchetti aggiuntivi.

\#slurp installazione -y xfsprogs perl lsscsi rsync wget kexec-degli strumenti

Questo comando Scarica questi 15 pacchetti dal repository CentOS 6.6 e li installa:

BC-1.06.95-1.el6.x86\_rpm a 64 bit

20.el6.x86 di 1.15.1 busybox\_rpm a 64 bit

elfutils-librerie-0.158-3.2.el6.x86\_rpm a 64 bit

strumenti di kexec-280.el6.x86 di 2.0.0\_rpm a 64 bit

2.el6.x86 di 0,23 lsscsi\_rpm a 64 bit

3.1.el6 di 2.03 lzo\_5.1.x86\_rpm a 64 bit

Perl-5.10.1-136.el6\_6.1.x86\_rpm a 64 bit

Perl-modulo-collegabili-3.90-136.el6\_6.1.x86\_rpm a 64 bit

Perl-unità-escape-1.04-136.el6\_6.1.x86\_rpm a 64 bit
    
Perl-unità-semplici-3.13-136.el6\_6.1.x86\_rpm a 64 bit

Perl-librerie-5.10.1-136.el6\_6.1.x86\_rpm a 64 bit

versione di Perl-136.el6 di 0.77\_6.1.x86\_rpm a 64 bit

12.el6.x86 di 3.0.6 rsync\_rpm a 64 bit

reattiva 1.1.0 1.el6.x86\_rpm a 64 bit

5.el6 di 1.12 wget\_6.1.x86\_rpm a 64 bit

Nota: Se il computer di origine utilizza Reiser o XFS file System per il dispositivo principale o di avvio, quindi pacchetti seguenti devono essere scaricati e installati Linux master destinazione prima di protezione.

\#percorso /usr/local CD

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#RPM - ivh kmod-reiserfs-0,0-1.el6.elrepo.x86\_a 64 bit rpm reiserfs-utilità-3.6.21-1.el6.elrepo.x86\_rpm a 64 bit

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#RPM - ivh xfsprogs-3.1.1-16.el6.x86\_rpm a 64 bit

#### <a name="apply-custom-configuration-changes"></a>Applicare le modifiche di configurazione personalizzata

Prima di applicare queste modifiche assicurarsi che è stata completata la sezione precedente, attenersi alla seguente procedura:


1. Copiare il binario RHEL 6-64 unificata agente al sistema operativo appena creato.

2. Eseguire questo comando per untar binario: **catrame - zxvf \<nome del File\> **

3. Eseguire questo comando per concedere le autorizzazioni: \# **./ApplyCustomChanges.sh 755 chmod**

4. Eseguire lo script: ** \# ./ApplyCustomChanges.sh**. Eseguire lo script una sola volta sul server. Riavviare il server dopo l'esecuzione di script.



### <a name="install-the-linux-server"></a>Installare il server Linux


1. [Scaricare](http://go.microsoft.com/fwlink/?LinkID=529757) il file di installazione.
2. Copiare il file sul computer virtuale destinazione Linux Master con un'utilità di client sftp preferito. In alternativa è possibile connettersi al computer virtuale di destinazione master Linux e utilizzare wget per scaricare il pacchetto di installazione dal collegamento specificato
3. Accesso Linux destinazione master server virtuale computer utilizzando un ssh client di propria scelta
4. Se si è connessi alla rete Azure in cui è stato distribuito il server di destinazione master Linux tramite una connessione VPN usare l'indirizzo IP interno per il server che è possibile trovare la scheda **Dashboard** macchina virtuale e porta 22 per connettersi al Server con Secure Shell di Linux master destinazione.
5. Se ci si connette al server di destinazione master Linux tramite una connessione internet pubblica usare virtuale indirizzo IP pubblico del server di destinazione master Linux (dalla scheda **Dashboard** macchine virtuali) e l'endpoint pubblico creato per ssh accedere al servder Linux.
6. Estrarre i file dall'archivio tar gzipped Linux destinazione master Server installer eseguendo: *"catrame – xvzf Microsoft ripristino\_UA\_8.2.0.0\_RHEL6 64\"* dalla directory che contiene il file di programma di installazione.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Se è stato estratto il programma di installazione i file in un'altra directory passare alla directory in cui archivia il contenuto di tar sono stati estratti. Da questo percorso della directory eseguito "sudo. / install.sh".

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Quando viene richiesto di scegliere un ruolo primario selezionare **2 (schema destinazione)**. Lasciare le altre opzioni di installazione interattiva valori predefiniti.
9. Attendere per continuare l'installazione e l'interfaccia di configurazione Host venga visualizzato. La configurazione Host per il sarget master Linux Server è un'utilità della riga di comando. Non ridimensionare il ssh finestra Utilità di client. Utilizzare i tasti di direzione per selezionare l'opzione **globale** e premere INVIO sulla tastiera.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. Nel campo **IP** immettere l'indirizzo IP interno del server di configurazione (è possibile trovarlo nella scheda **Dashboard** del server di configurazione macchine Virtuali) e premere INVIO. Nella casella **porta** immettere **22** e premere INVIO. 
11.  Uscire da **Utilizzare HTTPS** come **Sì**e premere INVIO.
12.  Immettere la Passphrase generato sul server di configurazione. Se si usa un client PUTTY da un computer Windows a ssh alla macchina virtuale Linux è possibile utilizzare MAIUSC + INS per incollare il contenuto degli Appunti. Copiare la Passphrase negli Appunti locali mediante Ctrl + C e utilizzare MAIUSC + INS per incollare il codice. Premere INVIO.
13.  Usare il tasto freccia destra per passare da chiudere e premere INVIO. Attendere il completamento dell'installazione.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Se per qualche motivo non è riuscita registrare il server di destinazione master Linux per il server di configurazione è possibile eseguire in modo nuovamente eseguendo l'host utilità di configurazione da /usr/local/ASR/Vx/bin/hostconfigcli (è innanzitutto necessario impostare le autorizzazioni di accesso alla directory eseguendo chmod come un utente con privilegi avanzati).


#### <a name="validate-master-target-registration"></a>Convalidare la registrazione di destinazione principale

È possibile convalidare che il server di destinazione master è stato registrato al server di configurazione nell'archivio il ripristino del sito di Azure > **Configurazione Server** > **Dettagli sul Server**.

>[AZURE.NOTE] Dopo aver registrato il server di destinazione principale, se vengono visualizzati errori di configurazione che la macchina virtuale potrebbe essere stata eliminata da Azure o che i punti finali non sono configurati correttamente, in questo modo anche se la configurazione di destinazione master viene rilevata da Azure dndpoints quando la destinazione master viene distribuita in Azure, questo non vale per un locale destinazione master server in locale. Questa operazione non influisce su failback ed è possibile ignorare questi errori. 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>Passaggio 4: Proteggere le macchine virtuali al sito locale

È necessario proteggere macchine virtuali al sito locale prima di eseguire il nuovo.

### <a name="before-you-begin"></a>Prima di iniziare

Quando una macchina virtuale è impossibile su Azure, aggiunge un'unità temp aggiuntivi per file della pagina. Si tratta di un'unità aggiuntiva che non è in genere necessaria per il non riuscito su macchine Virtuali dal momento che potrebbe un'unità dedicata per il file di pagina. Prima di iniziare la protezione inversa macchine virtuali, è necessario assicurarsi che l'unità viene disattivata, in modo che non vengono protette. Eseguire questa operazione come indicato di seguito:

1.  Aprire Gestione Computer e selezionare Gestione degli archivi in modo che elenca i dischi online collegato al computer.
2.  Selezionare il disco temporaneo collegato al computer e scegliere per portare offline. 

### <a name="protect-the-vms"></a>Proteggere le macchine virtuali

1. Nel portale di Azure, controllare gli stati della macchina virtuale per garantire che sta non sopra.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Avviare vContinuum nel computer in uso.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Fare clic su **Nuova protezione** e selezionare il tipo di sistema di operazione, il 

4.  Nella finestra Nuovo che Apri selezionare il **tipo di sistema operativo** > **Ottieni dettagli** per macchine virtuali che si desidera tornare esito negativo. In **informazioni server primario**, identificare e selezionare le macchine virtuali che si desidera proteggere. Macchine virtuali sono elencate sotto il nome host vCenter presenti prima del failover.
5.  Quando si seleziona una macchina virtuale per la protezione (e non riesce già sopra a Azure) una finestra popup fornisce due voci per la macchina virtuale. Ciò avviene perché il server di configurazione rileva due istanze di macchine virtuali di registrati. È necessario rimuovere la voce per la macchina virtuale locale in modo che è possibile proteggere la macchina virtuale corretta. Per individuare la voce di macchine Virtuali di Azure corretta qui, è possibile accedere a macchina virtuale Azure e passare al file c:\Programmi\Microsoft (x86) \Microsoft Azure Site Recovery\Application Data\etc. In drscout.conf file, individuare l'ID host. Nella finestra di dialogo vContinuum mantenere la voce per il quale è stato trovato l'ID host nella macchina virtuale. Eliminare tutte le altre voci. Per selezionare la macchina virtuale corretta è possibile fare riferimento al proprio indirizzo IP. L'IP indirizzo intervallo locale sarà la macchina virtuale locale.

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. Nel server vCenter interrompere la macchina virtuale. È inoltre possibile eliminare le macchine virtuali locale.
7. Quindi specificare il locale MT server a cui si desidera proteggere le macchine virtuali. A tale scopo, connettersi al server vCenter a cui si desidera tornare esito negativo.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Selezionare il server di destinazione principale in base all'host a cui si desidera ripristinare la macchina virtuale.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Specificare l'opzione di replica per ognuna delle macchine virtuali. Per eseguire questa operazione è necessario selezionare lato Ripristino archivio dati a cui verranno ripristinate macchine virtuali. Nella tabella sono riepilogate le diverse opzioni che è necessario fornire per ogni macchina virtuale.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **Opzione** | **Opzione valore consigliato**
    ---|---
    Indirizzo IP del server di processo | Selezionare il server di processo distribuito in Azure
    Dimensione di conservazione in MB| 
    Valore di conservazione | 1
    Giorni/ore | Giorni
    Intervallo coerenza | 1
    Selezionare archivio dati di destinazione | Archivio dati disponibile nel sito del ripristino. Archivio dati deve esserci spazio sufficiente e disponibile per l'host ESX in cui si desidera ripristinare la macchina virtuale.


10. Configurare le proprietà che la macchina virtuale acquisirà dopo il controllo a sito locale. Nella tabella seguente sono riepilogate le proprietà.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **Proprietà** | **Dettagli**
    ---|---
    Configurazione della rete| Per ogni scheda di rete rilevato, selezionarlo e fare clic su **Modifica** per configurare l'indirizzo IP failback per la macchina virtuale. 
    Configurazione hardware| Specificare la CPU e memoria per la macchina virtuale. Impostazioni possono essere applicate a tutte le macchine virtuali che si desidera proteggere. Per identificare i valori corretti per la CPU e memoria, è possibile fare riferimento a ottenere le dimensioni di ruolo macchine virtuali IAAS e visualizzare il numero di core e memoria assegnata.
    Nome visualizzato | Dopo l'opzione in locale, è possibile rinominare le macchine virtuali come appaiono in magazzino vCenter. Il nome predefinito è il nome host del computer macchina virtuale. Per individuare il nome di macchine Virtuali, è possibile fare riferimento all'elenco macchine Virtuali nel gruppo di protezione.
    Configurazione NAT | Descritto dettagliatamente in seguito

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>Configurare le impostazioni NAT

1. Per abilitare la protezione delle macchine virtuali, è necessario stabilire due canali di comunicazione. Il primo canale è compresa tra la macchina virtuale con il server di processo. Il canale consente di raccogliere i dati da macchina virtuale e invia al server di processo che invia i dati al server di destinazione principale. Se il server di processo e la macchina virtuale da proteggere siano nella stessa rete virtuale Azure non necessario utilizzare le impostazioni NAT. In caso contrario specificare le impostazioni NAT. Visualizzare l'indirizzo IP pubblico del server di processo in Azure. 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. Il secondo canale è compresa tra il server di processo e quello di destinazione master. L'opzione per utilizzare NAT o meno varia a seconda sono utilizzando una connessione VPN in base a o comunicare tramite internet. Non selezionare NAt se si usa VPN, ma solo se si usa una connessione a internet.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Se è stato eliminato macchine virtuali locale come specificato e, se l'archivio di dati che non riescono a ancora contiene il file VMDK precedente sarà necessario assicurarsi che il failback che macchine Virtuali viene creato in una nuova posizione. Per eseguire questa operazione selezionare impostazioni **Avanzate** e specificare un'altra cartella per ripristinare le impostazioni di **nome cartella**. Lasciare le altre opzioni con le impostazioni predefinite. Applicare le impostazioni di nome cartella a tutti i server.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Eseguire una verifica della preparazione per garantire che le macchine virtuali sono pronte per essere protetto al locale. 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Attendere il completamento. Se è correttamente in macchine virtuali tutti è possibile specificare un nome per il piano di protezione. Fare clic su **Proteggi** per iniziare.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. È possibile monitorare lo stato di avanzamento in vContinuum.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Dopo il passaggio che termina **l'Attivazione di pianificare la protezione** è possibile monitorare protezione macchine Virtuali nel portale del ripristino del sito.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. È possibile visualizzare lo stato esatto facendo clic su Virtual Machine e monitorare lo stato di avanzamento.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>Preparare il piano di failback

È possibile preparare un piano di failback utilizzando vContinuum in modo che possa errori dell'applicazione al sito locale in qualsiasi momento. Questi piani di ripristino sono molto simili ai piani di ripristino nel ripristino del sito.

1.  Avviare vContinuum e selezionare **Gestisci piani** > **Recupera.** È possibile vedere dell'elenco di tutti i piani a cui sono stati utilizzati per avere esito negativo su macchine virtuali. È possibile utilizzare lo stessa piani per recuperare.

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Selezionare il piano di protezione e tutte le macchine che si desidera ripristinare al suo interno. Quando si seleziona ogni macchina virtuale è possibile visualizzare altri dettagli, tra cui il server ESX di destinazione e il disco di macchine Virtuali di origine. Fare clic su **Avanti** per iniziare la procedura guidata recuperare, scegliere macchine virtuali da recuperare.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. È possibile recuperare in base alle opzioni più ma si consiglia di utilizzare **Tag più recenti** e selezionare **Applica per tutte le macchine virtuali** per assicurarsi che i dati più recenti dal computer virtuale verranno utilizzati.
4. Eseguire il **verifica preparazione.** Verifica se i parametri corretti sono configurati per abilitare il ripristino di macchine Virtuali. Se tutti i controlli hanno esito positivo, fare clic su **Avanti** . In caso contrario, controllare il registro e risolvere gli errori.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Configurazione di **Macchine Virtuali** assicurarsi che le impostazioni di ripristino siano impostate correttamente. Se necessario, è possibile modificare le impostazioni di macchine Virtuali.

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Esaminare l'elenco di macchine virtuali che verranno ripristinati e specificare un ordine di ripristino. Si noti che macchine virtuali sono elencate usando il nome host del computer. Potrebbe essere difficile da mappare il nome del computer host macchina virtuale.
Per mappare i nomi, passare alle macchine virtuali **Dashboard** in Azure e verificare il nome host macchina virtuale.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Specificare un nome di piano e selezionare **Recupera in un secondo momento**. È consigliabile recuperare in un secondo momento, poiché la protezione iniziale potrebbe non essere completata. 
11. Fare clic su **Recupera** per salvare il piano o impostare un trigger di ripristino se è stato selezionato per recuperare ora e non in un secondo momento. È possibile verificare lo stato di ripristino per verificare se è stato salvato il piano del.

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>Recuperare macchine virtuali

Dopo il piano di una volta creato, è possibile recuperare le macchine virtuali. Verificare prima che le macchine virtuali è stata completata la sincronizzazione. Se lo stato della replica Mostra OK, viene completata la protezione e la soglia rilasciato è stato raggiunto. È possibile verificare l'integrità nelle proprietà macchine Virtuali.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Disattivare le macchine virtuali Azure prima di avviare il ripristino. In questo modo è presente alcun cervello divisa e che gli utenti potranno accedere solo una copia dell'applicazione. 


1.  Avviare il piano salvato. Nella finestra vContinuum selezionare **Monitor** i piani. Elenca tutti i piani a cui sono stati eseguiti.

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Selezionare il piano di **ripristino** , fare clic su **Start**. È possibile monitorare ripristino. Dopo che siano attivate macchine virtuali in cui è possibile connettersi a tali nella vCenter.

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Proteggere in Azure dopo failback

Dopo aver completato failback sarà probabilmente necessario proteggere macchine virtuali nuovamente. Eseguire questa operazione come indicato di seguito:

1.  Verificare che le macchine virtuali locale siano lavorando e che le applicazioni siano raggiungibili.
2.  Nel portale il ripristino del sito di Azure, selezionare le macchine virtuali ed eliminarli. Selezionare questa opzione per disabilitare la protezione delle macchine virtuali. In questo modo che le macchine virtuali non sia più protetti.
3.  In Azure eliminare il volume su macchine virtuali di Azure
4.  Eliminare il vecchia macchine virtuali nei vSpehere. Si tratta di macchine virtuali che in precedenza non su in Azure.
5.  Per il recupero del sito portale proteggere macchine virtuali che non è recente sopra. Dopo che è stato protetto è possibile aggiungere a un piano di ripristino.
 
## <a name="next-steps"></a>Passaggi successivi



- [Conoscere](site-recovery-vmware-to-azure-classic.md) la replica macchine virtuali VMware e server fisici e Azure utilizzando la distribuzione avanzata.

 
