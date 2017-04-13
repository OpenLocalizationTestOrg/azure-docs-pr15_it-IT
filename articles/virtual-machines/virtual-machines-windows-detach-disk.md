<properties
    pageTitle="Scollegare un disco di dati da una macchina virtuale di Windows | Microsoft Azure"
    description="Informazioni su come scollegare un disco di dati da una macchina virtuale in Azure utilizzando il modello di distribuzione di Manager delle risorse."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Come faccio a scollegare un disco di dati da una macchina virtuale di Windows


Quando non è più necessario un disco dati collegati a una macchina virtuale, è possibile disconnettersi facilmente essa. Rimuove il disco dal computer virtuali, ma non rimuove dallo spazio di archiviazione. 

> [AZURE.WARNING] Se ci si disconnette un disco che non viene eliminato automaticamente. Se ha un abbonamento allo spazio di archiviazione Premium, si continuerà a sostenere spese di spazio di archiviazione per il disco. Per ulteriori informazioni vedere [prezzi](../storage/storage-premium-storage.md#pricing-and-billing)e la fatturazione quando si usa lo spazio di archiviazione Premium. 

Se si desidera riutilizzare i dati esistenti sul disco, è possibile allegarlo la stessa macchina virtuale o un altro.  


## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale

1. Nell'hub portale selezionare **macchine virtuali**.

2. Selezionare la macchina virtuale che contiene il disco di dati che si desidera scollegare e quindi fare clic su **tutte le impostazioni**.

3. Nella finestra e **Impostazioni** selezionare **dischi**.

4. In e il **dischi** , selezionare il disco di dati che si desidera scollegare.

5. In blade per il disco di dati, fare clic su **Disconnetti**.


    ![Schermata che mostra il pulsante Disconnetti.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

Il disco rimane in archiviazione ma non è più applicato a una macchina virtuale.


## <a name="detach-a-data-disk-using-powershell"></a>Scollegare un disco dati tramite PowerShell

In questo esempio, il primo comando Ottiene la macchina virtuale denominata **MyVM07** nel gruppo di risorse **RG11** utilizzando il cmdlet Get-AzureRmVM. Il comando Archivia la macchina virtuale nella variabile **$VirtualMachine** . 

Il secondo comando rimuove il disco di dati denominato DataDisk3 dal computer virtuale. 

Il comando finale Aggiorna lo stato della macchina virtuale per completare il processo di rimozione del disco di dati.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Per ulteriori informazioni, vedere [Rimuovi AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Passaggi successivi

Se si desidera riutilizzare il disco di dati, è possibile solo [allegarlo a un altro macchine Virtuali](virtual-machines-windows-attach-disk-portal.md)
