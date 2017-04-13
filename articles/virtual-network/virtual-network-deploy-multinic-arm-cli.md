<properties
   pageTitle="Distribuirle con più NIC utilizzando CLI Azure in Gestione risorse | Microsoft Azure"
   description="Informazioni su come distribuirle con più NIC utilizzando CLI Azure in Gestione risorse"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-using-the-azure-cli"></a>Distribuirle con più NIC usa CLI Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](virtual-network-deploy-multinic-classic-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Al momento non può contenere macchine virtuali con una singola scheda e macchine virtuali con più schede di rete nello stesso gruppo di risorse. È necessario implementare il server di back-end in un gruppo di risorse diverso rispetto a tutti gli altri componenti. La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per il gruppo di risorse principale e *Back-end IaaSStory* per il server di back-end.

## <a name="prerequisites"></a>Prerequisiti

Prima di distribuire il server di back-end, è necessario distribuire il gruppo di risorse principale con tutte le risorse necessarie per questo scenario. Per distribuire queste risorse, seguire la procedura seguente.

1. Passare alla [pagina il modello](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Nella pagina modello, a destra del **gruppo di risorse padre**, fare clic su **Distribuisci in Azure**.
3. Se necessario, modificare i valori dei parametri, quindi seguire i passaggi nel portale di Azure anteprima per distribuire il gruppo di risorse.

> [AZURE.IMPORTANT] Assicurarsi che i nomi degli account di archiviazione siano univoci. È possibile assegnare i nomi degli account di archiviazione duplicati in Azure.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Distribuire il back-end macchine virtuali

Macchine virtuali back-end dipendono la creazione di risorse elencate di seguito.

- **Account di archiviazione per i dischi dei dati**. Per migliorare le prestazioni, dischi di dati nel server di database utilizzerà la tecnologia unità SSD stato a tinta unita, che richiede un account di archiviazione premium. Verificare che il percorso di Azure distribuire per il supporto di archiviazione premium.
- **Schede di rete**. Ogni macchina virtuale avrà due schede di rete, uno per l'accesso al database e uno per la gestione.
- **Imposta la disponibilità**. Tutti i server di database verranno aggiunto alla disponibilità di una singola impostare, per assicurarsi che almeno una delle macchine virtuali di e l'esecuzione durante la manutenzione.

### <a name="step-1---start-your-script"></a>Passaggio 1 - iniziare uno script

È possibile scaricare lo script bash completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-cli.sh). Seguire la procedura seguente per modificare uno script da utilizzare nel proprio ambiente.

1. Modificare i valori delle variabili in base al gruppo di risorse esistenti distribuito sopra [i prerequisiti](#Prerequisites).

        existingRGName="IaaSStory"
        location="westus"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"
        remoteAccessNSGName="NSG-RemoteAccess"

2. Modificare i valori delle variabili in base ai valori da usare per la distribuzione di back-end.

        backendRGName="IaaSStory-Backend"
        prmStorageAccountName="wtestvnetstorageprm"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        publisher="Canonical"
        offer="UbuntuServer"
        sku="14.04.2-LTS"
        version="latest"
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskName="datadisk"
        nicNamePrefix="NICDB"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

3. Recuperare l'ID per il `BackEnd` subnet in cui verranno create macchine virtuali. È necessario farlo poiché NIC da associare alla subnet presenti in un gruppo di risorse diversi.

        subnetId="$(azure network vnet subnet show --resource-group $existingRGName \
                        --vnet-name $vnetName \
                        --name $backendSubnetName|grep Id)"
        subnetId=${subnetId#*/}

    >[AZURE.TIP] Il primo comando precedente utilizza [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) e [stringhe](http://tldp.org/LDP/abs/html/string-manipulation.html) (in particolare, la rimozione di sottostringa).

4. Recuperare l'ID per il `NSG-RemoteAccess` NSG. È necessario farlo poiché NIC essere associato a questo NSG presenti in un gruppo di risorse diversi.

        nsgId="$(azure network nsg show --resource-group $existingRGName \
                        --name $remoteAccessNSGName|grep Id)"
        nsgId=${nsgId#*/}

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passaggio 2 - Creazione di risorse necessarie per le macchine virtuali

1. Creare un nuovo gruppo di risorse per tutte le risorse back-end. Si noti l'utilizzo della `$backendRGName` variabili per il nome del gruppo di risorse e `$location` per l'area geografica Azure.

        azure group create $backendRGName $location

2. Creare un account di archiviazione premium per il disco del sistema operativo e dati che verrà utilizzato da quello personale più macchine virtuali.

        azure storage account create $prmStorageAccountName \
            --resource-group $backendRGName \
            --location $location \
            --type PLRS

3. Creare una disponibilità impostare per macchine virtuali.

        azure availset create --resource-group $backendRGName \
            --location $location \
            --name $avSetName

### <a name="step-3---create-the-nics-and-backend-vms"></a>Passaggio 3: creare le schede e back-end macchine virtuali

1. Avviare un ciclo per creare più macchine virtuali, in base alla `numberOfVMs` variabili.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Per ogni macchina virtuale, creare una scheda di accesso al database.

            nic1Name=$nicNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x
            azure network nic create --name $nic1Name \
                --resource-group $backendRGName \
                --location $location \
                --private-ip-address $ipAddress1 \
                --subnet-id $subnetId

3. Per ogni macchina virtuale, creare una scheda di rete per l'accesso remoto. Avviso di `--network-security-group` parametro, utilizzato per associare la scheda NIC per un NSG.

            nic2Name=$nicNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x
            azure network nic create --name $nic2Name \
                --resource-group $backendRGName \
                --location $location \
                --private-ip-address $ipAddress2 \
                --subnet-id $subnetId $vnetName \
                --network-security-group-id $nsgId

4. Creare la macchina virtuale.

            azure vm create --resource-group $backendRGName \
                --name $vmNamePrefix$suffixNumber \
                --location $location \
                --vm-size $vmSize \
                --subnet-id $subnetId \
                --availset-name $avSetName \
                --nic-names $nic1Name,$nic2Name \
                --os-type linux \
                --image-urn $publisher:$offer:$sku:$version \
                --storage-account-name $prmStorageAccountName \
                --storage-account-container-name vhds \
                --os-disk-vhd $osDiskName$suffixNumber.vhd \
                --admin-username $username \
                --admin-password $password

5. Per ogni macchina virtuale, creare due dischi di dati e la fine con la `done` comando.

            azure vm disk attach-new --resource-group $backendRGName \
                --vm-name $vmNamePrefix$suffixNumber \        
                --storage-account-name $prmStorageAccountName \
                --storage-account-container-name vhds \
                --vhd-name $dataDiskName$suffixNumber-1.vhd \
                --size-in-gb $diskSize \
                --lun 0

            azure vm disk attach-new --resource-group $backendRGName \
                --vm-name $vmNamePrefix$suffixNumber \        
                --storage-account-name $prmStorageAccountName \
                --storage-account-container-name vhds \
                --vhd-name $dataDiskName$suffixNumber-2.vhd \
                --size-in-gb $diskSize \
                --lun 1
        done


### <a name="step-4---run-the-script"></a>Passaggio 4 - eseguire lo script

Ora che si scaricato e modificato lo script in base alle proprie esigenze, eseguire lo script per creare i macchine virtuali di database back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal terminale **Bash** . Verrà visualizzato l'output iniziale, come illustrato di seguito.

        info:    Executing command group create
        info:    Getting resource group IaaSStory-Backend
        info:    Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command availset create
        info:    Looking up the availability set "ASDB"
        info:    Creating availability set "ASDB"
        info:    availset create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB1-DA"
        info:    Creating network interface "NICDB1-DA"
        info:    Looking up the network interface "NICDB1-DA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-DA
        data:    Name                            : NICDB1-DA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB1-RA"
        info:    Creating network interface "NICDB1-RA"
        info:    Looking up the network interface "NICDB1-RA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-RA
        data:    Name                            : NICDB1-RA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.54
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command vm create
        info:    Looking up the VM "DB1"
        info:    Using the VM Size "Standard_DS3"
        info:    The [OS, Data] Disk or image configuration requires storage account
        info:    Looking up the storage account wtestvnetstorageprm
        info:    Looking up the availability set "ASDB"
        info:    Found an Availability set "ASDB"
        info:    Looking up the NIC "NICDB1-DA"
        info:    Looking up the NIC "NICDB1-RA"
        info:    Creating VM "DB1"

2. Dopo alcuni minuti, terminerà l'esecuzione e il resto dell'output verrà visualizzato come illustrato di seguito.

        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB1"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-1.vhd
        info:    Updating VM "DB1"
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB1"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-2.vhd
        info:    Updating VM "DB1"
        info:    vm disk attach-new command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB2-DA"
        info:    Creating network interface "NICDB2-DA"
        info:    Looking up the network interface "NICDB2-DA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-DA
        data:    Name                            : NICDB2-DA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.5
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICDB2-RA"
        info:    Creating network interface "NICDB2-RA"
        info:    Looking up the network interface "NICDB2-RA"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-RA
        data:    Name                            : NICDB2-RA
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.2.55
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
        data:
        info:    network nic create command OK
        info:    Executing command vm create
        info:    Looking up the VM "DB2"
        info:    Using the VM Size "Standard_DS3"
        info:    The [OS, Data] Disk or image configuration requires storage account
        info:    Looking up the storage account wtestvnetstorageprm
        info:    Looking up the availability set "ASDB"
        info:    Found an Availability set "ASDB"
        info:    Looking up the NIC "NICDB2-DA"
        info:    Looking up the NIC "NICDB2-RA"
        info:    Creating VM "DB2"
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB2"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-1.vhd
        info:    Updating VM "DB2"
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Looking up the VM "DB2"
        info:    Looking up the storage account wtestvnetstorageprm
        info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-2.vhd
        info:    Updating VM "DB2"
        info:    vm disk attach-new command OK
