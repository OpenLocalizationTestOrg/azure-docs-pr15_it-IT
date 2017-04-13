<properties
   pageTitle="Failover dispositivo e ripristino di emergenza della matrice virtuale StorSimple"
   description="Ulteriori informazioni su come failover la matrice virtuale StorSimple."
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
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Failover dispositivo e ripristino di emergenza della matrice virtuale StorSimple


## <a name="overview"></a>Panoramica

Questo articolo descrive il ripristino di emergenza per la matrice di virtuali di Microsoft Azure StorSimple (noto anche come il StorSimple locale virtuale dispositivo) inclusi i passaggi dettagliati necessari venga reindirizzato a un altro dispositivo virtuale in caso di emergenza. Caso di errore consente di eseguire la migrazione dei dati da un dispositivo di *origine* nel centro dati a un altro dispositivo di *destinazione* che si trova nello stesso documento o una posizione geografica diversa. Failover dispositivo riguarda l'intera unità. Durante il failover, per l'origine dati cloud diventa la proprietà che il dispositivo di destinazione.

Dispositivo failover viene gestito tramite la funzionalità di ripristino () di emergenza e viene avviato dalla pagina **dispositivi** . Questa pagina raccoglie tutti i dispositivi di StorSimple connessi al servizio di gestione StorSimple. Per ogni dispositivo, vengono visualizzati il nome descrittivo, stato, capacità di provisioning e massima, tipo e modello.

![](./media/storsimple-ova-failover-dr/image15.png)

In questo articolo è applicabile alle matrici virtuale StorSimple solo. Per avere esito negativo su un dispositivo 8000 serie, passare a [Failover e ripristino del dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).


## <a name="what-is-disaster-recovery"></a>Che cos'è il ripristino di emergenza?

In uno scenario di ripristino () di emergenza, il dispositivo principale smette di funzionare. In questo caso, è possibile spostare i dati cloud associati con il dispositivo non riuscito a un altro dispositivo utilizzando il dispositivo principale come *origine* e specificando un altro dispositivo con quello di *destinazione*. Questo processo viene detto *failover*. Durante il failover, tutti i volumi o le azioni da dispositivo origine modificare la proprietà e vengono trasferite al dispositivo di destinazione. Nessun filtro dei dati è consentito.

DR è simile a quello come un ripristino completo dispositivo utilizzando il termica basati su mappa collegamento a livelli e verifica. Definito mediante l'assegnazione di un valore termica ai dati in base a una mappa termica in lettura e scrittura modelli. Questo termiche mappare quindi livelli più bassi blocchi di dati di termica nel cloud prima di tutto e mantenendo al contempo i blocchi di dati termiche alta (più usato) nel livello locale. Durante un DR mappa termica viene utilizzata per ripristinare e reidratarsi i dati dal cloud. Il dispositivo recupera tutti i volumi/condivisioni nell'ultimo backup recenti (determinato internamente) ed esegue il ripristino da tale backup. L'intero processo DR viene gestito tramite il dispositivo.


## <a name="prerequisites-for-device-failover"></a>Prerequisiti per failover dispositivo


### <a name="prerequisites"></a>Prerequisiti

Per failover qualsiasi dispositivo, devono essere soddisfatti i prerequisiti seguenti:

- Il dispositivo di origine deve essere in uno stato **Deactivated** .

- Il dispositivo di destinazione deve compaiano come **attivo** nel portale di classica Azure. È necessario effettuare il provisioning di un dispositivo virtuale di destinazione della capacità stessa o versione successiva. Utilizzare l'interfaccia web locale quindi come configurare e registrare correttamente il dispositivo virtuale.

    > [AZURE.IMPORTANT] Non tentare di configurare il dispositivo virtuale registrato tramite il servizio, fare clic su **configurazione di dispositivi completo**. Tramite il servizio, è non necessario eseguire alcuna configurazione dei dispositivi.

- Il dispositivo di origine e destinazione devono essere dello stesso tipo. È possibile solo esito negativo su un dispositivo virtuale configurato come un file server a un altro file server. Lo stesso avviene per un server iSCSI.

- Per un file server DR, è consigliabile aggiungere il dispositivo di destinazione a nello stesso dominio dell'origine in modo che le autorizzazioni di condivisione vengono risolte automaticamente. In questa versione è supportato solo l'il controllo a un dispositivo di destinazione nello stesso dominio.

### <a name="other-considerations"></a>Altre considerazioni

- È consigliabile eseguire tutti i volumi o condivisioni sul dispositivo di origine non in linea.

- Se si tratta di pianificati in caso di errore, è consigliabile eseguire un backup del dispositivo e quindi procedere con il failover per ridurre al minimo la perdita di dati. Se si tratta di un failover non pianificato, il backup più recente verrà utilizzato per ripristinare il dispositivo.

- I dispositivi disponibili per DR sono dispositivi che hanno la capacità stessa o maggiore rispetto al dispositivo di origine. I dispositivi connessi al servizio ma non soddisfa i criteri di spazio sufficiente non saranno disponibili come dispositivi di destinazione.

### <a name="dr-prechecks"></a>DR prechecks

Prima di iniziare il DR, prechecks vengono eseguite sul dispositivo. Questi controlli in modo che si verificherà alcun errore all'inizio del DR. Il prechecks includono:

- Convalidare l'account di archiviazione

- Verifica la connettività cloud Azure

- Controllo spazio disponibile sul dispositivo

- Verifica se un dispositivo di origine server iSCSI include i nomi validi ACR, IQN (non superiori a 220 caratteri) e la password CHAP (12 a 16 caratteri) associate ai volumi

Se uno dei prechecks sopra hanno esito negativo, non è possibile procedere con il DR. È necessario risolvere i problemi e quindi riprovare a DR.

Al termine di DR, la proprietà dei dati cloud sul dispositivo sorgente viene trasferita al dispositivo di destinazione. Il dispositivo di origine quindi non è più disponibile nel portale. Accesso a tutti i volumi/condivisioni sul dispositivo di origine è bloccato e il dispositivo di destinazione diventa attivo.

> [AZURE.IMPORTANT]
> 
> Se il dispositivo non è più disponibile, la macchina virtuale che è stato effettuato il provisioning nel sistema host è ancora tempo le risorse. Una volta completata la DR, è possibile eliminare la macchina virtuale dal sistema host.

## <a name="fail-over-to-a-virtual-array"></a>Impossibile su una matrice virtuale

È consigliabile avere un'altra matrice virtuale di StorSimple viene completato il provisioning, configurate tramite l'interfaccia web locale e registrato con il servizio di gestione StorSimple prima di eseguire questa procedura.


> [AZURE.IMPORTANT]
> 
> - Non è consentito esito negativo su da un dispositivo di serie 8000 StorSimple a un dispositivo virtuale 1200.
> - Può eseguire il da un dispositivo virtuale Federal Information Processing Standard (FIPS) abilitato distribuito nel portale per enti pubblici a un dispositivo virtuale nel portale classica Azure. Anche l'operazione true.

Eseguire la procedura seguente per ripristinare il dispositivo in un dispositivo virtuale StorSimple di destinazione.

1. Richiedere volumi/condivisioni non in linea nell'host. Seguire le istruzioni specifici del sistema operativo nell'host per disconnettere volumi/azioni. Se non è già stato offline, sarà necessario eseguire tutti i volumi/condivisioni offline sul dispositivo facendo clic su **dispositivi > condivisioni** (o **dispositivo > volumi**). Selezionare condivisione/volume e fare clic su **non in linea** nella parte inferiore della pagina. Quando viene richiesto di confermare l'operazione, fare clic su **Sì**. Ripetere questa procedura per tutti i condivisioni/volumi sul dispositivo.

2. Nella pagina **dispositivi** selezionare il dispositivo di origine per il failover e fare clic su **Disattiva**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. Verrà richiesto di confermare l'operazione. Disattivazione di dispositivo è un processo permanente che non può essere annullato. Anche ricevere promemoria per eseguire le azioni/volumi non in linea nell'host.

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. Dopo la conferma, verrà avviata la disattivazione. Al termine la disattivazione, verrà visualizzato.

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. Nella pagina **dispositivi** lo stato dispositivo ora assumerà **Deactivated**.

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. Selezionare il dispositivo disattivato e nella parte inferiore della pagina, fare clic su **Failover**.

6. Nella creazione guidata failover conferma visualizzata, eseguire le operazioni seguenti:

    1. Nell'elenco a discesa dei dispositivi disponibili, scegliere un **dispositivo di destinazione.** Solo i dispositivi sono sufficiente vengono visualizzati nell'elenco a discesa.

    2. Esaminare i dettagli associati al dispositivo di origine, ad esempio nome del dispositivo, capacità totale e i nomi di azioni che verranno eseguiti su.

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. Controllare **I concordino che failover è un'operazione permanente e al termine di failover, il dispositivo di origine verrà eliminato**.

8. Fare clic sull'icona di controllo ![](./media/storsimple-ova-failover-dr/image1.png).


9. Verrà avviato un processo di failover e si riceverà una notifica. Fare clic su **processo di visualizzazione** per controllare il failover.

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. Nella pagina **dei processi** , si vedrà un processo di failover creato per il dispositivo di origine. Questo processo esegue prechecks DR.

    ![](./media/storsimple-ova-failover-dr/image23.png)

    Dopo aver completato prechecks DR, il processo di failover verrà generato processi di ripristino per ogni condivisione/volume esistente in un dispositivo di origine.

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. Una volta completato il failover, passare alla pagina **dispositivi** .

    un. Selezionare il dispositivo virtuale StorSimple utilizzato come dispositivo di destinazione per il processo di failover.

    b. Passare alla pagina **condivisioni** (o **volumi** se iSCSI server). Tutte le azioni (volumi) dal dispositivo precedente a questo punto dovrebbero essere elencate.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**Video disponibile**

Questo video illustra come può avere esito negativo su un dispositivo virtuale locale StorSimple a un altro dispositivo virtuale.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>Ripristino di emergenza continuità aziendale (BCDR)

Uno scenario di ripristino (BCDR) emergenza continuità aziendale si verifica quando l'intero Data Center Azure non funziona più. Questo può influire sul servizio di gestione StorSimple e i dispositivi StorSimple associati.

Se sono presenti dispositivi StorSimple registrati prima che si è verificato un errore, questi dispositivi StorSimple potrebbero essere necessario eliminare. Dopo l'emergenza, è possibile ricreare e configurare i dispositivi.

## <a name="errors-during-dr"></a>Errori durante DR

**Interruzione del servizio di integrazione applicativa di cloud durante DR**

Se viene interrotta la connessione cloud iniziata DR e prima del ripristino del dispositivo, il DR avrà esito negativo e si riceverà una notifica. Il dispositivo di destinazione utilizzati per DR viene contrassegnato come *inutilizzabile.* Stesso dispositivo di destinazione non può essere usato quindi per DRs futuri.

**Nessun dispositivi compatibili**

Se i dispositivi disponibili non dispone di spazio sufficiente, si verrà visualizzato un errore per l'effetto che non sono presenti periferiche di destinazione compatibili.

**Errori precheck**

Se uno del prechecks non è soddisfatta, verrà visualizzata precheck errori.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su come [amministrare la matrice virtuale StorSimple tramite l'interfaccia web locale](storsimple-ova-web-ui-admin.md).
