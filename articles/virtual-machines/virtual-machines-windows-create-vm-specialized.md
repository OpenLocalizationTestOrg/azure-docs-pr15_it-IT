<properties
    pageTitle="Creare una copia di una macchina virtuale Windows | Microsoft Azure"
    description="Informazioni su come creare una copia di specializzate Azure una macchina virtuale che esegue Windows, nel modello di distribuzione Manager delle risorse."
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
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>Creare una macchina virtuale da un disco rigido virtuale specializzato

Creare una nuova macchina virtuale associando un disco rigido virtuale speciali come il disco del sistema operativo tramite Powershell. Un disco rigido virtuale specializzato mantiene gli account utente, applicazioni e altri dati di stato dalla macchina virtuale originale. 

Se si desidera creare una macchina virtuale da un disco rigido virtuale GRG, vedere [creare una macchina virtuale da un'immagine disco rigido virtuale GRG](virtual-machines-windows-create-vm-generalized.md).

## <a name="create-the-subnet-and-vnet"></a>Creare le subNet e vNet

Creare la vNet e subNet della [rete virtuale](../virtual-network/virtual-networks-overview.md).

1. Creare la subNet. In questo esempio viene creata una subnet denominata **mySubNet**in di gruppo di risorse **myResourceGroup**e imposta il prefisso subnet su **10.0.0.0/24**.

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. Creare il vNet. In questo esempio il nome di rete virtuale sia **myVnetName**, il percorso in cui **Ovest degli Stati Uniti**e il prefisso dell'indirizzo per la rete virtuale per **10.0.0.0/16**. 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>Creare un indirizzo IP pubblico e scheda di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, è necessario disporre di un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare l'indirizzo IP pubblico. In questo esempio, il nome dell'indirizzo IP pubblico è impostato su **myIP**.

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Creare una scheda di rete. In questo esempio, il nome di rete è impostato su **myNicName**.

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare una regola di RDP e il gruppo di sicurezza di rete

Per consentire agli utenti di accedere alle macchine Virtuali utilizzando RDP, è necessario disporre di una regola di sicurezza che consente l'accesso RDP sulla porta 3389. Poiché il disco rigido virtuale per la nuova macchina virtuale è stato creato da un oggetto esistente specializzate macchine Virtuali, dopo aver creata la macchina virtuale è possono usare un account esistente dal computer virtuale di origine che ha l'autorizzazione per l'accesso mediante RDP.

In questo esempio imposta il nome NSG **myNsg** e il nome della regola RDP a **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Per ulteriori informazioni sulle regole NSG e i punti finali, vedere [apertura di porte a una macchina virtuale in Azure tramite PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

## <a name="create-the-vm-configuration"></a>Creare la configurazione di macchine Virtuali

Configurare la configurazione di macchine Virtuali per allegare il disco rigido virtuale copiato come il disco rigido virtuale del sistema operativo.


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Se si dispone di dischi di dati che devono essere associati a macchina virtuale, è inoltre necessario aggiungere le operazioni seguenti: 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

I dati e il sistema operativo disco URL un aspetto simile al seguente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. È possibile trovare nel portale di esplorazione nel contenitore di destinazione di archiviazione, facendo clic su sistema operativo o dati disco rigido virtuale che è stato copiato e quindi copiare il contenuto dell'URL.


## <a name="create-the-vm"></a>Creare la macchina virtuale

Creare la macchina virtuale utilizzando le configurazioni appena creata.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se questo comando è stato completato, si noterà output come segue:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>Verificare che sia stata creata la macchina virtuale 
 
È necessario visualizzare la macchina virtuale appena creata nel [portale di Azure](https://portal.azure.com), in **Sfoglia** > **macchine virtuali**, oppure usando i comandi di PowerShell seguenti:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi

Per accedere alla nuova macchina virtuale, passare alla macchina virtuale nel [portale](https://portal.azure.com), fare clic su **Connetti**e aprire il file RDP Remote Desktop. Usare le credenziali dell'account del computer virtuale originale per accedere alla nuova macchina virtuale. Per ulteriori informazioni, vedere [come connettere e accedere a una macchina virtuale Azure che esegue Windows](virtual-machines-windows-connect-logon.md).







