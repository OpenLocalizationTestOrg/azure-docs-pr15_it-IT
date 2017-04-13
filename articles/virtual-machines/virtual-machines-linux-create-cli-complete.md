
<properties
   pageTitle="Creare un ambiente Linux completo tramite CLI Azure | Microsoft Azure"
   description="Creare lo spazio di archiviazione, una VM Linux, una rete virtuale e subnet, un bilanciamento del carico, un pulsante, un indirizzo IP pubblico e un gruppo di sicurezza di rete, tutto da zero utilizzando CLI Azure."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-complete-linux-environment-by-using-the-azure-cli"></a>Creare un ambiente Linux completo tramite CLI Azure

In questo articolo, creiamo una rete semplice con un bilanciamento del carico e una coppia di macchine virtuali che sono utili per lo sviluppo e computing semplice. Abbiamo esaminato il processo di comando, fino a quando non si dispone di due lavorativi, sicura Linux macchine virtuali a cui è possibile connettersi da un punto qualsiasi su Internet. Quindi è possibile passare al reti e ambienti più complessi.

Inoltre, si informazioni sulla gerarchia delle dipendenze che fornisce il modello di distribuzione di Manager delle risorse e sulle quantità power fornisce. Quando viene visualizzata la modalità di compilazione di sistema, è possibile ricreare molto più rapidamente con i [modelli di gestione risorse di Azure](../resource-group-authoring-templates.md). Inoltre, dopo aver appreso come interagiscono tra le parti dell'ambiente, la creazione di modelli per automatizzarle diventa più semplice.

L'ambiente contiene:

- Due macchine virtuali all'interno di un set di disponibilità.
- Un servizio di bilanciamento del carico con una regola di bilanciamento del carico sulla porta 80.
- Regole di gruppo (NSG) di sicurezza per proteggere le macchine Virtuali di traffico indesiderato di rete.

![Panoramica di ambiente di base](./media/virtual-machines-linux-create-cli-complete/environment_overview.png)

Per creare questo ambiente personalizzato, è necessario più recente [Azure CLI](../xplat-cli-install.md) in modalità di gestione risorse (`azure config mode arm`). È necessario anche un JSON dello strumento di analisi. In questo esempio viene usata [jq](https://stedolan.github.io/jq/).

## <a name="quick-commands"></a>Comandi rapidi
Se è necessario eseguire rapidamente attività, i seguenti dettagli della sezione della base comandi per caricare una macchina virtuale in Azure. Ulteriori informazioni e il contesto per istruzioni dettagliate, vedere il resto del documento, iniziale [qui](#detailed-walkthrough).

Assicurarsi di avere [CLI Azure](../xplat-cli-install.md) eseguito l'accesso e utilizzo della modalità di gestione risorse:

```bash
azure config mode arm
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. Includere i nomi dei parametri di esempio `myResourceGroup`, `mystorageaccount`, e `myVM`.

Creare il gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `westeurope` percorso:

```bash
azure group create -n myResourceGroup -l westeurope
```

Verificare il gruppo di risorse utilizzando il parser JSON:

```bash
azure group show myResourceGroup --json | jq '.'
```

Creare l'account di archiviazione. Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount` (il nome dell'account di archiviazione deve essere univoco, quindi immettere il proprio nome univoco):

```bash
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Verificare l'account di archiviazione utilizzando il parser JSON:

```bash
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Creare la rete virtuale. Nell'esempio seguente viene creata una rete virtuale denominata `myVnet`:

```bash
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Creare una subnet. Nell'esempio seguente viene creata una subnet denominata `mySubnet`"

```bash
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Verificare la rete virtuale e subnet utilizzando il parser JSON:


```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Creare un indirizzo IP pubblico. Nell'esempio seguente viene creato un indirizzo IP pubblico denominato `myPublicIP` con il nome DNS del `mypublicdns` (nome DNS deve essere univoco, quindi immettere il proprio nome univoco):

```bash
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Creare il servizio di bilanciamento del carico. Nell'esempio seguente viene creato un bilanciamento del carico denominata `myLoadBalancer`:

```bash
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Creare un pool front-end di indirizzi IP per bilanciamento del carico e associare l'indirizzo IP pubblico. Nell'esempio seguente viene creato un pool front-end IP denominato `mySubnetPool`:

```bash
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool 
```

Creare il pool di indirizzi IP back-end per il servizio di bilanciamento del carico. Nell'esempio seguente viene creato un pool di indirizzi IP di back-end denominato `myBackEndPool`:

```bash
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Creare rete in ingresso SSH indirizzo NAT di conversione regole per il servizio di bilanciamento del carico. Nell'esempio seguente consente di creare due regole di bilanciamento carico, `myLoadBalancerRuleSSH1` e `myLoadBalancerRuleSSH2`:

```bash
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Creare il sito web in ingresso regole NAT per il servizio di bilanciamento del carico. Nell'esempio seguente viene creata una regola di bilanciamento carico denominata`myLoadBalancerRuleWeb`

```bash
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Creare la verifica dell'integrità bilanciamento carico. Nell'esempio seguente viene creato un sondaggio TCP denominata `myHealthProbe`:

```bash
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Verificare il bilanciamento del carico, pool di indirizzi IP e le regole NAT utilizzando parser JSON:

```bash
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Creare la prima scheda di rete (NIC). Sostituire il `#####-###-###` sezioni con il proprio ID abbonamento Azure. L'abbonamento ID è indicato nell'output di `jq` per esaminare le risorse che si sta creando. È inoltre possibile visualizzare l'ID di abbonamento con `azure account list`. 

Nell'esempio seguente viene creato un NIC denominata `myNic1`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Creare la seconda scheda di rete. Nell'esempio seguente viene creato un pulsante denominato `myNic2`:

```bash
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Verificare due schede di rete utilizzando il parser JSON:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Creare il gruppo di sicurezza di rete. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```bash
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Aggiungere due le regole in entrata per il gruppo di sicurezza di rete. Nell'esempio seguente vengono create due regole, `myNetworkSecurityGroupRuleSSH` e `myNetworkSecurityGroupRuleHTTP`:

```bash
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Verificare il gruppo di sicurezza di rete e le regole in entrata utilizzando parser JSON:

```bash
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Associare il gruppo di sicurezza di rete a due schede di rete:

```bash
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Creare il set di disponibilità. Nell'esempio seguente viene creato una disponibilità set denominata `myAvailabilitySet`:

```bash
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Creare il primo VM Linux. Nell'esempio seguente viene creata una macchina virtuale denominata `myVM1`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Creare la seconda VM Linux. Nell'esempio seguente viene creata una macchina virtuale denominata `myVM2`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
```

Utilizzare il parser JSON per verificare che tutto ciò che è stato creato:

```bash
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Esportare il nuovo ambiente in un modello per creare rapidamente nuovamente nuove istanze:

```bash
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Descrizione dettagliata
I passaggi che seguono illustrano ogni comando operazioni eseguite durante la compilazione di fuori dell'ambiente. Questi concetti sono utili quando si creano ambienti personalizzati per lo sviluppo o produzione.

Assicurarsi di avere [CLI Azure](../xplat-cli-install.md) eseguito l'accesso e utilizzo della modalità di gestione risorse:

```bash
azure config mode arm
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. Includere i nomi dei parametri di esempio `myResourceGroup`, `mystorageaccount`, e `myVM`.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Creare gruppi di risorse e scegliere percorsi di distribuzione

Gruppi di risorse Azure sono entità distribuzione logica che contengono le informazioni sulla configurazione e i metadati per attivare la gestione logica delle distribuzioni di risorsa. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `westeurope` percorso:

```bash
azure group create --name myResourceGroup --location westeurope
```

Output:

```bash                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

È necessario disporre gli account di archiviazione per i dischi macchine Virtuali e per qualsiasi disco dati aggiuntivi che si desidera aggiungere. Creare gli account di archiviazione quasi immediatamente dopo la creazione di gruppi di risorse.

Nell'esempio si utilizza il `azure storage account create` comando, passando il percorso dell'account, il gruppo di risorse che i controlli e il tipo di supporto di archiviazione desiderato. Nell'esempio seguente viene creato un account di archiviazione denominato `mystorageaccount`:

```bash
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Output:

```bash
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Per esaminare il gruppo di risorse utilizzando il `azure group show` comando, è possibile utilizzare lo strumento [jq](https://stedolan.github.io/jq/) lungo con la `--json` opzione CLI Azure. (È possibile utilizzare **jsawk** o qualsiasi raccolta lingua che si preferisce per analizzare il JSON.)

```bash
azure group show myResourceGroup --json | jq '.'
```

Output:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Per visualizzare informazioni sugli account di archiviazione tramite CLI, è innanzitutto necessario impostare i tasti e i nomi degli account. Sostituire il nome dell'account di archiviazione nell'esempio seguente con un nome che si è scelto:

```
AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

È possibile visualizzare le informazioni dello spazio di archiviazione facilmente:

```bash
azure storage container list
```

Output:

```bash
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e subnet

Successivamente si intende necessarie per creare una rete virtuale in esecuzione in Azure e una subnet in cui è possibile creare nelle macchine virtuali. Nell'esempio seguente viene creata una rete virtuale denominata `myVnet` con la `192.168.0.0/16` prefisso indirizzo:

```bash
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16 
```

Output:

```bash
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Nuovo, utilizzare l'opzione - json di `azure group show` e **jq** per vedere come creare le risorse. Avere un `storageAccounts` delle risorse e un `virtualNetworks` risorse.  

```bash
azure group show myResourceGroup --json | jq '.'
```

Output:

```bash
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

A questo punto creiamo una subnet nel `myVnet` virtuali in cui vengono distribuite macchine virtuali. Viene utilizzata la `azure network vnet subnet create` comando insieme risorse abbiamo creato: il `myResourceGroup` gruppo di risorse e la `myVnet` virtuali. Nell'esempio seguente si aggiunge alla subnet denominata `mySubnet` con il prefisso di indirizzo subnet `192.168.1.0/24`:

```bash
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Output:

```bash
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Perché la subnet è all'interno della rete virtuale, è esaminare le informazioni di subnet con un comando leggermente diverso. Il comando serve `azure network vnet show`, ma è stato possibile continuare a esaminare l'output JSON utilizzando **jq**.

```bash
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Output:

```bash
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address-pip"></a>Creare un indirizzo IP pubblico (PIP)

A questo punto creare l'indirizzo IP pubblico (PIP) assegnato al servizio di bilanciamento del carico. Consente di connettere nelle macchine virtuali da Internet mediante la `azure network public-ip create` comando. Poiché l'indirizzo predefinito è dinamico, viene creata una voce denominata DNS nel dominio di **cloudapp.azure.com** utilizzando il `--domain-name-label` opzione. Nell'esempio seguente viene creato un indirizzo IP pubblico denominato `myPublicIP` con il nome DNS del `mypublicdns`. Il nome DNS deve essere univoco, immettere il proprio nome DNS univoco:

```bash
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Output:

```bash
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

L'indirizzo IP pubblico è una risorsa principale, in modo da visualizzare con `azure group show`.

```bash
azure group show myResourceGroup --json | jq '.'
```

Output:

```bash
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

È possibile esaminare ulteriori dettagli della risorsa, inclusi il nome di dominio completo (FQDN) del sottodominio, utilizzando il completamento `azure network public-ip show` comando. La risorsa indirizzo IP pubblica allocata in modo logico, ma non è stato ancora assegnato un indirizzo specifico. Per ottenere un indirizzo IP, sarà necessario un bilanciamento del carico, che non è stata ancora creata.

```bash
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Output:

```bash
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Creare un bilanciamento del carico e pool di indirizzi IP
Quando si crea un bilanciamento del carico, è possibile distribuire il traffico su più macchine virtuali. Fornisce inoltre ridondanza all'applicazione eseguendo più macchine virtuali che rispondono alle richieste dell'utente in caso di manutenzione o sovraccarico. Nell'esempio seguente viene creato un bilanciamento del carico denominata `myLoadBalancer`:

```bash
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Output:

```bash
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Il servizio di bilanciamento del carico è abbastanza vuoto, pertanto creiamo alcuni pool di indirizzi IP. Si desidera creare due pool di indirizzi IP per il bilanciamento del carico - uno per front-end e uno per back-end. Il pool front-end IP è visibile pubblicamente. È anche il percorso in cui viene assegnato il PIP creato in precedenza. Quindi il pool di back-end è utilizzato come un percorso per il nostro macchine virtuali a cui connettersi. In questo modo l'il traffico attraverso il bilanciamento del carico alle macchine virtuali.

Prima di tutto, creare il pool di indirizzi IP front-end. Nell'esempio seguente viene creato un pool front-end denominato `myFrontEndPool`:

```bash
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool 
```

Output:

```bash
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Si noti come è stato usato il `--public-ip-name` passa a passare la `myPublicIP` creata in precedenza. Assegnare l'indirizzo IP pubblico per il servizio di bilanciamento del carico consente di raggiungere nelle macchine virtuali su Internet.

Successivamente, creare il secondo pool IP, questa volta per il traffico di back-end. Nell'esempio seguente viene creato un pool di back-end denominato `myBackEndPool`:

```bash
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Output:

```bash
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

È possibile vedere come il bilanciamento del carico procede controllando con `azure network lb show` ed esaminare l'output JSON:

```bash
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Output:

```bash
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Creare regole NAT di bilanciamento del carico
Per ottenere traffico attraverso il servizio di bilanciamento del carico, è necessario creare regole di conversione che specificano azioni in ingresso o in uscita indirizzo di rete. È possibile specificare il protocollo da utilizzare e quindi eseguire il mapping porte esterne a porte interne in base alle esigenze. Per l'ambiente, creare alcune regole che consentono di SSH tramite il bilanciamento del carico per il nostro macchine virtuali. È necessario impostare le porte TCP 4222 e 4223 indirizzare alla porta TCP 22 nostro macchine virtuali (che viene creata in un secondo momento). Nell'esempio seguente viene creata una regola denominata `myLoadBalancerRuleSSH1` per eseguire il mapping di porte TCP 4222 alla porta 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Output:

```bash
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Ripetere la procedura per la seconda regola NAT per SSH. Nell'esempio seguente viene creata una regola denominata `myLoadBalancerRuleSSH2` per eseguire il mapping di porte TCP 4223 alla porta 22:

```bash
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Di seguito anche procedere e creare una regola NAT per la porta TCP 80 per il traffico web, collegare la regola per il pool di indirizzi IP. Se è collegare la regola al pool di indirizzi IP, invece di associazione di regola per il nostro macchine virtuali singolarmente, è possibile aggiungere o rimuovere macchine virtuali dal pool di indirizzi IP. Bilanciamento del carico vengono regolate automaticamente il flusso di traffico. Nell'esempio seguente viene creata una regola denominata `myLoadBalancerRuleWeb` per eseguire il mapping di porta TCP 80 alla porta 80:

```bash
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Output:

```bash
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Creare una verifica dell'integrità di bilanciamento carico

Dello stato presenza periodicamente controlli in macchine virtuali che si trovano dietro il bilanciamento del carico per assicurarsi che vengono operativo e rispondere alle richieste come definito. In caso contrario, vengono rimossi per garantire che gli utenti che non vengano indirizzati a tali. È possibile definire controlli personalizzati per la verifica dell'integrità della, oltre a intervalli e valori di timeout. Per ulteriori informazioni sull'integrità ricerche, vedere [ricerche di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md). Nell'esempio seguente viene creato un TCP integrità sono state esaminate denominato `myHealthProbe`:

```bash
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Output:

```bash
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

In questo caso, è specificato un intervallo di 15 secondi per i controlli di integrità. È possibile perdere un massimo di quattro ricerche (un minuto) prima di bilanciamento ritiene che l'host non funziona.

## <a name="verify-the-load-balancer"></a>Verificare il bilanciamento del carico
Terminata la configurazione di bilanciamento del carico. Ecco i passaggi:

1. Crea un bilanciamento del carico.
2. Quindi creato un pool di indirizzi IP front-end e assegnato un indirizzo IP pubblico.
3. Successivo che è stato creato un pool di indirizzi IP di back-end in grado di connettersi a macchine virtuali.
4. Successivamente, si create le regole NAT che consentono di SSH macchine virtuali per la gestione, insieme a una regola che consenta la porta TCP 80 per il web app.
5. Infine è stato aggiunto una verifica dell'integrità per controllare periodicamente le macchine virtuali. La verifica dell'integrità garantisce che gli utenti non tentano di accedere a una macchina virtuale non funziona o che servono contenuto.

Esaminiamo il bilanciamento del carico aspetto ora:

```bash
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Output:

```bash
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Creare una scheda di rete per l'uso con VM Linux

NIC sono disponibili a livello di programmazione perché è possibile applicare regole all'uso. È anche possibile più monitor. Di seguito `azure network nic create` comando agganciare NIC al pool di indirizzi IP di carico back-end e associare la regola NAT per consentire il traffico SSH.
 
Sostituire il `#####-###-###` sezioni con il proprio ID abbonamento Azure. L'abbonamento ID è indicato nell'output di `jq` per esaminare le risorse che si sta creando. È inoltre possibile visualizzare l'ID di abbonamento con `azure account list`. 

Nell'esempio seguente viene creato un NIC denominata `myNic1`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Output:

```bash
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

È possibile visualizzare i dettagli esaminando direttamente la risorsa. Esaminare le risorse usando la `azure network nic show` comando:

```bash
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Output:

```bash
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Ora è creare NIC secondo, collegare al nostro pool di indirizzi IP di back-end. Questa regola NAT ora la seconda consente il traffico SSH. Nell'esempio seguente viene creato un NIC denominata `myNic2`:

```bash
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Creare un gruppo di sicurezza di rete e le regole

Ora è creare un gruppo di sicurezza di rete e le regole in entrata che regolano l'accesso alla scheda. Un gruppo di sicurezza di rete possono essere applicato a una scheda di rete o subnet. Definire regole per controllare il flusso di traffico da e verso il macchine virtuali. Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

È possibile aggiungere la regola in entrata per NSG consentire le connessioni in ingresso sulla porta 22 (per il supporto SSH). Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRuleSSH` per consentire la porta TCP 22:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

A questo punto aggiungere la regola in entrata per NSG consentire le connessioni in ingresso sulla porta 80 (per supportare il traffico web). Nell'esempio seguente viene creata una regola denominata `myNetworkSecurityGroupRuleHTTP` per consentire la porta TCP 80:

```bash
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [AZURE.NOTE] Regola in entrata è un filtro per le connessioni in ingresso. In questo esempio, si associa il NSG alla NIC virtuale macchine virtuali, il che significa che qualsiasi richiesta di porta 22 è passato tramite alla scheda il nostro macchine Virtuali. Questa regola in entrata è su una connessione di rete e non su un endpoint, che è cosa sarebbe nelle distribuzioni classiche. Per aprire una porta, è necessario lasciare la `--source-port-range` impostata su '\*' (valore predefinito) per accettare le richieste in ingresso provenienti da **qualsiasi** richiesta di una porta. Porte sono in genere dinamiche.

## <a name="bind-to-the-nic"></a>Associare la scheda di rete

Associare il NSG per le schede di rete. È necessario connettere il nostro NIC con il gruppo di sicurezza di rete. Eseguire entrambi i comandi, collegare entrambe le schede di rete:

```bash
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```bash
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Creare un set di disponibilità
Disponibilità imposta Guida diffusione nelle macchine virtuali tra domini di errore e l'aggiornamento. Creare una disponibilità impostare per le macchine virtuali. Nell'esempio seguente viene creato una disponibilità set denominata `myAvailabilitySet`:

```bash
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Domini guasto definiscono un raggruppamento macchine virtuali che condividono un parametro di origine e di rete power comune. Per impostazione predefinita, le macchine virtuali che vengono configurate all'interno di un set di disponibilità sono separate domini guasto fino a tre. L'idea è un problema hardware in uno di questi domini guasto non influisce ogni macchina virtuale che esegue l'app. Azure distribuisce automaticamente macchine virtuali tra i domini di errore durante il posizionamento in un set di disponibilità.

Aggiornamento domini indicano i gruppi di macchine virtuali e hardware fisico sottostante che può essere riavviato nello stesso momento. L'ordine in cui sono riavviati domini aggiornamento potrebbe non essere sequenza durante la manutenzione pianificata, ma solo un aggiornamento riavvio alla volta. Nuovo Azure distribuisce automaticamente nelle macchine virtuali domini aggiornamento quando posizionandole in un sito di disponibilità.

Altre informazioni su come [gestire la disponibilità di macchine virtuali](./virtual-machines-linux-manage-availability.md).

## <a name="create-the-linux-vms"></a>Creare le macchine virtuali Linux

È stata creata le risorse di archiviazione e di rete per supportare macchine virtuali accessibili da Internet. A questo punto verrà creato quelle macchine virtuali e proteggerli con un tasto di SSH che non dispone di una password. In questo caso, verranno per creare un Ubuntu macchine Virtuali in base a risultati più recente. È necessario individuare le informazioni di immagine utilizzando `azure vm image list`, come descritto nella [ricerca di immagini di macchine Virtuali di Azure](virtual-machines-linux-cli-ps-findimage.md).

È selezionata un'immagine usando il comando `azure vm image list westeurope canonical | grep LTS`. In questo caso, viene utilizzato `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Per l'ultimo campo passiamo `latest` in modo che in futuro è sempre accedere build più recente. (Stringa serve `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

In questo passaggio successivo è familiare a qualsiasi utente che ha già creato un ssh chiave pubblica e privata rsa associare nel Mac o Linux utilizzando **ssh keygen-t rsa -b 2048**. Se non si dispone qualsiasi coppie di parole chiave di certificato `~/.ssh` directory, è possibile crearli:

- Automaticamente, utilizzando il `azure vm create --generate-ssh-keys` opzione.
- Manualmente, utilizzando [le istruzioni per creare tali cartelle](virtual-machines-linux-mac-create-ssh-keys.md).

In alternativa, è possibile utilizzare il `--admin-password` metodo per l'autenticazione delle connessioni SSH dopo aver creata la macchina virtuale. Questo metodo è in genere meno sicuro.

Viene creata la macchina virtuale da portare tutte le risorse e le informazioni in combinazione con la `azure vm create` comando:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Output:

```bash
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

È possibile connettersi immediatamente la macchina virtuale utilizzando le chiavi SSH predefinito. Assicurarsi che specificare la porta appropriata poiché passaggio tramite il servizio di bilanciamento del carico. (Per il primo macchine Virtuali, si imposta la NAT regola per inoltrare porta 4222 a nostro macchine Virtuali):

```bash
 ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Output:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Procedere e creazione di una seconda macchina virtuale nello stesso modo:

```bash
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username ops
```

Ed è ora possibile utilizzare il `azure vm show myResourceGroup myVM1` comando per esaminare è stata creata. A questo punto, si esegue il macchine virtuali Ubuntu dietro un bilanciamento del carico in Azure che non è possibile accedere a solo con la coppia di chiavi SSH (perché le password sono disabilitate). È possibile installare nginx o httpd, distribuire un'app web e visualizzare il traffico passano attraverso il bilanciamento del carico per entrambe le macchine virtuali.

```bash
azure vm show --resource-group myResourceGroup --name myVM1
```

Output:

```bash
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Esportare l'ambiente come modello
Dopo aver creato il questo ambiente, cosa accade se si desidera creare un ambiente di sviluppo aggiuntivi con gli stessi parametri o un ambiente di produzione di corrispondenti? Manager delle risorse vengono utilizzati modelli JSON che definiscono tutti i parametri per l'ambiente. Creare ambienti interi facendo riferimento a questo modello JSON. È possibile [creare modelli di JSON manualmente](../resource-group-authoring-templates.md) o esportare un ambiente esistente per creare il modello JSON necessità:

```bash
azure group export --name myResourceGroup
```

Questo comando consente di creare il `myResourceGroup.json` file nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, viene chiesto di tutti i nomi delle risorse, inclusi i nomi di bilanciamento del carico, le interfacce di rete o macchine virtuali. È possibile inserire i nomi seguenti nel file di modello aggiungendo il `-p` o `--includeParameterDefaultValue` parametro per il `azure group export` comando illustrata in precedenza. Modificare il modello JSON per specificare i nomi delle risorse o [creare un file parameters.json](../resource-group-authoring-templates.md#parameters) che specifica i nomi delle risorse.

Per creare un ambiente in base al modello:

```bash
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

È consigliabile leggere [altre informazioni sulla distribuzione di modelli](../resource-group-template-deploy-cli.md). Informazioni su come aggiornare gli ambienti, utilizzare il file di parametri e accedere ai modelli da una posizione di archiviazione in modo incrementale.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti iniziare a lavorare con più componenti di rete e macchine virtuali. È possibile usare questo ambiente di esempio per creare l'applicazione tramite i componenti di base introdotti qui.
