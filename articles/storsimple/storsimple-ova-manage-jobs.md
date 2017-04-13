<properties 
   pageTitle="Visualizzare e gestire i processi di matrice virtuale StorSimple | Microsoft Azure"
   description="Vengono illustrati la pagina dei processi del servizio di StorSimple Manager e come usarlo per tenere traccia di processi recenti e correnti per la matrice virtuale StorSimple."
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
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Utilizzare il servizio di gestione di StorSimple per visualizzare i processi per la matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

La pagina **dei processi** fornisce un singolo portale centrale per la visualizzazione e la gestione dei processi avviati sulle matrici virtuale (noto anche come locale dispositivi virtuali) connessi al servizio di gestione StorSimple. È possibile visualizzare i processi in esecuzione, completati e non riusciti per più dispositivi virtuali. Risultati sono visualizzati in formato tabulare. 

![Pagina dei processi](./media/storsimple-ova-manage-jobs/ovajobs1.png)

È possibile trovare rapidamente i processi desiderati applicando un filtro basato sui campi, ad esempio:

- **Stato** : È possibile cercare tutti, i processi in esecuzione, completati o non riusciti.
- **Da e a** – processi possono essere filtrati in base l'intervallo di data e ora.
- **Tipo** : il tipo di processo possa essere tutti, ripristino, failover, scaricare gli aggiornamenti o installare gli aggiornamenti.
- **Dispositivi** -processi vengono avviati in un dispositivo specifico connesso al servizio. Processi filtrati quindi vengano inseriti in base ad attributi seguenti:

    - **Tipo** : il tipo di processo possa essere tutti, ripristino, failover, scaricare gli aggiornamenti o installare gli aggiornamenti.

    - **Stato** : consente di processi da tutti, in esecuzione, completati o non è riusciti.

    - **Entità** -i processi possono essere associati a un volume, condivisione o dispositivo. 

    - **Dispositivo** : il nome del dispositivo in cui è stato avviato il processo.

    - **È stata avviata alle** : ora di inizio del processo.

    - **Lo stato di avanzamento** – la percentuale di completamento di un processo in esecuzione. Per un'operazione completata, deve sempre essere 100%.

L'elenco dei processi viene aggiornato ogni 30 secondi.

## <a name="view-job-details"></a>Visualizzare i dettagli del processo

Per visualizzare i dettagli di un processo, procedere come segue.

#### <a name="to-view-job-details"></a>Per visualizzare i dettagli dei processi

1. Nella pagina **processi** , visualizzare i processi desiderate eseguendo una query con filtri appropriati. È possibile cercare processi completati o parziale.

2. Selezionare un processo dall'elenco tabulare di processi.

3. Nella parte inferiore della pagina, fare clic su **Dettagli**.

4. Nella finestra di dialogo **Dettagli** per visualizzare lo stato, dettagli e le statistiche ora. L'illustrazione seguente mostra un esempio della finestra di dialogo **Dettagli dei processi di Backup** .
 
    ![Pagina dei dettagli di processo](./media/storsimple-ova-manage-jobs/ovajobs2.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Errori di processo quando la macchina virtuale viene sospesa in hypervisor

Quando è in un processo dello stato di avanzamento la matrice virtuale StorSimple e il dispositivo (macchina virtuale effettuato il provisioning in hypervisor) è sospeso per più di 15 minuti, il processo avrà esito negativo. Questo a causa del tempo di matrice virtuale StorSimple viene sincronizzato con l'ora di Microsoft Azure. Nella schermata seguente è illustrato un esempio di un errore di processo di ripristino.

![Ripristinare un errore del processo](./media/storsimple-ova-manage-jobs/restorejobfailure.png)

Questi errori vengono applicate a processi di backup, ripristino, aggiornamento e failover. Se la macchina virtuale viene effettuato il provisioning in Hyper-V, il computer verrà infine sincronizzazione con l'hypervisor. Una volta in tal caso, è possibile riavviare il processo. 

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come utilizzare l'interfaccia utente per amministrare la matrice virtuale StorSimple web locale](storsimple-ova-web-ui-admin.md).
