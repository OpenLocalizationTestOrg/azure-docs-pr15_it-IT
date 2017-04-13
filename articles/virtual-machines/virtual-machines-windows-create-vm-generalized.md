<properties
    pageTitle="Creare macchine Virtuali da un disco rigido virtuale GRG | Microsoft Azure"
    description="Informazioni su come creare una macchina virtuale di Windows da un'immagine disco rigido virtuale GRG tramite PowerShell di Azure, nel modello di distribuzione Manager delle risorse."
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
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-generalized-vhd-image"></a>Creare una macchina virtuale da un'immagine disco rigido virtuale GRG

Un'immagine disco rigido virtuale GRG ha tutte le informazioni dell'account personale rimosse mediante [Sysprep](virtual-machines-windows-generalize-vhd.md). È possibile creare un disco rigido virtuale GRG eseguendo Sysprep in una macchina virtuale locale, quindi [caricare il disco rigido virtuale in Azure](virtual-machines-windows-upload-image.md), o se si esegue Sysprep su un'esistente macchine Virtuali di Azure e quindi [copiare il disco rigido virtuale](virtual-machines-windows-vhd-copy.md).

Se si desidera creare una macchina virtuale da un disco rigido virtuale specifico, vedere [creare una macchina virtuale da un disco rigido virtuale specializzato](virtual-machines-windows-create-vm-specialized.md).

Il modo più rapido per creare una macchina virtuale da un disco rigido virtuale GRG consiste nell'utilizzare [introduttive modello] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image). 


## <a name="prerequisites"></a>Prerequisiti di

Se si prevede di utilizzare un disco rigido virtuale caricato da una macchina virtuale locale, ad esempio una creata con Hyper-V, verificare che è stata seguita le istruzioni contenute in [preparare un disco rigido virtuale di Windows per caricare in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 

Dischi rigidi virtuali caricati e dischi rigidi virtuali macchine Virtuali di Azure esistente necessario essere generalizzata prima di poter creare una macchina virtuale con questo metodo. Per ulteriori informazioni, vedere [utilizzo di Sysprep Generalize una macchina virtuale di Windows](virtual-machines-windows-generalize-vhd.md). 


## <a name="set-the-uri-of-the-vhd"></a>Impostare l'URI del file

URI per il disco rigido virtuale da utilizzare è nel formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**VHD. In questo esempio il disco rigido virtuale denominato **myVHD** è l' account di archiviazione **mystorageaccount** nel contenitore **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Creare una rete virtuale

Creare la vNet e subnet della [rete virtuale](../virtual-network/virtual-networks-overview.md).


1. Creare la subnet. Nell'esempio seguente viene creato una subnet denominata **mySubnet** nel gruppo risorse **myResourceGroup** con il prefisso di **10.0.0.0/24**indirizzo.  

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
      
2. Creare la rete virtuale. Nell'esempio seguente viene creato un virtuali denominata **myVnet** nel percorso **US ovest** con il prefisso del **10.0.0.0/16**.  

    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-network-interface"></a>Creare un'interfaccia pubblica di rete e indirizzo IP

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, è necessario disporre di un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare un indirizzo IP pubblico. In questo esempio viene creato un indirizzo IP pubblico denominato **myPip**. 

    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Creare una scheda di rete. In questo esempio viene creata una scheda di rete denominato **myNic**. 

    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare una regola di RDP e il gruppo di sicurezza di rete

Per consentire agli utenti di accedere alle macchine Virtuali utilizzando RDP, è necessario disporre di una regola di protezione che consente l'accesso RDP sulla porta 3389. 

In questo esempio viene creato un NSG denominato **myNsg** che contiene una regola denominata **myRdpRule** che consenta il traffico RDP sulla porta 3389. Per ulteriori informazioni su NSGs, vedere [apertura di porte a una macchina virtuale in Azure tramite PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Creare una variabile per la rete virtuale

Creare una variabile per la rete virtuale completata. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Creare la macchina virtuale

Script di PowerShell seguente viene illustrato come impostare le configurazioni macchina virtuale e utilizzare l'immagine di macchine Virtuali caricato come origine per la nuova installazione.

</br>


```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential
    
    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"
    
    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"
    
    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"
    
    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"
    
    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"
    
    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage, Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"
    
    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName
    
    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    
    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    
    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
    
    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows
    
    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Verificare che sia stata creata la macchina virtuale 

Al termine, verrà visualizzata la macchina virtuale appena creata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **macchine virtuali**, oppure usando i comandi di PowerShell seguenti:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi

Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [macchine virtuali di gestione tramite Gestione risorse di Azure e PowerShell](virtual-machines-windows-ps-manage.md).


