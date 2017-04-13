<properties
   pageTitle="Distribuirle con più NIC tramite PowerShell nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come distribuirle con più NIC tramite PowerShell nel modello di distribuzione classica"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-powershell"></a>Distribuire più macchine virtuali NIC (classica) tramite PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

È possibile creare macchine () in Azure e collegare ognuna delle proprie macchine virtuali più interfacce di rete (NIC). Più schede di rete attivare separazione dei tipi di traffico attraverso NIC. Ad esempio una scheda NIC potrebbe comunicare con Internet, mentre un altro comunica solo con le risorse interne non è connessione a Internet. La possibilità di separare il traffico di rete tra più schede di rete è necessaria per molti rete dispositivi di rete, ad esempio distribuzione delle applicazioni e le soluzioni di ottimizzazione WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Al momento non può contenere macchine virtuali con una singola scheda e macchine virtuali con più schede di rete lo stesso servizio cloud. È necessario implementare il server di back-end in un servizio cloud diverso rispetto a e tutti gli altri componenti lo scenario. La procedura seguente usa un servizio cloud denominato *IaaSStory* per le risorse principale e *Back-end IaaSStory* per il server di back-end.

## <a name="prerequisites"></a>Prerequisiti

Prima di distribuire il server di back-end, è necessario distribuire il servizio cloud principale con tutte le risorse necessarie per questo scenario. Almeno, è necessario creare una rete virtuale con una subnet per back-end. Visitare [Crea virtuali tramite PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) per informazioni su come distribuire una rete virtuale.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Distribuire il back-end macchine virtuali

Macchine virtuali back-end dipendono la creazione di risorse elencate di seguito.

- **Subnet back-end**. I server di database sarà parte di una subnet distinta, separare il traffico. Lo script seguente presuppone subnet esiste un vnet denominato *WTestVnet*.
- **Account di archiviazione per i dischi dei dati**. Per migliorare le prestazioni, dischi di dati nel server di database utilizzerà la tecnologia unità SSD stato a tinta unita, che richiede un account di archiviazione premium. Verificare che il percorso di Azure distribuire per il supporto di archiviazione premium.
- **Imposta la disponibilità**. Tutti i server di database verranno aggiunto alla disponibilità di una singola impostare, per assicurarsi che almeno una delle macchine virtuali di e l'esecuzione durante la manutenzione.

### <a name="step-1---start-your-script"></a>Passaggio 1 - iniziare uno script

È possibile scaricare lo script di PowerShell completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Seguire la procedura seguente per modificare uno script da utilizzare nel proprio ambiente.

1. Modificare i valori delle variabili in base al gruppo di risorse esistenti distribuito sopra [i prerequisiti](#Prerequisites).

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. Modificare i valori delle variabili in base ai valori da usare per la distribuzione di back-end.

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passaggio 2 - Creazione di risorse necessarie per le macchine virtuali

È necessario creare un nuovo servizio cloud e un account di archiviazione per dischi di dati per tutte le macchine virtuali. Inoltre, è necessario specificare un'immagine e un account di amministratore locale per macchine virtuali. Per creare queste risorse, eseguire la procedura seguente.

1. Creare un nuovo servizio cloud.

        New-AzureService -ServiceName $backendCSName -Location $location

2. Creare un nuovo account di archiviazione premium.

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. Impostare l'account di archiviazione creato in precedenza come account di archiviazione corrente per l'abbonamento.

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. Selezionare un'immagine per la macchina virtuale.

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. Impostare le credenziali dell'account di amministratore locale.

        $cred = Get-Credential -Message "Enter username and password for local admin account"

### <a name="step-3---create-vms"></a>Passaggio 3 - creare macchine virtuali

È necessario utilizzare un ciclo per creare tanti macchine virtuali come desiderato e creare gli NIC e macchine virtuali necessari all'interno del ciclo. Per creare le schede di rete e macchine virtuali, eseguire la procedura seguente.

1. Avviare un `for` Esegui ciclo continuo per ripetere i comandi necessari per creare una macchina virtuale e due schede di rete le volte che è necessario, in base al valore della `$numberOfVMs` variabile.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Creare un `VMConfig` oggetto che specifica l'immagine, le dimensioni e la disponibilità impostato per la macchina virtuale.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. Preparare la macchina virtuale come una macchina virtuale di Windows.

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. Impostare il valore predefinito NIC e assegnare un indirizzo IP statico.

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Aggiungere una seconda scheda di rete per ogni macchina virtuale.

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig

6. Creare in dischi di dati per ogni macchina virtuale.

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk1Name `
                -LUN 0       

            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk2Name `
                -LUN 1

7. Creare ogni macchina virtuale e la fine del ciclo.

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }

### <a name="step-4---run-the-script"></a>Passaggio 4 - eseguire lo script

Ora che si scaricato e modificato lo script in base alle proprie esigenze, ha Run script per creare i macchine virtuali di database back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal prompt dei comandi di **PowerShell** o **PowerShell ISE**. Verrà visualizzato l'output iniziale, come illustrato di seguito.

        OperationDescription    OperationId                          OperationStatus
        --------------------    -----------                          ---------------
        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

        WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Compilare le informazioni necessarie al prompt dei comandi di credenziali e fare clic su **OK**. Verrà visualizzato l'output riportata di seguito.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
