<properties 
   pageTitle="Ripristinare un volume StorSimple da un backup | Microsoft Azure"
   description="In questo articolo viene spiegato come utilizzare la pagina di catalogo di Backup del servizio di gestione di StorSimple per ripristinare un volume StorSimple da un set di backup."
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

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Ripristinare un volume StorSimple da un set di backup (aggiornamento 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Panoramica

La pagina **Catalogo di Backup** Visualizza tutti i set di backup creato quando vengono eseguite backup manuale o automatico. È possibile utilizzare questa pagina per elencare tutti i backup di un criterio di backup o un volume degli scambi, selezionare o eliminare i backup o usare una copia di backup per ripristinare o duplicare un volume.

 ![Eseguire il backup pagina catalogo](./media/storsimple-restore-from-backup-set-u2/restore.png)

In questa esercitazione viene illustrato come utilizzare la pagina **Catalogo di Backup** per ripristinare il dispositivo da un set di backup.

È possibile ripristinare un volume da locale o backup cloud. In entrambi i casi, l'operazione di ripristino porta in linea il volume istantaneamente mentre dati vengono scaricati in background. 

Prima di avviare un'operazione di ripristino, deve essere presente quanto segue:

- **Che è necessario eseguire il volume non in linea** : Take offline il volume sia l'host che il dispositivo prima di avviare l'operazione di ripristino. Anche se l'operazione di ripristino conferisce automaticamente il volume online nel dispositivo, è necessario riportare il dispositivo in linea manualmente nell'host. È possibile portare online il volume nell'host non appena il volume sia online nel dispositivo. (Non è necessario attendere fino al completamento dell'operazione di ripristino.) Per procedure, passare a [scrivere un volume offline](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **Tipo di volume dopo il ripristino** : volumi eliminati verranno ripristinati in base al tipo di snapshot. vale a dire volumi che sono state aggiunte localmente verranno ripristinati come volumi localmente bloccati e volumi che sono state a più livelli verranno ripristinati come volumi a più livelli.

    Per indicare i volumi esistenti, il tipo di utilizzo corrente del volume sostituisce il tipo di archiviata nello snapshot. Ad esempio, se si ripristina un volume da uno snapshot che è stato eseguito quando è stata a più livelli il tipo di volume e tipo di volume a questo punto viene aggiunta in locale (a causa di un'operazione di conversione che è stata eseguita), quindi il volume verrà ripristinato come volume localmente bloccato. Analogamente, se un volume locale bloccato esistente è stato espanso e successivamente ripristinato da un'istantanea precedente quando il volume è stato inferiore, il volume ripristinato manterrà la dimensione espansa corrente.

    Non è possibile convertire un volume da un volume a più livelli in un volume locale bloccato o da un volume locale aggiunto a un volume a più livelli mentre viene ripristinato il volume. Attendere finché non viene completata l'operazione di ripristino e quindi è possibile convertire il volume in un altro tipo. Per informazioni sulla conversione di un volume, passare alla sezione [Modifica il tipo di volume](storsimple-manage-volumes-u2.md#change-the-volume-type). 

- **La dimensione del volume verrà eseguite anche il volume ripristinato** : questo è importante se si desidera ripristinare un volume locale bloccato eliminato (perché completamente il provisioning di volumi localmente bloccati). Verificare che si dispone di spazio sufficiente prima di provare a ripristinare un volume locale aggiunto in precedenza è stato eliminato. 

- **Che non è possibile espandere un volume durante il processo di ripristino** – attendere fino al completamento dell'operazione di ripristino prima di tentare di espandere il volume. Per informazioni su un volume di espansione, passare a [Modifica un volume](storsimple-manage-volumes-u2.md#modify-a-volume).

- **È possibile eseguire un backup mentre si ripristina un volume locale** : per le procedure di passare a [usare il servizio di gestione di StorSimple per gestire i criteri di backup](storsimple-manage-backup-policies.md).

- **Che è possibile annullare un'operazione di ripristino** : se si annulla il processo di ripristino, quindi il volume verrà ripristinata lo stato precedente è stata avviata l'operazione di ripristino. Per procedure, vedere [annullare un processo](storsimple-manage-jobs-u2.md#cancel-a-job).

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

È possibile utilizzare la pagina **Catalogo di Backup** per ripristinare il volume StorSimple da un backup specifico. Tenere presente, tuttavia, tale ripristino di un volume verrà ripristinato il volume lo stato quando è stato eseguito il backup. Tutti i dati che è stata aggiunta dopo l'operazione di backup andranno persa.

> [AZURE.WARNING] Il ripristino da un backup sostituirà i volumi esistenti dal backup. Questa operazione può causare la perdita dei dati che è stato scritto dopo l'esecuzione del backup.

### <a name="to-restore-your-volume"></a>Per ripristinare il volume

1. Nella pagina servizio StorSimple Manager, fare clic sulla scheda **catalogo di Backup** .

    ![Catalogo di backup](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. Selezionare una copia di backup impostare come segue:
  1. Selezionare il dispositivo appropriato.
  2. Nell'elenco a discesa scegliere i criteri di volume o copia di backup per la copia di backup che si desidera selezionare.
  3. Specificare l'intervallo di tempo.
  4. Fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) Per eseguire la query.
 
    I backup associato il volume selezionato o criteri di backup devono essere visualizzati nell'elenco dei set di backup.

3. Espandere il set per visualizzare i volumi associati di backup. Questi volumi disconnettere nell'host e un dispositivo per poter ripristinare. Accedere a volumi nella pagina **Volume contenitori** e seguire la procedura descritta in [eseguire un volume non in linea](storsimple-manage-volumes-u2.md#take-a-volume-offline) da terzi offline.

    > [AZURE.IMPORTANT] Assicurarsi di avere eseguito i volumi nell'host prima di tutto, prima di portare offline i volumi sul dispositivo. Se non si effettua i volumi non in linea nell'host, potrebbero potenzialmente causare il danneggiamento dei dati.

4. Passare alla scheda **Catalogo di Backup** e selezionare un set di backup.

5. Fare clic su **Ripristina** nella parte inferiore della pagina.

6. Verrà richiesto di confermare l'operazione. Esaminare le informazioni di ripristino e quindi selezionare la casella di controllo conferma.

    ![Pagina di conferma](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Fare clic sull'icona di controllo ![selezionare icona](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Verrà avviata un processo di ripristino che è possibile visualizzare, accedere alla pagina **dei processi** . 

8. Al termine del ripristino, è possibile verificare che il contenuto dei volumi viene sostituito da volumi dal backup.

![Video disponibile](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile utilizzare la copia e ripristinare le funzionalità di StorSimple per recuperare i file eliminati, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Se il ripristino non riesce

Se l'operazione di ripristino non riesce per qualsiasi motivo, verrà visualizzato un avviso. In questo caso, aggiornare l'elenco di backup per verificare che il backup è ancora valido. Se il backup è valido e si desidera ripristinare dal cloud, quindi problemi di connettività possono causare il problema. 

Per completare l'operazione di ripristino, eseguire il volume non in linea nell'host e ripetere l'operazione di ripristino. Si noti che le modifiche ai dati che sono state eseguite durante il processo di ripristino volume andranno persi.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire StorSimple volumi](storsimple-manage-volumes-u2.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
