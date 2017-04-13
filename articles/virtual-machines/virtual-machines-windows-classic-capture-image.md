<properties
    pageTitle="Acquisire un'immagine di una macchina virtuale Windows Azure | Microsoft Azure"
    description="Acquisire un'immagine di una macchina virtuale di Windows Azure creata con il modello di distribuzione classica."
    services="virtual-machines-windows"
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Acquisire un'immagine di una macchina virtuale di Windows Azure creata con il modello di distribuzione classica.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Per informazioni sul modello di Manager delle risorse, vedere [creare una copia macchine Virtuali di Windows in esecuzione in Azure](virtual-machines-windows-vhd-copy.md).


In questo articolo viene illustrato come acquisire una macchina virtuale Azure che esegue Windows in modo che è possibile usare come immagine per creare altre macchine virtuali. Questa immagine include il disco di sistema operativo e qualsiasi disco dati associati alla macchina virtuale. Non include le configurazioni di rete, sarà necessario configurare quelli quando si crea altre macchine virtuali che utilizzano l'immagine.

Azure memorizza l'immagine in **Immagini personali**. Questa è la stessa posizione in cui sono archiviate le immagini di che aver caricato. Per informazioni dettagliate sulle immagini, vedere [informazioni sulle immagini per macchine virtuali](virtual-machines-linux-classic-about-images.md).

##<a name="before-you-begin"></a>Prima di iniziare##

Questa procedura si presuppone che aver creato una macchina virtuale Azure e di aver configurato il sistema operativo, tra cui associare qualsiasi disco di dati. Se si è già fatto questo ancora, vedere queste istruzioni:

- [Creare una macchina virtuale da un'immagine](virtual-machines-windows-classic-createportal.md)
- [Come collegare un disco dati a una macchina virtuale](virtual-machines-windows-classic-attach-disk.md)
- Verificare che i ruoli del server sono supportate con Sysprep. Per ulteriori informazioni, vedere [Supporto di Sysprep per ruoli del Server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Questo processo elimina la macchina virtuale originale dopo che viene acquisito. 

Prima di caputuring un'immagine di una macchina virtuale Azure, è consigliabile eseguire il backup la macchina virtuale di destinazione. Azure macchine virtuali possono essere copiate tramite Azure Backup. Per informazioni dettagliate, vedere [eseguire il backup macchine virtuali di Azure](../backup/backup-azure-vms.md). Altre soluzioni disponibili partner certificati. Per scoprire cosa è attualmente disponibile, eseguire una ricerca di Azure Marketplace.


##<a name="capture-the-virtual-machine"></a>Acquisire la macchina virtuale

1. Nel [portale classica Azure](http://manage.windowsazure.com), **connettersi** alla macchina virtuale. Per ulteriori informazioni, vedere [come effettuare l'accesso a una macchina virtuale che eseguono Windows Server] [].

2.  Aprire una finestra del prompt dei comandi come amministratore.

3.  Passare alla directory `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.

4.  Verrà visualizzata la finestra di dialogo **Utilità** . Eseguire le operazioni seguenti:

    - In **Azione pulizia sistema**, selezionare **sistema immettere pronte della configurazione guidata** e verificare che sia selezionata **Generalize** . Per ulteriori informazioni sull'utilizzo di Sysprep, vedere [come utilizzare Sysprep: un'introduzione][].

    - Nelle **Opzioni di arresto**, selezionare **arresto**.

    - Fare clic su **OK**.

    ![Eseguirlo](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep si arresta la macchina virtuale attiva lo stato della macchina virtuale nel portale di classica Azure **arrestare**.

8.  Nel portale di classica Azure, fare clic su **macchine virtuali** e selezionare l'immagine da acquisire.

9.  Scegliere **Acquisisci**barra dei comandi.

    ![Acquisire macchina virtuale](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    Verrà visualizzata la finestra di dialogo **acquisire la macchina virtuale** .

10. **Nome dell'immagine**, digitare un nome per la nuova immagine.

11. Prima di aggiungere un'immagine di Windows Server per il set di immagini personalizzate, deve essere generalizzata eseguendo Sysprep come descritto nei passaggi precedenti. Fare clic su **ho eseguirlo sul computer virtuale** per indicare che l'azione.

12. Fare clic sul segno di spunta per acquisire un'immagine. La nuova immagine è ora disponibile in **immagini**.

    ![Acquisizione di immagini completato](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Passaggi successivi

L'immagine è pronto per essere utilizzato per creare macchine virtuali. A tale scopo, sarà necessario creare una macchina virtuale utilizzando la voce di menu **Dalla raccolta** e selezionare l'immagine che appena creato. Per ulteriori informazioni, vedere [creare una macchina virtuale da un'immagine](virtual-machines-windows-classic-createportal.md).



[Come effettuare l'accesso a una macchina virtuale che eseguono Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Utilizzo di Sysprep: introduzione]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
