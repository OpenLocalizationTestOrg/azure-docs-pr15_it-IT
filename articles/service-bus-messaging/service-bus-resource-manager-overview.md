<properties
    pageTitle="Creazione di risorse Bus di servizio tramite Gestione risorse Azure modelli | Microsoft Azure"
    description="Usare i modelli di gestione di risorse Azure per automatizzare la creazione di risorse Bus di servizio"
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
    ms.author="sethm"/>

# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Creazione di risorse Bus di servizio tramite Gestione risorse Azure modelli

In questo articolo viene descritto come creare e distribuire risorse Bus di servizio e hub evento usando modelli Manager delle risorse di Azure, PowerShell e il provider di risorse Bus di servizio.

Modelli di gestione risorse Azure consentono di definire le risorse per la distribuzione per una soluzione e per specificare i parametri e variabili che consentono di valori di input per ambienti diversi. Il modello è composto da JSON ed espressioni che è possibile utilizzare per creare i valori per la distribuzione. Per informazioni dettagliate sulla creazione di modelli di gestione di risorse Azure e una descrizione del formato del modello, vedere [modelli di creazione condivisa Manager delle risorse di Azure](../resource-group-authoring-templates.md). 

>[AZURE.NOTE] Negli esempi di questo articolo viene illustrato come utilizzare Gestione risorse di Azure per creare un servizio Bus dello spazio dei nomi e messaggistica entità (coda). Per altri esempi di modello, visitare la [raccolta di modelli di Guida introduttiva di Azure][] e cercare "Servizio Bus".

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Modelli Bus di servizio e gestione di risorse hub di evento

Questi modelli Bus di servizio e gestione di risorse di evento hub Azure sono disponibili per il download e distribuzione. Fare clic sui collegamenti seguenti per informazioni dettagliate su ognuno di essi, con collegamenti per i modelli presenti GitHub: 

- [Creare uno spazio dei nomi Bus di servizio](service-bus-resource-manager-namespace.md)
- [Creare uno spazio dei nomi del servizio Bus con coda](service-bus-resource-manager-namespace-queue.md)
- [Creare uno spazio dei nomi del servizio Bus con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
- [Creare uno spazio dei nomi del servizio Bus con regole di autorizzazione e coda](service-bus-resource-manager-namespace-auth-rule.md)
- [Creare uno spazio dei nomi di evento hub con un gruppo di evento Hub e consumer](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

La procedura seguente descrive come usare PowerShell per distribuire un modello di gestione risorse Azure che consente di creare uno spazio dei nomi Bus di servizio di livello **Standard** e una coda nello spazio dei nomi. Questo esempio è basato sul modello di [creare uno spazio dei nomi di servizio Bus con coda](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . Il flusso di lavoro approssimativa è il seguente:

1. Installare PowerShell.
2. Creare il modello e, facoltativamente, un file di parametri.
2. In PowerShell, accedere al proprio account Azure.
3. Creare un nuovo gruppo di risorse se non esiste.
4. Test della distribuzione.
5. Se lo si desidera, impostare la modalità di distribuzione.
6. Distribuire il modello.

Per informazioni complete sulla distribuzione di modelli di Manager delle risorse di Azure, vedere [risorse di distribuzione con i modelli di gestione risorse di Azure][].

### <a name="install-powershell"></a>Installare PowerShell

Installare PowerShell Azure seguendo le istruzioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

### <a name="create-a-template"></a>Creare un modello

Duplicare o copiare il modello [201-servicebus-Crea-coda](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) GitHub:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Creare un file di parametri (facoltativo)

Per utilizzare un file di parametri facoltativi, copiare il file [201-servicebus-Crea-coda](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Sostituire il valore di `serviceBusNamespaceName` con il nome dello spazio dei nomi Bus di servizio che si desidera creare la distribuzione e sostituire il valore di `serviceBusQueueName` con il nome della coda che si desidera creare. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Per ulteriori informazioni, vedere l'argomento [file dei parametri](../resource-group-template-deploy.md#parameter-file) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Accedere a Azure e impostare la sottoscrizione di Azure

Da un prompt di PowerShell, eseguire il comando seguente:

```
Login-AzureRmAccount
```

Viene chiesto di accedere al proprio account Azure. Dopo l'accesso, eseguire il comando seguente per visualizzare le sottoscrizioni disponibili.

```
Get-AzureRMSubscription
```

Questo comando restituisce un elenco delle sottoscrizioni di Azure disponibili. Scegliere un abbonamento per la sessione corrente, eseguire il comando seguente. Sostituire `<YourSubscriptionId>` con GUID per la sottoscrizione di Azure che si desidera utilizzare.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Impostare il gruppo di risorse

Se non si dispone di un gruppo di risorse esistente, creare un nuovo gruppo di risorse con il comando **Nuovo AzureRmResourceGroup** . Specificare il nome del gruppo di risorse e posizione che si desidera utilizzare. Per esempio:

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

In caso contrario, viene visualizzato un riepilogo di nuovo gruppo di risorse.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Verificare la distribuzione

Convalidare la distribuzione eseguendo la `Test-AzureRmResourceGroupDeployment` cmdlet. Durante la verifica della distribuzione, fornire parametri esattamente come quando si esegue la distribuzione.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Creare la distribuzione

Per creare la nuova distribuzione, eseguire la `New-AzureRmResourceGroupDeployment` comando e fornire i parametri necessari quando richiesto. I parametri includono un nome per la distribuzione, il nome del gruppo di risorse e il percorso o l'URL per il file del modello. Se il parametro **Mode** non viene specificato, viene utilizzato il valore predefinito **incrementale** . Per ulteriori informazioni, vedere [distribuzioni complete e incrementale](../resource-group-template-deploy.md#incremental-and-complete-deployments).

Il comando seguente chiede tre parametri nella finestra di PowerShell:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Per specificare se, tuttavia un file di parametri, utilizzare il comando seguente.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

È inoltre possibile utilizzare parametri all'interno del testo quando si esegue il cmdlet di distribuzione. Il comando è il seguente:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Per eseguire una distribuzione [completa](../resource-group-template-deploy.md#incremental-and-complete-deployments) , impostare il parametro **modalità** a **completo**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Verificare la distribuzione

Se le risorse vengono distribuite correttamente, verrà visualizzato un riepilogo della distribuzione nella finestra di PowerShell:

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato illustrato il flusso di lavoro di base e comandi per la distribuzione di un modello di gestione di risorse Azure. Per ulteriori informazioni, visitare i collegamenti seguenti:

- [Panoramica di gestione risorse Azure][]
- [Distribuire le risorse con i modelli di Manager delle risorse di Azure][]
- [Creazione di modelli](../resource-group-authoring-templates.md)


[Panoramica di gestione risorse Azure]: ../resource-group-overview.md
[Distribuire le risorse con i modelli di Manager delle risorse di Azure]: ../resource-group-template-deploy.md
[Raccolta di modelli di Guida introduttiva Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus