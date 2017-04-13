<properties
   pageTitle="Creare una VM Linux con più schede di rete | Microsoft Azure"
   description="Informazioni su come creare una VM Linux con più schede di rete associate mediante i modelli di CLI Azure o Manager delle risorse."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>Creazione di una VM Linux con più schede di rete
È possibile creare una macchine () in Azure con più interfacce di rete virtuale (NIC) allegate. Scenari comuni, è possibile avere diverse subnet per la connettività front-end e back-end o una rete dedicata in una soluzione di monitoraggio o di backup. Questo articolo fornisce comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Per informazioni dettagliate, incluse informazioni sulla creazione di più schede di rete all'interno di script personalizzati Bash ulteriori informazioni sulla [distribuzione macchine virtuali multi-NIC](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diverse [dimensioni macchine Virtuali](virtual-machines-linux-sizes.md) supporto una serie di schede di rete, pertanto la dimensione di una macchina virtuale conseguenza.

>[AZURE.WARNING] Quando si crea una macchina virtuale - non è possibile aggiungere schede a una macchina virtuale esistente, è necessario collegare più schede di rete. È possibile [creare una macchina virtuale basata su disco virtuale originale](virtual-machines-linux-copy-vm.md) e creare più schede di rete come distribuire la macchina virtuale.

## <a name="quick-commands"></a>Comandi rapidi
Assicurarsi di avere [Azure CLI](../xplat-cli-install.md) eseguito l'accesso e utilizzo della modalità di gestione risorse:

```bash
azure config mode arm
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. I nomi dei parametri di esempio inclusi `myResourceGroup`, `mystorageaccount`, e `myVM`.

Creare un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `WestUS` percorso:

```bash
azure group create myResourceGroup -l WestUS
```

Creare un account di archiviazione per mettere in attesa nelle macchine virtuali. Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Creare una rete virtuale per connettere le macchine virtuali a. Nell'esempio seguente viene creata una rete virtuale denominata `myVnet` con un prefisso di indirizzo di `192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Creare due subnet virtuali - uno per il traffico front-end e uno per il traffico di back-end. Nell'esempio seguente viene creato due subnet, denominata `mySubnetFrontEnd` e `mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Creare due schede di rete, il collegamento di una scheda NIC alla subnet front-end e una scheda NIC alla subnet back-end. Nell'esempio seguente viene creato due schede di rete, denominate `myNic1` e `myNic2`e lo connette le subnet:

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Infine, creare la macchina virtuale, associare due schede di rete è stato creato in precedenza. Nell'esempio seguente viene creata una macchina virtuale denominata `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Creazione di più schede di rete utilizzando CLI Azure
Se è stato creato in precedenza una macchina virtuale utilizzando CLI Azure, comandi rapidi dovrebbero essere noti. Il processo è la stessa operazione per creare uno NIC o più schede di rete. È possibile leggere altre informazioni dettagliate sulla [distribuzione di più schede di rete utilizzando CLI Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), inclusi gli script il processo di ciclo per creare tutte le NIC.

Nell'esempio seguente viene creato due schede di rete, denominate `myNic1` e `myNic2`, con una scheda NIC connettere ogni subnet:

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

In genere si anche creare un [Gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) o [bilanciamento del carico](../load-balancer/load-balancer-overview.md) , per agevolare la gestione e distribuire il traffico su nelle macchine virtuali. Di nuovo i comandi sono uguali quando si utilizzano più schede di rete. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Associare il NIC al gruppo di sicurezza di rete utilizzando `azure network nic set`. Nell'esempio seguente associa `myNic1` e `myNic2` con `myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Quando si crea la macchina virtuale, è ora possibile specificare più schede di rete. Anziché con `--nic-name` per fornire una singola scheda, invece utilizzare `--nic-names` e ottenere un elenco di schede NIC separati da virgola. Inoltre, è necessario prestare attenzione quando si seleziona la dimensione di memoria virtuale. Esistono limiti per il numero totale di schede di rete che è possibile aggiungere a una macchina virtuale. Altre informazioni sulle [dimensioni dei macchine Virtuali Linux](virtual-machines-linux-sizes.md). Nell'esempio seguente viene illustrato come specificare più schede di rete e quindi una dimensione macchine Virtuali che supporta l'utilizzo di più schede di rete (`Standard_DS2_v2`):

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
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

È inoltre possibile utilizzare una `copyIndex()` a un numero utilizzato per aggiungere un nome di risorsa, che consente di creare `myNic1`, `myNic2`e così via. Di seguito viene illustrato un esempio di aggiungendo il valore di indice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

È possibile leggere un esempio di completamento della [creazione di più schede di rete utilizzando i modelli di Manager delle risorse](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passaggi successivi
Assicurarsi di esaminare [le dimensioni di macchine Virtuali Linux](virtual-machines-linux-sizes.md) quando si tenta di creare una macchina virtuale con più schede di rete. Prestare attenzione al numero massimo di NIC supporta le dimensioni di ogni macchina virtuale. 

Tenere presente che non è possibile aggiungere altre schede a una macchina virtuale esistente, è necessario creare tutte le NIC quando si distribuisce la macchina virtuale. Prestare particolare attenzione durante la pianificazione delle distribuzioni per assicurarsi di avere tutto la connettività di rete richiesto dall'inizio.