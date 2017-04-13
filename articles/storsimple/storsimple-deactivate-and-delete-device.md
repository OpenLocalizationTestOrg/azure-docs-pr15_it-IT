<properties 
   pageTitle="Disattivare ed eliminare un dispositivo StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto come rimuovere StorSimple dispositivo dal servizio per disattivarlo prima di tutto e quindi eliminarla."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>Disattivare ed eliminare un dispositivo StorSimple

## <a name="overview"></a>Panoramica

Si consiglia di eseguire un dispositivo StorSimple fuori servizio (ad esempio, se si sostituisce o l'aggiornamento del dispositivo o se si utilizza non è più StorSimple). In questo caso, è necessario disattivare il dispositivo prima di poter eliminare. La disattivazione di server la connessione tra il dispositivo e il servizio di gestione StorSimple corrispondente. In questa esercitazione viene illustrato come rimuovere un dispositivo StorSimple dal servizio prima disattivarlo e quindi eliminarla. 

Quando si disattiva un dispositivo, tutti i dati archiviati in locale nel dispositivo non saranno accessibili. È possono ripristinare solo i dati associati al dispositivo archiviati nel cloud.  

>[AZURE.WARNING] La disattivazione è un'operazione permanente e non può essere annullata. Un dispositivo disattivato non è registrato con il servizio di gestione StorSimple a meno che non viene prima reimpostato dalla factory. 
>
>La factory Reimposta le eliminazioni processo tutti i dati archiviati in locale nel dispositivo. Pertanto, è essenziale creare uno snapshot cloud di tutti i dati prima di disattivare un dispositivo. In questo modo sarà possibile recuperare tutti i dati in una fase successiva.

In questa esercitazione viene illustrato come:

- Disattivare un dispositivo ed eliminare i dati
- Disattivare un dispositivo e mantenere i dati

Viene inoltre la modalità di disattivazione e l'eliminazione funzionamento in un dispositivo virtuale StorSimple.

>[AZURE.NOTE] Prima di disattivare un dispositivo fisico o virtuale StorSimple, assicurarsi di arrestare o eliminare i client e a ospitare che dipendono da tale dispositivo.

## <a name="deactivate-and-delete-data"></a>Disattivare ed eliminare i dati

Se si desidera eliminare completamente il dispositivo e non si desidera mantenere i dati nel dispositivo, quindi eseguire la procedura seguente.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Per disattivare il dispositivo ed eliminare i dati  

1. Prima di disattivare un dispositivo, è necessario eliminare tutto il volume contenitori (e i volumi) associati al dispositivo. È possibile eliminare i contenitori volume solo dopo aver eliminato le copie di backup associati.

2. Disattivare il dispositivo come indicato di seguito:

    1. Nella pagina **dispositivi** servizio StorSimple Manager, selezionare il dispositivo che si desidera disattivare e, nella parte inferiore della pagina, fare clic su **Disattiva**.

    2. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare. Il processo di Disattiva verrà avviato e richiedere alcuni minuti.

3. Dopo la disattivazione, è possibile eliminare completamente il dispositivo. Quando si elimina un dispositivo, esso dall'elenco dei dispositivi collegati al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. Per eliminare il dispositivo, usare i passaggi seguenti:

    1. Nella pagina Gestione StorSimple servizio **dispositivi** selezionare un dispositivo disattivato che si desidera eliminare.

    2. Nella parte inferiore della pagina fare clic su **Elimina**.

    3. Verrà richiesto di confermare l'operazione. Fare clic su **Sì** per continuare.

    Possono richiedere alcuni minuti per il dispositivo da eliminare.

## <a name="deactivate-and-retain-data"></a>Disattivare e conservare i dati

Se si è interessati eliminando il dispositivo ma si desidera mantenere i dati, quindi eseguire la procedura seguente.

####<a name="to-deactivate-a-device-and-retain-the-data"></a>Per disattivare un dispositivo e mantenere i dati 

1. Disattivare il dispositivo. Tutti i contenitori di volume e snapshot del dispositivo verranno mantenuti.

    1. Nella pagina **dispositivi** servizio StorSimple Manager, selezionare il dispositivo che si desidera disattivare e, nella parte inferiore della pagina, fare clic su **Disattiva**.

    2. Verrà visualizzato un messaggio di conferma. Fare clic su **Sì** per continuare. Il processo di Disattiva verrà avviato e richiedere alcuni minuti.

2. A questo punto, può eseguire il contenitori volume e snapshot associato. Per procedure, passare a [Failover e ripristino di emergenza del dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Dopo la disattivazione e failover, è possibile eliminare completamente il dispositivo. Quando si elimina un dispositivo, esso dall'elenco dei dispositivi collegati al servizio. Il servizio quindi non può più gestire il dispositivo eliminato. Completare la procedura seguente per eliminare il dispositivo:
 
    1. Nella pagina Gestione StorSimple servizio **dispositivi** selezionare un dispositivo disattivato che si desidera eliminare.

    2. Nella parte inferiore della pagina fare clic su **Elimina**.

    3. Verrà richiesto di confermare l'operazione. Fare clic su **Sì** per continuare.

    Possono richiedere alcuni minuti per il dispositivo da eliminare.

## <a name="deactivate-and-delete-a-virtual-device"></a>Disattivare ed eliminare un dispositivo virtuale

Per un dispositivo virtuale StorSimple, disattivazione ne esegue la macchina virtuale. È possibile eliminare la macchina virtuale e le risorse create quando è stato eseguito il provisioning. Dopo che è stato disattivato il dispositivo virtuale, non possono essere ripristinato allo stato precedente. 

Disattivazione dei risultati nelle azioni seguenti:

- Il dispositivo virtuale StorSimple viene rimosso.

- Il OSDisk e dischi di dati creati per il dispositivo virtuale StorSimple vengono rimossi.

- Il servizio ospitato e virtuali che sono stati creati durante il provisioning vengono mantenuti. Se non si usa queste entità, è necessario eliminarli manualmente.

- Snapshot cloud creati dal dispositivo virtuale StorSimple vengono mantenuti.

## <a name="next-steps"></a>Passaggi successivi
- Per ripristinare il dispositivo disattivato predefinite, passare a [reimpostare il dispositivo per le impostazioni predefinite](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Per assistenza tecnica, [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md).

- Per ulteriori informazioni su come utilizzare il servizio di gestione StorSimple, passare a [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md). 
