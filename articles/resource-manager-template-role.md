<properties
   pageTitle="Modello di Manager delle risorse per le assegnazioni di ruolo | Microsoft Azure"
   description="Mostra lo schema di Manager delle risorse per la distribuzione di un'assegnazione di ruolo tramite un modello."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="tomfitz"/>

# <a name="role-assignments-template-schema"></a>Schema del modello assegnazioni di ruolo

Assegna un utente, gruppo o principale del servizio a un ruolo di un ambito specificato.

## <a name="resource-format"></a>Formato di risorse

Per creare un'assegnazione di ruolo, aggiungere lo schema di seguito nella sezione risorse del modello.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valori

Le tabelle seguenti descrivono i valori che è necessario impostare nello schema.

| Nome | Obbligatorio | Descrizione |
| ---- | -------- | ----------- |
| tipo | Sì    | Tipo di risorsa da creare.<br /><br /> Per il gruppo di risorse:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Per risorsa:<br />**{provider spazio dei nomi} / {tipo di risorsa} / provider/roleAssignments** |
| apiVersion |Sì | La versione dell'API da utilizzare per la creazione della risorsa.<br /><br /> Utilizzare **2015-07-01**. | 
| nome | Sì | Identificatore univoco globale per la nuova assegnazione di ruolo. |
| dependsOn | No | In forma di matrice separati da virgola di una risorsa nomi o gli identificatori univoci delle risorse.<br /><br />Raccolta di risorse che dipende l'assegnazione di ruolo. Se assegnare un ruolo che nell'ambito di una risorsa e delle risorse viene distribuita nello stesso modello, includere il nome di risorsa in questo elemento per assicurarsi che la risorsa viene distribuita prima di tutto. |
| proprietà | Sì | Oggetto di proprietà che identifica la definizione di ruolo, capitale e l'ambito. |

### <a name="properties-object"></a>oggetto di proprietà

| Nome | Obbligatorio | Descrizione |
| ---- | -------- | ----------- |
| roleDefinitionId | Sì |  Identificatore di una definizione di ruolo esistente da utilizzare per l'assegnazione di ruolo.<br /><br /> Utilizzare il formato seguente:<br /> **/ subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId | Sì | Identificatore univoco globale per un capitale esistente. Questo valore esegue il mapping a id all'interno della directory e possono fare riferimento a un utente, principale del servizio o gruppo di sicurezza. |
| ambito | No | L'ambito in cui l'assegnazione di ruolo si applica a.<br /><br />Per i gruppi di risorse, usare:<br />**/resourceGroups/ /Subscriptions/ {id abbonamento} {nome gruppo risorsa}**  <br /><br />Per le risorse, usare:<br />**/ subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>Come usare la risorsa delle assegnazioni di ruolo

Aggiungere un'assegnazione di ruolo al modello quando è necessario aggiungere un utente, gruppo o servizio principale a un ruolo durante l'installazione. Assegnazione di ruolo vengono ereditate dal livelli superiori di ambito, pertanto se un'entità è già stato aggiunto a un ruolo a livello di abbonamento, non sarà necessario sovrapposte per il gruppo di risorse o delle risorse.

Sono disponibili numerosi valori identificatore che è necessario specificare quando si utilizzano le assegnazioni di ruolo. È possibile recuperare i valori tramite PowerShell o CLI Azure.

### <a name="powershell"></a>PowerShell

Il nome dell'assegnazione di ruolo richiede un identificatore univoco globale. È possibile generare un nuovo identificatore per **nome** con:

    $name = [System.Guid]::NewGuid().toString()

È possibile recuperare l'identificatore per la definizione di ruolo con:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

È possibile recuperare l'identificatore per il capitale con uno dei comandi seguenti.

Per un gruppo denominato **controllori**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Per un utente denominato **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Per un servizio capitale denominato **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>CLI Azure

È possibile recuperare l'identificatore per la definizione di ruolo con:

    azure role show Reader --json | jq .[].Id -r

È possibile recuperare l'identificatore per il capitale con uno dei comandi seguenti.

Per un gruppo denominato **controllori**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Per un utente denominato **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Per un servizio capitale denominato **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Esempi

Il modello seguente riceve un identificatore per un ruolo e un identificatore per un utente, gruppo o principale del servizio. Viene assegnato il ruolo di livello di gruppo risorse.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



Il modello successivo viene creato un account di archiviazione e assegna il ruolo di lettore all'account di spazio di archiviazione. Gli identificatori per due gruppi e il ruolo di lettore sono stati inclusi nel modello per semplificare la distribuzione. Tali valori potrebbero recuperati in fase di distribuzione tramite script e passati come parametri.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Guida introduttiva modelli

I modelli di seguito viene illustrato come utilizzare la risorsa delle assegnazioni di ruolo:

- [Assegnare ruoli predefiniti al gruppo di risorse](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Assegnare ruoli predefiniti in macchine Virtuali esistenti](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Assegnare ruoli predefiniti più macchine virtuali esistenti](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla struttura del modello, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md).
- Per ulteriori informazioni sul controllo dell'accesso basato sui ruoli, vedere [Controllo dell'accesso basato sui ruoli di Azure Active Directory](./active-directory/role-based-access-control-configure.md).
