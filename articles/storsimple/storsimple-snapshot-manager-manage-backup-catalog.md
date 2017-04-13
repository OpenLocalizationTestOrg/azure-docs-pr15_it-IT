<properties 
   pageTitle="Catalogo di backup StorSimple Snapshot Manager | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare il componente aggiuntivo di MMC StorSimple Snapshot Manager per visualizzare e gestire il catalogo di backup."
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
   ms.date="04/26/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Usare StorSimple Snapshot Manager per gestire il catalogo di backup

## <a name="overview"></a>Panoramica

La funzione principale di gestione di Snapshot di StorSimple è che consente di creare copie di backup coerenti con l'applicazione di volumi StorSimple sotto forma di istantanee. Snapshot sono elencati in un file XML denominato un *catalogo di backup*. Il catalogo di backup organizza istantanee dal gruppo volume e quindi in snapshot locale o snapshot cloud. 

In questa esercitazione viene descritto come è possibile utilizzare il nodo **Catalogo di Backup** per completare le attività seguenti:

- Ripristinare un volume 
- Duplicare un volume o un gruppo di volume 
- Eliminare una copia di backup 
- Ripristinare un file
- Ripristinare il database Storsimple Snapshot Manager

È possibile visualizzare il catalogo di backup, espandere il nodo del **Catalogo di Backup** nel riquadro di **ambito** e quindi espandere il gruppo volume.

- Se si fa clic sul nome del gruppo volume, il riquadro **risultati** Mostra il numero di istantanee locali e cloud snapshot disponibili per il gruppo volume. 

- Se si fa clic su **Snapshot locale** o **Uno Snapshot Cloud**, il riquadro **risultati** Mostra le informazioni seguenti sull'ogni snapshot backup (a seconda delle impostazioni di **visualizzazione** ): 

    - **Nome** : l'ora che di creazione dello snapshot. 

    - **Tipo** : se si tratta di uno snapshot locale o uno snapshot cloud. 

    - **Proprietario** : il proprietario del contenuto. 

    - **Disponibile** -se non è attualmente disponibile lo snapshot. **True** indica che lo snapshot è disponibile e può essere ripristinato; **Falso** indica che lo snapshot non è più disponibile. 

    - **Importati** : se si è importato il backup. **True** indica che il backup è stato importato dal servizio di gestione StorSimple al momento che il dispositivo è stato configurato in StorSimple Snapshot Manager. **Falso** indica che non è stato importato, ma è stata creata da StorSimple Snapshot Manager. (È possibile identificare facilmente un gruppo di volume importato perché viene aggiunto un suffisso che identifica il dispositivo da cui è stato importato il gruppo volume.)

    ![Catalogo di backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Se si espande **Snapshot locale** o **Uno Snapshot Cloud**e quindi fare clic su un nome di singoli snapshot, il riquadro **risultati** Mostra le informazioni seguenti sull'immagine selezionata:

    - **Nome** : il volume identificato da lettera. 

    - **I nomi locali** : il nome locale dell'unità (se disponibile). 

    - **Dispositivo** : il nome del dispositivo in cui si trova il volume. 

    - **Disponibile** -se non è attualmente disponibile lo snapshot. **True** indica che lo snapshot è disponibile e può essere ripristinato; **Falso** indica che lo snapshot non è più disponibile. 


## <a name="restore-a-volume"></a>Ripristinare un volume

Utilizzare la procedura seguente per ripristinare un volume da un backup.

#### <a name="prerequisites"></a>Prerequisiti di

Se non è già stato fatto, creare un gruppo di volume e il volume e quindi eliminare il volume. Per impostazione predefinita, StorSimple Snapshot Manager il backup di un volume prima di consentire di eliminarla. Se il volume viene eliminato involontariamente o se i dati da recuperare per qualsiasi motivo, questa misura possa evitare perdite di dati. 

StorSimple Snapshot Manager viene visualizzato il seguente messaggio durante la creazione di backup prevenzione.

![Messaggio istantanea automatica](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]È possibile eliminare un volume che fa parte di un gruppo di volume. Non è disponibile l'opzione di eliminazione. <br>

#### <a name="to-restore-a-volume"></a>Per ripristinare un volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro di **ambito** , espandere il nodo **Catalogo di Backup** , espandere un gruppo di volume e quindi fare clic su **Cloud istantanee**o **Istantanee locale** . Nel riquadro **risultati** viene visualizzato un elenco copie di backup. 

3. Individuare la copia di backup che si desidera ripristinare, rapida, quindi fare clic su **Ripristina**. 

    ![Ripristinare un catalogo di backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. Nella pagina di conferma, rivedere i dettagli, digitare **Conferma**e quindi fare clic su **OK**. StorSimple Snapshot Manager utilizza la copia di backup per ripristinare il volume. 

    ![Ripristinare un messaggio di conferma](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. È possibile monitorare l'operazione di ripristino mentre è in esecuzione. Nel riquadro di **ambito** , espandere il nodo **processi** e quindi fare clic su **in esecuzione**. I dettagli vengono visualizzate nel riquadro **risultati** . Al termine del processo di ripristino, i dettagli vengono trasferiti all'elenco delle **ultime 24 ore** .

## <a name="clone-a-volume-or-volume-group"></a>Duplicare un volume o un gruppo di volume

Utilizzare la procedura seguente per creare un duplicato (duplicato) di un volume o un gruppo di volume.

#### <a name="to-clone-a-volume-or-volume-group"></a>Per duplicare un volume o un gruppo di volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** , espandere il nodo **Catalogo di Backup** , espandere un gruppo di volume e quindi fare clic su **Cloud istantanee**. Nel riquadro **risultati** viene visualizzato un elenco di backup.

3. Individuare il volume o il gruppo di volume che si desidera duplicare, rapida il volume o il nome del gruppo volume e fare clic su **duplicato**. Verrà visualizzata la finestra di dialogo **Snapshot Cloud duplicato** .

    ![Duplicare un'istantanea del cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Completare la finestra di dialogo **Clona Cloud Snapshot** come indicato di seguito: 

    1. Nella casella di testo **nome** digitare un nome per il volume duplicato. Questo nome verrà visualizzato del nodo **volumi** . 

    2. (Facoltativo) selezionare **l'unità**e quindi selezionare una lettera dall'elenco a discesa. 

    3. (Facoltativo) selezionare **Cartella (NTFS)**, digitare il percorso della cartella o fare clic su Sfoglia e selezionare un percorso per la cartella. 

    4. Fare clic su **Crea**.

5. Al termine del processo di duplicazione, è necessario inizializzare il volume duplicato. Avviare Server Manager e quindi si avvia Gestione disco. Per informazioni dettagliate, vedere [collegare volumi](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Dopo l'inizializzazione, il volume verrà elencato sotto il nodo **volumi** nel riquadro di **ambito** . Se non è visibile il volume elencato, aggiornare l'elenco di volumi (rapida del nodo **volumi** e quindi fare clic su **Aggiorna**).

## <a name="delete-a-backup"></a>Eliminare una copia di backup

Utilizzare la procedura seguente per eliminare uno snapshot dal catalogo di backup. 

>[AZURE.NOTE]Se si elimina uno snapshot, dati di backup associati all'istantanea. Tuttavia, il processo di pulizia dei dati dal cloud può richiedere alcuni minuti.<br>
 
#### <a name="to-delete-a-backup"></a>Per eliminare una copia di backup

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** , espandere il nodo **Catalogo di Backup** , espandere un gruppo di volume e quindi fare clic su **Cloud istantanee**o **Istantanee locale** . Nel riquadro **risultati** viene visualizzato un elenco di snapshot. 

3. Pulsante destro del mouse snapshot che si desidera eliminare e quindi fare clic su **Elimina**.

4. Quando viene visualizzato il messaggio di conferma, fare clic su **OK**. 

## <a name="recover-a-file"></a>Ripristinare un file

Se un file viene eliminato accidentalmente da un volume, è possibile ripristinare i file di recupero uno snapshot che pre-date l'eliminazione, usando lo snapshot per creare un duplicato del volume e quindi copiare il file da volume duplicato per il volume originale.

#### <a name="prerequisites"></a>Prerequisiti di

Prima di iniziare, accertarsi di avere una copia di backup corrente del gruppo volume. Eliminare un file memorizzato in uno dei volumi di tale gruppo volume. Infine, utilizzare la procedura seguente per ripristinare i file eliminato dal backup. 

#### <a name="to-recover-a-deleted-file"></a>Per ripristinare un file eliminato

1. Fare clic sull'icona di gestione di Snapshot StorSimple sul desktop. Viene visualizzata la finestra di console StorSimple Snapshot Manager. 

2. Nel riquadro di **ambito** , espandere il nodo **Catalogo di Backup** e passare a uno snapshot contenente il file eliminato. In genere, è necessario selezionare uno snapshot creato prima dell'eliminazione. 

3. Trovare il volume che si desidera clonare, pulsante destro del mouse, fare clic su **duplicato**. Verrà visualizzata la finestra di dialogo **Snapshot Cloud duplicato** .

    ![Duplicare uno snapshot cloud](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Completare la finestra di dialogo **Clona Cloud Snapshot** come indicato di seguito: 

   1. Nella casella di testo **nome** digitare un nome per il volume duplicato. Questo nome verrà visualizzato del nodo **volumi** . 

   2. (Facoltativo) Selezionare **l'unità**e quindi selezionare una lettera dall'elenco a discesa. 

   3. (Facoltativo) Selezionare **Cartella (NTFS)**, digitare il percorso della cartella o fare clic su **Sfoglia** e selezionare un percorso per la cartella. 

   4. Fare clic su **Crea**. 

5. Al termine del processo di duplicazione, è necessario inizializzare il volume duplicato. Avviare Server Manager e quindi si avvia Gestione disco. Per informazioni dettagliate, vedere [collegare volumi](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Dopo l'inizializzazione, il volume verrà elencato sotto il nodo **volumi** nel riquadro di **ambito** . 

    Se non è visibile il volume elencato, aggiornare l'elenco di volumi (rapida del nodo **volumi** e quindi fare clic su **Aggiorna**).

6. Aprire la cartella NTFS contenente il volume duplicato, espandere il nodo **volumi** e quindi aprire il volume duplicato. Individuare il file che si desidera ripristinare e copiare il volume principale.

7. Dopo avere ripristinato il file, è possibile eliminare la cartella NTFS che contiene il volume duplicato.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Ripristinare il database StorSimple Snapshot Manager

Regolarmente eseguire il backup del database StorSimple Snapshot Manager nel computer host. Se si verifica un danno o il computer host non riesce per qualsiasi motivo, è quindi possibile ripristinarlo dal backup. Creazione di backup del database è un processo manuale.

#### <a name="to-back-up-and-restore-the-database"></a>Eseguire il backup e ripristino del database

1. Arrestare il servizio di gestione StorSimple Microsoft:

    1. Avviare Server Manager.

    2. Selezionare **servizi**nel dashboard di Server Manager dal menu **Strumenti** .

    3. Nella finestra **servizi** selezionare il **Servizio di gestione StorSimple Microsoft**.

    4. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **arrestare il servizio**.

2. Nel computer host, passare alla C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData è una cartella nascosta.
 
3. Individuare il file XML di catalogo, copiare il file e archiviare la copia in un percorso sicuro o nel cloud. Se l'host non riesce, è possibile utilizzare il file di backup per recuperare i criteri di backup creato in StorSimple Snapshot Manager.

    ![File di backup catalogo StorSimple Azure](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Riavviare il servizio di gestione StorSimple Microsoft: 

    1. Selezionare **servizi**nel dashboard di Server Manager dal menu **Strumenti** .
    
    2. Nella finestra **servizi** selezionare il **Servizio di gestione StorSimple Microsoft**.

    3. Nel riquadro destro, sotto **Il servizio di gestione di Microsoft StorSimple**, fare clic su **riavviare il servizio**.

5. Nel computer host, passare alla C:\ProgramData\Microsoft\StorSimple\BACatalog. 

6. Eliminare il file XML di catalogo e sostituirlo con la versione di backup creato. 

7. Fare clic sull'icona di gestione di Snapshot StorSimple desktop per avviare Gestione Snapshot StorSimple. 

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni [sull'utilizzo di gestione di Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
- Ulteriori informazioni sui [flussi di lavoro e StorSimple Snapshot Gestione attività](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).
