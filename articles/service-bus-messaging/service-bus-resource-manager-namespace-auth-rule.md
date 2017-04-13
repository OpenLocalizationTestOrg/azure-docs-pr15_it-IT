<properties
    pageTitle="Creare una regola di autorizzazione Bus di servizio utilizzando un modello di gestione di risorse Azure | Microsoft Azure"
    description="Creare una regola di autorizzazione Bus di servizio per spazio dei nomi e coda utilizzando il modello di gestione risorse di Azure"
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

# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Creare una regola di autorizzazione Bus di servizio per spazio dei nomi e coda utilizzando un modello di gestione risorse di Azure

In questo articolo viene illustrato come utilizzare un modello di gestione di risorse Azure che crea una [regola di autorizzazione](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) per un servizio Bus dello spazio dei nomi e una coda. Si apprenderà come definire quali risorse vengono distribuite e come definire i parametri specificati quando viene eseguita la distribuzione. È possibile utilizzare questo modello per il proprio distribuzioni oppure personalizzarlo in base alle esigenze.

Per ulteriori informazioni sulla creazione di modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure][].

Per il modello completo, vedere il [modello di regola auth Bus di servizio][] in GitHub.

>[AZURE.NOTE] Modelli di gestione di risorse Azure seguenti sono disponibili per il download e distribuzione.
>
>-    [Creare uno spazio dei nomi di evento hub con un gruppo di evento Hub e consumer](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Creare uno spazio dei nomi del servizio Bus con coda](service-bus-resource-manager-namespace-queue.md)
>-    [Creare uno spazio dei nomi del servizio Bus con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
>-    [Creare uno spazio dei nomi Bus di servizio](service-bus-resource-manager-namespace.md)
>
>Per cercare modelli più recenti, visitare la raccolta di [Modelli di Guida introduttiva di Azure][] e cercare "Servizio Bus".

## <a name="what-will-you-deploy"></a>Che cos'è verrà distribuito?

Con questo modello, distribuire una regola di autorizzazione Bus di servizio per un spazio dei nomi e messaggistica entità (in questo caso, una coda).

Questo modello utilizza [Condiviso accesso firma (SA)](service-bus-sas-overview.md) per l'autenticazione. SA consente alle applicazioni eseguire l'autenticazione al servizio Bus utilizza un tasto configurato lo spazio dei nomi o a entità messaggistica (coda o argomento) a cui sono associati diritti specifici. È quindi possibile utilizzare questo tasto per generare un token di sa che i clienti possono utilizzare per l'autenticazione a Bus di servizio.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuire Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

Azure Gestione risorse, definire i parametri per i valori per specificare quando si distribuisce il modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori di parametro. È necessario definire un parametro per i valori che variano in base a project che si distribuisce o in base all'ambiente per distribuire. Non definire i parametri per i valori che saranno sempre lo stesso. Ogni valore di parametro viene utilizzato nel modello per definire le risorse che vengono distribuite.

Il modello consente di definire i parametri seguenti.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Il nome dello spazio dei nomi Bus di servizio per creare.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName 

Il nome della regola di autorizzazione per lo spazio dei nomi.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Il nome della coda dello spazio dei nomi Bus di servizio.

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

Crea nomi Bus di servizio del tipo di **messaggistica**e una regola di autorizzazione Bus di servizio per spazio dei nomi ed entità.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Comandi per l'esecuzione di distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato e distribuito risorse Gestione risorse di Azure, informazioni su come gestire queste risorse per visualizzare gli articoli seguenti:

- [Gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Gestire le risorse Bus di servizio con Esplora aree Bus di servizio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Autenticazione Bus di servizio e le autorizzazioni](service-bus-authentication-and-authorization.md)

  [Creazione di modelli di Manager delle risorse di Azure]: ../resource-group-authoring-templates.md
  [Guida introduttiva di Azure modelli]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Modello di regola auth Bus di servizio]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
