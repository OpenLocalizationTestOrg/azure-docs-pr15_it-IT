<properties
    pageTitle="Distribuire macchina dell'area di lavoro con Azure modello Manager delle risorse di formazione | Microsoft Azure"
    description="Come distribuire un'area di lavoro per l'apprendimento Azure utilizzando il modello di gestione risorse di Azure"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Distribuire macchina dell'area di lavoro con Azure Manager delle risorse di formazione

## <a name="introduction"></a>Introduzione
Una gestione risorse Azure modello installazione consente di risparmiare tempo si assegnando un modo scalable per distribuire i componenti interconnessi con una convalida e riprovare a meccanismo. Per configurare le aree di lavoro di Azure computer risorse, ad esempio, è necessario prima configurare un account di archiviazione Azure e quindi distribuire l'area di lavoro. Si supponga di eseguire questa operazione manualmente per centinaia di aree di lavoro. Un'alternativa più semplice consiste nell'usare un modello di gestione di risorse Azure per distribuire un'area di lavoro di Azure Machine formazione e le relative dipendenze. In questo articolo vengono illustrate in questo processo. Per una panoramica di gestione di risorse di Azure, vedere [Panoramica di gestione di risorse Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Procedura dettagliata: creare un'area di lavoro di risorse di computer
Si verrà creato un gruppo di risorse Azure e quindi distribuire un nuovo account di archiviazione Azure e una nuova Azure Machine Learning area di lavoro utilizzando un modello di Manager delle risorse. Al termine della distribuzione, si verrà stampato importanti informazioni sulle aree di lavoro che sono stati creati (la chiave primaria, il workspaceID e l'URL per l'area di lavoro).

### <a name="create-an-azure-resource-manager-template"></a>Creare un modello di gestione risorse di Azure
Un'area di lavoro risorse computer richiede un account di archiviazione Azure per archiviare il set di dati a esso collegati.
Il modello seguente utilizza il nome del gruppo di risorse per generare il nome dell'account di archiviazione e il nome dell'area di lavoro.  Utilizza anche il nome dell'account di archiviazione come proprietà quando si creano l'area di lavoro.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Salvare il modello come file mlworkspace.json in c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Distribuire il gruppo di risorse in base al modello
* Apri PowerShell
* Installare moduli per Gestione risorse di Azure e gestione dei servizi di Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Questa procedura Scarica e installa i moduli necessari per completare i passaggi rimanenti. Solo deve essere eseguita una volta nell'ambiente in cui si siano eseguendo i comandi di PowerShell.   

* Eseguire l'autenticazione in Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Questo passaggio deve essere ripetuto per ogni sessione. Dopo l'autenticazione, le informazioni sull'abbonamento devono essere visualizzati.

![Account Azure][1]

Ora che è possibile accedere al Azure, è possibile creare il gruppo di risorse.

* Creare un gruppo di risorse

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Verificare che il gruppo di risorse correttamente effettuato il provisioning. **ProvisioningState** deve essere "ha avuto esito positivo."
Il nome del gruppo di risorse viene utilizzato il modello per generare il nome dell'account di archiviazione. Il nome dell'account di archiviazione deve essere compreso tra 3 e 24 caratteri e utilizzare numeri e lettere minuscole solo.

![Gruppo di risorse][2]

* Utilizzo della distribuzione del gruppo di risorse, distribuire una nuova area di lavoro risorse computer.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Al termine della distribuzione, è semplice per accedere alle proprietà dell'area di lavoro che è stato distribuito. Ad esempio, è possibile accedere Token di chiave primaria.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Un altro modo per recuperare i token di area di lavoro esistente consiste nell'utilizzare il comando richiama AzureRmResourceAction. Ad esempio, è possibile elencare i token primari e secondari di tutte le aree di lavoro.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Dopo l'area di lavoro viene eseguito il provisioning, è inoltre possibile automatizzare molte attività di Azure Machine Learning Studio uso del [Modulo PowerShell per l'apprendimento Azure](http://aka.ms/amlps).

## <a name="next-steps"></a>Passaggi successivi 
* Ulteriori informazioni sui [Modelli di gestione risorse Azure condivisa](../resource-group-authoring-templates.md). 
* Avere un'occhiata [Archivio modelli Guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates). 
* Guardare questo video sulle [Gestione risorse di Azure](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
