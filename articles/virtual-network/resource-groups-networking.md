<properties
   pageTitle="Cenni preliminari sul Provider di risorse di rete | Microsoft Azure"
   description="Informazioni sui Provider di risorse di rete in Gestione risorse di Azure nuovo"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="network-resource-provider"></a>Provider di risorse di rete
Un'esigenza supplementare nel successo dell'azienda odierna, è la possibilità di creare e gestire applicazioni di rete su larga scala in modo agile, flessibile, sicuro e ripetibile. Manager delle risorse Azure (ARM) consente di creare tali applicazioni, come una singola raccolta di risorse in gruppi di risorse. Ad esempio risorse vengono gestite tramite diversi provider di risorse in ARM.

Gestione risorse di Azure si basa sui provider di risorse diversi per consentire l'accesso alle risorse. Esistono tre provider di risorse principale: rete, lo spazio di archiviazione ed elaborazione. In questo documento vengono illustrate le caratteristiche e vantaggi del Provider di risorse di rete, tra cui:

- **Metadati** : è possibile aggiungere informazioni alle risorse utilizzando i tag. Questi tag possono essere utilizzati per tenere traccia di utilizzo delle risorse nelle sottoscrizioni e gruppi di risorse.
- **Maggiore controllo della rete** - rete risorse siano strettamente collegate ed è possibile controllare loro in modo più dettagliato. Questo significa che è maggiore flessibilità nella gestione risorse di rete.
- **Configurazione processore** - perché le risorse di rete siano strettamente collegate, è possibile creare e gestire le risorse di rete in parallelo. Questo è notevolmente ridotta momento della configurazione.
- **Controllo dell'accesso basato su ruoli** - RBAC fornisce i ruoli predefiniti, con ambito di protezione specifico, oltre a consentire la creazione di ruoli personalizzati per la gestione della protezione.
- **Agevolare la gestione e distribuzione** -, è possibile distribuire e gestire applicazioni, dal momento che è possibile creare una serie intera applicazione come un'unica raccolta di risorse in un gruppo di risorse. E più veloce per distribuire, dal momento che è possibile distribuire fornendo semplicemente un payload JSON modello.
- **Personalizzazione rapida** , è possibile utilizzare i modelli di stile dichiarativo per consentire ripetibile e rapido la personalizzazione delle installazioni.
- **Personalizzazione ripetibile** - è possibile utilizzare i modelli di stile dichiarativo per consentire ripetibile e rapido la personalizzazione delle installazioni.
- **Interfacce di gestione** - è possibile utilizzare una delle seguenti interfacce per gestire le risorse:
    - RESTO in base all'API
    - PowerShell
    - .NET SDK
    - Node SDK
    - Linguaggio SDK
    - CLI Azure
    - Portale di anteprima
    - Lingua modello ARM

## <a name="network-resources"></a>Risorse di rete
È ora possibile gestire le risorse di rete in modo indipendente, anziché affidare a loro gestiti tramite una risorsa di elaborazione singola (una macchina virtuale). In questo modo un maggiore flessibilità e flessibilità quando si creano un'infrastruttura complessa e su larga scala in un gruppo di risorse.

Visualizzazione concettuale di una distribuzione di esempio che comportano un'applicazione a più livelli viene presentata di seguito. Ogni risorsa che viene visualizzato, ad esempio schede di rete, gli indirizzi IP pubblici e macchine virtuali, può essere gestito in modo indipendente.

![Modello di risorse di rete](./media/resource-groups-networking/Figure2.png)

Tutte le risorse contiene un insieme comune di proprietà e le relative proprietà singoli impostate. Le proprietà comuni sono:

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**nome**|Nome di risorsa univoco. Ogni tipo di risorsa è i proprio restrizioni di denominazione.|PIP01, VM01, NIC01|
|**posizione**|Azure area geografica in cui si trova la risorsa|westus, eastus|
|**ID**|Identificazione URI in base a|/Subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

È possibile controllare le singole proprietà delle risorse nelle sezioni seguenti.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfacce di gestione
È possibile gestire le risorse di rete Azure utilizzando diverse interfacce. In questo documento è dedicata due di tali interfacce: API REST e i modelli.

### <a name="rest-api"></a>API REST
Come detto in precedenza, le risorse di rete possono essere gestite tramite una vasta gamma di interfacce, tra cui API REST, .NET SDK, Node SDK, linguaggio SDK, PowerShell, CLI, portale Azure e modelli.

L'API Rest rispettare le specifiche del protocollo HTTP 1.1. La struttura URI generale dell'API viene presentata di seguito:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

I parametri e tra parentesi graffe rappresentano gli elementi seguenti:

- **id abbonamento** : l'id di abbonamento Azure.
- **spazio dei nomi delle risorse provider** - spazio dei nomi per il provider in uso. Il valore per il provider di risorse di rete è *Microsoft.Network*.
- **nome dell'area** - il nome dell'area di Azure

Quando le chiamate all'API REST, sono supportati i seguenti metodi HTTP:

- **Inserire** - usati per creare una risorsa di un determinato tipo modificare una proprietà di risorse o modificare un'associazione tra le risorse.
- È possibile **ottenere** - utilizzati per recuperare le informazioni per una risorsa di provisioning.
- **Eliminare** - utilizzato per eliminare una risorsa esistente.

Richiesta e risposta conforme a un formato del carico utile JSON. Per ulteriori informazioni, vedere [Azure API di gestione delle risorse](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="arm-template-language"></a>Lingua modello ARM
Oltre a gestire le risorse imperativo (tramite API o SDK), è anche possibile utilizzare uno stile di programmazione dichiarativo per creare e gestire le risorse di rete utilizzando la lingua del modello ARM.

Una rappresentazione di esempio di un modello di seguito:

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

Il modello è principalmente una descrizione JSON delle risorse e i valori di istanza inseriti tramite parametri. Nell'esempio seguente può essere usata per creare una rete virtuale con 2 subnet.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Si ha la possibilità di fornire i valori dei parametri manualmente quando si usa un modello o è possibile utilizzare un file di parametri. Nell'esempio seguente mostra un set di possibili valori dei parametri per l'uso con il modello:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Vantaggi principali di uso dei modelli sono:

- È possibile creare un'infrastruttura complessa in un gruppo di risorse in uno stile dichiarativo. Orchestrazione di creazione di risorse, inclusa la gestione delle dipendenze, viene gestita da ARM.
- L'infrastruttura di può essere creata in modo ripetibile attraverso varie regioni e in un'area modificando semplicemente parametri.
- Stile dichiarativo conduce ad più breve tempo di anticipo in creazione di modelli e implementazione dell'infrastruttura.

Per i modelli di esempio, vedere [Guida introduttiva di Azure modelli](https://github.com/Azure/azure-quickstart-templates).

Per ulteriori informazioni sul linguaggio modello ARM, vedere [Lingua del modello di Azure Manager delle risorse](../resource-group-authoring-templates.md).

Nell'esempio di modello precedente utilizza la rete virtuale e risorse della subnet. Sono disponibili altre risorse di rete che è possibile utilizzare come indicato di seguito:

### <a name="using-a-template"></a>Utilizzo di un modello

È possibile distribuire i servizi in Azure da un modello tramite PowerShell, AzureCLI, o se eseguire un clic di distribuzione da GitHub. Per distribuire i servizi da un modello in GitHub, eseguire la procedura seguente:

1. Aprire il file template3 da GitHub. Ad esempio, aprire [rete virtuale con due subnet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Fare clic su **Distribuisci in Azure**e quindi accedere al portale di Azure con le credenziali.
3. Verificare il modello e quindi fare clic su **Salva**.
4. Fare clic su **Modifica i parametri** e selezionare una posizione, ad esempio *Occidentale degli Stati Uniti*, vnet e subnet.
5. Se necessario, modificare i parametri **ADDRESSPREFIX** e **SUBNETPREFIX** e quindi fare clic su **OK**.
6. Fare clic su **Seleziona un gruppo di risorse** e quindi fare clic sul gruppo di risorse a che si desidera aggiungere la vnet e subnet. In alternativa, è possibile creare un nuovo gruppo di risorse, fare clic su **o Crea nuovo**.
3. Fare clic su **Crea**. Si noti il riquadro visualizzazione **la distribuzione dei modelli di Provisioning**. Una volta completata la distribuzione, si verrà visualizzata una schermata simile a quella riportata di seguito.

![Distribuzione dei modelli di esempio](./media/resource-groups-networking/Figure6.png)


## <a name="next-steps"></a>Passaggi successivi

[Lingua del modello di Azure Manager delle risorse](../resource-group-authoring-templates.md)

[Azure Networking – utilizzati modelli](https://github.com/Azure/azure-quickstart-templates)

[Calcolare Provider di risorse](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

[Panoramica di gestione risorse Azure](../azure-resource-manager/resource-group-overview.md)
