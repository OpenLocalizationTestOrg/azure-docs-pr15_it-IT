<properties
    pageTitle="Allegare un disco a una macchina virtuale | Microsoft Azure"
    description="Allegare un disco dati a una macchina virtuale di Windows creata con il modello di distribuzione classica e inizializzare."
    services="virtual-machines-windows, storage"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Allegare un disco dati a una macchina virtuale di Windows creata con il modello di distribuzione classica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se si desidera utilizzare il nuovo portale, informazioni su [come allegare un disco di dati a una macchina virtuale di Windows nel portale di Azure](virtual-machines-windows-attach-disk-portal.md).

Se è necessario un disco dati aggiuntivi, è possibile allegare un disco vuoto o un disco esistente con i dati a una macchina virtuale. In entrambi i casi, i dischi sono VHD che si trovano in un account di archiviazione Azure. In caso di un nuovo disco, dopo avere collegato il disco, è anche necessario inizializzare in modo che sia pronto per l'utilizzo da una macchina virtuale di Windows.

Per ulteriori informazioni sui dischi, vedere [sui dischi e dischi rigidi virtuali per macchine virtuali](virtual-machines-windows-about-disks-vhds.md).


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Inizializzare il disco

1. Connettere la macchina virtuale. Per istruzioni, vedere [come effettuare l'accesso a una macchina virtuale che eseguono Windows Server][logon].

2. Dopo l'accesso al computer virtuale, aprire **Server Manager**. Nel riquadro a sinistra, selezionare **File e servizi di archiviazione**.

    ![Aprire Gestione Server](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Espandere il menu e selezionare **dischi**.

4. La sezione **dischi** Elenca i dischi. Nella maggior parte dei casi, avrà disco 0, disco 1 e 2 disco. Disco 0 è il sistema operativo, disco 1 è il disco temporaneo e disco 2 è il disco di dati allegato per la macchina virtuale. Il nuovo disco dati verrà elencati la partizione come **sconosciuto**. Pulsante destro del mouse sul disco e selezionare **inizializzare**.

5.  Si riceverà una notifica che tutti i dati vengono cancellati quando il disco è inizializzato. Fare clic su **Sì** per confermare l'avviso e inizializzare il disco. Al termine, la partizione verrà elencati come **GPT**. Fare di nuovo il disco e selezionare **Nuovo Volume**.

6.  Completare la procedura guidata utilizzando i valori predefiniti. Quando viene eseguita la procedura guidata, la sezione **volumi** Elenca il nuovo volume. Il disco è online e pronto per l'archiviazione dei dati.

    ![Volume inizializzata completata](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] Le dimensioni della macchina virtuale determinano quanti dischi è possibile collegarsi. Per informazioni dettagliate, vedere [le dimensioni per macchine virtuali](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Risorse aggiuntive

[Come faccio a scollegare un disco da una macchina virtuale di Windows](virtual-machines-windows-classic-detach-disk.md)

[Sui dischi e dischi rigidi virtuali per macchine virtuali](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
