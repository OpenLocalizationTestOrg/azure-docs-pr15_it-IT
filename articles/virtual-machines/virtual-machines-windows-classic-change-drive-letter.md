<properties
    pageTitle="Rendere l'unità d: di una macchina virtuale un disco dati | Microsoft Azure"
    description="Descrive come modificare le lettere di unità per una macchina virtuale di Windows in modo che è possibile utilizzare l'unità d: come un'unità di dati."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utilizzare l'unità d: come un'unità di dati in una macchina virtuale di Windows 

Se l'applicazione deve essere utilizzato nell'unità D per archiviare i dati, seguire queste istruzioni per utilizzare una lettera di unità diversa per il disco temporaneo. Non utilizzare mai il disco temporaneo per archiviare i dati che è necessario mantenere.

Se si ridimensiona o **Interrompi (Deallocate)** una macchina virtuale, questa può attivare posizione della macchina virtuale per un nuovo hypervisor. Un evento di manutenzione pianificata o non pianificato può attivare questa posizione. In questo scenario, il disco temporaneo verrà riassegnato alla prima lettera di unità disponibile. Se si dispone di un'applicazione che richiede l'unità d: in particolare, è necessario seguire questa procedura per spostare la Pagefile, allegare un nuovo disco di dati e assegnare la lettera D e quindi spostare di nuovo la Pagefile unità temporanea temporaneamente. Una volta completato, Azure non porterà d: se la macchina virtuale si sposta un hypervisor diverso.

Per ulteriori informazioni su come Azure utilizza il disco temporaneo, vedere [informazioni sull'unità temporanea in macchine virtuali di Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Collegare il disco di dati

È necessario prima di tutto, collegare il disco di dati per la macchina virtuale. 

- Per usare il portale, vedere [come collegare un disco dati nel portale di Azure](virtual-machines-windows-attach-disk-portal.md)
- Per utilizzare il portale classico, informazioni su [come allegare un disco di dati a una macchina virtuale di Windows](virtual-machines-windows-classic-attach-disk.md). 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Spostare temporaneamente Pagefile unità C

1. Connettere la macchina virtuale. 

2. Menu di scelta rapida **Start** e selezionare **sistema**.

3. Nel menu a sinistro, selezionare **impostazioni di sistema avanzate**.

4. Nella sezione **prestazioni** selezionare **Impostazioni**.

5. Selezionare la scheda **Avanzate** .

5. Nella sezione **memoria virtuale** selezionare **Modifica**.

6. Selezionare l'unità **C** e quindi fare clic su **dimensioni gestite dal sistema** e quindi fare clic su **Imposta**.

7. Selezionare l'unità **D** e quindi fare clic su **Nessun file di spostamento** e quindi fare clic su **Imposta**.

8. Fare clic su Applica. Verrà visualizzato un avviso che il computer deve essere riavviato rendere effettive le modifiche.

9. Riavviare il computer virtuale.




## <a name="change-the-drive-letters"></a>Modificare le lettere di unità 

1. Dopo aver riavviato la macchina virtuale, accedere alla macchina virtuale.

2. Fare clic sul menu **Start** e digitare **diskmgmt** e premere INVIO. Verrà avviato Gestione disco.

3. Pulsante destro del mouse su **D**, l'unità di archiviazione temporanea e selezionare **Cambia lettera e percorsi**.

4. In unità, selezionare l'unità **c** e quindi fare clic su **OK**. 

5. Pulsante destro del mouse sul disco dati e selezionare **Cambia lettera e unità percorsi**.

6. In unità, selezionare l'unità **D** e quindi fare clic su **OK**. 

7. Pulsante destro del mouse su **G**, l'unità di archiviazione temporanea e selezionare **Cambia lettera e percorsi**.

8. In unità, selezionare l'unità **E** e quindi fare clic su **OK**. 

> [AZURE.NOTE] Se la macchina virtuale è altri dischi o unità, utilizzare lo stesso metodo per riassegnare le lettere di unità di altri dischi e unità. Si desidera la configurazione del disco:  
>- C: disco rigido del sistema operativo  
>- D: dati disco  
>- E: disco temporaneo



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Riportare Pagefile l'unità di memorizzazione temporaneo 

1. Menu di scelta rapida **Start** e selezionare **sistema**

2. Nel menu a sinistro, selezionare **impostazioni di sistema avanzate**.

3. Nella sezione **prestazioni** selezionare **Impostazioni**.

4. Selezionare la scheda **Avanzate** .

5. Nella sezione **memoria virtuale** selezionare **Modifica**.

6. Selezionare l'unità di sistema operativo **C** e fare clic su **Nessun file di spostamento** e quindi fare clic su **Imposta**.

7. Selezionare l'unità di memorizzazione temporaneo **E** quindi fare clic su **dimensioni gestite dal sistema** e quindi fare clic su **Imposta**.

8. Fare clic su **Applica**. Verrà visualizzato un avviso che il computer deve essere riavviato rendere effettive le modifiche.

9. Riavviare il computer virtuale.




## <a name="next-steps"></a>Passaggi successivi
- È possibile aumentare spazio di archiviazione disponibile nel computer in uso virtuale associando [un disco dati aggiuntivi](virtual-machines-windows-attach-disk-portal.md).



