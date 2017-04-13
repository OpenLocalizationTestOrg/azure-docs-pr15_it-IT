<properties
    pageTitle="Creare una macchina virtuale Azure tramite PowerShell | Microsoft Azure"
    description="Consente di creare facilmente una nuova macchina virtuale che eseguono Windows Server Azure PowerShell e gestione di risorse di Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Creare una macchina virtuale di Windows tramite Gestione risorse e PowerShell

In questo articolo viene illustrato come creare rapidamente una macchina virtuale Azure che eseguono Windows Server e le risorse che necessarie utilizzando [Manager delle risorse](../azure-resource-manager/resource-group-overview.md) e PowerShell. 

Tutti i passaggi descritti in questo articolo sono necessari per creare una macchina virtuale e impiega circa 30 minuti per eseguire la procedura. Sostituire i valori di parametro esempio nei comandi con nomi che è possibile utilizzare per l'ambiente.

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: Installare PowerShell Azure

Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni sull'installazione della versione più recente di PowerShell di Azure, selezionare l'abbonamento e l'accesso al proprio account.
        
## <a name="step-2-create-a-resource-group"></a>Passaggio 2: Creare un gruppo di risorse

Tutte le risorse devono essere contenuti in un gruppo di risorse, pertanto consente di creare prima di tutto.  

1. È possibile ottenere un elenco delle posizioni disponibili nel punto in cui è possono creare le risorse.

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Impostare la posizione per le risorse. Questo comando imposta la posizione **centralus**.

    ```powershell
    $location = "centralus"
    ```
    
3. Creare un gruppo di risorse. Questo comando Crea gruppo di risorse denominato **myResourceGroup** nella posizione impostate.

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>Passaggio 3: Creare un account di archiviazione

Un [account di archiviazione](../storage/storage-introduction.md) necessario per archiviare il disco rigido virtuale che viene utilizzato da un computer virtuali creati. I nomi degli account di archiviazione deve essere compreso tra 3 e 24 caratteri e può contenere solo lettere minuscole e numeri.

1. Verificare il nome dell'account di archiviazione per l'univocità. Questo comando verifica il nome **myStorageAccount**.

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    Se il comando restituisce **True**, il nome proposto sia univoco all'interno di Azure. 
    
2. A questo punto, creare l'account di archiviazione.
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>Passaggio 4: Creare una rete virtuale

Macchine virtuali che fanno parte di una [rete virtuale](../virtual-network/virtual-networks-overview.md).

1. Creare una subnet per la rete virtuale. Questo comando crea una subnet denominata **mySubnet** con un prefisso di indirizzo di 10.0.0.0/24.
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. A questo punto, creare la rete virtuale. Questo comando crea una rete virtuale denominata **myVnet** utilizzando la subnet che è stato creato e un prefisso di **10.0.0.0/16**indirizzo.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Passaggio 5: Creare un'interfaccia di rete e indirizzo IP pubblica

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, è necessario disporre di un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare l'indirizzo IP pubblico. Questo comando crea un indirizzo IP pubblico denominato **myPublicIp** con un metodo di allocazione di **dinamico**.
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. Creare l'interfaccia di rete. Questo comando consente di creare un'interfaccia di rete denominata **myNIC**.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>Passaggio 6: Creare una macchina virtuale

Dopo aver creato tutti gli elementi in posizione, è possibile creare la macchina virtuale.

1. Eseguire questo comando per impostare il nome dell'account amministratore e la password per la macchina virtuale.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    La password deve essere installato 12-123 caratteri e avere almeno una lettera minuscola, un carattere di lettere maiuscole, un numero e un carattere speciale. 
        
2. Creare l'oggetto di configurazione per la macchina virtuale. Questo comando crea un oggetto di configurazione denominato **myVmConfig** che definisce il nome della macchina virtuale e le dimensioni della macchina virtuale.

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    Per un elenco di formati disponibili per una macchina virtuale, vedere [dimensioni per macchine virtuali in Azure](virtual-machines-windows-sizes.md) .
    
3. Configurare le impostazioni di sistema operativo per la macchina virtuale. Questo comando imposta il nome del computer, tipo di sistema operativo e le credenziali dell'account per la macchina virtuale.

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. Definire l'immagine da utilizzare per effettuare il provisioning macchina virtuale. Questo comando consente di definire l'immagine di Windows Server da utilizzare per la macchina virtuale. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    Per ulteriori informazioni sulla selezione di immagini da utilizzare, vedere [Naviga e selezionare immagini macchina virtuale di Windows Azure con PowerShell o CLI](virtual-machines-windows-cli-ps-findimage.md).
        
5. Aggiungere l'interfaccia di rete che è stato creato per la configurazione.

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. Definire il nome e il percorso del disco rigido macchine Virtuali. In un contenitore è archiviato il file di disco rigido virtuale. Questo comando crea il disco in un contenitore denominato **vhds/WindowsVMosDisk.vhd** nell'account di archiviazione che è stato creato.

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. Aggiungere informazioni sul disco rigido sistema operativo per la configurazione di macchine Virtuali. Sostituire il valore di **$diskName** con un nome per il disco di sistema operativo. Creare la variabile di e aggiungere le informazioni su disco alla configurazione.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. Infine, creare la macchina virtuale.

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con la distribuzione, un passaggio successivo, è possibile esaminare [le distribuzioni di risoluzione dei problemi delle risorse gruppo il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Informazioni su come gestire la macchina virtuale creata consultando [macchine virtuali di gestione tramite Gestione risorse di Azure e PowerShell](virtual-machines-windows-ps-manage.md).
- Vantaggi offerti da un modello per creare una macchina virtuale in base alle informazioni in [creare una macchina virtuale di Windows con un modello di Manager delle risorse](virtual-machines-windows-ps-template.md)
