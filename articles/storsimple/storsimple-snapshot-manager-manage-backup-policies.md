<properties 
   pageTitle="Criteri di backup StorSimple Snapshot Manager | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare snap-in MMC StorSimple Snapshot Manager per creare e gestire i criteri di backup che controllano pianificati."
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
   ms.date="05/12/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Gestione delle istantanea StorSimple consente di creare e gestire i criteri di backup

## <a name="overview"></a>Panoramica

Criteri di backup crea una pianificazione per il backup dei dati del volume locale o nel cloud. Quando si crea un criterio di backup, è inoltre possibile specificare criteri di conservazione. (È possibile mantenere un massimo di 64 istantanee). Per ulteriori informazioni sui criteri di backup, vedere [tipi di Backup](storsimple-what-is-snapshot-manager.md#backup-type) in [serie 8000 StorSimple: una soluzione di cloud ibrida](storsimple-overview.md).

In questa esercitazione viene illustrato come:

- Creare un criterio di backup 
- Modificare un criterio di backup 
- Eliminare un criterio di backup 

## <a name="create-a-backup-policy"></a>Creare un criterio di backup

Utilizzare la procedura seguente per creare un nuovo criterio di backup.

#### <a name="to-create-a-backup-policy"></a>Per creare un criterio di backup

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** rapida **Criteri di Backup**e fare clic su **Crea criterio di Backup**.

    ![Creare un criterio di backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Viene visualizzata la finestra di dialogo **Crea un criterio** . 

    ![Creazione di un criterio - scheda Generale](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. Nella scheda **Generale** , completare le informazioni seguenti:

   1. Nella casella di testo **nome** digitare un nome per il criterio.

   2. Nella casella di testo **gruppo Volume** digitare il nome del gruppo volume associato al criterio.

   3. Selezionare **Snapshot locale** o **Snapshot Cloud**.

   4. Selezionare il numero di snapshot per la conservazione. Se si seleziona **tutto**, 64 snapshot saranno mantenuti (max). 

4. Fare clic sulla scheda **programmazione** .

    ![Creazione di un criterio - scheda programmazione](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. Nella scheda **programmazione** , completare le informazioni seguenti: 

   1. Selezionare la casella di controllo **Abilita** per pianificare il backup successivo.

   2. In **Impostazioni**selezionare **una sola volta**, **giornaliera**, **settimanale**o **mensile**. 

   3. Nella casella di testo **Start** fare clic sull'icona calendario e selezionare una data di inizio.

   4. In **Impostazioni avanzate**, è possibile impostare le pianificazioni di ripetizione facoltative e una data di fine.

   5. Fare clic su **OK**.

Dopo aver creato un criterio di backup, nel riquadro **risultati** vengono visualizzate le informazioni seguenti:

- **Nome** : il nome del criterio backup.

- **Tipo** : snapshot locale o uno snapshot cloud.

- **Gruppo volume** : gruppo volume associato al criterio.

- **Criteri di conservazione** : il numero di snapshot conservato. il valore massimo è 64.

- **Data creazione** : la data in cui è stato creato il criterio.

- **Enabled** – se il criterio è attiva: **True** indica che è in effetti; **Falso** indica che non sia attivo. 

## <a name="edit-a-backup-policy"></a>Modificare un criterio di backup

Utilizzare la procedura seguente per modificare un criterio di backup esistente.

#### <a name="to-edit-a-backup-policy"></a>Per modificare un criterio di backup

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro di **ambito** , fare clic sul nodo **Criteri di Backup** . Tutti i criteri di backup vengono visualizzate nel riquadro **risultati** . 

3. Mouse criterio che si desidera modificare e quindi fare clic su **Modifica**. 

    ![Modificare un criterio di backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png) 

4. Quando viene visualizzata la finestra **Crea un criterio** , apportare le modifiche desiderate e quindi fare clic su **OK**. 

## <a name="delete-a-backup-policy"></a>Eliminare un criterio di backup

Utilizzare la procedura seguente per eliminare un criterio di backup.

#### <a name="to-delete-a-backup-policy"></a>Per eliminare un criterio di backup

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro di **ambito** , fare clic sul nodo **Criteri di Backup** . Tutti i criteri di backup vengono visualizzate nel riquadro **risultati** . 

3. Pulsante destro del mouse criteri di backup che si desidera eliminare e quindi fare clic su **Elimina**.

4. Quando viene visualizzato il messaggio di conferma, fare clic su **Sì**.

    ![Eliminare conferma di criteri di backup](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare Gestione Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
- Informazioni su come [utilizzare Gestione Snapshot StorSimple per visualizzare e gestire processi di backup](storsimple-snapshot-manager-manage-backup-jobs.md).
