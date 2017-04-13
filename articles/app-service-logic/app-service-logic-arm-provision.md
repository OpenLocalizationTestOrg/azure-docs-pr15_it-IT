<properties 
    pageTitle="Creare un'App di logica di utilizzare i modelli di gestione di risorse Azure in Azure App servizio | Microsoft Azure" 
    description="Utilizzare un modello di gestione di risorse Azure per distribuire un'App logica vuota per la definizione di flussi di lavoro." 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>Creare un'App di logica utilizzando un modello

Un modello di gestione risorse Azure consente di creare un'app vuota logica che può essere utilizzata per definire i flussi di lavoro. È possibile definire le risorse sono distribuite e su come definire i parametri specificati quando viene eseguita la distribuzione. È possibile utilizzare questo modello per il proprio distribuzioni oppure personalizzarlo in base alle esigenze.

Per ulteriori informazioni sulle proprietà logica app, vedere [API di gestione di logica di App del flusso di lavoro](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Per esempi di definizione stessa, vedere [le definizioni di autore logica App](app-service-logic-author-definitions.md). 

Per ulteriori informazioni sulla creazione di modelli, vedere [Creazione di modelli di Manager delle risorse Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere [modello di App logica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Si distribuisce

Con questo modello, si distribuisce un'app di logica.

Per eseguire automaticamente la distribuzione, selezionare il pulsante seguente:  

[![Distribuire Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>Risorse per la distribuzione

### <a name="logic-app"></a>App logica

Crea app logica.

I modelli di utilizza un parametro per il nome dell'app logica. Imposta la posizione dell'app logica nella stessa posizione al gruppo di risorse. 

Questa definizione particolare viene eseguito una volta all'ora e il ping nel percorso specificato nel parametro **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Comandi per l'esecuzione di distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
