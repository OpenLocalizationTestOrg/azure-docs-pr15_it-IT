<properties 
   pageTitle="Gestire il catalogo di backup StorSimple | Microsoft Azure"
   description="In questo articolo viene spiegato come utilizzare la pagina di catalogo di Backup del servizio di gestione StorSimple di elenco, selezionare ed eliminare i set di backup per un volume."
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Utilizzare il servizio di gestione di StorSimple per gestire il catalogo di backup

## <a name="overview"></a>Panoramica

La pagina di **Catalogo di Backup** del servizio di gestione StorSimple consente di visualizzare tutti i set di backup creato quando vengono eseguite backup manuale o pianificato. È possibile utilizzare questa pagina per elencare tutti i backup di un criterio di backup o un volume degli scambi, selezionare o eliminare i backup o usare una copia di backup per ripristinare o duplicare un volume.

In questa esercitazione viene illustrato come elenco, selezionare ed eliminare un set di backup. Per informazioni su come ripristinare il dispositivo da un backup, passare a [ripristinare il dispositivo da un set di backup](storsimple-restore-from-backup-set.md). Per informazioni su come duplicare un volume, passare a [un volume StorSimple duplicato](storsimple-clone-volume.md).

![Catalogo di backup](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

La pagina **Catalogo di Backup** fornisce una query per limitare il backup imposta la selezione. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

- **Dispositivo** : il dispositivo in cui è stato creato il set di backup.

- **Criteri di backup o al Volume** -i criteri di backup o al volume associato a questo set di backup.

- **Da e a** : l'intervallo di data e ora quando è stato creato il set di backup.

I set di backup filtrati vengono quindi inseriti in base agli attributi seguenti:

- **Nome** : il nome dei criteri di backup volume associato il set di backup.

- **Dimensione** : le dimensioni effettive del set di backup.

- **Data creazione** : la data e l'ora situazioni in cui sono state create le copie di backup. 

- **Tipo** : set di Backup possono essere istantanee locale o nel cloud istantanee. Uno snapshot locale è una copia di backup di tutti i dati di volume archiviati in locale nel dispositivo, mentre uno snapshot cloud si intende il backup dei dati di volume che risiedono nel cloud. Snapshot locale accedere più rapidamente, mentre snapshot cloud ne vengono scelti per il recupero di dati.

- **Autore** : il backup può essere avviato automaticamente da una pianificazione o manualmente da un utente. È possibile utilizzare un criterio di backup per pianificare l'esecuzione di backup. In alternativa, è possibile utilizzare l'opzione di **eseguire backup** per eseguire un backup manuale.

## <a name="list-backup-sets-for-a-volume"></a>Set di backup di elenco per un volume
 
Completare la procedura seguente per elencare tutti i backup di un volume.

#### <a name="to-list-backup-sets"></a>Per set di backup di elenco

1. Nella pagina servizio StorSimple Manager, fare clic sulla scheda **catalogo di Backup** .

2. Filtrare le selezioni come indicato di seguito:

    1. Selezionare il dispositivo appropriato.

    2. Nell'elenco a discesa scegliere un volume per visualizzare i backup corrispondente.

    3. Specificare l'intervallo di tempo.

    4. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Per eseguire la query.
 
    I backup associati il volume selezionato devono essere visualizzati nell'elenco dei set di backup.

## <a name="select-a-backup-set"></a>Selezionare un set di backup

Completare i passaggi seguenti per selezionare un set di backup per un volume o criteri di backup.

#### <a name="to-select-a-backup-set"></a>Per selezionare un set di backup

1. Nella pagina servizio StorSimple Manager, fare clic sulla scheda **catalogo di Backup** .

2. Filtrare le selezioni come indicato di seguito:

    1. Selezionare il dispositivo appropriato.

    2. Nell'elenco a discesa scegliere i criteri di volume o copia di backup per la copia di backup che si desidera selezionare.

    3. Specificare l'intervallo di tempo.

    4. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Per eseguire la query.

    I backup associato il volume selezionato o criteri di backup devono essere visualizzati nell'elenco dei set di backup.

3. Selezionare ed espandere un set di backup. Nella parte inferiore della pagina vengono visualizzate le opzioni di **ripristinare** ed **eliminare** . È possibile eseguire una di queste azioni nel set di backup che è stata selezionata.

## <a name="delete-a-backup-set"></a>Eliminare un set di backup

Eliminare una copia di backup quando non si desidera mantenere i dati associati. Per eliminare un set di backup, procedere come segue.

#### <a name="to-delete-a-backup-set"></a>Per eliminare un set di backup

1. Nella pagina servizio StorSimple Manager, fare clic sulla **scheda catalogo di Backup**.

2. Filtrare le selezioni come indicato di seguito:

    1. Selezionare il dispositivo appropriato.

    2. Nell'elenco a discesa scegliere i criteri di volume o copia di backup per la copia di backup che si desidera selezionare.

    3. Specificare l'intervallo di tempo.

    4. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) Per eseguire la query.

    I backup associato il volume selezionato o criteri di backup devono essere visualizzati nell'elenco dei set di backup.

3. Selezionare ed espandere un set di backup. Nella parte inferiore della pagina vengono visualizzate le opzioni di **ripristinare** ed **eliminare** . Fare clic su **Elimina**.

4. Verrà avvertiti l'eliminazione è in corso e quando viene eseguito correttamente al termine. Dopo l'eliminazione, aggiornare la query in questa pagina. Il set di backup eliminato non verrà più visualizzato nell'elenco dei set di backup.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare il catalogo di backup per ripristinare il dispositivo da un set di backup](storsimple-restore-from-backup-set.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
