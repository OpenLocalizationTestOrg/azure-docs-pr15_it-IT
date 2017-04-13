<properties 
    pageTitle="Distribuire un'app web collegato a un archivio GitHub" 
    description="Utilizzare un modello di gestione di risorse Azure per distribuire un'app web che contiene un progetto da un repository GitHub." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Distribuire un'app web collegata a un archivio GitHub

In questo argomento si imparerà creare un modello di gestione di risorse Azure che distribuisce un'applicazione web che è collegata a un progetto in un repository GitHub. Si apprenderà come definire quali risorse vengono distribuite e come definire i parametri specificati quando viene eseguita la distribuzione. È possibile utilizzare questo modello per il proprio distribuzioni oppure personalizzarlo in base alle esigenze.

Per ulteriori informazioni sulla creazione di modelli, vedere [Creazione di modelli di Manager delle risorse Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere [Web App collegata a un modello GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>Si distribuisce

Con questo modello, distribuire un'applicazione web che contiene il codice di un progetto in GitHub.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuire Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

URL per l'archivio GitHub contenente il progetto per la distribuzione. Questo parametro contiene un valore predefinito, ma questo valore viene usato solo per illustrare fornire l'URL per repository. È possibile utilizzare questo valore quando si verifica il modello, ma è possibile specificare l'URL del proprio repository quando si lavora con il modello.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>diramazione

Diramazione di repository da utilizzare quando si distribuisce l'applicazione. Il valore predefinito è master, ma è possibile specificare il nome di una diramazione nel repository che si desidera distribuire.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>Risorse per la distribuzione

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Web app

Consente di creare l'applicazione web che è collegato al progetto in GitHub. 

Specificare il nome dell'applicazione web tramite il parametro **NomeSito** e il percorso dell'applicazione web tramite il parametro **siteLocation** . Nell'elemento **dependsOn** il modello definisce l'applicazione web come dipende dal servizio di hosting piano. Perché dipende dal piano hosting, il web app non è stato creato fino al completamento della creazione il piano di hosting. L'elemento **dependsOn** viene utilizzato solo per specificare l'ordine di distribuzione. Se non si seleziona l'applicazione web come dipende dal piano di hosting, Azure risorsa Mananger tenterà di creare entrambe le risorse allo stesso tempo e che venga visualizzato un errore se l'applicazione web viene creata prima il piano di hosting.

L'applicazione web include inoltre una risorsa figlio definito nella sezione **risorse** riportata di seguito. Questa risorsa figlio definisce il codice sorgente per il progetto distribuito con web app. In questo modello, il controllo di origine è collegato a un particolare repository GitHub. Archivio GitHub viene definita con il codice **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** si potrebbe codificare l'URL dell'archivio quando si desidera creare un modello che distribuisce più volte un singolo progetto ma solo se il numero minimo di parametri.
Anziché impostare come hardcoded l'URL dell'archivio, è possibile aggiungere un parametro per l'URL dell'archivio e usare il valore per la proprietà **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Comandi per l'esecuzione di distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
