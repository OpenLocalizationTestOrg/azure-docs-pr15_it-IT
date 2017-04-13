<properties
    pageTitle="Creare e caricare un'immagine di macchine Virtuali tramite Powershell | Microsoft Azure"
    description="Informazioni su come creare e caricare un'immagine di Windows Server GRG (disco rigido virtuale) utilizzando il modello di distribuzione classica e Azure Powershell."
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
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Creare e caricare un disco rigido Server virtuale di Windows Azure

In questo articolo viene illustrato come caricare la propria immagine macchine Virtuali GRG come un disco rigido virtuale (disco rigido virtuale) in modo che è possibile utilizzare per creare macchine virtuali. Per ulteriori informazioni sui dischi e dischi rigidi virtuali di Microsoft Azure, vedere [sui dischi e dischi rigidi virtuali per macchine virtuali](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. È inoltre possibile [caricare](virtual-machines-windows-upload-image.md) una macchina virtuale utilizzando il modello di Manager delle risorse. 

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone che si dispone di:

- **Azure un abbonamento** , se non si dispone di uno, è possibile [aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - si dispone del modulo di Microsoft Azure PowerShell installato e configurato per usare l'abbonamento. 

- **A . File disco rigido virtuale** - operativi sistema archiviati in un file con estensione vhd e associati a una macchina virtuale Windows supportati. Verificare se i ruoli del server in esecuzione su disco rigido virtuale sono supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto di Sysprep per ruoli del Server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.IMPORTANT] Il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato disco rigido virtuale utilizzando Hyper-V Manager o il [disco rigido virtuale Converti cmdlet](http://technet.microsoft.com/library/hh848454.aspx). Per informazioni dettagliate, vedere questo [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Passaggio 1: Preparare il disco rigido virtuale 

Prima di caricare il disco rigido virtuale in Azure, è necessario essere generalizzata utilizzando quest '. Consente di preparare il disco rigido virtuale da utilizzare come immagine. Per informazioni dettagliate su Sysprep, vedere [come utilizzare Sysprep: un'introduzione](http://technet.microsoft.com/library/bb457073.aspx). Eseguire il backup della macchina virtuale prima di eseguire Sysprep.

Dal computer virtuale che è stato installato il sistema operativo, attenersi alla seguente procedura:

1. Accedere al sistema operativo.

2. Aprire una finestra del prompt dei comandi come amministratore. Passare alla directory **%WINDIR%\system32\sysprep.**e quindi eseguire `sysprep.exe`.

    ![Aprire una finestra del prompt dei comandi](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.  Verrà visualizzata la finestra di dialogo **Utilità** .

    ![Avviare Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Nello **Strumento di preparazione del sistema**, selezionare **Immettere sistema fuori della casella guidata** e verificare che sia selezionata **Generalize** .

5.  Nelle **Opzioni di arresto**, selezionare **arresto**.

6.  Fare clic su **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Passaggio 2: Creare un account di archiviazione e un contenitore

È necessario un account di archiviazione in Azure in modo da avere una posizione in cui caricare file con estensione vhd. Questo passaggio viene illustrato come creare un account o ottenere le informazioni che necessarie da un account esistente. Sostituire le variabili di &lsaquo; tra parentesi quadre &rsaquo; con le informazioni personalizzate.

1. Account di accesso

        Add-AzureAccount

1. Impostare l'abbonamento Azure.

        Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Creare un nuovo account di archiviazione. Il nome dell'account di archiviazione deve essere univoco, 3-24 caratteri. Il nome può essere qualsiasi combinazione di lettere e numeri. È necessario specificare un percorso ad esempio "EST Contattaci"
        
        New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Impostare il nuovo account di archiviazione come predefinito.
        
        Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Creare un nuovo contenitore.

        New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## <a name="step-3-upload-the-vhd-file"></a>Passaggio 3: Caricare file con estensione vhd

Usare [Aggiungi AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) per caricare il disco rigido virtuale.

Nella finestra di PowerShell Azure usato nel passaggio precedente, digitare il comando seguente e sostituire le variabili di &lsaquo; tra parentesi quadre &rsaquo; con le informazioni personalizzate.

        Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Passaggio 4: Aggiungere l'immagine all'elenco di immagini personalizzate

Utilizzare il cmdlet [AzureVMImage Aggiungi](https://msdn.microsoft.com/library/mt589167.aspx) per aggiungere l'immagine nell'elenco delle immagini personalizzate.

        Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## <a name="next-steps"></a>Passaggi successivi

È possibile [creare una macchina virtuale personalizzata](virtual-machines-windows-classic-createportal.md) utilizzando l'immagine caricata.

