<properties 
   pageTitle="Gestione di Snapshot StorSimple e volumi | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare il componente aggiuntivo di MMC StorSimple Snapshot Manager per visualizzare e gestire i volumi e per configurare i backup."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Gestione delle istantanea StorSimple consente di visualizzare e gestire i volumi

## <a name="overview"></a>Panoramica

È possibile utilizzare il nodo StorSimple Snapshot Manager **volumi** (nel riquadro **dell'ambito** ) per selezionare volumi e visualizzare le relative informazioni. I volumi vengono presentati come unità che corrispondono ai volumi installati dall'host. Il nodo **volumi** Mostra volumi locali e tipi di volume supportati da StorSimple, inclusi i volumi individuati mediante l'utilizzo di iSCSI e un dispositivo. 

Per ulteriori informazioni sui volumi supportati, visitare [il supporto per più tipi di volume](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Elenco di volumi nel riquadro dei risultati](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Il nodo **volumi** consente anche di analisi o eliminare i volumi dopo StorSimple Snapshot rilevato loro. 

In questa esercitazione viene illustrato come è possibile installare, inizializzare e formattare volumi e quindi utilizzare Gestione Snapshot StorSimple per:

- Visualizzare le informazioni relative ai volumi 
- Eliminare i volumi
- Ripetere l'analisi di volumi 
- Configurare un volume di base ed eseguire il backup
- Configurare un volume speculare dinamico ed eseguire il backup

>[AZURE.NOTE] Tutte le azioni di nodo **Volume** sono disponibili anche nel riquadro **Azioni** .
 
## <a name="mount-volumes"></a>Collegare volumi

Utilizzare la procedura seguente per installare, inizializzare e formattare i volumi StorSimple. Questa procedura utilizza Gestione disco, un'utilità di sistema per la gestione dei dischi rigidi e i volumi o partizioni corrispondente. Per ulteriori informazioni su Gestione disco, passare a [Gestione disco](https://technet.microsoft.com/library/cc770943.aspx) del sito Web Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Per collegare volumi

1. Nel computer host, avviare l'iniziatore Microsoft.

2. Fornire uno degli indirizzi IP interfaccia come il portale di destinazione o indirizzo IP di individuazione e connettersi al dispositivo. Una volta connesso il dispositivo, i volumi saranno possibile accedere al sistema di Windows. Per ulteriori informazioni sull'uso di iniziatore Microsoft, passare alla sezione "La connessione a un dispositivo di destinazione iSCSI" [nell'installazione e configurazione di Microsoft iSCSI iniziatore][1].

3. Per avviare Gestione disco, usare una delle opzioni seguenti:

    - Nella casella **Esegui** , digitare diskmgmt.

    - Avviare Server Manager, espandere il nodo **dello spazio di archiviazione** e quindi selezionare **Gestione disco**.

    - Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione Computer** e quindi selezionare **Gestione disco**. 

    >[AZURE.NOTE] È necessario utilizzare i privilegi di amministratore per eseguire Gestione disco.
 
4. Portare online il volume:

   1. In Gestione disco, fare clic volumi contrassegnati come **Offline**.

   2. Fare clic su **Riattiva disco**. Il disco deve essere contrassegnato **Online** dopo la riattivazione.

5. Inizializzare i volumi:

   1. Pulsante destro del mouse volumi scoperta di recente.

   2. Nel menu selezionare **Inizializzare disco**.

   3. Nella finestra di dialogo **Inizializzare disco** selezionare dischi da inizializzare e quindi fare clic su **OK**.

6. Formattare i volumi semplici:

   1. Fare clic su un volume che si desidera formattare.

   2. Nel menu, selezionare **Nuovo Volume semplice**.

   3. Per formattare il volume, utilizzare la creazione guidata nuovo Volume semplice:

      - Specificare la dimensione del volume.
      - Fornire una lettera di unità.
      - Selezionare del file system.
      - Specificare la dimensione di unità di assegnazione 64 KB.
      - Eseguire una formattazione rapida.

7. Formattare i volumi partizioni multiple. Per istruzioni, passare alla sezione, "Partizioni e volumi" in [Implementazione della gestione del disco](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Visualizzare le informazioni sugli volumi

Utilizzare la procedura seguente per visualizzare informazioni su Azure StorSimple volumi e locale.

#### <a name="to-view-volume-information"></a>Per visualizzare le informazioni di volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro di **ambito** , fare clic sul nodo **volumi** . Viene visualizzato un elenco dei volumi locali e collegati, inclusi tutti i volumi StorSimple Azure, nel riquadro **risultati** . Le colonne nel riquadro **risultati** sono configurabili. (Rapida del nodo **volumi** , selezionare **Visualizza**e quindi selezionare **Aggiungi/Rimuovi colonne**.)

    ![Configurare le colonne](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Colonna dei risultati | Descrizione 
    :--------------|:-------------
    Nome           | Nella colonna **nome** contiene la lettera assegnata a ciascun volume scoperta di recente.
    Dispositivo         | La colonna **dispositivo** contiene l'indirizzo IP del dispositivo collegato al computer host.
    Nome del Volume dispositivo | Nella colonna **Nome Volume dispositivo** contiene il nome del volume dispositivo a cui appartiene il volume selezionato. Questo è il nome di volume definito nel portale di classica Azure per tale volume specifico.
    Percorsi di accesso   | Nella colonna **Percorsi di accesso** viene visualizzato il percorso di accesso per il volume. Questo è il punto di montaggio o una lettera di unità in cui il volume è accessibile sul computer host.
 
## <a name="delete-a-volume"></a>Eliminare un volume

Utilizzare la procedura seguente per eliminare un volume da StorSimple Snapshot Manager.

>[AZURE.NOTE] È possibile eliminare un volume se fa parte di un gruppo di volume. (L'opzione di eliminazione non è disponibile per i volumi che fanno parte di un gruppo di volume.) È necessario eliminare il gruppo intero volume per eliminare il volume.


#### <a name="to-delete-a-volume"></a>Per eliminare un volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** , fare clic sul nodo **volumi** . 

3. Nel riquadro **dei risultati** fare clic sul volume che si desidera eliminare.

4. Nel menu fare clic su **Elimina**. 

    ![Eliminare un volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. Verrà visualizzata la finestra di dialogo **Elimina Volume** . Digitare **Conferma** nella casella di testo e quindi fare clic su **OK**.

6. Per impostazione predefinita, StorSimple Snapshot Manager il backup di un volume prima di eliminarlo. Questo precauzioni consente di evitare la perdita di dati se non è stata intenzionale l'eliminazione. Gestione Snapshot StorSimple visualizzato un messaggio di stato di avanzamento delle **Istantanea automatica** mentre si esegue il backup del volume. 

    ![Messaggio istantanea automatica](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Ripetere l'analisi di volumi

Utilizzare la procedura seguente per analisi volumi connessi a StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>L'analisi di volumi

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** destro **volumi**e quindi fare clic su **Ripeti analisi volumi**.

    ![Ripetere l'analisi di volumi](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Questa procedura Sincronizza l'elenco di volume con StorSimple Snapshot Manager. Le modifiche, ad esempio nuovi volumi o volumi eliminati, verranno eseguite anche i risultati.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurare e drill-up un volume di base

Utilizzare la procedura seguente per configurare una copia di backup di un volume di base e avviare immediatamente una copia di backup o creazione di un criterio per i backup pianificati.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

- Assicurarsi che il computer host e dispositivo StorSimple siano configurati correttamente. Per ulteriori informazioni, passare alla [distribuzione dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

- Installare e configurare StorSimple Snapshot Manager. Per ulteriori informazioni, passare alla [Distribuzione StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Per configurare il backup del volume di base

1. Creare un volume di base sul dispositivo StorSimple.

2. Montaggio, inizializzare e formattare il volume come descritto in [collegare volumi](#mount-volumes). 

3. Fare clic sull'icona di gestione di Snapshot StorSimple sul desktop. Verrà visualizzata la finestra Gestione Snapshot StorSimple. 

4. Nel riquadro **ambito** rapida del nodo **volumi** e scegliere **analisi volumi**. Al termine dell'analisi, un elenco di volumi dovrebbero essere visualizzate nel riquadro **risultati** . 

5. Nel riquadro **dei risultati** destro il volume e quindi selezionare **Crea gruppo Volume**. 

    ![Creare il gruppo di volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. Nella finestra di dialogo **Crea gruppo Volume** digitare un nome per il gruppo volume, assegnarvi volumi e quindi fare clic su **OK**.

7. Nel riquadro di **ambito** , espandere il nodo **Gruppi Volume** . Il nuovo gruppo di volume deve essere visualizzato sotto il nodo **Gruppi Volume** . 

8. Pulsante destro del mouse sul nome del gruppo volume.

    - Per avviare un processo di backup (su richiesta) interattivo, fare clic su **Eseguire Backup**. 

    - Per pianificare un backup automatico, fare clic su **Crea criterio di Backup**. Nella pagina **Generale** , selezionare un gruppo di volume dall'elenco. Nella pagina **pianificazione** , immettere i dettagli di pianificazione. Al termine, fare clic su **OK**. 

9. Per confermare che si è iniziata il processo di backup, espandere il nodo **processi** nel riquadro di **ambito** e quindi fare clic sul nodo **in esecuzione** . Nel riquadro **risultati** viene visualizzato l'elenco dei processi attualmente in esecuzione. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurare e drill-up volume speculare dinamico

Completare la procedura seguente per configurare il backup di un volume speculare dinamico:

- Passaggio 1: Utilizzo del disco per creare un volume speculare dinamico. 

- Passaggio 2: Utilizzare StorSimple Snapshot Manager per configurare il backup.

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

- Assicurarsi che il computer host e dispositivo StorSimple siano configurati correttamente. Per ulteriori informazioni, passare alla [distribuzione dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

- Installare e configurare StorSimple Snapshot Manager. Per ulteriori informazioni, passare alla [Distribuzione StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

- Configurare due volumi sul dispositivo StorSimple. (Negli esempi, i volumi disponibili sono **disco 1** e **2 disco**). 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Passaggio 1: Utilizzo del disco per creare un volume speculare dinamico

Gestione disco è un'utilità di sistema per la gestione dei dischi rigidi e i volumi o partizioni che contengono. Per ulteriori informazioni su Gestione disco, passare a [Gestione disco](https://technet.microsoft.com/library/cc770943.aspx) del sito Web Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Per creare un volume speculare dinamico

1. Per avviare Gestione disco, usare una delle opzioni seguenti: 

   - Aprire la finestra di dialogo **Esegui** , digitare **diskmgmt**e premere INVIO.

   - Avviare Server Manager, espandere il nodo **dello spazio di archiviazione** e quindi selezionare **Gestione disco**. 

   - Avviare **Strumenti di amministrazione**, espandere il nodo **Gestione Computer** e quindi selezionare **Gestione disco**. 

2. Assicurarsi di avere due volumi disponibili nel dispositivo StorSimple. (Nell'esempio, i volumi disponibili sono **disco 1** e **2 disco**). 

3. Nella finestra Gestione disco, nella colonna a destra del riquadro inferiore destro del mouse sul **disco 1** e selezionare **Nuovo Volume speculare**. 

    ![Nuovo Volume speculare](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. Nella pagina della procedura guidata **Nuovo Volume riflesso** , fare clic su **Avanti**.

5. Nella pagina **Seleziona dischi** selezionare **disco 2** nel riquadro **selezionati** , fare clic su **Aggiungi**e quindi fare clic su **Avanti**. 

6. Nella pagina **Assegna lettera di unità o percorso** accettare le impostazioni predefinite e quindi fare clic su **Avanti**. 

7. Nella pagina **Format Volume** nella casella **Dimensione dell'unità di assegnazione** , selezionare **64 KB**. Selezionare la casella di controllo **Esegui formattazione veloce** e quindi fare clic su **Avanti**. 

8. Nella pagina **completamento il nuovo Volume riflesso** esaminare le impostazioni e quindi fare clic su **Fine**. 

9. Viene visualizzato un messaggio per indicare che il disco di base viene convertito in un disco. Fare clic su **Sì**.

    ![Messaggio di conversione disco dinamico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. In Gestione disco, verificare che il disco 1 e 2 disco vengono visualizzati come volumi dinamici. (**Dinamico** deve essere visualizzato nella colonna stato e il colore delle barre capacità dovrebbe essere rosso, che indica un volume speculare) 

    ![Disco gestione riflesso dinamici](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Passaggio 2: Utilizzare StorSimple Snapshot Gestione configurazione di backup

Utilizzare la procedura seguente per configurare un volume speculare dinamico e avviare immediatamente una copia di backup o creazione di un criterio per i backup pianificati.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Per configurare il backup del volume speculare dinamico

1. Fare clic sull'icona di gestione di Snapshot StorSimple sul desktop. Verrà visualizzata la finestra Gestione Snapshot StorSimple. 

2. Nel riquadro **ambito** mouse nodo **volumi** e scegliere **analisi volumi**. Al termine dell'analisi, un elenco di volumi dovrebbero essere visualizzate nel riquadro **risultati** . Il volume speculare dinamico viene elencato in un unico volume. 

3. Nel riquadro **dei risultati** mouse sul volume speculare e quindi fare clic su **Crea gruppo Volume**. 

4. Nella finestra di dialogo **Crea gruppo Volume** digitare un nome per il gruppo volume, assegnare il volume speculare dinamico a questo gruppo e quindi fare clic su **OK**. 

5. Nel riquadro di **ambito** , espandere il nodo **Gruppi Volume** . Il nuovo gruppo di volume deve essere visualizzato sotto il nodo **Gruppi Volume** . 

6. Pulsante destro del mouse sul nome del gruppo volume. 

    - Per avviare un processo di backup (su richiesta) interattivo, fare clic su **Eseguire Backup**. 

    - Per pianificare un backup automatico, fare clic su **Crea criterio di Backup**. Nella pagina **Generale** , selezionare il gruppo volume dall'elenco. Nella pagina **pianificazione** , immettere i dettagli di pianificazione. Al termine, fare clic su **OK**. 

7. È possibile monitorare il processo di backup mentre è in esecuzione. Nel riquadro di **ambito** , espandere il nodo **processi** e quindi fare clic su **esecuzione**, i dettagli vengono visualizzate nel riquadro **risultati** . Al termine del processo di backup, i dettagli vengono trasferiti all'elenco dei processi di **ultime 24** ore. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare Gestione Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
- Informazioni su come [utilizzare Gestione Snapshot StorSimple per creare e gestire i gruppi di volume](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
