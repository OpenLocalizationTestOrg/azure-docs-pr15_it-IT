<properties
    pageTitle="Creare uno spazio dei nomi del servizio Bus con coda utilizzando un modello di gestione di risorse Azure | Microsoft Azure"
    description="Creare uno spazio dei nomi Bus di servizio e una coda utilizzando il modello di gestione risorse di Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/14/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi Bus di servizio e una coda utilizzando un modello di gestione risorse di Azure

In questo articolo viene illustrato come utilizzare un modello di gestione risorse Azure che crea una coda e uno spazio dei nomi Bus di servizio. Si apprenderà come definire quali risorse vengono distribuite e come definire i parametri specificati quando viene eseguita la distribuzione. È possibile utilizzare questo modello per il proprio distribuzioni oppure personalizzarlo in base alle esigenze.

Per ulteriori informazioni sulla creazione di modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure][].

Per il modello completo, vedere il [modello di spazio dei nomi e coda Bus di servizio][] in GitHub.

>[AZURE.NOTE] Modelli di gestione di risorse Azure seguenti sono disponibili per il download e distribuzione.
>
>-    [Creare uno spazio dei nomi del servizio Bus con regole di autorizzazione e coda](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Creare uno spazio dei nomi del servizio Bus con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
>-    [Creare uno spazio dei nomi Bus di servizio](service-bus-resource-manager-namespace.md)
>-    [Creare uno spazio dei nomi di evento hub con un gruppo di evento Hub e consumer](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Per cercare modelli più recenti, visitare la raccolta di [Modelli di Guida introduttiva di Azure][] e cercare "Servizio Bus".

## <a name="what-will-you-deploy"></a>Che cos'è verrà distribuito?

Con questo modello, verrà distribuito uno spazio dei nomi di servizio Bus con una coda.

[Servizio Bus code](service-bus-queues-topics-subscriptions.md#queues) offrono prima In recapito dei messaggi FIFO (First Out) a uno o più consumer concorrenti.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuire Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

Azure Gestione risorse, definire i parametri per i valori per specificare quando si distribuisce il modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori di parametro. È necessario definire un parametro per i valori che variano in base a project che si distribuisce o in base all'ambiente per distribuire. Non definire i parametri per i valori che saranno sempre lo stesso. Ogni valore di parametro viene utilizzato nel modello per definire le risorse che vengono distribuite.

Il modello consente di definire i parametri seguenti.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Il nome dello spazio dei nomi Bus di servizio per creare.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Il nome della coda creata dello spazio dei nomi Bus di servizio.

```
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La versione Service Bus API del modello.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Risorse per la distribuzione

Crea nomi Bus di servizio del tipo di **messaggistica**, con una coda.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandi per l'esecuzione di distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato e distribuito risorse Gestione risorse di Azure, informazioni su come gestire queste risorse per visualizzare gli articoli seguenti:

- [Gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Gestire le risorse Bus di servizio con Esplora aree Bus di servizio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Creazione di modelli di Manager delle risorse di Azure]: ../resource-group-authoring-templates.md
  [Modello di spazio dei nomi e coda Bus di servizio]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Guida introduttiva di Azure modelli]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
