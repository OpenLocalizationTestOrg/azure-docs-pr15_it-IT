<properties
   pageTitle="Distribuzione di calcolare le risorse con i modelli di gestione risorse Azure | Microsoft Azure"
   description="Esercitazione DotNet Core Azure macchina virtuale"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-architecture-with-azure-resource-manager-templates"></a>Architettura delle applicazioni con i modelli di Manager delle risorse di Azure

Quando lo sviluppo di una distribuzione di Manager delle risorse di Azure, calcolare requisiti devono essere mappati ai servizi e risorse Azure. Se un'applicazione è costituito da diversi endpoint http, un database e un servizio di memorizzazione dei dati, le risorse Azure che ospitano ogni di questi componenti deve essere ragionato. Ad esempio, l'applicazione dell'archiviazione musica di esempio include un'applicazione web che si trova in una macchina virtuale e un database SQL, è ospitato in database SQL Azure. 

In questo documento dettaglio configurazione delle risorse di elaborazione musica Store nel modello di gestione di risorse Azure di esempio. Tutte le dipendenze e configurazioni univoche vengono evidenziate. Per risultati ottimali, distribuire un'istanza della soluzione per l'abbonamento Azure e il lavoro con il modello di gestione di risorse Azure. Il modello completato sono disponibili qui: [Musica distribuzione Store in Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="virtual-machine"></a>Macchina virtuale

L'applicazione dell'archiviazione musica include un'applicazione web in cui i clienti possono individuare e acquistare musica. Sebbene esistano numerosi servizi Azure che possono contenere le applicazioni web, ad esempio, viene utilizzata una macchina virtuale. Usa il modello di archiviazione musica di esempio, una macchina virtuale viene distribuita, installare un server web e il sito Web Store musica installato e configurato. Per motivi di questo articolo è illustrata solo la distribuzione di macchina virtuale. La configurazione del server web e l'applicazione verrà descritta in dei successivi articoli.

Una macchina virtuale possono essere aggiunti a un modello con la procedura guidata Aggiungi nuova risorsa di Visual Studio o inserendo un JSON valido nel modello di distribuzione. Quando si distribuisce una macchina virtuale, sono necessari anche diverse risorse correlate. In Visual Studio per creare il modello, queste risorse vengono create automaticamente. Se si crea manualmente il modello, queste risorse necessario inserito e configurato.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [JSON macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L285).

```none
{
  {
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
  ........<truncated>  
}
```

Una volta distribuito, le proprietà di macchina virtuale possono essere visualizzate nel portale di Azure.

![Macchina virtuale](./media/virtual-machines-windows-dotnet-core/vm-win.png)

## <a name="storage-account"></a>Account di archiviazione

Gli account di archiviazione sono molte opzioni di archiviazione e funzionalità. Per il contesto di macchine virtuali di Azure, un account di archiviazione contiene dischi rigidi virtuali di macchina virtuale e dischi eventuali dati aggiuntivi. L'esempio musica archivio include un account di archiviazione per mettere in attesa virtuale disco rigido di ogni macchina virtuale nella distribuzione. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse- [Account di archiviazione](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L98).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Un account di archiviazione è associare a una macchina virtuale all'interno di dichiarazione del modello Manager delle risorse della macchina virtuale. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [associazione macchine virtuali e Account di archiviazione](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L321).

```none
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

Dopo la distribuzione, l'account di archiviazione può essere visualizzato nel portale di Azure.

![Account di archiviazione](./media/virtual-machines-windows-dotnet-core/storacct-win.png)

Fare clic su nel contenitore di blob account lo spazio di archiviazione, è possibile visualizzare il file di unità disco rigido virtuale per ogni macchina virtuale distribuito con il modello.

![Dischi rigidi virtuali](./media/virtual-machines-windows-dotnet-core/vhd-win.png)

Per ulteriori informazioni sull'archiviazione Azure, vedere [la documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Rete virtuale

Se una macchina virtuale richiede rete interna, ad esempio la possibilità di comunicare con altre macchine virtuali e risorse Azure, è necessario un virtuali Azure.  Una rete virtuale non rendere accessibile la macchina virtuale tramite internet. Connettività per la pubblica richiede un indirizzo IP pubblico, descritto dettagliatamente più avanti in questa serie.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [virtuali e subnet](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L126).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
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
    ]
  }
}
```

Dal portale di Azure, la rete virtuale si presenta come nella figura seguente. Si noti che tutte le macchine virtuali distribuite con il modello siano collegate alla rete virtuale.

![Rete virtuale](./media/virtual-machines-windows-dotnet-core/vnet-win.png)

## <a name="network-interface"></a>Interfaccia di rete

 Un'interfaccia di rete, una macchina virtuale si connette a una rete, in particolare a una subnet definita nella rete virtuale. 
 
 Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [Interfaccia di rete](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L156).
 
```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceName'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'RDP-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Ogni risorsa macchina virtuale include un profilo di rete. Interfaccia di rete è associata a macchina virtuale in questo profilo.  

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse- [Profilo di rete macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L330).


```none
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceName'), copyindex()))]"
    }
  ]
}
```

Dal portale di Azure, l'interfaccia di rete si presenta come nella figura seguente. L'indirizzo IP interno e associazione macchina virtuale possono essere visualizzati nella risorsa dell'interfaccia di rete.

![Interfaccia di rete](./media/virtual-machines-windows-dotnet-core/nic-win.png)

Per ulteriori informazioni sulle reti virtuali Azure, consultare [Virtuali Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>Database SQL Azure

Oltre a una macchina virtuale hosting del sito Web Store musica, un Database di SQL Azure viene distribuito per ospitare il database di archivio di musica. Il vantaggio derivante dall'uso Database SQL Azure qui è che non è necessaria una seconda serie di macchine virtuali e scala e la disponibilità integrato nel servizio.

Un database SQL Azure può essere aggiunti tramite Visual Studio Aggiungi nuova risorsa guidata, oppure inserendo JSON validi in un modello. Risorsa di SQL Server include un nome utente e la password che dispone dei diritti amministrativi su istanza di SQL. Inoltre, viene aggiunta una risorsa di firewall SQL. Per impostazione predefinita, le applicazioni ospitate in Azure in grado di connettersi con l'istanza SQL. Per consentire l'applicazione esterna ad esempio un SQL Server Management studio per connettersi all'istanza di SQL, il firewall deve essere configurato. Per demo musica Store è fitta la configurazione predefinita. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [DB di SQL Azure](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L379).


```none
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicstoresqlName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('adminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicstoresqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Visualizzazione di SQL server database MusicStore visto nel portale di Azure.

![SQL Server](./media/virtual-machines-windows-dotnet-core/sql-win.png)

Per ulteriori informazioni sulla distribuzione di Database SQL Azure, vedere [la documentazione di Database SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Passaggio successivo

<hr>

[Passaggio 2 - accesso e la sicurezza in Azure modelli Manager delle risorse](./virtual-machines-windows-dotnet-core-3-access-security.md)
