<properties
    pageTitle="Creare una scala di macchina virtuale impostata tramite PowerShell | Microsoft Azure"
    description="Creare una scala di macchina virtuale impostata tramite PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Creare una scala di macchina virtuale di Windows impostata tramite PowerShell Azure

Questi passaggi un approccio selezione-in di-vuote per la creazione di un set di scala Azure macchina virtuale. Vedere [Panoramica Imposta scala di macchina virtuale](virtual-machine-scale-sets-overview.md) per altre informazioni sui set di scala.

Richiede circa 30 minuti per eseguire la procedura descritta in questo articolo.

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: Installare PowerShell Azure

Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e l'accesso al proprio account.

## <a name="step-2-create-resources"></a>Passaggio 2: Creare le risorse

Creare le risorse sono necessari per il nuovo set di scala.

### <a name="resource-group"></a>Gruppo di risorse

Un set di scala macchina virtuale deve essere contenuto in un gruppo di risorse.

1. Ottenere un elenco delle posizioni disponibili nel punto in cui è possibile inserire risorse:

        Get-AzureLocation | Sort Name | Select Name

2. Selezionare un percorso che adatto alle proprie esigenze, sostituire il valore di **$locName** con il nome della località e quindi si crea la variabile:

        $locName = "location name from the list, such as Central US"

3. Sostituire il valore di **$rgName** con il nome che si desidera utilizzare per il nuovo gruppo di risorse e quindi si crea la variabile: 

        $rgName = "resource group name"
        
4. Creare il gruppo di risorse:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Verrà visualizzato un elemento come in questo esempio:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Account di archiviazione

Un account di archiviazione viene utilizzato da una macchina virtuale per archiviare il disco di sistema operativo e dati di diagnostica utilizzati per il ridimensionamento. Se possibile, è buona norma disporre di un account di archiviazione per ciascuna macchina virtuale creato in un set di scala. Se non è possibile, pianificare non più di 20 macchine virtuali per conto di spazio di archiviazione. L'esempio in questo articolo illustra la creazione di tre macchine virtuali tre account di spazio di archiviazione.

1. Sostituire il valore di **$saName** con un nome per l'account di archiviazione. Verificare il nome per l'univocità. 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Se la risposta è **vera**, il nome proposto sia univoco.

3. Sostituire il valore di **$saType** con il tipo di account di archiviazione e quindi si crea la variabile di:  

        $saType = "storage account type"
        
    I valori possibili sono: Standard_LRS, Standard_GRS, Standard_RAGRS o Premium_LRS.
        
4. Creare l'account:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Verrà visualizzato un elemento come in questo esempio:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Ripetere i passaggi da 1 a 4 per creare tre account di archiviazione, ad esempio myst1, myst2 e myst3.

### <a name="virtual-network"></a>Rete virtuale

Una rete virtuale è necessaria per le macchine virtuali nel set di scala.

1. Sostituire il valore di **$subnetName** con il nome che si desidera utilizzare per la subnet nella rete virtuale e quindi si crea la variabile: 

        $subnetName = "subnet name"
        
2. Creare la configurazione di subnet:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    Il prefisso può essere diverso della rete virtuale.

3. Sostituire il valore di **$netName** con il nome che si desidera utilizzare per la rete virtuale e quindi si crea la variabile: 

        $netName = "virtual network name"
        
4. Creare la rete virtuale:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configurazione del set di scala

Si dispone di tutte le risorse che è necessario per la scala del set di configurazione, pertanto è possibile creare.  

1. Sostituire il valore di **$ipName** con il nome che si desidera utilizzare per la configurazione di indirizzi IP e quindi si crea la variabile: 

        $ipName = "IP configuration name"
        
2. Creare la configurazione IP:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Sostituire il valore di **$vmssConfig** con il nome che si desidera utilizzare per la configurazione di set di scala e quindi si crea la variabile:   

        $vmssConfig = "Scale set configuration name"
        
3. Creare la configurazione per il set di scala:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    In questo esempio mostra che una scala imposta viene creato con tre macchine virtuali. Per ulteriori informazioni sulla capacità di set di scala, vedere [Panoramica di Imposta scala di macchina virtuale](virtual-machine-scale-sets-overview.md) . Questo passaggio include anche l'impostazione della dimensione (definita SkuName) macchine virtuali nel set. Per trovare una dimensione che corrisponde alle proprie esigenze, esaminare [le dimensioni per macchine virtuali](../virtual-machines/virtual-machines-windows-sizes.md).
    
4. Aggiungere la configurazione dell'interfaccia di rete per la configurazione di set di scala:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Verrà visualizzato un elemento come in questo esempio:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Profilo del sistema operativo

1. Sostituire il valore di **$computerName** con il prefisso del nome computer che si desidera utilizzare e quindi si crea la variabile: 

        $computerName = "computer name prefix"
        
2. Sostituire il valore di **$adminName** il nome dell'account di amministratore in macchine virtuali e quindi si crea la variabile:

        $adminName = "administrator account name"
        
3. Sostituire il valore di **$adminPassword** con la password dell'account e quindi si crea la variabile:

        $adminPassword = "password for administrator accounts"
        
4. Creare il profilo del sistema operativo:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Profilo di spazio di archiviazione

1. Sostituire il valore di **$storageProfile** con il nome che si desidera utilizzare per il profilo di spazio di archiviazione e quindi si crea la variabile:  

        $storageProfile = "storage profile name"
        
2. Creare le variabili di definiscono l'immagine da usare:  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Per informazioni su altre immagini da utilizzare, osservare [Naviga e selezionare macchina virtuale Azure immagini con Windows PowerShell e CLI Azure](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Sostituire il valore di **$vhdContainers** con un elenco che contiene i percorsi in cui sono archiviati dischi rigidi virtuali, ad esempio "https://mystorage.blob.core.windows.net/vhds" e quindi si crea la variabile:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Creare il profilo di spazio di archiviazione:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Impostare la scala macchina virtuale

Infine, è possibile creare il set di scala.

1. Sostituire il valore di **$vmssName** con il nome del set di scala macchina virtuale e quindi si crea la variabile:

        $vmssName = "scale set name"
        
2. Creare il set di scala:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Verrà visualizzato un elemento come nell'esempio che mostra una corretta distribuzione:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Passaggio 3: Esplorare le risorse

Utilizzare queste risorse per esplorare il set di scala macchina virtuale creato:

- È disponibile tramite il portale di Azure portale - limitato delle informazioni.
- [Esplora risorse azure](https://resources.azure.com/) - questo strumento più appropriato per l'esplorazione lo stato corrente del set di scala.
- Azure - PowerShell usare questo comando per ottenere informazioni:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>Passaggi successivi

- Gestire l'insieme di scala appena creata utilizzando le informazioni contenute in [gestire macchine virtuali in un Set di scala macchina virtuale](virtual-machine-scale-sets-windows-manage.md)
- È possibile impostare il ridimensionamento automatico di una scala impostata utilizzando le informazioni in [scala macchina virtuale e il ridimensionamento automatico imposta](virtual-machine-scale-sets-autoscale-overview.md)
- Altre informazioni sul ridimensionamento verticale consultando [autoscale verticale con scala macchina virtuale set](virtual-machine-scale-sets-vertical-scale-reprovision.md)
