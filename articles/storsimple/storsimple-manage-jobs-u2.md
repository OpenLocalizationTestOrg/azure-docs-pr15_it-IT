<properties 
   pageTitle="Visualizzare e gestire i processi di StorSimple | Microsoft Azure"
   description="Vengono illustrati la pagina di processi del servizio di gestione StorSimple e come usarlo per tenere traccia dei processi di backup recenti, correnti e pianificati."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>Utilizzare il servizio di gestione StorSimple per visualizzare e gestire i processi di StorSimple (aggiornamento 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Panoramica

La pagina **dei processi** fornisce un singolo portale centrale per la visualizzazione e la gestione dei processi avviati nei dispositivi connessi al servizio di gestione StorSimple. È possibile visualizzare i processi programmati, in esecuzione, completati, annullati e non riusciti per più dispositivi. Risultati sono visualizzati in formato tabulare. 

![Pagina dei processi](./media/storsimple-manage-jobs-u2/jobs.png)

È possibile trovare rapidamente i processi desiderati applicando un filtro basato sui campi, ad esempio:

- **Stato** : processi possono essere in esecuzione, completato, annullato, non riuscito, annullamento o completato con errori.
- **Da e a** – processi possono essere filtrati in base l'intervallo di data e ora.
- **Tipo** : il tipo di processo può essere backup, backup manuale, Ripristina, duplicato, failover dispositivo, creare volume localmente bloccata, modificare il volume, aggiornare, supporta pacchetto o il provisioning di dispositivo virtuale.

- **Dispositivi** -processi vengono avviati in un determinato dispositivo connesso al servizio.
Processi filtrati quindi vengano inseriti in base ad attributi seguenti:

    - **Tipo** : backup, backup manuale, Ripristina, duplicato, failover dispositivo, creare volume localmente bloccata, modificare il volume, aggiornare, supporta pacchetto o il provisioning di dispositivo virtuale.

    - **Stato** : in esecuzione, completato, annullato, non riuscito, annullamento o completato con errori.

    - **Entità** : I processi possono essere associati a un volume, un criterio di backup o un dispositivo. Ad esempio, un processo duplicato è associato a un volume, mentre un processo di backup pianificato è associato a un criterio di backup. Un processo di dispositivo viene creato un simbolo di emergenza (DR) o un'operazione di ripristino.

    - **Dispositivo** : il nome del dispositivo in cui è stato avviato il processo.

    - **È stata avviata alle** : ora di inizio del processo.

    - **Lo stato di avanzamento** – la percentuale di completamento di un processo in esecuzione. Per un'operazione completata, deve sempre essere 100%.

L'elenco dei processi viene aggiornato ogni 30 secondi.

In questa pagina, è possibile eseguire operazioni relative ai processi seguenti:

- Visualizzare i dettagli del processo

- Annullare un processo

## <a name="view-job-details"></a>Visualizzare i dettagli del processo

Per visualizzare i dettagli di un processo, procedere come segue.

#### <a name="to-view-job-details"></a>Per visualizzare i dettagli dei processi

1. Nella pagina **processi** , visualizzare i processi desiderate eseguendo una query con filtri appropriati. È possibile cercare completato, è in esecuzione o annullato processi.

2. Selezionare un processo.

3. Nella parte inferiore della pagina, fare clic su **Dettagli**.

4. Nella finestra di dialogo **Dettagli dei processi di Backup** , è possibile visualizzare lo stato, dettagli, statistiche temporali e le statistiche sui dati.
 
    ![Pagina dei dettagli di processo](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>Annullare un processo

Per annullare un processo in esecuzione, procedere come segue.

>[AZURE.NOTE] Alcuni processi, ad esempio la modifica di un volume per modificare il tipo di volume o espandere un volume degli scambi, non possono essere annullati.

### <a name="to-cancel-a-job"></a>Per annullare un processo

1. Nella pagina **processi** , visualizzare i processi in esecuzione che si desidera annullare eseguendo una query con filtri appropriati.

1. Selezionare il processo.

1. Nella parte inferiore della pagina, fare clic su **Annulla**.

1. Quando viene richiesto di confermare l'operazione, fare clic su **Sì**.

Questo processo viene annullato ora.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire i criteri di backup StorSimple](storsimple-manage-backup-policies.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
