<properties 
   pageTitle="Gruppi di volume StorSimple Snapshot Manager | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare snap-in MMC StorSimple Snapshot Manager per creare e gestire i gruppi di volume."
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

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Gestione delle istantanea StorSimple consente di creare e gestire gruppi di volume

## <a name="overview"></a>Panoramica

È possibile utilizzare il nodo **Volume gruppi** nel riquadro **dell'ambito** per assegnare volumi ai gruppi di volume, visualizzare le informazioni relative a un gruppo di volume, pianificare l'esecuzione di backup e modificare gruppi volume. 

I gruppi di volume sono pool di volumi correlati utilizzati per garantire la coerenza con l'applicazione di backup. Per ulteriori informazioni, vedere [volumi e gruppi volume](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [l'integrazione con Windows Volume ombreggiatura copia servizio](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT] 
>
> * Tutti i volumi in un gruppo di volume devono provenire da un provider di servizi cloud singola.
> 
> * Quando si configurano gruppi volume, non combinare volumi condivisi cluster (CSVs) e non CSVs nello stesso gruppo volume. Gestione Snapshot StorSimple non supporta una combinazione di CSVs e non CSVs nello stesso snapshot.
 
![Nodo gruppi volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nodo StorSimple Snapshot Manager Volume gruppi** 

In questa esercitazione illustra come è possibile utilizzare Gestione Snapshot StorSimple per:

- Visualizzare le informazioni sui gruppi volume 
- Creare un gruppo di volume
- Eseguire il backup di un gruppo di volume
- Modificare un gruppo di volume
- Eliminare un gruppo di volume

Tutte le azioni eseguite sono disponibili anche nel riquadro **Azioni** .
 
## <a name="view-volume-groups"></a>Visualizza gruppi di volume

Se si sceglie il nodo **Gruppi Volume** , il riquadro **risultati** Mostra le seguenti informazioni relative a ogni gruppo di volume, a seconda le selezioni effettuate colonna. (Le colonne nel riquadro **risultati** sono configurabili. Pulsante destro del mouse il nodo **volumi** , selezionare **Visualizza**e quindi selezionare **Aggiungi/Rimuovi colonne**.)

Colonna dei risultati | Descrizione 
:--------------|:------------ 
Nome           | Nella colonna **nome** contiene il nome del gruppo volume.
Applicazione    | La colonna **applicazioni** Mostra il numero di autori VSS attualmente installata e l'esecuzione dell'host di Windows.
Selezionata       | La colonna **selezionata** Mostra il numero di volumi contenuti nel gruppo volume. Uguale a zero (0) indica che nessuna applicazione è associata a volumi nel gruppo volume.
Importazione       | La colonna **importati** Mostra il numero di volumi importati. Se è impostato su **True**, questa colonna indica che un gruppo di volume importato dal portale di classica Azure e non è stato creato in StorSimple Snapshot Manager.
 
>[AZURE.NOTE] Gruppi di volume StorSimple Snapshot Manager vengono visualizzati anche nella scheda **Criteri di Backup** nel portale di classica Azure.
 
## <a name="create-a-volume-group"></a>Creare un gruppo di volume

Utilizzare la procedura seguente per creare un gruppo di volume.

#### <a name="to-create-a-volume-group"></a>Per creare un gruppo di volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro **ambito** destro **Volume gruppi**e quindi fare clic su **Crea gruppo Volume**. 

    ![Creare il gruppo di volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Viene visualizzata la finestra di dialogo **Crea un gruppo di volume** . 

    ![Creare una conversazione di gruppo volume](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  Immettere le informazioni seguenti: 

    1. Nella casella **nome** digitare un nome univoco per il nuovo gruppo di volume. 

    2. Nella finestra di **applicazioni** , selezionare applicazioni associate ai volumi verrà aggiunto al gruppo volume. 

        Le **applicazioni** riportato solo le applicazioni che utilizzano volumi StorSimple e sono writer VSS abilitate per la loro. Un writer VSS è disponibile solo se tutti i volumi che riconosce il writer sono volumi StorSimple. Se la casella di applicazioni è vuota, applicazioni che utilizzano volumi StorSimple Azure e sono supportati writer VSS non vengono installate. (Al momento Azure StorSimple supporta Microsoft Exchange e SQL Server). Per ulteriori informazioni sui processi di scrittura VSS, notare [un'integrazione con il servizio di copia ombreggiatura Volume di Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Se si seleziona un'applicazione, vengono selezionati automaticamente tutti i volumi associati. Viceversa, se si selezionano volumi associati a un'applicazione specifica, l'applicazione automaticamente è selezionata nella finestra di **applicazioni** . 

    3. Nella casella **volumi** selezionare volumi StorSimple per aggiungere al gruppo volume. 

      - È possibile includere volumi con uno o più partizioni. (Più volumi partizione possono essere dischi dinamici o dischi di base con più partizioni.) Un volume che contiene più partizioni viene considerato come un'unica. Di conseguenza, se si aggiunge solo una delle partizioni a un gruppo di volume, tutte le altre partizioni vengono aggiunti automaticamente al gruppo volume nello stesso momento. Dopo aver aggiunto un volume partizione più a un gruppo di volume, il volume partizione più continua a essere considerato come un'unica.

      - È possibile creare gruppi volume vuoto assegnando non volumi ad essi. 

      - Non combinare volumi condivisi cluster (CSVs) e non CSVs nello stesso gruppo volume. Gestione Snapshot StorSimple non supporta una combinazione di volumi CSV e non CSV nello stesso snapshot. 

4. Fare clic su **OK** per salvare il gruppo volume.

## <a name="back-up-a-volume-group"></a>Eseguire il backup di un gruppo di volume

Utilizzare la procedura seguente per eseguire il backup di un gruppo di volume.

#### <a name="to-back-up-a-volume-group"></a>Eseguire il backup di un gruppo di volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** espandere il nodo **Volume gruppi** e fare clic su **Backup richiedere**destro del mouse sul nome di un gruppo di volume. 

    ![Eseguire il backup gruppo volume immediatamente](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. Nella finestra di dialogo **Eseguire Backup** selezionare **Snapshot locale** o **Uno Snapshot Cloud**e quindi fare clic su **Crea**. 

    ![Finestra di dialogo backup eseguire](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. Per confermare l'esecuzione di backup, espandere il nodo **processi** e quindi fare clic su **in esecuzione**. Il backup dovrebbe essere elencato.

5. Per visualizzare istantanea completata, espandere il nodo **Catalogo di Backup** , espandere il nome del gruppo volume e quindi fare clic su **Snapshot locale** o **Uno Snapshot Cloud**. Se è stata completata, verrà visualizzato il backup. 

## <a name="edit-a-volume-group"></a>Modificare un gruppo di volume

Utilizzare la procedura seguente per modificare un gruppo di volume.

#### <a name="to-edit-a-volume-group"></a>Modificare un gruppo di volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** espandere il nodo **Gruppi Volume** destro del mouse sul nome di un gruppo di volume e quindi fare clic su **Modifica**. 

3. Viene visualizzata la finestra di dialogo **Crea un gruppo di volume **. È possibile modificare le voci di **nome**, **applicazioni**e **volumi** . 

4. Fare clic su **OK** per salvare le modifiche.

## <a name="delete-a-volume-group"></a>Eliminare un gruppo di volume

Utilizzare la procedura seguente per eliminare un gruppo di volume. 

>[AZURE.WARNING] Viene eliminato anche tutti i backup associati al gruppo volume.

#### <a name="to-delete-a-volume-group"></a>Per eliminare un gruppo di volume

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro **ambito** espandere il nodo **Gruppi Volume** destro del mouse sul nome di un gruppo di volume e quindi fare clic su **Elimina**. 

3. Viene visualizzata la finestra di dialogo **Elimina gruppo Volume** . Digitare **Conferma** nella casella di testo e quindi fare clic su **OK**. 

    Gruppo volume eliminato torni dall'elenco nel riquadro dei **risultati** e tutti i backup che sono associati a tale gruppo volume vengono eliminati dal catalogo di backup.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare Gestione Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
- Informazioni su come [utilizzare Gestione Snapshot StorSimple per creare e gestire i criteri di backup](storsimple-snapshot-manager-manage-backup-policies.md).
