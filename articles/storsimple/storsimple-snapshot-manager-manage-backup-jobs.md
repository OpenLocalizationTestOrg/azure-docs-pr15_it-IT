<properties 
   pageTitle="Processi di backup StorSimple Snapshot Manager | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare il componente aggiuntivo di MMC StorSimple Snapshot Manager per visualizzare e gestire processi di backup pianificati, attualmente in esecuzione e completati."
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


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Gestione delle istantanea StorSimple consente di visualizzare e gestire processi di backup

## <a name="overview"></a>Panoramica

Il nodo **processi** nel riquadro di **ambito** Mostra i **pianificato**, **ultime 24 ore**, **eseguire** le attività di backup che è stata avviata in modo interattivo o da un criterio configurato. 

In questa esercitazione viene illustrato come è possibile utilizzare il nodo **processi** per visualizzare le informazioni sui processi di backup pianificati, recenti e attualmente in esecuzione. (Viene visualizzato l'elenco dei processi e informazioni corrispondenti nel riquadro **risultati** .) È inoltre possibile rapida un processo nell'elenco e visualizzare un menu di scelta rapida in cui sono elencate le azioni disponibili.

## <a name="view-scheduled-jobs"></a>Visualizzare i processi pianificati

Utilizzare la procedura seguente per visualizzare i processi di backup pianificati.

#### <a name="to-view-scheduled-jobs"></a>Per visualizzare i processi pianificati

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple. 

2. Nel riquadro di **ambito** , espandere il nodo **processi** e fare clic su **pianificazione**. Le informazioni seguenti viene visualizzata nel riquadro **risultati** :

    - **Nome** : il nome dello snapshot pianificato

    - **Prossima esecuzione** : la data e l'ora del successivo snapshot pianificato

    - **Ultima esecuzione** : la data e l'ora dell'ultima istantanea pianificata

    >[AZURE.NOTE] Per gli snapshot solo monouso, la **Prossima esecuzione** ed **Eseguire ultima** saranno gli stessi. 
 
    ![Processi di backup pianificati](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. Per eseguire altre azioni su un processo specifico, pulsante destro del mouse sul nome di processo nel riquadro **risultati** e selezionare le opzioni del menu.

## <a name="view-recent-jobs"></a>Visualizza processi recenti

Utilizzare la procedura seguente per visualizzare backup e ripristino processi che sono stati completati nelle ultime 24 ore.

#### <a name="to-view-recent-jobs"></a>Per visualizzare processi recenti

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro di **ambito** , espandere il nodo **processi** e fare clic su **ultime 24 ore**. Il riquadro **risultati** Mostra processi di backup delle ultime 24 ore (per un massimo di 64 processi). Le informazioni seguenti viene visualizzato nel riquadro **dei risultati** , a seconda delle opzioni di **visualizzazione** che specificare:

    - **Nome** : il nome dello snapshot pianificato.
 
    - **Introduzione** : la data e l'ora in cui ha iniziato lo snapshot.

    - **Arrestato** : data e ora quando lo snapshot termine o è stata interrotta.

    - **Tempo trascorso** : la quantità di tempo tra **l'avviato** e **arrestato** volte.

    - **Stato** : lo stato del processo completato recentemente. **Positivo** indica che il backup è stato creato correttamente. **Errore** indica che il processo non è stato eseguito correttamente.

    - **Informazioni** : il motivo dell'errore.

    - **Byte elaborati (MB)** – la quantità di dati dal gruppo volume in cui è stato elaborato (MB). 

    ![Processi in esecuzione nello ultime 24 ore](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. Per eseguire altre azioni su un processo specifico, pulsante destro del mouse sul nome di processo nel riquadro **risultati** e selezionare le opzioni del menu.

    ![Eliminare un processo](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>Visualizzare i processi attualmente in esecuzione

Utilizzare la procedura seguente per Visualizza processi attualmente in esecuzione.

#### <a name="to-view-currently-running-jobs"></a>Per visualizzare i processi attualmente in esecuzione

1. Fare clic sull'icona del desktop per avviare Gestione Snapshot StorSimple.

2. Nel riquadro **ambito** espandere il nodo **processi** e fare clic su **esecuzione**. A seconda delle opzioni di **visualizzazione** che specificare le informazioni seguenti viene visualizzata nel riquadro **risultati** : 

    - **Nome** : il nome dello snapshot pianificato.

    - **Introduzione** : la data e l'ora in cui ha iniziato lo snapshot.

    - **Punto di controllo** : l'azione corrente del backup.

    - **Stato** : la percentuale di completamento.
    
    - **Tempo trascorso** : la quantità di tempo trascorso dall'inizio dei backup. 

    - **Velocità media (MB)** : rapporto tra vendite di byte totali dei dati elaborati a quello del tempo totale per l'elaborazione (MB).

    - **Byte elaborati (MB)** -byte totali dei dati elaborati (in MB).

    - **Byte scritti (MB)** -byte totali di dati scritti (in MB). Include i dati, nonché i metadati e pertanto è in genere maggiore byte elaborati.

    ![Processi attualmente in esecuzione](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Per eseguire altre azioni su un processo specifico, pulsante destro del mouse sul nome di processo nel riquadro **risultati** e selezionare le opzioni del menu.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [utilizzare Gestione Snapshot StorSimple per amministrare la soluzione StorSimple](storsimple-snapshot-manager-admin.md).
- Informazioni su come [usare StorSimple Snapshot Manager per gestire il catalogo di backup](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

