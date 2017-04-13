<properties
   pageTitle="Creare una macchina virtuale di Windows con più schede di rete | Microsoft Azure"
   description="Informazioni su come creare una macchina virtuale di Windows con più schede di rete associati all'uso dei modelli di PowerShell Azure o Manager delle risorse."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>Creazione di una macchina virtuale di Windows con più schede di rete
È possibile creare una macchine () in Azure con più interfacce di rete virtuale (NIC) allegate. Scenari comuni, è possibile avere diverse subnet per la connettività front-end e back-end o una rete dedicata in una soluzione di monitoraggio o di backup. Questo articolo fornisce comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Per informazioni dettagliate, incluse informazioni sulla creazione di più schede di rete all'interno gli script di PowerShell e altre informazioni sulla [distribuzione macchine virtuali multi-NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diverse [dimensioni macchine Virtuali](virtual-machines-windows-sizes.md) supporto una serie di schede di rete, pertanto la dimensione di una macchina virtuale conseguenza.

>[AZURE.WARNING] Quando si crea una macchina virtuale - non è possibile aggiungere schede a una macchina virtuale esistente, è necessario collegare più schede di rete. È possibile [creare una macchina virtuale basata su disco virtuale originale](virtual-machines-windows-vhd-copy.md) e creare più schede di rete come distribuire la macchina virtuale.

## <a name="create-core-resources"></a>Creare risorse di base
Assicurarsi di avere [più recente Azure PowerShell installato e configurato](../powershell-install-configure.md). Accedere al proprio account Azure:

```powershell
Login-AzureRmAccount
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. I nomi dei parametri di esempio inclusi `myResourceGroup`, `mystorageaccount`, e `myVM`.

Creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `WestUs` percorso:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Creare un account di archiviazione per mettere in attesa nelle macchine virtuali. Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Creare subnet e virtuali
Definire due subnet virtuali - uno per il traffico front-end e uno per il traffico di back-end. Nell'esempio seguente viene definito due subnet, denominata `mySubnetFrontEnd` e `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Creare virtuali e subnet. Nell'esempio seguente viene creata una rete virtuale denominata `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Creare più schede di rete
Creare due schede di rete, il collegamento di una scheda NIC alla subnet front-end e una scheda NIC alla subnet back-end. Nell'esempio seguente viene creato due schede di rete, denominate `myNic1` e `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

In genere si anche creare un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) o [bilanciamento del carico](../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico su nelle macchine virtuali. L'articolo [informazioni più dettagliate macchine Virtuali multi-NIC](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) Guida l'utente tramite la creazione di un gruppo di sicurezza di rete e l'assegnazione di schede di rete.


## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale
Iniziare subito a creare la configurazione di macchine Virtuali. Ogni dimensione memoria virtuale ha un limite per il numero totale di schede di rete che è possibile aggiungere a una macchina virtuale. Altre informazioni sulle [dimensioni dei macchine Virtuali di Windows](virtual-machines-windows-sizes.md). 

Innanzitutto, impostare le credenziali di macchine Virtuali di `$cred` variabile come indicato di seguito:

```powershell
$cred = Get-Credential
```

Nell'esempio seguente definisce una macchina virtuale denominata `myVM` e viene utilizzata una dimensione macchine Virtuali che supporta fino a due schede di rete (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Creare il resto del file config macchine Virtuali. Nell'esempio seguente viene creata una macchina virtuale di Windows Server 2012 R2:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Collegare due schede di rete è stato creato in precedenza:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configurare l'archiviazione e disco virtuale per la nuova macchina virtuale:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Infine, creare una macchina virtuale:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Creazione di più schede di rete utilizzando i modelli di Manager delle risorse
Modelli di gestione risorse Azure usare i file JSON dichiarativi per definire l'ambiente. È possibile leggere una [Panoramica di gestione di risorse di Azure](../azure-resource-manager/resource-group-overview.md). Modelli di Manager delle risorse consentono di creare più istanze di una risorsa durante la distribuzione, ad esempio la creazione di più schede di rete. Utilizzare *Copia* per specificare il numero di istanze per creare:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Altre informazioni sulla [creazione di più istanze tramite *operazioni di copia*](../resource-group-create-multiple.md). 

È inoltre possibile utilizzare una `copyIndex()` a un numero utilizzato per aggiungere un nome di risorsa, che consente di creare `myNic1`, `MyNic2`e così via. Di seguito viene illustrato un esempio di aggiungendo il valore di indice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

È possibile leggere un esempio di completamento della [creazione di più schede di rete utilizzando i modelli di Manager delle risorse](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passaggi successivi
Assicurarsi di esaminare [le dimensioni di macchine Virtuali di Windows](virtual-machines-windows-sizes.md) quando si tenta di creare una macchina virtuale con più schede di rete. Prestare attenzione al numero massimo di NIC supporta le dimensioni di ogni macchina virtuale. 

Tenere presente che non è possibile aggiungere altre schede a una macchina virtuale esistente, è necessario creare tutte le NIC quando si distribuisce la macchina virtuale. Prestare particolare attenzione durante la pianificazione delle distribuzioni per assicurarsi di avere tutto la connettività di rete richiesto dall'inizio.