<properties 
   pageTitle="Ripristinare un volume StorSimple da un backup | Microsoft Azure"
   description="In questo articolo viene spiegato come utilizzare la pagina di catalogo di Backup del servizio di gestione di StorSimple per ripristinare un volume StorSimple da un set di backup."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Ripristinare un volume StorSimple da un set di backup

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Panoramica

La pagina **Catalogo di Backup** Visualizza tutti i set di backup creato quando vengono eseguite backup manuale o automatico. È possibile utilizzare questa pagina per elencare tutti i backup di un criterio di backup o un volume degli scambi, selezionare o eliminare i backup o usare una copia di backup per ripristinare o duplicare un volume.

 ![Eseguire il backup pagina catalogo](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

In questa esercitazione viene illustrato come utilizzare la pagina **Catalogo di Backup** per ripristinare un volume sul dispositivo da un set di backup.

## <a name="how-to-use-the-backup-catalog"></a>Come usare il catalogo di backup 

La pagina **Catalogo di Backup** fornisce una query che consente di limitare il backup set di selezione. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

- **Dispositivo** : il dispositivo in cui è stato creato il set di backup.
- **Criteri di backup** o al **volume** -i criteri di backup o al volume associato a questo set di backup.
- **Da** e **a** : l'intervallo di data e ora quando è stato creato il set di backup.

I set di backup filtrati vengono quindi inseriti in base agli attributi seguenti:

- **Nome** : il nome dei criteri di backup volume associato il set di backup.
- **Dimensione** : le dimensioni effettive del set di backup.
- **Creazione** : la data e l'ora situazioni in cui sono state create le copie di backup. 
- **Tipo** : set di Backup possono essere istantanee locale o nel cloud istantanee. Uno snapshot locale è una copia di backup di tutti i dati di volume archiviati in locale nel dispositivo, mentre uno snapshot cloud si intende il backup dei dati di volume che risiedono nel cloud. Snapshot locale accedere più rapidamente, mentre snapshot cloud ne vengono scelti per il recupero di dati.
- **Autore** : il backup può essere avviato automaticamente in base a una programmazione o manualmente da un utente. (È possibile utilizzare un criterio di backup per pianificare l'esecuzione di backup. In alternativa, è possibile utilizzare l'opzione di **eseguire backup** per eseguire un backup interattivi.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Come ripristinare il volume StorSimple da una copia di backup

È possibile utilizzare la pagina **Catalogo di Backup** per ripristinare il volume StorSimple da un backup specifico. 

> [AZURE.WARNING] Il ripristino da un backup sostituirà i volumi esistenti dal backup. Questa operazione può causare la perdita dei dati che è stato scritto dopo l'esecuzione del backup.

Prima di avviare un ripristino su un volume, assicurarsi che il volume sia offline. Sarà necessario eseguire il volume non in linea nell'host prima e quindi il dispositivo. Seguire i passaggi descritti in [eseguire un volume non in linea](storsimple-manage-volumes.md#take-a-volume-offline). Eseguire la procedura seguente per ripristinare un volume da un set di backup.

### <a name="to-restore-from-a-backup-set"></a>Per ripristinare da un set di backup

1. Nella pagina servizio StorSimple Manager, fare clic sulla scheda **catalogo di Backup** .

    ![Catalogo di backup](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Selezionare una copia di backup impostare come segue:
  1. Selezionare il dispositivo appropriato.
  2. Nell'elenco a discesa scegliere i criteri di volume o copia di backup per la copia di backup che si desidera selezionare.
  3. Specificare l'intervallo di tempo.
  4. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) Per eseguire la query.
 
    I backup associato il volume selezionato o criteri di backup devono essere visualizzati nell'elenco dei set di backup.

3. Espandere il set per visualizzare i volumi associati di backup. Questi volumi disconnettere nell'host e un dispositivo per poter ripristinare. Seguire i passaggi descritti in [eseguire un volume offline](storsimple-manage-volumes.md#take-a-volume-offline).

    >  [AZURE.IMPORTANT] Assicurarsi di avere eseguito i volumi nell'host prima di tutto, prima di portare offline i volumi sul dispositivo. Se non si effettua i volumi non in linea nell'host, potrebbero potenzialmente causare il danneggiamento dei dati.

4. Selezionare un set di backup. Fare clic su **Ripristina** nella parte inferiore della pagina.

6. Verrà richiesto di confermare l'operazione. 

    ![Pagina di conferma](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Esaminare le informazioni di ripristino e fare clic sull'icona di controllo ![selezionare icona](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Verrà avviata un processo di ripristino che è possibile visualizzare, accedere alla pagina **dei processi** . 

8. Al termine del ripristino, è possibile verificare che il contenuto dei volumi viene sostituito da volumi dal backup.

![Video disponibile](./media/storsimple-restore-from-backup-set/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile utilizzare la copia e ripristinare le funzionalità di StorSimple per recuperare i file eliminati, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire StorSimple volumi](storsimple-manage-volumes.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
