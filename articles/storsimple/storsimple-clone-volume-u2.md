<properties
   pageTitle="Duplicare il volume StorSimple | Microsoft Azure"
   description="Vengono descritti i tipi di Clona diversi e sul loro utilizzo e come è possibile usare un set di backup da duplicare volume singolo."
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
   ms.date="07/27/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>Utilizzare il servizio di gestione di StorSimple per duplicare un volume (aggiornamento 2)

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Panoramica

La pagina di **Catalogo di Backup** del servizio di gestione StorSimple consente di visualizzare tutti i set di backup creato quando vengono eseguite backup manuale o automatico. È possibile utilizzare questa pagina per elencare tutti i backup di un criterio di backup o un volume degli scambi, selezionare o eliminare i backup o usare una copia di backup per ripristinare o duplicare un volume.

![Pagina catalogo di backup](./media/storsimple-clone-volume-u2/backupCatalog.png)  

In questa esercitazione viene illustrato come utilizzare un set di backup da duplicare volume singolo. Viene inoltre la differenza tra *temporanee* e *permanenti* duplicati.

>[AZURE.NOTE] 
>
>Un volume locale bloccato verrà duplicato come volume a più livelli. Se è necessario il volume duplicato per essere bloccata in locale, è possibile convertire la copia in un volume localmente bloccato dopo l'operazione duplicato è stato completato. Per informazioni sulla conversione di un volume a più livelli in un volume locale bloccato, passare alla sezione [Modifica il tipo di volume](storsimple-manage-volumes-u2.md#change-the-volume-type).
>
>Se si tenta di convertire un volume duplicato rispetto a più livelli a localmente aggiunte immediatamente dopo la duplicazione (quando è ancora un duplicato temporaneo), la conversione avrà esito negativo con il seguente messaggio di errore:
>
>`Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
>
>Questo errore viene ricevuto solo se clonare su un dispositivo diverso. È possibile convertire correttamente il volume a aggiunte localmente se si converte innanzitutto duplicato temporaneo in un duplicato permanente. Per convertire un duplicato permanente Clona temporanea, creare uno snapshot cloud di esso.

## <a name="create-a-clone-of-a-volume"></a>Creare un duplicato di un volume

È possibile creare un duplicato del dispositivo stesso, un altro dispositivo o anche una macchina virtuale utilizzando locale o uno snapshot cloud.

#### <a name="to-clone-a-volume"></a>Per duplicare un volume

1. Nella pagina servizio StorSimple Manager, fare clic sulla scheda **catalogo di Backup** e selezionare un set di backup.

2. Espandere il set per visualizzare i volumi associati di backup. Fare clic su e selezionare un volume del set di backup.

     ![Duplicare un volume](./media/storsimple-clone-volume-u2/CloneVol.png) 

3. Fare clic su **duplicato** per iniziare la duplicazione il volume selezionato.

4. Nella creazione guidata Volume duplicato, in **percorso e il nome specificare**:

  1. Identificare un dispositivo di destinazione. Questa è la posizione in cui verrà creato il duplicato. È possibile scegliere il dispositivo stesso o specificare un altro dispositivo. Se si sceglie un volume associato con altri provider di servizi cloud (non Azure), l'elenco a discesa per il dispositivo di destinazione verrà mostrati solo dispositivi fisici. Non è possibile duplicare un volume associato con altri provider di servizi cloud su un dispositivo virtuale.

        >[AZURE.NOTE] Assicurarsi che le capacità necessarie per la copia sono inferiore rispetto alla capacità disponibile sul dispositivo.

  2. Specificare un nome univoco volume per il duplicato. Il nome deve contenere da 3 a 127 caratteri. 
    
        >[AZURE.NOTE] Il campo **Duplicato Volume come** sarà **Tiered** anche se si clonare un volume locale bloccato. Non è possibile modificare questa impostazione. Tuttavia, se è necessario il volume duplicato necessario localmente bloccare anche, è possibile convertire la copia in un volume localmente bloccato dopo aver creato correttamente il duplicato. Per informazioni sulla conversione di un volume a più livelli in un volume locale bloccato, passare alla sezione [Modifica il tipo di volume](storsimple-manage-volumes-u2.md#change-the-volume-type).

        ![Creazione guidata Clona 1](./media/storsimple-clone-volume-u2/clone1.png) 

  3. Fare clic sull'icona freccia ![icona freccia](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) Per passare alla pagina successiva.

5. In **specificare host che possono utilizzare questo volume**:

  1. Specificare un record di controllo di accesso (ACR) per la copia. È possibile aggiungere un nuovo ACR o scegliere dall'elenco esistente.

        ![Creazione guidata Clona 2](./media/storsimple-clone-volume-u2/clone2.png) 

  2. Fare clic sull'icona di controllo ![icona di controllo](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)Per completare l'operazione.

6. Verrà avviato un processo di copia e si riceverà una notifica quando il duplicato è stato creato correttamente. Fare clic su **Processo di visualizzazione** per controllare il processo duplicato nella pagina **processi** . Al termine del processo di duplicato, si verrà visualizzato il messaggio seguente:

    ![Messaggio duplicato](./media/storsimple-clone-volume-u2/CloneMsg.png) 

7. Una volta completato il processo duplicato:

  1. Passare alla pagina **dispositivi** e selezionare la scheda **Contenitori Volume** . 
  2. Selezionare il contenitore volume associato il volume di origine che duplicato. Nell'elenco di volumi, verrà visualizzata la copia appena creata.

>[AZURE.NOTE] Impostazione predefinita e il monitoraggio backup verranno disattivate automaticamente in un volume duplicato.

Un duplicato che viene creato in questo modo è un duplicato temporaneo. Per ulteriori informazioni sui tipi di duplicato, vedere [temporanee e permanenti duplicati](#transient-vs.-permanent-clones).

Questo duplicato è ora un volume normale e le operazioni che sono possibile utilizzare in un volume saranno disponibili per la copia. È necessario configurare il volume per i backup.

## <a name="transient-vs-permanent-clones"></a>Temporanei e permanente duplicati

Duplicati temporanee vengono creati solo quando clonare a un dispositivo diverso. È possibile duplicare un volume specifico da un backup impostato su un dispositivo diverso gestito da Gestione StorSimple. Utilizza tali dati per leggere e scrivere in locale nel dispositivo di destinazione duplicato temporaneo avrà riferimenti ai dati del volume originale. 

Dopo un'istantanea cloud di un duplicato temporaneo, Clona risultante sarà un duplicato *permanente* . Durante questo processo viene creata una copia dei dati nel cloud e il tempo necessario per copiare i dati varia a seconda delle dimensioni dei dati e la latenza Azure (si tratta di una copia di Azure a Azure). Questo processo può richiedere giorni alla visualizzazione per settimana. Clona temporaneo diventa un duplicato permanente in questo modo e non contenga alcun riferimento ai dati originali volume che è stata duplicata da. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenari per temporanee e permanenti duplicati

Le sezioni seguenti descrivono situazioni di esempio in cui possono essere utilizzati temporanei e permanente duplicati.

### <a name="item-level-recovery-with-a-transient-clone"></a>Ripristino di livello di elemento con un duplicato temporaneo

È necessario ripristinare un file di presentazione di Microsoft PowerPoint uno anni. L'amministratore IT identifica il backup specifico dal periodo di tempo e quindi viene filtrato il volume. L'amministratore quindi Duplica il volume, individua il file che si sta cercando e fornisce all'utente. In questo scenario, viene utilizzato un duplicato temporaneo. 
 
![Video disponibile](./media/storsimple-clone-volume-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile utilizzare la copia e ripristinare le funzionalità di StorSimple per recuperare i file eliminati, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Test nell'ambiente di produzione con un duplicato permanente

È necessario verificare un test bug nell'ambiente di produzione. Creare un duplicato del volume nell'ambiente di produzione e quindi un'istantanea cloud di questo duplicato per creare un volume duplicato indipendente. In questo scenario, viene utilizzato un duplicato permanente.  

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [ripristinare un volume StorSimple da un set di backup](storsimple-restore-from-backup-set-u2.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

 
