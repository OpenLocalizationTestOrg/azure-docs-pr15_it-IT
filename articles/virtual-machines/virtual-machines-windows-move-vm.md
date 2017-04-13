<properties
    pageTitle="Spostare una macchina virtuale di Windows | Microsoft Azure"
    description="Spostare una macchina virtuale di Windows a un altro Azure abbonamento o delle risorse gruppo nel modello di distribuzione Manager delle risorse."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Spostare una macchina virtuale di Windows a un altro gruppo Azure abbonamento o delle risorse 

In questo articolo illustra come spostare una macchina virtuale di Windows tra gruppi di risorse o le sottoscrizioni. Lo spostamento tra le sottoscrizioni può essere utile se è stato creato originariamente una macchina virtuale in un abbonamento personale e a questo punto si desidera spostarlo all'abbonamento della società per continuare il lavoro.

> [AZURE.NOTE] Nuovi ID risorsa viene creato come parte dello spostamento. Dopo la macchina virtuale è stata spostata, sarà necessario aggiornare gli strumenti e script da utilizzare la nuova risorsa ID. 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>Usare Powershell per spostare una macchina virtuale

Per spostare una macchina virtuale a un altro gruppo di risorse, è necessario assicurarsi anche spostare tutte le risorse dipendenti. Per utilizzare il cmdlet AzureRMResource sposta, è necessario disporre di nome della risorsa e il tipo di risorsa. È possibile ottenere entrambe le operazioni da cmdlet trova AzureRMResource.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

Per spostare una macchina virtuale è necessario spostare più risorse. È possibile dopo aver creato variabili separate per ogni risorsa, quindi con un elenco. In questo esempio include la maggior parte delle risorse di base per una macchina virtuale, ma è possibile aggiungere più in base alle esigenze.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Per spostare le risorse in sottoscrizione diversa, includere il parametro **- DestinationSubscriptionId** . 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Verrà richiesto di confermare che si desidera spostare le risorse specificate. Digitare **Y** per confermare che si desidera spostare le risorse.

  
## <a name="next-steps"></a>Passaggi successivi

È possibile spostare diversi tipi di risorse tra le sottoscrizioni e gruppi di risorse. Per ulteriori informazioni, vedere [spostare le risorse nuovo gruppo di risorse o l'abbonamento](../resource-group-move-resources.md).    