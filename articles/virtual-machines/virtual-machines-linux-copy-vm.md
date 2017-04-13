<properties
    pageTitle="Creare una copia del supporto virtuale Linux Azure | Microsoft Azure"
    description="Informazioni su come creare una copia della macchina virtuale Linux Azure nel modello di distribuzione Manager delle risorse"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Creare una copia di una macchina virtuale Linux in esecuzione in Azure


In questo articolo viene illustrato come creare una copia del Azure macchine () che eseguono Linux utilizzando il modello di distribuzione di Manager delle risorse. Prima di tutto copiare il sistema operativo e dischi di dati in un nuovo contenitore, quindi impostare le risorse di rete e creare la nuova macchina virtuale.

È inoltre possibile [caricare e creare una macchina virtuale da immagine disco personalizzato](virtual-machines-linux-upload-vhd.md).


## <a name="before-you-begin"></a>Prima di iniziare

Verificare che siano soddisfatti i prerequisiti seguenti prima di iniziare la procedura:

- Si dispone di [Azure CLI] (... / xplat-cli-install.md) scaricato e installato nel computer in uso. 

- Sarà necessario anche alcune informazioni sul supporto virtuale Linux Azure esistente:

| Informazioni sul macchine Virtuali di origine | Dove procurarla |
|------------|-----------------|
| Nome macchina virtuale | `azure vm list` |
| Nome del gruppo di risorse | `azure vm list` |
| Posizione | `azure vm list` |
| Nome dell'Account di archiviazione | `azure storage account list -g <resourceGroup>` |
| Nome del contenitore | `azure storage container list -a <sourcestorageaccountname>` |
| Nome del file di origine VM VHD | `azure storage blob list --container <containerName>` |



- È necessario apportare alcune opzioni sulla nuova macchina virtuale:   <br> -Nome contenitore   <br> Nome - macchine Virtuali   <br> Dimensione - memoria virtuale   <br> nome - vNet   <br> -Nome subNet   <br> Nome - IP   <br> Nome - NIC
    

## <a name="login-and-set-your-subscription"></a>Accesso e configurare l'abbonamento

1. Accedere al CLI.
        
        azure login

2. Assicurarsi che siano in modalità di gestione risorse.
    
        azure config mode arm

3. Impostare la sottoscrizione corretta. È possibile utilizzare 'elenco di account azure' per visualizzare tutte le sottoscrizioni.

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>Interrompere la macchina virtuale 

Arrestare e rilasciare macchine Virtuali di origine. È possibile utilizzare 'elenco macchine virtuali azure' per ottenere un elenco di tutte le macchine in abbonamento e le risorse nomi dei gruppi.
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>Copiare il disco rigido virtuale


È possibile copiare il disco rigido virtuale dallo spazio di archiviazione origine di destinazione mediante il `azure storage blob copy start`. In questo esempio, che verranno copiare il disco rigido virtuale per lo stesso account di archiviazione, ma un contenitore diverso.

Per copiare il disco rigido virtuale in un altro contenitore nello stesso account di archiviazione, digitare:

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurare la rete virtuale per la nuova macchina virtuale

Configurare una rete virtuale e scheda di rete per la nuova macchina virtuale. 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>Creare la nuova macchina virtuale 

È ora possibile creare una macchina virtuale dal disco virtuale caricati [utilizzando un modello di gestione delle risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) o tramite CLI specificando URI sul disco copiato digitando:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare Azure CLI per gestire il nuovo computer virtuale, vedere [Azure CLI comandi per la gestione risorse di Azure](azure-cli-arm-commands.md).
