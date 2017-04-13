<properties
    pageTitle="Acquisire un'immagine di macchine Virtuali da macchine Virtuali di Azure GRG | Microsoft Azure"
    description="Informazioni su come acquisire un'immagine di macchine Virtuali da una macchina virtuale Azure GRG creati nel modello di distribuzione Manager delle risorse"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Come acquisire un'immagine di macchine Virtuali da una macchina virtuale Azure GRG


In questo articolo viene illustrato come usare PowerShell Azure per creare un'immagine di una macchina virtuale Azure GRG. È quindi possibile utilizzare l'immagine per creare un'altra macchina virtuale. L'immagine include il disco del sistema operativo e dischi di dati associati alla macchina virtuale. L'immagine non include le risorse virtuali, pertanto è necessario configurare le risorse quando si crea una nuova macchina virtuale. 


## <a name="prerequisites"></a>Prerequisiti di

- È necessario avere già [generalized macchina virtuale](virtual-machines-windows-generalize-vhd.md). Generalizzazione una macchina virtuale rimuove tutte le informazioni personali, ad esempio e consente di preparare il computer da utilizzare come immagine.

- È necessario disporre di Azure PowerShell versione 1.0. x o versioni successive. Se non ha ancora installato PowerShell, informazioni, [vedere installare e configurare Azure PowerShell](../powershell-install-configure.md) per la procedura di installazione.


## <a name="log-in-to-azure-powershell"></a>Accedere a PowerShell Azure

1. Aprire PowerShell Azure e accedere al proprio account Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.

2. È possibile ottenere l'ID di abbonamento per le proprie sottoscrizioni disponibili.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Impostare la sottoscrizione corretta utilizzando l'ID di sottoscrizione.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Rilasciare la macchina virtuale e impostare lo stato su generalized       

1. Rilasciare risorse macchine Virtuali.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    Lo *stato* per macchine Virtuali nel portale di Azure le modifiche dal **arrestato** su **arrestato (rilasciato)**.

2. Impostare lo stato della macchina virtuale **Generalized**. 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. Controllare lo stato della macchina virtuale. La sezione **OSState/generalizzato** per la macchina virtuale deve avere **DisplayStatus** impostato su **macchine Virtuali generalizzato**.  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Creare l'immagine 

1. Copiare l'immagine di macchina virtuale al contenitore di spazio di archiviazione di destinazione mediante il comando. L'immagine viene creata nello stesso account di archiviazione la macchina virtuale originale. Il `-Path` parametro Salva una copia del modello JSON in locale. Il `-DestinationContainerName` parametro è il nome del contenitore che si desidera tenere le immagini. Se non è presente il contenitore, viene creato automaticamente.

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    È possibile ottenere l'URL dell'immagine dal modello di file JSON. Vai alle **risorse** > **storageProfile** > **osDisk** > **immagine** > sezione**uri** per il percorso completo dell'immagine. L'URL dell'immagine è simile alla: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
    
    È inoltre possibile verificare l'URI nel portale. L'immagine viene copiata in un contenitore denominato **system** nell'account di archiviazione. 


## <a name="next-steps"></a>Passaggi successivi

- A questo punto è possibile [creare una macchina virtuale dall'immagine](virtual-machines-windows-create-vm-generalized.md).

