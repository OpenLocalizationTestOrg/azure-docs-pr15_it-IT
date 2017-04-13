<properties
    pageTitle="Spostare una macchina virtuale Linux | Microsoft Azure"
    description="Spostare una VM Linux in un'altra Azure abbonamento o delle risorse gruppo nel modello di distribuzione Manager delle risorse."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Spostare una VM Linux a un altro gruppo di sottoscrizione o delle risorse

In questo articolo illustra come spostare una VM Linux tra gruppi di risorse o le sottoscrizioni. Lo spostamento di una macchina virtuale tra abbonamenti può essere utile se una macchina virtuale è stato creato in un abbonamento personale e vuole spostarla all'abbonamento della società.

> [AZURE.NOTE] Come parte dello spostamento vengono creati nuovi ID risorsa. Dopo la macchina virtuale è stata spostata, è necessario aggiornare gli strumenti e script da utilizzare la nuova risorsa ID. 


## <a name="use-the-azure-cli-to-move-a-vm"></a>Consente di spostare una macchina virtuale CLI Azure 

Per spostare una macchina virtuale, è necessario spostare la macchina virtuale e tutte le risorse di supporto. Usare il comando **Mostra gruppo azure** per visualizzare un elenco di tutte le risorse in un gruppo di risorse e i relativi ID. Può essere utile per inviare l'output del comando in un file in modo che è possibile copiare e incollare gli ID comandi successivi.

    azure group show <resourceGroupName>

Per spostare una macchina virtuale e le relative risorse in un altro gruppo di risorse, utilizzare il comando CLI **risorsa azure Sposta** . Nell'esempio seguente viene illustrato come spostare una macchina virtuale e le risorse più comuni che richiede l'esecuzione. Abbiamo utilizzare il parametro **-i** e passare a un elenco delimitato da virgole (senza spazi) di ID per le risorse da spostare.

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
Se si desidera spostare la macchina virtuale e le relative risorse a un abbonamento diverso, aggiungere il **-destinazione subscriptionId #60; destinationSubscriptionID & #62;** parametro per specificare la sottoscrizione di destinazione.

Se si utilizza il prompt dei comandi in un computer Windows, è necessario aggiungere un **$** davanti ai nomi di variabile quando si dichiara. Questa operazione non è necessaria in Linux.

Viene chiesto di confermare che si desidera spostare la risorsa specificata. Digitare **Y** per confermare che si desidera spostare le risorse.
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile spostare diversi tipi di risorse tra le sottoscrizioni e gruppi di risorse. Per ulteriori informazioni, vedere [spostare le risorse nuovo gruppo di risorse o l'abbonamento](../resource-group-move-resources.md).    