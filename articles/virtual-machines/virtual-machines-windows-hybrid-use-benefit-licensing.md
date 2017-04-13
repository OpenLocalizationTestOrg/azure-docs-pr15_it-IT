<properties
   pageTitle="Azure ibrido usare vantaggio per Windows Server | Microsoft Azure"
   description="Informazioni su come aumentare al massimo i vantaggi del proprio Windows Server Software Assurance per portare licenze locale in Azure"
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
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Vantaggi di usare Azure ibrido per Windows Server

Se si utilizza Windows Server con Software Assurance, è possibile rendere le locale licenze di Windows Server Azure ed eseguire macchine virtuali di Windows Server in Azure costi ridotti. Il vantaggio di utilizzare ibrido Azure consente di eseguire macchine virtuali di Windows Server in Azure e ottenere effettuata solo per la velocità di elaborazione di base. Per ulteriori informazioni, vedere [Azure ibrido usare vantaggio licensing pagina](https://azure.microsoft.com/pricing/hybrid-use-benefit/). In questo articolo viene illustrato come distribuire macchine virtuali di Windows Server in Azure per sfruttare questa licenza.

> [AZURE.NOTE] È possibile usare le immagini di Azure Marketplace per distribuirle Windows Server che utilizzano il vantaggio di utilizzare ibrido di Azure. È necessario distribuirle l'uso dei modelli di PowerShell o Manager delle risorse per registrare correttamente nelle macchine virtuali come idoneo per sconto velocità di elaborazione di base.

## <a name="pre-requisites"></a>Prerequisiti
Esistono un paio di prerequisiti per utilizzare Azure ibrido usare vantaggio per Windows Server macchine virtuali di Azure:

- È installato il modulo di Azure PowerShell
- Il disco rigido virtuale di Windows Server caricati allo spazio di archiviazione di Azure

### <a name="install-azure-powershell"></a>Installare PowerShell Azure
Accertarsi di avere [installato e configurato il più recente di Azure PowerShell](../powershell-install-configure.md). Anche se si intende distribuirle l'uso dei modelli di Manager delle risorse, è comunque necessario PowerShell Azure installato per caricare il disco rigido virtuale di Windows Server (vedere la sezione seguente).

### <a name="upload-a-windows-server-vhd"></a>Caricare un disco rigido virtuale di Windows Server

Per distribuire una macchina virtuale Server Windows in Azure, è innanzitutto necessario creare un disco rigido virtuale che contiene la compilazione di Windows Server base. Il disco rigido virtuale deve essere preparato in modo appropriato tramite Sysprep prima di caricarlo in Azure. È possibile [leggere altre informazioni, vedere il disco rigido virtuale requisiti e il processo di Sysprep](./virtual-machines-windows-upload-image.md) e [Il supporto di Sysprep per ruoli del Server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Eseguire il backup della macchina virtuale prima di eseguire Sysprep. Dopo aver preparato il disco rigido virtuale, caricare il disco rigido virtuale per l'account di archiviazione Azure utilizzando il `Add-AzureRmVhd` cmdlet come descritto di seguito:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server e SharePoint Server Dynamics possono essere utilizzate anche la gestione delle licenze di Software Assurance. È comunque necessario preparare l'immagine di Windows Server per l'installazione dei componenti dell'applicazione e fornire codici di licenza di conseguenza, quindi caricare l'immagine del disco Azure. Esaminare la documentazione appropriata per l'esecuzione di Sysprep con l'applicazione, ad esempio [Considerazioni per l'installazione di SQL Server tramite Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) o [creazione di un'immagine di SharePoint Server 2016 riferimento (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

È inoltre possibile leggere altre informazioni sul [caricamento di disco rigido virtuale al processo Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] In questo articolo è incentrato sulla distribuzione macchine virtuali di Windows Server. È inoltre possibile distribuire macchine virtuali di Windows Client nello stesso modo. Nell'esempio seguente, sostituire `Server` con `Client` in modo appropriato.

## <a name="deploy-a-vm-via-powershell-quick-start"></a>Distribuire una macchina virtuale tramite introduttiva PowerShell
Quando si distribuisce il supporto virtuale Server Windows PowerShell, si dispone di un parametro aggiuntivo per `-LicenseType`. Dopo avere inserito il disco rigido virtuale caricamento di Azure, si crea una nuova macchine Virtuali mediante `New-AzureRmVM` e specificare il tipo di licenza come indicato di seguito:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

È possibile [leggere una descrizione più dettagliata sulla distribuzione una macchina virtuale in Azure tramite PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) seguente o Guida descrittivo più lungo le diverse fasi per [creare una macchina virtuale di Windows tramite Gestione risorse e PowerShell](./virtual-machines-windows-ps-create.md).

## <a name="deploy-a-vm-via-resource-manager"></a>Distribuire una macchina virtuale tramite Gestione risorse
All'interno dei modelli di Manager delle risorse, un parametro aggiuntivo per `licenseType` può essere specificato. È possibile leggere informazioni sulla [creazione di modelli di gestione risorse di Azure](../resource-group-authoring-templates.md). Dopo avere inserito il disco rigido virtuale caricamento di Azure, modificare il modello di Manager delle risorse per includere il tipo di licenza come parte del provider di calcolo e distribuire il modello seguendo la normale procedura:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verificare che le macchine Virtuali utilizzano il vantaggio di gestione delle licenze
Dopo aver distribuito le macchine Virtuali tramite uno dei due metodo di distribuzione PowerShell o Manager delle risorse, verificare il tipo di licenza con `Get-AzureRmVM` come indicato di seguito:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

L'output è simile al seguente:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Al contrario distribuita senza licenze Azure ibrido usare Benefit, ad esempio una macchina virtuale distribuita direttamente dalla raccolta di Azure macchina virtuale seguente:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>Descrizione dettagliata di PowerShell

La seguente procedura dettagliata di PowerShell Mostra una distribuzione completa di una macchina virtuale. È possibile leggere maggiore contesto per quanto riguarda i cmdlet effettivo e i diversi componenti che vengono creati in [creare una macchina virtuale di Windows tramite Gestione risorse e PowerShell](./virtual-machines-windows-ps-create.md). Scorrere la creazione del gruppo di risorse, account di archiviazione e rete virtuale, quindi definire le macchine Virtuali si infine crea la macchina virtuale.
 
Prima di tutto, in modo sicuro le credenziali, impostare un percorso e nome gruppo di risorse:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Creare un indirizzo IP pubblico:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Definire le subnet, NIC e VNET:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Assegnare un nome di una macchina virtuale e crea una configurazione macchine Virtuali:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Definire il sistema operativo:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Aggiungere la scheda di rete per la macchina virtuale:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Definire l'account di archiviazione da usare:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Caricare il disco rigido virtuale adeguatamente preparato e connettere la macchina virtuale per l'uso:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Infine, creare la macchina virtuale e definire il tipo di licenza per utilizzare Azure ibrido usare vantaggi:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [licenze di Azure ibrido usare vantaggio](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Ulteriori informazioni [sull'utilizzo dei modelli di Manager delle risorse](../azure-resource-manager/resource-group-overview.md).
