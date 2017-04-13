<properties
   pageTitle="Accesso e la sicurezza nei modelli di gestione risorse Azure | Microsoft Azure" 
   description="Esercitazione DotNet Core Azure macchina virtuale"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="access-and-security-in-azure-resource-manager-templates"></a>Accesso e la sicurezza nei modelli di Manager delle risorse di Azure

Le applicazioni ospitate in Azure probabile che devono essere accesso tramite internet o una connessione VPN / connessione strada con Azure. Con l'esempio di applicazione archivio musica, il sito web è disponibile su internet con un indirizzo IP pubblico. Con l'accesso stabilito, le connessioni all'applicazione e accesso alle risorse macchina virtuale devono essere protetti. Per la sicurezza di access viene fornita con un gruppo di sicurezza di rete. 

In questo documento dettaglio come l'applicazione dell'archiviazione musica è protetta nel modello di gestione di risorse Azure di esempio. Tutte le dipendenze e configurazioni univoche vengono evidenziate. Per risultati ottimali, distribuire un'istanza della soluzione per l'abbonamento Azure e il lavoro con il modello di gestione di risorse Azure. Il modello completato sono disponibili qui: [Musica archivio distribuzione su Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


## <a name="public-ip-address"></a>Indirizzo IP pubblico

Per consentire l'accesso pubblico a una risorsa Azure, è possibile utilizzare una risorsa indirizzo IP pubblica. Indirizzo IP pubblico può essere configurato con un indirizzo IP statico o dinamico. Se viene utilizzato un indirizzo dinamico e la macchina virtuale viene arrestata e rilasciata, viene rimosso gli indirizzi. Quando il computer viene avviato nuovamente, può essere assegnato un indirizzo IP pubblico diverso. Per impedire la modifica di un indirizzo IP, è possibile utilizzare un indirizzo IP riservato. 

Un indirizzo IP pubblico possono essere aggiunti a un modello di gestione di risorse Azure utilizzando Visual Studio risorsa guidata nuovo o inserendo un JSON valido in un modello. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [Indirizzo IP pubblico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Un indirizzo IP pubblico può essere associato a una scheda di rete virtuale o un servizio di bilanciamento del carico. In questo esempio, perché il sito Web Store musica è il bilanciamento del carico a diversi computer virtuali, l'indirizzo IP pubblico è collegato il servizio di bilanciamento del carico.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [indirizzo IP pubblico associazione con bilanciamento del carico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

L'indirizzo IP pubblico dal portale di Azure punto di vista. Si noti che l'indirizzo IP pubblico è associato a un servizio di bilanciamento del carico e non una macchina virtuale. Servizi di bilanciamento del carico rete sono riportate nel documento successivo di questa serie.

![Indirizzo IP pubblico](./media/virtual-machines-linux-dotnet-core/pubip.png)

Per ulteriori informazioni sugli indirizzi IP pubblico Azure, vedere [gli indirizzi IP in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Gruppo di sicurezza di rete

Una volta stabilita l'accesso alle risorse di Azure, questo accesso dovrebbe essere limitato. Per macchine virtuali Azure, l'accesso sicuro attraverso un gruppo di sicurezza di rete. Con l'esempio di applicazione archivio musica, tutti gli accessi alla macchina virtuale sono limitato ad eccezione di porta 80 per l'accesso http e porta 22 per l'accesso SSH. Un gruppo di sicurezza di rete possono essere aggiunti a un modello di gestione di risorse Azure utilizzando Visual Studio risorsa guidata nuovo o inserendo un JSON valido in un modello.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [Gruppo di sicurezza di rete](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68).

```none
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

In questo esempio, il gruppo di sicurezza di rete è associa con l'oggetto subnet dichiarato nella risorsa virtuali. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [associazione gruppo di sicurezza di rete con virtuali](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158).


```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

Ecco cosa il gruppo di sicurezza di rete è simile dal portale di Azure. Si noti che può essere rappresentato da un NSG per associare un'interfaccia subnet e / o di rete. In questo caso, il NSG è associato a una subnet. In questa configurazione, le regole in entrata applicano a tutte le macchine virtuali connesse alla subnet.

![Gruppo di sicurezza di rete](./media/virtual-machines-linux-dotnet-core/nsg.png)

Per informazioni dettagliate sui gruppi di sicurezza di reti, vedere [che cos'è un gruppo di sicurezza di rete]( https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Passaggio successivo

<hr>

[Passaggio 3 - disponibilità e scalabilità nei modelli di gestione risorse di Azure](./virtual-machines-linux-dotnet-core-4-availability-scale.md)
