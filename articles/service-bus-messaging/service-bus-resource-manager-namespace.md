<properties
    pageTitle="Creare uno spazio dei nomi di servizio Bus utilizzando un modello di gestione risorse | Microsoft Azure"
    description="Utilizzare Gestione risorse Azure modello per creare uno spazio dei nomi Bus di servizio"
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
    ms.date="10/04/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi di servizio Bus utilizzando un modello di gestione risorse di Azure

In questo articolo viene descritto come utilizzare un modello di gestione di risorse Azure che consente di creare uno spazio dei nomi di Bus di servizio del tipo di **messaggistica** con uno SKU Standard/Basic. L'articolo definisce anche i parametri specificati per l'esecuzione della distribuzione. È possibile utilizzare questo modello per il proprio distribuzioni oppure personalizzarlo in base alle esigenze.

Per ulteriori informazioni sulla creazione di modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure][].

Per il modello completo, vedere il [modello di servizio Bus dello spazio dei nomi][] su GitHub.

>[AZURE.NOTE] Modelli di gestione di risorse Azure seguenti sono disponibili per il download e distribuzione. 
>
>-    [Creare uno spazio dei nomi di evento hub con un gruppo di evento Hub e consumer](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Creare uno spazio dei nomi del servizio Bus con coda](service-bus-resource-manager-namespace-queue.md)
>-    [Creare uno spazio dei nomi del servizio Bus con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
>-    [Creare uno spazio dei nomi del servizio Bus con regole di autorizzazione e coda](service-bus-resource-manager-namespace-auth-rule.md)
>
>Per cercare modelli più recenti, visitare la raccolta di [Modelli di Guida introduttiva di Azure][] e cercare Bus di servizio.

## <a name="what-will-you-deploy"></a>Che cos'è verrà distribuito?

Con questo modello, verrà distribuito uno spazio dei nomi di servizio Bus con una [base, Standard o Premium](https://azure.microsoft.com/pricing/details/service-bus/) SKU.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuire Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

Azure Gestione risorse, definire i parametri per i valori per specificare quando si distribuisce il modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori di parametro. È necessario definire un parametro per i valori che variano in base a project che si distribuisce o in base all'ambiente per distribuire. Non definire i parametri per i valori che saranno sempre lo stesso. Ogni valore di parametro viene utilizzato nel modello per definire le risorse che vengono distribuite.

Questo modello consente di definire i parametri seguenti.

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

### <a name="servicebussku"></a>serviceBusSKU

Il nome del servizio Bus [SKU](https://azure.microsoft.com/pricing/details/service-bus/) per creare.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

Il modello definisce i valori che sono consentiti per questo parametro (Basic, Standard o Premium) e viene assegnato un valore predefinito (Standard) se viene specificato alcun valore.

Per ulteriori informazioni sui prezzi Bus di servizio, vedere [Bus di servizio prezzi e fatturazione][].

### <a name="servicebusapiversion"></a>serviceBusApiVersion

La versione Service Bus API del modello.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Risorse per la distribuzione

### <a name="service-bus-namespace"></a>Spazio dei nomi Bus di servizio

Crea nomi Bus di servizio del tipo di **messaggistica**.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Comandi per l'esecuzione di distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato e distribuito risorse Gestione risorse di Azure, informazioni su come gestire queste risorse, leggere gli articoli seguenti:

- [Gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Gestire le risorse Bus di servizio con Esplora aree Bus di servizio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Creazione di modelli di Manager delle risorse di Azure]: ../resource-group-authoring-templates.md
  [Modello di spazio dei nomi Bus di servizio]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [Guida introduttiva di Azure modelli]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Servizio Bus prezzi e fatturazione]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
