<properties
   pageTitle="Disponibilità e scalabilità nei modelli di gestione risorse Azure | Microsoft Azure"
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

# <a name="availability-and-scale-in-azure-resource-manager-templates"></a>Disponibilità e scalabilità nei modelli di Manager delle risorse di Azure

Disponibilità e scalabilità fanno riferimento a tempi di attività e la possibilità di soddisfare la domanda. Se un'applicazione deve essere del 99,9% del tempo, è necessario disporre di un'architettura che consente di più risorse di elaborazione simultanee. Anziché un unico sito Web, ad esempio, una configurazione con un livello superiore della disponibilità include più istanze del sito stesso, con tecnologia davanti loro di bilanciamento. In questa configurazione, può essere creata un'istanza dell'applicazione verso il basso per la manutenzione, mentre il rimanente continuano a funzionare. Scala indica invece la capacità di applicazioni per servire richiesta. Con un carico di un'applicazione, aggiungendo o rimuovendo istanze del pool consente a un'applicazione scalare per rispondere alla domanda.

In questo documento dettaglio come la distribuzione di esempio musica Store è configurata per la disponibilità e scala. Tutte le dipendenze e configurazioni univoche vengono evidenziate. Per risultati ottimali, distribuire un'istanza della soluzione per l'abbonamento Azure e il lavoro con il modello di gestione di risorse Azure. Il modello completato sono disponibili qui: [Musica archivio distribuzione su Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="availability-set"></a>Set di disponibilità

Un Set di disponibilità in modo logico si estende su macchine virtuali di Azure attraverso host fisici e altri componenti infrastrutturali, ad esempio alimentazione e fisico hardware di rete. Set di disponibilità assicurarsi che durante la manutenzione, errore o altri tempi di inattività, non tutte le macchine virtuali sono effettuate. Un Set di disponibilità possono essere aggiunti a un modello di gestione di risorse Azure usando Visual Studio risorsa guidata nuovo o inserimento JSON validi in un modello.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [Configurare la disponibilità](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L387).


```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "avalibility-set"
  },
  "properties": {
    "platformUpdateDomainCount": 5,
    "platformFaultDomainCount": 3
  }
}
```

Un Set di disponibilità viene dichiarato come una proprietà di una risorsa macchina virtuale. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [configurare la disponibilità di associazione con macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L313).


```none
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Disponibilità impostare visto dal portale di Azure. Ogni macchina virtuale e informazioni dettagliate sulla configurazione sono riportate di seguito.

![Set di disponibilità](./media/virtual-machines-linux-dotnet-core/aset.png)

Per informazioni dettagliate sui set di disponibilità, vedere [gestire la disponibilità di macchine virtuali](./virtual-machines-linux-manage-availability.md). 

## <a name="network-load-balancer"></a>Bilanciamento del carico di rete

Mentre un set di disponibilità fornisce tolleranza applicazione, un bilanciamento del carico rende disponibili numerose istanze dell'applicazione in un singolo indirizzo di rete. Più istanze di un'applicazione possono essere ospitate in numerose macchine virtuali, ognuno di essi connesso a un servizio di bilanciamento del carico. Come si accede l'applicazione, bilanciamento del carico indirizza la richiesta in arrivo tra i membri di allegati. Un servizio di bilanciamento del carico possono essere aggiunti tramite Visual Studio risorsa guidata nuovo oppure inserendo correttamente formattato risorsa JSON nel modello di gestione di risorse Azure.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [Bilanciamento del carico di rete](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-front"
  },
  ........<truncated>
}
```

Poiché l'applicazione di esempio è esposto a internet con un indirizzo IP pubblico, questo indirizzo è associato il bilanciamento del carico. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [associazione di bilanciamento del carico di rete con indirizzo IP pubblico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L221).

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

Dal portale di Azure, la panoramica di bilanciamento carico di rete mostra l'associazione con l'indirizzo IP pubblico.

![Bilanciamento del carico di rete](./media/virtual-machines-linux-dotnet-core/nlb.png)

## <a name="load-balancer-rule"></a>Regola di bilanciamento carico

Quando si usa un bilanciamento del carico, le regole sono configurate che controllano come il traffico viene bilanciato tra le risorse desiderate. Con l'applicazione di archiviazione musica di esempio, il traffico arriva sulla porta 80 dell'indirizzo IP pubblico e distribuito in porta 80 di tutte le macchine virtuali. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di Manager delle risorse: [Regola di bilanciamento del carico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270).


```none
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

Visualizzazione della regola bilanciamento carico di rete dal portale.

![Regola di bilanciamento carico di rete](./media/virtual-machines-linux-dotnet-core/lbrule.png)

## <a name="load-balancer-probe"></a>Verifica di bilanciamento carico

Bilanciamento del carico deve inoltre monitorare ogni macchina virtuale in modo che le richieste sono disponibili solo ai sistemi. Questo controllo viene eseguita tramite sondaggio costante di una porta predefinita. La distribuzione di musica Store è configurata per ricercare la porta 80 in tutte le macchine virtuali incluse. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [Presenza di bilanciamento del carico](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L257).


```none
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Verifica di bilanciamento carico visualizzata dal portale di Azure.

![Verifica di bilanciamento carico di rete](./media/virtual-machines-linux-dotnet-core/lbprobe.png)

## <a name="inbound-nat-rules"></a>Regole in entrata NAT

Quando si usa un servizio di bilanciamento del carico, è necessario mettere a punto che consentono l'accesso non bilanciata non carico a ogni macchina virtuale regole. Ad esempio, quando si crea un collegamento SSH con ogni macchina virtuale, il traffico non deve essere bilanciamento del carico, anziché un percorso predeterminato deve essere configurato. percorsi predeterminati possono essere configurati procedendo una risorsa in ingresso NAT regola. Usa questa risorsa, è possono mappare a singole macchine virtuali comunicazioni in ingresso. 

Con l'applicazione di archiviazione di musica, una porta iniziando 5000 sia assegnata alla porta 22 in ogni macchina virtuale per l'accesso SSH. Il `copyindex()` funzione viene utilizzata per incrementare la porta in arrivo, in modo che la seconda macchina virtuale riceve una porta in ingresso di 5001, 5002 terzo e così via.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse- [Regole NAT in entrata](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270). 

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'SSH-VM', copyIndex())]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 22,
    "enableFloatingIP": false
  }
}
```

Esempio di una regola NAT in entrata visto nel portale di Azure. Viene creata una regola di SSH NAT per ogni macchina virtuale nella distribuzione.

![Regola NAT in entrata](./media/virtual-machines-linux-dotnet-core/natrule.png)

Per informazioni dettagliate sul bilanciamento del carico di rete Azure, vedere [il bilanciamento del carico per i servizi di infrastruttura Azure](./virtual-machines-linux-load-balance.md).

## <a name="deploy-multiple-vms"></a>Distribuire più macchine virtuali

Infine, per un Set di disponibilità o di bilanciamento del carico in genere in modo efficace, sono necessari più macchine virtuali. Più macchine virtuali possono essere distribuite utilizzando la funzione di copia del modello Manager delle risorse di Azure. Usa la funzione di copia, non è necessario definire un numero limitato di macchine virtuali, ma questo valore può essere fornito in modo dinamico in fase di distribuzione. La funzione di copia utilizza il numero di istanze creata e punti di manipolazione di distribuzione il numero di macchine virtuali e le risorse associate appropriato.

Nel modello di esempio archivio musica, un parametro viene definito che entrerà in un conteggio delle istanze. Questo numero viene utilizzato in tutto il modello quando si creano macchine virtuali e le relative risorse.

```none
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 1,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
}
```

Sulla risorsa macchina virtuale ciclo copia viene assegnato un nome e il numero del parametro istanze utilizzato per controllare il numero di copie risultante.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [Funzione copia macchina virtuale](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L300). 


```none
"apiVersion": "2015-06-15",
"type": "Microsoft.Compute/virtualMachines",
"name": "[concat(variables('vmName'),copyindex())]",
"location": "[resourceGroup().location]",
"copy": {
  "name": "virtualMachineLoop",
  "count": "[parameters('numberOfInstances')]"
}
```

L'iterazione corrente della funzione copia è possibile accedere con il `copyIndex()` funzione. Il valore della funzione indice copia può essere utilizzato per assegnare un nome a macchine virtuali e altre risorse. Ad esempio, se sono distribuite due istanze di una macchina virtuale, devono nomi diversi. Il `copyIndex()` funzione può essere utilizzata come parte del nome del computer virtuale per creare un nome univoco. Esempio del `copyindex()` funzione utilizzata per la denominazione scopi può essere visualizzate nella risorsa macchina virtuale. In questo caso, il nome del computer è costituito il `vmName` parametro e la `copyIndex()` funzione. 

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse – [Copia indice](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L319). 


```none
"osProfile": {
  "computerName": "[concat(parameters('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": "true",
    "ssh": {
      "publicKeys": [
        {
          "path": "[variables('sshKeyPath')]",
          "keyData": "[parameters('sshKeyData')]"
        }
      ]
    }
  }
}
```

Il `copyIndex` viene utilizzata più volte nel modello di esempio musica Store. Risorse e funzioni che utilizzano `copyIndex` includere qualsiasi altro elemento specifico a una singola istanza della macchina virtuale, ad esempio l'interfaccia di rete, le regole di bilanciamento carico, e qualsiasi dipende da funzioni. 

Per ulteriori informazioni sulla funzione copia, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](../resource-group-create-multiple.md).

## <a name="next-step"></a>Passaggio successivo

<hr>

[Passaggio 4 - distribuzione di applicazioni con i modelli di Azure Manager delle risorse](./virtual-machines-linux-dotnet-core-5-app-deployment.md)