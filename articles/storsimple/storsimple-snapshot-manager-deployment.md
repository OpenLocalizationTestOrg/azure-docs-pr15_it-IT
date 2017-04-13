<properties 
   pageTitle="Distribuire StorSimple Snapshot Manager | Microsoft Azure"
   description="Informazioni su come scaricare e installare StorSimple Snapshot gestore MMC snap-in per la gestione delle funzionalità di protezione e backup dei dati StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Distribuire snap-in MMC StorSimple Snapshot Manager

## <a name="overview"></a>Panoramica

È il responsabile di Snapshot StorSimple snap-in Microsoft Management Console (MMC) che semplifica la protezione dei dati e la gestione di backup in un ambiente di Microsoft Azure StorSimple. Con Manager StorSimple Snapshot consente di gestire Microsoft Azure StorSimple locali e cloud lo spazio di archiviazione come se fosse un sistema di archiviazione completamente integrato, pertanto semplificare i processi di backup e ripristino e ridurre i costi. 

In questa esercitazione vengono illustrati i requisiti di configurazione, nonché le procedure per l'installazione, rimozione e aggiornamento StorSimple Snapshot Manager.

>[AZURE.NOTE] 
>
>- È possibile usare StorSimple Snapshot Manager per gestire Microsoft Azure StorSimple virtuale matrici (noto anche come StorSimple locale dispositivi virtuali).
>
>- Se si intende installare l'aggiornamento StorSimple 2 sul dispositivo StorSimple, assicurarsi di scaricare la versione più recente di gestione di Snapshot di StorSimple e installarlo **prima di installare StorSimple aggiornamento 2**. La versione più recente di StorSimple Snapshot Manager è compatibile con le versioni precedenti e interagisce con tutte le versioni di Microsoft Azure StorSimple. Se si utilizza la versione precedente di StorSimple Snapshot Manager, sarà necessario aggiornarli (non occorre prima di installare la nuova versione, disinstallare la versione precedente).

## <a name="storsimple-snapshot-manager-installation"></a>Installazione di StorSimple Snapshot Manager

StorSimple Snapshot Manager può essere installato nel computer con sistema operativo Windows Server 2012 R2, Windows Server 2008 R2 SP1 o Windows Server 2012. In server che esegue Windows 2008 R2, è necessario installare Windows Server 2008 SP1 e Windows Management Framework 3.0. 

Prima di installare o aggiornare lo snap-in StorSimple Snapshot Manager per Microsoft Management Console (MMC), assicurarsi che il server di Microsoft Azure StorSimple dispositivo e host siano configurati correttamente. 

## <a name="configure-prerequisites"></a>Configurare i prerequisiti

I passaggi seguenti offrono una panoramica delle attività di configurazione, è necessario eseguire prima di installare il responsabile di Snapshot StorSimple. Per completare la configurazione di Microsoft Azure StorSimple e informazioni di installazione, inclusi i requisiti di sistema e istruzioni dettagliate, vedere [distribuire il dispositivo di StorSimple locale](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Prima di iniziare, esaminare l' [elenco di controllo configurazione distribuzione](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) ed e i [Prerequisiti per la distribuzione](storsimple-deployment-walkthrough.md#deployment-prerequisites) in [Distribuisci dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>Prima di installare StorSimple Snapshot Manager

1. Decomprimere, installare e connettere il dispositivo di Microsoft Azure StorSimple come descritto in [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) o [dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

2. Verificare che il computer host sia in esecuzione uno dei sistemi operativi seguenti:

    - Windows Server 2008 R2 (sui server che esegue Windows 2008 R2, è inoltre necessario installare Windows Management Framework 3.0 e Windows Server 2008 SP1)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Per un dispositivo virtuale StorSimple, l'host deve essere una macchina virtuale di Microsoft Azure. 

3. Assicurarsi che siano soddisfatti tutti i requisiti di configurazione di Microsoft Azure StorSimple. Per informazioni dettagliate, vedere [Prerequisiti per la distribuzione](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Connettere il dispositivo all'host ed eseguire la configurazione iniziale. Per informazioni dettagliate, vedere [passaggi di distribuzione](storsimple-deployment-walkthrough.md#deployment-steps).

5. Creare volumi nel dispositivo, assegnarle all'host e verificare che l'host è possibile installare e utilizzarli. Gestione di Snapshot StorSimple supporta i tipi di volumi seguenti: 

    - Volumi di base
    - Volumi semplici
    - Volumi dinamici
    - Volumi dinamici (RAID 1)
    - Volumi condivisi cluster
 
    Per informazioni sulla creazione di volumi nel dispositivo StorSimple o nel dispositivo virtuale StorSimple, passare a [passaggio 6: creare un volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume), in [Distribuisci dispositivo StorSimple locale](storsimple-deployment-walkthrough.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installare una nuova gestione delle istantanea StorSimple

Prima di installare StorSimple Snapshot Manager, assicurarsi che i volumi creati nel dispositivo StorSimple o StorSimple dispositivo virtuale sono installati, inizializzato e formattata come descritto in [Configura prerequisiti](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Per un dispositivo virtuale StorSimple, l'host deve essere una macchina virtuale di Microsoft Azure. 
>
>- L'host deve eseguire Windows 2008 R2, Windows Server 2012 o Windows Server 2012 R2. Se il server è in esecuzione Windows Server 2008 R2, è necessario installare Windows Server 2008 SP1 e Windows Management Framework 3.0.
>
>- È necessario configurare una connessione iSCSI dall'host al dispositivo StorSimple per potersi connettere il dispositivo di StorSimple Snapshot Manager.

Seguire questi passaggi per completare delle installazioni di StorSimple Snapshot Manager. Se si installa un aggiornamento, passare a [aggiornare o reinstallare StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Passaggio 1: Installare Snapshot StorSimple Manager 
- Passaggio 2: Connettersi StorSimple Snapshot Manager a un dispositivo 
- Passaggio 3: Verificare la connessione al dispositivo 

###<a name="step-1-install-storsimple-snapshot-manager"></a>Passaggio 1: Installare Snapshot StorSimple Manager

Utilizzare la procedura seguente per installare StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Per installare StorSimple Snapshot Manager

1. Scaricare il software di gestione di Snapshot StorSimple (Vai alla [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) in Microsoft Download Center) e salvare localmente nell'host.

2. In Esplora File, fare clic sulla cartella compressa e quindi fare clic su **Estrai tutti**.

3. Nella finestra di **estrarre compresse cartelle** , nella casella **Selezionare una destinazione ed estrarre file** , digitare o selezionare il percorso in cui si desidera archiviare da estrarre. 

       >[AZURE.IMPORTANT] È necessario installare StorSimple Snapshot Manager nell'unità c.
 
4. Selezionare la casella di controllo **Mostra file estratti al termine dell'operazione** e quindi fare clic su **Estrai**.

    ![Finestra di dialogo file Estrai](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. Al termine dell'estrazione, verrà visualizzata la cartella di destinazione. Fare doppio clic sull'icona di configurazione dell'applicazione che viene visualizzato nella cartella di destinazione.

5. Quando viene visualizzata la **Corretta configurazione del** messaggio, fare clic su **Chiudi**. Verrà visualizzata l'icona StorSimple Snapshot Manager sul desktop.

    ![icona del desktop](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Passaggio 2: Connettersi StorSimple Snapshot Manager a un dispositivo

Utilizzare la procedura seguente per connettere il gestore di Snapshot StorSimple a un dispositivo StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Per connettere il gestore di Snapshot StorSimple a un dispositivo

1. Fare clic sull'icona di gestione di Snapshot StorSimple sul desktop. Verrà visualizzata la finestra Gestione Snapshot StorSimple. La finestra contiene un riquadro **ambito** , un riquadro dei **risultati** e un riquadro **Azioni** . 

    ![Interfaccia utente StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - Il riquadro di **ambito** (riquadro a sinistra) contiene un elenco dei nodi organizzati in una struttura ad albero. È possibile espandere alcuni nodi per selezionare una visualizzazione o dati specifici relativi a tale nodo. Fare clic sull'icona freccia per espandere o comprimere un nodo. Fare clic su un elemento nel riquadro di **ambito** per visualizzare un elenco delle azioni disponibili per l'elemento. 

    - Riquadro dei **risultati** (riquadro centrale) contiene informazioni dettagliate sullo stato il nodo, visualizzazione o dati selezionati nel riquadro di **ambito** .

    - Nel riquadro **Azioni** sono elencate le operazioni che è possibile eseguire per il nodo, vista o dati selezionati nel riquadro di **ambito** .

    Per una descrizione completa dell'interfaccia utente StorSimple Snapshot Manager, vedere [interfaccia utente StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

2. Nel riquadro **ambito** destro nodo **dispositivi** e quindi fare clic su **Configura un dispositivo**. Verrà visualizzata la finestra di dialogo **Configura un dispositivo** .

    ![Configurare un dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. Nella casella di riepilogo **dispositivo** , selezionare l'indirizzo IP del dispositivo virtuale o di Microsoft Azure StorSimple dispositivo. Nella casella di testo **Password** digitare la password StorSimple Snapshot Manager creato per il dispositivo nel portale di classica Azure. Fare clic su **OK**.

4. Gestione di Snapshot StorSimple Cerca il dispositivo che sono stati identificati. Se il dispositivo è disponibile, StorSimple Snapshot Manager aggiunge una connessione. È possibile [verificare la connessione al dispositivo](#to-verify-the-connection) per confermare che la connessione è stato aggiunto.

    Se il dispositivo non è disponibile per qualsiasi motivo, StorSimple Snapshot Manager restituisce un messaggio di errore. Fare clic su **OK** per chiudere il messaggio di errore e quindi fare clic su **Annulla** per chiudere la finestra di dialogo **Configura un dispositivo** .

5. Quando si connette a un dispositivo, StorSimple Snapshot Manager Importa ogni gruppo volume configurato per il dispositivo, purché al gruppo volume sono associati backup. Gruppi di volume non dispone di backup associato non vengono importati. Inoltre, i criteri di backup che sono stati creati per un gruppo di volume non vengono importati. Per visualizzare i gruppi importati, il nodo di **Gruppi di Volume** più alto nel **riquadro** destro e fare clic su **Mostra/Nascondi importati gruppi**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Passaggio 3: Verificare la connessione al dispositivo

Utilizzare la procedura seguente per verificare che StorSimple Snapshot Manager sia connesso al dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Per verificare la connessione

1. Nel riquadro di **ambito** , fare clic sul nodo di **dispositivi** .

    ![Stato dispositivo è StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. Controllare il riquadro dei **risultati** : 

   - Se viene visualizzato un indicatore verde sull'icona del dispositivo e **disponibile** viene visualizzato nella colonna **stato** , il dispositivo è collegato. 

   - Se viene visualizzato un indicatore rosso sull'icona del dispositivo e viene visualizzato non disponibile nella colonna **stato** , il dispositivo non è connesso. 

   - Se **l'aggiornamento** viene visualizzato nella colonna **stato** , StorSimple Snapshot Manager è recupero volume gruppi e backup associato per un dispositivo connesso.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Aggiornare o reinstallare StorSimple Snapshot Manager

È necessario disinstallare completamente StorSimple Snapshot Manager prima di eseguire l'aggiornamento o reinstallare il software. 

Prima di reinstallare StorSimple Snapshot Manager, eseguire il backup database StorSimple Snapshot Manager esistente nel computer host. In questo modo le informazioni di configurazione e criteri backup in modo da poter ripristinare facilmente i dati da un backup.

Se si aggiorna o la reinstallazione StorSimple Snapshot Manager, procedere come segue:

- Passaggio 1: Disinstallare StorSimple Snapshot Manager 
- Passaggio 2: Eseguire il backup del database StorSimple Snapshot Manager 
- Passaggio 3: Reinstallare StorSimple Snapshot Manager e ripristinare il database 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Passaggio 1: Disinstallare StorSimple Snapshot Manager

Utilizzare la procedura seguente per disinstallare StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Per disinstallare StorSimple Snapshot Manager

1. Nel computer host, aprire il **Pannello di controllo**, fare clic su **programmi**e quindi fare clic su **programmi e funzionalità**.

2. Nel riquadro sinistro fare clic su **Disinstalla o modifica programma**.

3. Pulsante destro del mouse **StorSimple Snapshot Manager**e quindi fare clic su **Disinstalla**.

4. Per avviare il programma di installazione di gestione di Snapshot StorSimple. Fare clic su **Modifica impostazioni**e quindi fare clic su **Disinstalla**.

    >[AZURE.NOTE] Se sono presenti i processi MMC in esecuzione in background, ad esempio StorSimple Snapshot Manager o Gestione disco, la disinstallazione avrà esito negativo e verrà visualizzato un messaggio per chiudere tutte le istanze di MMC prima di provare a disinstallare il programma. Selezionare **chiudere automaticamente le applicazioni e provare a riavviare i al termine dell'installazione**e quindi fare clic su **OK**.
 
5. Al termine del processo di disinstallazione, viene visualizzato un messaggio di **Configurazione corretta** . Fare clic su **Chiudi**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Passaggio 2: Eseguire il backup del database StorSimple Snapshot Manager

Utilizzare la procedura seguente per creare e salvare una copia del database di gestione di Snapshot StorSimple.

#### <a name="to-back-up-the-database"></a>Per eseguire il backup del database

1. Arrestare il servizio di gestione StorSimple Microsoft:

   1. Avviare Server Manager.

   2. Selezionare **servizi**nel Dashboard di Server Manager dal menu **Strumenti** .

   3. Nella pagina **servizi** selezionare **Il servizio di gestione di Microsoft StorSimple**.

   4. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **arrestare il servizio**.

        ![Arrestare il servizio di gestione StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Passare alla C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData è una cartella nascosta.

3. Individuare il file XML di catalogo, copiare il file e archiviare la copia in un percorso sicuro o nel cloud.

    ![File di backup del catalogo StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Riavviare il servizio di gestione StorSimple Microsoft: 

    1. Selezionare **servizi**nel Dashboard di Server Manager dal menu **Strumenti** .

    2. Nella pagina **servizi** selezionare il **Servizio di gestione di Microsoft StorSimple**e.

    3. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **riavviare il servizio**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Passaggio 3: Reinstallare StorSimple Snapshot Manager e ripristinare il database

Per reinstallare StorSimple Snapshot Manager, seguire i passaggi descritti in [installare una nuova gestione delle istantanea StorSimple](#install-a-new-storsimple-snapshot-manager). Quindi, utilizzare la procedura seguente per ripristinare il database StorSimple Snapshot Manager.

#### <a name="to-restore-the-database"></a>Per ripristinare il database

1. Arrestare il servizio di gestione StorSimple Microsoft:

    1. Avviare Server Manager.

    2. Selezionare **servizi**nel Dashboard di Server Manager dal menu **Strumenti** .

    3. Nella pagina **servizi** selezionare **Il servizio di gestione di Microsoft StorSimple**.

    4. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **arrestare il servizio**.

2. Passare alla C:\ProgramData\Microsoft\StorSimple\BACatalog. 

     >[AZURE.NOTE] ProgramData è una cartella nascosta.

3. Eliminare il file XML di catalogo e sostituirlo con la versione salvata in precedenza.

4. Riavviare il servizio di gestione StorSimple Microsoft: 

    1. Selezionare **servizi**nel Dashboard di Server Manager dal menu **Strumenti** .

    2. Nella pagina **servizi** selezionare **Il servizio di gestione di Microsoft StorSimple**.

    3. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **riavviare il servizio**.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su StorSimple Snapshot Manager, passare a [che cos'è Gestione Snapshot StorSimple?](storsimple-what-is-snapshot-manager.md).

- Per altre informazioni sull'interfaccia utente di StorSimple Snapshot Manager, passare [all'interfaccia utente StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

- Per ulteriori informazioni sull'utilizzo di gestione di Snapshot StorSimple, passare a [Usare StorSimple Snapshot Manager per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
