<properties
    pageTitle="Creare una macchina virtuale SQL Server Azure PowerShell (Manager delle risorse) | Microsoft Azure"
    description="Vengono fornite procedure e gli script di PowerShell per la creazione di una macchina virtuale Azure con immagini raccolta macchina virtuale di SQL Server."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Effettuare il provisioning di una macchina virtuale di SQL Server tramite PowerShell Azure (Manager delle risorse)

> [AZURE.SELECTOR]
- [Portale](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come creare una singola macchina virtuale Azure utilizzando il modello di distribuzione di **Gestione risorse di Azure** uso dei cmdlet di PowerShell di Azure. In questa esercitazione si creerà una singola macchina virtuale utilizzando una sola unità disco da un'immagine nella raccolta SQL. Sarà necessario creare nuovi provider per l'archiviazione, rete e risorse di elaborazione che verranno utilizzate dalla macchina virtuale. Se si dispone di un provider esistenti per una di queste risorse, è possibile utilizzare invece i provider.

Se è necessaria la versione classica di questo argomento, vedere [disposizione una macchina virtuale di SQL Server utilizzando Azure PowerShell classica](virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Prerequisiti di

Per questa esercitazione è necessario:

- Un account Azure e abbonamento prima di iniziare. Se non si ha uno, iscriversi a una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Azure PowerShell)](../powershell-install-configure.md), la versione minima di 1.4.0 o versioni successive (questa esercitazione scritta con versione 1.5.0).
    - Per recuperare la versione in uso, digitare **Get-modulo Azure ListAvailable**.

## <a name="configure-your-subscription"></a>Configurare l'abbonamento

Aprire Windows PowerShell e stabilire l'accesso al proprio account Azure eseguendo il seguente cmdlet. Verrà visualizzata una schermata di accesso immettere le credenziali. Usare lo stesso messaggio di posta elettronica e password utilizzati per accedere al portale di Azure.

    Add-AzureRmAccount

Dopo aver completato l'accesso verranno visualizzate alcune informazioni sullo schermo che include il SubscriptionId con cui è stato effettuato l'accesso in. Si tratta della sottoscrizione in cui le risorse per questa esercitazione verranno create a meno che non si modifica per una sottoscrizione diversa. Se si dispone di più SubscriptionIds, eseguire il cmdlet seguente per visualizzare un elenco di tutte le SubscriptionIds:

    Get-AzureRmSubscription

Per passare a un'altra SubscriptionID, eseguire il cmdlet seguente con il SubscriptionId desiderato.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Definire le variabili di immagine

Per semplificare la facilità di utilizzo e la comprensione dello script completato da questa esercitazione, si inizierà a mediante la definizione di un numero di variabili. Modificare i valori dei parametri come base alle esigenze, prestare attenzione ai denominazione restrizioni riguardanti la lunghezza del nome e caratteri speciali quando si modificano i valori forniti.

### <a name="location-and-resource-group"></a>Percorso e il gruppo di risorse
Utilizzare due variabili per definire l'area dati e il gruppo di risorse in cui è necessario creare altre risorse per la macchina virtuale.

Modificare come desiderato e quindi eseguire i cmdlet seguenti per inizializzare le variabili.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Proprietà di archiviazione

Utilizzare le seguenti variabili per definire l'account di archiviazione e la modalità di archiviazione che verrà utilizzato da macchina virtuale.

Modificare come desiderato e quindi eseguire il seguente cmdlet per inizializzare le variabili. Si noti che, in questo esempio, si sta utilizzando [Lo spazio di archiviazione Premium](../storage/storage-premium-storage.md), che è consigliabile per carichi di lavoro di produzione. Per informazioni dettagliate su questa Guida e le altre indicazioni, vedere [prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Proprietà di rete

Utilizzare le seguenti variabili per definire l'interfaccia di rete, il metodo di allocazione TCP/IP, il nome di rete virtuale, il nome virtuale subnet, l'intervallo di indirizzi IP per la rete virtuale, l'intervallo di indirizzi IP per subnet e l'etichetta del nome di dominio pubblico da utilizzare per la rete nella macchina virtuale.

Modificare come desiderato e quindi eseguire il seguente cmdlet per inizializzare le variabili.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>Proprietà macchina virtuale

Utilizzare le seguenti variabili per definire il nome del computer virtuale, il nome del computer, le dimensioni di macchina virtuale e il nome del disco rigido del sistema operativo per la macchina virtuale.

Modificare come desiderato e quindi eseguire il seguente cmdlet per inizializzare le variabili.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Proprietà delle immagini

Utilizzare le seguenti variabili per definire l'immagine da utilizzare per la macchina virtuale. In questo esempio viene usata l'immagine di SQL Server 2016 Enterprise.

Modificare come desiderato e quindi eseguire il seguente cmdlet per inizializzare le variabili.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Si noti che è possibile ottenere un elenco completo delle offerte di immagine di SQL Server con il comando Get-AzureRmVMImageOffer:

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Ed è possibile visualizzare gli SKU disponibili per un'offerta con il comando Get-AzureRmVMImageSku. Il comando seguente mostra che tutti gli SKU disponibili per **SQL2014SP1 WS2012R2** offrono.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Con il modello di distribuzione di Manager delle risorse, il primo oggetto che si crea è il gruppo di risorse. Il cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) verrà usata per creare un gruppo di risorse Azure e le relative risorse con il nome del gruppo di risorse e il percorso definito da variabili che in precedenza inizializzato.

Eseguire il cmdlet seguente per creare il nuovo gruppo di risorse.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

La macchina virtuale richiede risorse di archiviazione per il disco di sistema operativo e per i file di dati e log di SQL Server. Per semplicità, verrà creato un singolo disco entrambi. È possibile allegare dischi aggiuntivi in un secondo momento utilizzando il cmdlet [Disco Aggiungi Azure](https://msdn.microsoft.com/library/azure/dn495252.aspx) per inserire i dati di SQL Server e accedere file su dischi dedicati. Il cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) verrà usata per creare un account di archiviazione standard nel nuovo gruppo di risorse e con il nome dell'account di archiviazione, un nome di Sku lo spazio di archiviazione e un percorso definito utilizzando le variabili in precedenza inizializzato.

Eseguire il cmdlet seguente per creare un nuovo account di archiviazione.  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Creare le risorse di rete

La macchina virtuale richiede un numero di risorse di rete per la connettività di rete.

- Ogni macchina virtuale richiede una rete virtuale.
- Una rete virtuale deve avere almeno una subnet definita.
- Un'interfaccia di rete deve essere definita con un pubblico o un indirizzo IP privato.

### <a name="create-a-virtual-network-subnet-configuration"></a>Creare una configurazione subnet virtuali

Iniziamo creando una configurazione subnet per la rete virtuale. Per l'esercitazione, verrà creata una subnet predefinita utilizzando il cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) . La configurazione di subnet virtuali verrà creato con il prefisso del nome e indirizzo subnet definito utilizzando le variabili in precedenza inizializzato.

>[AZURE.NOTE] È possibile definire proprietà aggiuntive della configurazione di subnet virtuali utilizzando questo cmdlet, ma che non rientrano nell'ambito di questa esercitazione.

Eseguire il cmdlet seguente per creare la configurazione di subnet virtuale.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Creare una rete virtuale

Successivamente, verrà creato il nostro virtuali mediante il cmdlet [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) . Nel nuovo gruppo di risorse verrà creato il nostro virtuali, con il nome, percorso e l'indirizzo prefisso definito utilizzando le variabili precedentemente inizializzato e usare la configurazione di subnet definita nel passaggio precedente.

Eseguire il cmdlet seguente per creare la rete virtuale.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Creare l'indirizzo IP pubblico

Ora che è stata creata la rete virtuale definita, è necessario configurare un indirizzo IP per la connettività per la macchina virtuale. Per questa esercitazione si creerà un indirizzo IP pubblico utilizzando IP dinamico indirizzamento per supportare la connettività Internet. Il cmdlet [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) verrà usata per creare l'indirizzo IP pubblico in gruppo di risorse creato prevously e con il nome, percorso, il metodo di allocazione ed etichetta di nome di dominio DNS definito utilizzando le variabili in precedenza inizializzato.

>[AZURE.NOTE] È possibile definire proprietà aggiuntive dell'indirizzo IP pubblico questo cmdlet, ma che non rientrano nell'ambito di questa esercitazione iniziale. È anche possibile creare un indirizzo privato o un indirizzo con un indirizzo statico, ma è anche lo scopo di questa esercitazione.

Eseguire il cmdlet seguente per creare l'indirizzo IP pubblico.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Creare l'interfaccia di rete

È ora sono pronti per creare l'interfaccia di rete che verrà utilizzato il nostro macchina virtuale. Il cmdlet [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) verrà usata per creare l'interfaccia di rete nel gruppo di risorse precedentemente creato e con il nome, percorso, subnet e indirizzo IP pubblico definita in precedenza.

Eseguire il cmdlet seguente per creare l'interfaccia di rete.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Configurare un oggetto macchina virtuale

Ora che si dispone di spazio di archiviazione e di rete risorse definite, sono pronti per definire le risorse di calcolo per la macchina virtuale. Per l'esercitazione, si verrà specificare le dimensioni di macchina virtuale e diverse proprietà di sistema operativo, specificare l'interfaccia di rete creato in precedenza, definire archiviazione blob e quindi specificare il disco di sistema operativo.

### <a name="create-the-vm-object"></a>Creare l'oggetto macchine Virtuali

Iniziamo specificando le dimensioni di macchina virtuale. Per questa esercitazione, è in corso che specifica un DS13. Il cmdlet [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) verrà usata per creare un oggetto configurabili macchina virtuale con il nome e dimensioni definiti utilizzando le variabili in precedenza inizializzato.

Eseguire il cmdlet seguente per creare l'oggetto macchina virtuale.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Creare un oggetto di credenziali per mettere in attesa il nome e la password per le credenziali di amministratore locale

È possibile impostare le proprietà del sistema operativo per la macchina virtuale, è necessario immettere le credenziali per l'account di amministratore locale come una stringa protetta. A tale scopo, verrà utilizzato il cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Eseguire il seguente cmdlet e, nella finestra di richiesta di credenziali di Windows PowerShell, digitare il nome e una password da utilizzare per l'account di amministratore locale nella macchina virtuale di Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Impostare le proprietà del sistema operativo per la macchina virtuale

A questo punto si è pronti impostare le proprietà del sistema operativo del computer virtuale. Il cmdlet [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) verrà usata per impostare il tipo del sistema operativo come Windows, richiedere l' [agente di macchina virtuale](virtual-machines-windows-classic-agents-and-extensions.md) per essere installata, specificare che il cmdlet consente di aggiornamento automatico e impostare il nome del computer virtuale, il nome del computer e le credenziali utilizzando le variabili in precedenza inizializzato.

Eseguire il seguente cmdlet per impostare le proprietà del sistema operativo per la macchina virtuale.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Aggiungere l'interfaccia di rete per la macchina virtuale

A questo punto si aggiungerà l'interfaccia di rete che è stato creato in precedenza alla macchina virtuale. Il cmdlet [Aggiungi AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) verrà usata per aggiungere l'interfaccia di rete utilizzando la variabile dell'interfaccia di rete definita in precedenza.

Eseguire il seguente cmdlet per impostare l'interfaccia di rete per la macchina virtuale.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Impostare la posizione di archiviazione blob del disco che verrà utilizzato da macchina virtuale

Abbiamo impostare quindi la posizione di archiviazione blob del disco che verrà utilizzato da macchina virtuale utilizzando le variabili definita in precedenza.

Eseguire il seguente cmdlet per impostare la posizione di archiviazione blob.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Impostare il sistema operativo disco proprietà per la macchina virtuale

Successivamente, verranno impostate il sistema operativo disco per la macchina virtuale. Il cmdlet [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) verrà usata per specificare che il sistema operativo per la macchina virtuale verrà provenire da un'immagine, impostare la memorizzazione nella cache per di sola lettura (poiché viene installato SQL Server sullo stesso disco) e definire il nome e il disco di sistema operativo definita utilizzando le variabili definita in precedenza.

Eseguire il seguente cmdlet per impostare il sistema operativo disco proprietà per la macchina virtuale.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Specificare l'immagine di piattaforma per la macchina virtuale

L'ultimo passaggio consiste nello specificare l'immagine di piattaforma per la macchina virtuale. Per le esercitazioni si utilizza l'immagine di SQL Server 2016 CTP più recente. Il cmdlet [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) verrà usata per usare questa immagine definito dalle variabili definita in precedenza.

Eseguire il seguente cmdlet per specificare l'immagine di piattaforma per la macchina virtuale.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Creare la macchina virtuale SQL

Ora che aver completato la procedura di configurazione, si è pronti creare la macchina virtuale. Il cmdlet [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) verrà usata per creare la macchina virtuale utilizzando le variabili che sono state definite.

Eseguire il cmdlet seguente per creare la macchina virtuale.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

Viene creata la macchina virtuale. Si noti che viene creato un account di archiviazione standard per diagnostica avvio perché l'account di archiviazione specificato per il disco di macchina virtuale un account di archiviazione premium.

È ora possibile visualizzare questo computer nel portale di Azure per visualizzare [l'indirizzo IP pubblico e il relativo nome di dominio completo](virtual-machines-windows-portal-sql-server-provision.md#Connect).  

## <a name="example-script"></a>Script di esempio

Il seguente script contiene lo script di PowerShell completo per questa esercitazione. Si presuppone che sia già imposta la sottoscrizione Azure da utilizzare con i comandi **Aggiungi AzureRmAccount** e **Selezionare AzureRmSubscription** .


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creata la macchina virtuale, si è pronti per connettersi al computer virtuali mediante RDP e configurazione di connettività. Per ulteriori informazioni, vedere [connettersi a un SQL Server virtuale computer Azure (Manager delle risorse)](virtual-machines-windows-sql-connect.md).
