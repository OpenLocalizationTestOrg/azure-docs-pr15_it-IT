<properties
    pageTitle="Creare uno spazio dei nomi di evento hub con Hub di eventi e consumer gruppo utilizzando un modello di gestione di risorse Azure | Microsoft Azure"
    description="Creare uno spazio dei nomi di evento hub Hub di eventi e un gruppo di consumer utilizzando il modello di gestione risorse di Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi di evento hub con Hub di eventi e consumer gruppo utilizzando un modello di gestione risorse di Azure

In questo articolo viene illustrato come utilizzare un modello di gestione di risorse Azure che consente di creare uno spazio dei nomi di evento hub con un Hub di eventi e un gruppo di consumer. Si apprenderà come definire quali risorse vengono distribuite e come definire i parametri specificati quando viene eseguita la distribuzione. È possibile utilizzare questo modello per il proprio distribuzioni o personalizzarlo in base alle esigenze

Per ulteriori informazioni sulla creazione di modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure][].

Per il modello completo, vedere [evento Hub e il modello di gruppo consumer][] in GitHub.

>[AZURE.NOTE]
>Per cercare modelli più recenti, visitare la raccolta di [Modelli di Guida introduttiva di Azure][] e cercare hub evento.

## <a name="what-will-you-deploy"></a>Che cos'è verrà distribuito?

Con questo modello, verrà distribuito uno spazio dei nomi di evento hub con un Hub di eventi e un gruppo di consumer.

[Hub di evento](../event-hubs/event-hubs-what-is-event-hubs.md) è un evento servizio utilizzato per fornire eventi e telemetry ingresso in Azure in scala enorme con latenza bassa e affidabilità di elaborazione.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuire Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

Azure Gestione risorse, definire i parametri per i valori per specificare quando si distribuisce il modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori di parametro. È necessario definire un parametro per i valori che variano in base a project che si distribuisce o in base all'ambiente per distribuire. Non definire i parametri per i valori che saranno sempre lo stesso. Ogni valore di parametro viene utilizzato nel modello per definire le risorse che vengono distribuite.

Il modello consente di definire i parametri seguenti.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Il nome dello spazio dei nomi evento hub per creare.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Il nome dell'evento Hub creato in eventi hub dello spazio dei nomi.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Il nome del gruppo consumer creato per l'Hub di evento.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

La versione dell'API del modello.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Risorse per la distribuzione

Crea uno spazio dei nomi del tipo di **EventHubs**, con un Hub di eventi e un gruppo di consumer.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Comandi per l'esecuzione di distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Creazione di modelli di Manager delle risorse di Azure]: ../resource-group-authoring-templates.md
[Guida introduttiva di Azure modelli]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Modello di gruppo Hub e consumer eventi]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
