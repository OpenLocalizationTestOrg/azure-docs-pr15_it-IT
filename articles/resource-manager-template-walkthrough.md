<properties
   pageTitle="Procedura dettagliata modello Manager delle risorse | Microsoft Azure"
   description="Una procedura dettagliata passo dopo passo di un modello di gestione delle risorse il provisioning di un'architettura di Azure IaaS base."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>Procedura dettagliata modello Manager delle risorse

Una delle prime domande durante la creazione di un modello è "come avviare". Una possibile iniziare da un modello vuoto, seguendo la struttura di base descritta nell' [articolo modello creazione](resource-group-authoring-templates.md#template-format)e aggiungere le risorse e parametri appropriati e le variabili. Una buona alternativa, è possibile avviare attraverso la [raccolta Guida introduttiva](https://github.com/Azure/azure-quickstart-templates) e cercare scenari simili a quello che si vuole creare. È possibile unire diversi modelli o modificarne uno esistente per soddisfare il proprio scenario specifico. 

Esaminiamo un'infrastruttura comune:

* Due macchine virtuali che utilizzano lo stesso account di archiviazione sono nello stesso set di disponibilità e della stessa subnet di una rete virtuale.
* Un singolo NIC e VM l'indirizzo IP per ogni macchina virtuale.
* Un servizio di bilanciamento del carico regola sulla porta 80 di bilanciamento del carico

![architettura](./media/resource-group-overview/arm_arch.png)

In questo argomento illustra i passaggi della creazione di un modello di Manager delle risorse per tale infrastruttura. Il modello finale che si crea è basato su un modello di Guida introduttiva chiamato [2 macchine virtuali in un servizio di bilanciamento del carico e le regole di bilanciamento del carico](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Tuttavia, che è molto creare in una sola volta, pertanto è necessario prima creare un account di archiviazione e distribuirlo. Dopo che in grado di utilizzare la creazione di account di archiviazione, si verrà aggiunto altre risorse e ridistribuire il modello per completare l'infrastruttura.

>[AZURE.NOTE] È possibile utilizzare qualsiasi tipo di editor durante la creazione del modello. Visual Studio fornisce strumenti che semplificano lo sviluppo di modello, ma non è necessario Visual Studio per completare questa esercitazione. Per un'esercitazione sull'utilizzo di Visual Studio per creare una distribuzione Web App e Database SQL, vedere [creazione e la distribuzione di gruppi di risorse Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## <a name="create-the-resource-manager-template"></a>Creare il modello di Manager delle risorse

Il modello è un file JSON che definisce tutte le risorse che verrà distribuito. Consente inoltre per definire i parametri specificati durante la distribuzione, variabili costruite da altri valori e le espressioni e gli output di distribuzione. 

Iniziamo con il modello più semplice:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Salvare il file come **azuredeploy.json** (si noti che il modello può avere il nome desiderato, solo che non deve essere un file json).

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
All'interno della sezione di **risorse** , aggiungere un oggetto che definisce l'account di archiviazione, come illustrato di seguito. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Per comprendere le proprietà e i valori di provengono. Proprietà **tipo**, **nome**, **apiVersion**e **posizione** sono elementi standard che sono disponibili per tutti i tipi di risorse. Per informazioni sugli elementi comuni [risorse](resource-group-authoring-templates.md#resources). **nome** è impostato su un valore di parametro è passare durante la distribuzione e **posizione** come percorso utilizzato da un gruppo di risorse. Vengono illustrate come si determinano **tipo** e **apiVersion** nelle sezioni seguenti.

La sezione **proprietà** contiene tutte le proprietà univoche per un determinato tipo. I valori specificati in questa sezione esattamente corrispondano l'operazione di caricamento in API REST per la creazione di quel tipo di risorsa. Quando si crea un account di archiviazione, è necessario specificare un **accountType**. Si noti dell' [API REST per la creazione di un account di archiviazione](https://msdn.microsoft.com/library/azure/mt163564.aspx) che la sezione proprietà dell'operazione di resto contiene anche una proprietà **accountType** e indicati i valori consentiti. In questo esempio, il tipo di account è impostato su **Standard_LRS**, ma è possibile specificare un altro valore o consentire agli utenti di passare al tipo di account come parametro di.

A questo punto si passare alla sezione **parametri** e vedere come si definisce il nome dell'account di archiviazione. Sono disponibili ulteriori informazioni sull'utilizzo di parametri [parametri](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Di seguito è definito un parametro di tipo stringa che conterrà il nome dell'account di archiviazione. Il valore di questo parametro verrà fornito durante la distribuzione dei modelli.

## <a name="deploying-the-template"></a>Distribuire il modello
Si dispone di un modello completo per la creazione di un nuovo account di archiviazione. Come si ricorderà, il modello è stato salvato nel file **azuredeploy.json** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Esistono alcuni modi per distribuire un modello, come illustrato nell' [articolo di distribuzione delle risorse](resource-group-template-deploy.md). Per distribuire il modello tramite PowerShell di Azure, usare:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

In alternativa, per distribuire il modello utilizzando CLI Azure, usare:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

A questo punto si è proprietari di lieta di un account di archiviazione!

Passaggi successivi sarà per aggiungere tutte le risorse necessarie per distribuire l'architettura all'inizio di questa esercitazione. Queste risorse verranno aggiunti nello stesso modello che stanno lavorando.

## <a name="availability-set"></a>Set di disponibilità
Dopo la definizione dell'account di archiviazione, aggiungere un disponibilità impostato per le macchine virtuali. Sono non disponibili in questo caso, nessuna proprietà aggiuntiva obbligatoria, in modo che la relativa definizione è semplice. Nel caso in cui si desidera definire l'aggiornamento dominio conta. numeri e guasto dominio contare valori, vedere l' [API REST per la creazione di un Set di disponibilità](https://msdn.microsoft.com/library/azure/mt163607.aspx) per la sezione proprietà completo.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Si noti che il **nome** viene impostato sul valore di una variabile. Per questo modello, il nome del set di disponibilità è necessarie in vari punti. È possibile gestire più facilmente il modello che definisce il valore di una volta e utilizzando in varie posizioni.

Il valore specificato per **tipo** contiene il provider di risorse e il tipo di risorsa. Per i set di disponibilità, il provider di risorse è **Microsoft.Compute** e il tipo di risorsa è **availabilitySets**. È possibile ottenere l'elenco dei provider di risorse disponibili eseguendo il seguente comando di PowerShell:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

In alternativa, se si usa CLI Azure, è possibile eseguire il comando seguente:
```
    azure provider list
```
Dato che in questo argomento si sta creando con gli account di archiviazione, macchine virtuali e reti virtuali, si utilizzeranno:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Per visualizzare i tipi di risorse per un particolare provider, eseguire il comando PowerShell seguente:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

In alternativa, per CLI Azure, il comando seguente restituirà i tipi disponibili nel formato JSON e salvarlo in un file.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Verrà visualizzato **availabilitySets** come uno dei tipi di **Microsoft.Compute**. Il nome completo del tipo è **Microsoft.Compute/availabilitySets**. È possibile determinare il nome del tipo di risorsa per tutte le risorse nel modello di è.

## <a name="public-ip"></a>IP pubblico
Definire un indirizzo IP pubblico. Osservare nuovamente l' [API REST per gli indirizzi IP pubblici](https://msdn.microsoft.com/library/azure/mt163590.aspx) per le proprietà impostare.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

Il metodo di allocazione è impostato su **dinamico** ma è possibile impostare il valore è necessario o impostarlo in modo che accettano un valore di parametro. È stata attivata gli utenti del modello per passare a un valore per l'etichetta di nome di dominio.

A questo punto, esaminiamo come determinare è **apiVersion**. Il valore specificato è sufficiente corrisponde alla versione dell'API REST che si desidera utilizzare per la creazione della risorsa. Pertanto, è possibile esaminare la documentazione API REST per il tipo di risorsa. In alternativa, è possibile eseguire il seguente comando di PowerShell per un particolare tipo.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Che restituisce i valori seguenti:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Per visualizzare le versioni di API con Azure CLI, eseguire lo stesso comando **Mostra provider azure** mostrato in precedenza.

Quando si crea un nuovo modello, selezionare la versione più recente di API.

## <a name="virtual-network-and-subnet"></a>Subnet e virtuali
Creare una rete virtuale con una subnet. Esaminare l' [API REST per le reti virtuali](https://msdn.microsoft.com/library/azure/mt163661.aspx) per tutte le proprietà impostare.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Bilanciamento del carico
A questo punto si creerà un bilanciamento del carico esposto esterni. Poiché il servizio di bilanciamento del carico utilizza l'indirizzo IP pubblico, è necessario dichiarare una dipendenza sull'indirizzo IP pubblico nella sezione **dependsOn** . Questo errore indica che il bilanciamento del carico non vengono distribuita fino al completamento della distribuzione l'indirizzo IP pubblico. Senza definire la relazione, si riceverà un errore perché Gestione risorse tenterà di distribuire le risorse in parallelo e tenterà di configurare il servizio di bilanciamento del carico all'indirizzo IP pubblico che non esiste ancora. 

Con una verifica tcp sulla porta 80 nella definizione delle risorse, è necessario creare un pool di indirizzi back-end, un paio delle regole in entrata NAT a RDP in macchine virtuali e una regola di bilanciamento del carico. Estrazione [API REST di bilanciamento del carico](https://msdn.microsoft.com/library/azure/mt163574.aspx) per tutte le proprietà.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Interfaccia di rete
È necessario creare 2 interfacce di rete, uno per ogni macchina virtuale. Invece di dover includere le voci duplicate interfacce di rete, è possibile utilizzare la [funzione copyIndex()](resource-group-create-multiple.md) per scorrere il ciclo di copia (denominato nicLoop) e creare le interfacce di rete numeriche come definito nella `numberOfInstances` variabili. Interfaccia di rete dipende dalla creazione di bilanciamento del carico e la rete virtuale. Utilizza subnet definita nella creazione virtuali e l'id di bilanciamento del carico per configurare il pool di indirizzi bilanciamento carico e le regole NAT in entrata.
Esaminare l' [API REST per interfacce di rete](https://msdn.microsoft.com/library/azure/mt163668.aspx) per tutte le proprietà.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Macchina virtuale
È necessario creare macchine virtuali 2, utilizzando la funzione copyIndex(), seguendo la creazione di [interfacce di rete](#network-interface).
La creazione di macchine Virtuali dipende dall'account di archiviazione set interfaccia e la disponibilità di rete. Questa macchina virtuale verrà creata da un'immagine marketplace, come definito nella `storageProfile` proprietà - `imageReference` viene utilizzato per definire il publisher immagine, offerta, sku e la versione. Infine, viene configurato un profilo di diagnostico per attivare la diagnostica per la macchina virtuale. 

Per trovare le relative proprietà per un'immagine marketplace, eseguire gli articoli [Selezionare Linux macchina virtuale immagini](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) o [immagini macchina virtuale di Windows](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) .

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Per le immagini pubblicate da **3 ° fornitori di terze parti**, sarà necessario specificare un'altra proprietà denominata `plan`. Un esempio disponibili in [questo modello](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) dalla raccolta Guida introduttiva. 

Terminata la definizione delle risorse per il modello.

## <a name="parameters"></a>Parametri

Nella sezione parametri definire i valori che è possono specificare quando si distribuisce il modello. Solo definire i parametri di valori che si ritiene che può variare durante la distribuzione. È possibile fornire un valore predefinito per un parametro utilizzato se non viene specificato durante la distribuzione. È inoltre possibile definire i valori consentiti, come illustrato per il parametro **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variabili

Nella sezione variabili, è possibile definire valori utilizzati in più posizioni nel modello scelto o valori che sono costruiti da altre espressioni o variabili. Variabili vengono utilizzate per semplificare la sintassi del modello.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

È stata completata il modello. È possibile confrontare il modello in base al modello nella [raccolta Guida introduttiva](https://github.com/Azure/azure-quickstart-templates) in [macchine 2 virtuali con bilanciamento del carico e caricare il modello di regole bilanciamento](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)completo. Il modello potrebbe essere leggermente diverso in base sull'uso di diversi numeri di versione. 

È possibile distribuire nuovamente il modello con gli stessi comandi usato per la distribuzione di account di archiviazione. Non è necessario eliminare l'account di archiviazione prima di distribuire nuovamente perché Gestione risorse ignorerà ricreazione risorse che esistono già e non sono stati modificati.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Manager delle risorse modello visualizzatore (ARMViz)](http://armviz.io/#/) è un ottimo strumento per visualizzare i modelli di ARM quando diventano troppo grande per comprendere solo di leggere il file json.
- Per ulteriori informazioni sulla struttura di un modello, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md).
- Per informazioni sulla distribuzione di un modello, vedere [distribuire un gruppo di risorse con il modello di gestione risorse di Azure](resource-group-template-deploy.md)
