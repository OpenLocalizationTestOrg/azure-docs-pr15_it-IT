<properties
   pageTitle="Distribuirle con più NIC utilizzando CLI Azure nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come distribuirle con più NIC utilizzando CLI Azure nel modello di distribuzione classica"
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

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>Distribuirle con più NIC (classico) usa CLI Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

È possibile creare macchine () in Azure e collegare ognuna delle proprie macchine virtuali più interfacce di rete (NIC). Più schede di rete attivare separazione dei tipi di traffico attraverso NIC. Ad esempio una scheda NIC potrebbe comunicare con Internet, mentre un altro comunica solo con le risorse interne non è connessione a Internet. La possibilità di separare il traffico di rete tra più schede di rete è necessaria per molti rete dispositivi di rete, ad esempio distribuzione delle applicazioni e le soluzioni di ottimizzazione WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Al momento non può contenere macchine virtuali con una singola scheda e macchine virtuali con più schede di rete lo stesso servizio cloud. È necessario implementare il server di back-end in un servizio cloud diverso rispetto a e tutti gli altri componenti lo scenario. La procedura seguente usa un servizio cloud denominato *IaaSStory* per le risorse principale e *Back-end IaaSStory* per il server di back-end.

## <a name="prerequisites"></a>Prerequisiti

Prima di distribuire il server di back-end, è necessario distribuire il servizio cloud principale con tutte le risorse necessarie per questo scenario. Almeno, è necessario creare una rete virtuale con una subnet per back-end. Visitare [Crea virtuali mediante CLI Azure](virtual-networks-create-vnet-classic-cli.md) per informazioni su come distribuire una rete virtuale.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Distribuire il back-end macchine virtuali

Macchine virtuali back-end dipendono la creazione di risorse elencate di seguito.

- **Account di archiviazione per i dischi dei dati**. Per migliorare le prestazioni, dischi di dati nel server di database utilizzerà la tecnologia unità SSD stato a tinta unita, che richiede un account di archiviazione premium. Verificare che il percorso di Azure distribuire per il supporto di archiviazione premium.
- **Schede di rete**. Ogni macchina virtuale avrà due schede di rete, uno per l'accesso al database e uno per la gestione.
- **Imposta la disponibilità**. Tutti i server di database verranno aggiunto alla disponibilità di una singola impostare, per assicurarsi che almeno una delle macchine virtuali di e l'esecuzione durante la manutenzione.

### <a name="step-1---start-your-script"></a>Passaggio 1 - iniziare uno script

È possibile scaricare lo script bash completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Seguire la procedura seguente per modificare uno script da utilizzare nel proprio ambiente.

1. Modificare i valori delle variabili in base al gruppo di risorse esistenti distribuito sopra [i prerequisiti](#Prerequisites).

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. Modificare i valori delle variabili in base ai valori da usare per la distribuzione di back-end.

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passaggio 2 - Creazione di risorse necessarie per le macchine virtuali

1. Creare un nuovo servizio cloud per tutte le macchine virtuali back-end. Si noti l'utilizzo della `$backendCSName` variabili per il nome del gruppo di risorse e `$location` per l'area geografica Azure.

        azure service create --serviceName $backendCSName \
            --location $location

2. Creare un account di archiviazione premium per il disco del sistema operativo e dati che verrà utilizzato da quello personale più macchine virtuali.

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>Passaggio 3 - creare macchine virtuali con più schede di rete

1. Avviare un ciclo per creare più macchine virtuali, in base alla `numberOfVMs` variabili.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Per ogni macchina virtuale, specificare il nome e l'indirizzo IP di ciascuna delle due schede di rete.

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. Creare la macchina virtuale. Si noti l'utilizzo della `--nic-config` parametro, contenente un elenco di tutte le NIC con nome, subnet e indirizzo IP.

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Creare due dischi di dati per ogni macchina virtuale.

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>Passaggio 4 - eseguire lo script

Ora che si scaricato e modificato lo script in base alle proprie esigenze, eseguire lo script per creare i macchine virtuali di database back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal terminale **Bash** . Verrà visualizzato l'output iniziale, come illustrato di seguito.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Dopo alcuni minuti, terminerà l'esecuzione e il resto dell'output verrà visualizzato come illustrato di seguito.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
