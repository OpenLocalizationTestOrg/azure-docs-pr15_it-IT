<properties 
   pageTitle="Esercitazione backup matrice virtuale StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come eseguire il backup matrice virtuale StorSimple condivisioni e volumi."
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
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>Eseguire il backup della matrice virtuale StorSimple

## <a name="overview"></a>Panoramica 

In questa esercitazione si applica alla versione di Microsoft Azure StorSimple virtuale in forma di matrice (noto anche come dispositivo virtuale locale StorSimple o periferica virtuale StorSimple) in esecuzione marzo 2016 disponibilità generale (GA) o versioni successive.

La matrice virtuale StorSimple è un ibrido cloud lo spazio di archiviazione locale virtuale dispositivo che può essere configurato come un file server o su un server iSCSI. È possibile creare backup, ripristinare da un backup ed eseguire failover dispositivo se il ripristino di emergenza è necessaria. Se configurato come file server, nonché ripristino a livello di elemento. In questa esercitazione viene illustrato come utilizzare il portale classico Azure o sul web StorSimple dell'interfaccia utente per creare un backup manuale e programmato della matrice virtuale StorSimple.


## <a name="back-up-shares-and-volumes"></a>Eseguire il backup condivisioni e volumi

Backup in un momento proteggere, migliorare recupero e ridurre i tempi di ripristino per condivisioni e volumi. È possibile eseguire il backup una condivisione o volume sul dispositivo StorSimple in due modi: **pianificata** o **manuale**. Ognuno dei metodi viene descritto nelle sezioni seguenti.

> [AZURE.NOTE] In questa versione, pianificati vengono creati da un criterio predefinito che viene eseguita ogni giorno in un momento specificato ed esegue il backup tutte le azioni o i volumi sul dispositivo. Non è possibile creare criteri personalizzati per i backup pianificati al momento.

## <a name="change-the-backup-schedule"></a>Modificare la pianificazione di backup

Il dispositivo virtuale StorSimple ha un criterio di backup predefinito che avvia un'ora specifica del giorno (22:30) ed esegue il backup di tutte le azioni o i volumi sul dispositivo una volta al giorno. È possibile modificare l'ora in cui l'avvio del backup, ma la frequenza e la conservazione (che specifica il numero di backup per la conservazione) non può essere modificati. Durante i backup, il dispositivo virtuale intero viene eseguito il backup; di conseguenza, è consigliabile pianificare i backup per le ore.

Nel [portale classica Azure](https://manage.windowsazure.com/) per modificare l'ora di inizio backup predefinito, procedere come segue.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Per modificare l'ora di inizio per il criterio di backup predefinito

1. Passare alla scheda **configurazione** dispositivo.

2. Nella sezione **Backup** , specificare l'ora di inizio per il backup giornaliero.

3. Fare clic su **Salva**.

### <a name="take-a-manual-backup"></a>Eseguire un backup manuale

Oltre a backup pianificati, è possibile scrivere un manuale (su richiesta) backup in qualsiasi momento.

#### <a name="to-create-a-manual-on-demand-backup"></a>Per creare un backup manuale (su richiesta)

1. Passare alla scheda **Azioni** o **volumi** .

2. Nella parte inferiore della pagina, fare clic su **Backup tutti**. Verrà richiesto di verificare che si desidera eseguire il backup ora. Fare clic sull'icona di controllo ![selezionare icona](./media/storsimple-ova-backup/image3.png) per procedere con il backup.

    ![Conferma backup](./media/storsimple-ova-backup/image4.png)

    Verrà visualizzato l'avvio di un processo di backup.

    ![avvio di backup](./media/storsimple-ova-backup/image5.png)

    L'utente verrà informato che il processo è stato creato correttamente.

    ![processo di backup creato](./media/storsimple-ova-backup/image7.png)

3. Per verificare l'avanzamento del processo, fare clic su **Visualizza processo**.

4. Al termine del processo di backup, fare clic sulla scheda **catalogo di Backup** . Verrà visualizzato il backup completato.

    ![Backup completato](./media/storsimple-ova-backup/image8.png)

5. Impostare le opzioni di filtro al dispositivo appropriato, criteri di backup e intervallo di tempo e quindi fare clic sull'icona di controllo ![Selezionare l'icona](./media/storsimple-ova-backup/image3.png).

    Il backup dovrebbero essere visualizzate nell'elenco dei set di backup che viene visualizzato nel catalogo.

## <a name="view-existing-backups"></a>Visualizzare i backup esistenti

Nel portale di classica Azure per visualizzare i backup esistenti, procedere come segue.

#### <a name="to-view-existing-backups"></a>Per visualizzare i backup esistenti

1. Nella pagina servizio StorSimple Manager, fare clic sulla scheda **catalogo di Backup** .

2. Selezionare una copia di backup impostare come segue:

    1. Selezionare il dispositivo.

    2. Nell'elenco a discesa scegliere Condivisione o al volume per il backup che si desidera selezionare.

    3. Specificare l'intervallo di tempo.

    4. Fare clic sull'icona di controllo ![](./media/storsimple-ova-backup/image3.png) per eseguire la query.

    I backup associato alla condivisione selezionata o volume devono essere visualizzati nell'elenco dei set di backup.

![video_icon](./media/storsimple-ova-backup/video_icon.png) **Video disponibile**

Guardare il video per scoprire come è possibile creare condivisioni, condivisioni il backup e ripristinare i dati in una matrice virtuale StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'amministrazione della matrice virtuale StorSimple](storsimple-ova-web-ui-admin.md).
