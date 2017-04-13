<properties
   pageTitle="Modello di Manager delle risorse per il collegamento risorse | Microsoft Azure"
   description="Mostra lo schema di Manager delle risorse per la distribuzione di collegamenti tra le relative risorse tramite un modello."
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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# <a name="resource-links-template-schema"></a>Schema del modello di risorsa collegamenti

Crea un collegamento tra due risorse. Il collegamento viene applicato a una risorsa nota come la risorsa di origine. Seconda risorsa nella casella collegamento è noto come risorsa di destinazione.

## <a name="schema-format"></a>Formato di schema

Per creare un collegamento, aggiungere lo schema di seguito nella sezione risorse del modello.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## <a name="values"></a>Valori

Le tabelle seguenti descrivono i valori che è necessario impostare nello schema.

| Nome | Valore |
| ---- | ---- |
| tipo | Enumerazione<br />Obbligatorio<br />**{spazio dei nomi del} / {digitare} / provider/collegamenti**<br /><br />Tipo di risorsa da creare. {Spazio dei nomi} e i valori {tipo} fare riferimento al tipo di spazio dei nomi e delle risorse provider della risorsa origine. |
| apiVersion | Enumerazione<br />Obbligatorio<br />**01 / 01 2015**<br /><br />La versione dell'API da utilizzare per la creazione della risorsa. |  
| nome | Stringa<br />Obbligatorio<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> fino a 64 caratteri e non può contenere <>, % &,?, o qualsiasi caratteri di controllo.<br /><br />Valore che specifica il nome della risorsa origine sia un nome per il collegamento. |
| dependsOn | In forma di matrice<br />Facoltativo<br />Un elenco delimitato da virgole di una risorsa nomi o gli identificatori univoci delle risorse.<br /><br />Raccolta di risorse che dipende questo collegamento. Se le risorse che del collegamento vengono installate nello stesso modello, inclusi i nomi delle risorse in questo elemento per assicurarsi che siano distribuite prima di tutto. | 
| proprietà | Oggetto<br />Obbligatorio<br />[oggetto di proprietà](#properties)<br /><br />Un oggetto che identifica la risorsa a cui collegarsi e note relative al collegamento. |  

<a id="properties" />
### <a name="properties-object"></a>oggetto di proprietà

| Nome | Valore |
| ------- | ---- |
| targetId | Stringa<br />Obbligatorio<br />**{id risorsa}**<br /><br />Identificatore della risorsa di destinazione a cui collegarsi. |
| note | Stringa<br />Facoltativo<br />fino a 512 caratteri<br /><br />Descrizione del blocco. |


## <a name="how-to-use-the-link-resource"></a>Come usare la risorsa di collegamento

Si applica un collegamento tra due risorse quando le risorse hanno una dipendenza continua dopo la distribuzione. Ad esempio un'app può connettersi a un database in un gruppo di risorse diversi. È possibile definire tale dipendenza creando un collegamento da app al database. I collegamenti consentono di relazione tra due risorse del documento. In un secondo momento, qualcuno dell'organizzazione può richiedere una risorsa per i collegamenti scoprire come la risorsa funziona con altre risorse.

Tutte le risorse collegate devono appartenere allo stesso abbonamento. Ogni risorsa può essere collegato a 50 altre risorse. Se tutte le risorse collegate eliminati o spostati, il proprietario del collegamento è necessario pulire il collegamento rimanente.

Per lavorare con i collegamenti tra resto, vedere [Le risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Usare il comando PowerShell Azure seguente per visualizzare tutti i collegamenti nell'abbonamento. È possibile fornire altri parametri per limitare i risultati.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Esempi

Nell'esempio seguente viene applicato un blocco di sola lettura per un'app web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
            }
        ],
        "outputs": {}
    }

## <a name="quickstart-templates"></a>Guida introduttiva modelli

Modelli di Guida introduttiva seguenti distribuire risorse con un collegamento.

- [Invia avviso a coda con logica app](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Avviso per il margine di flessibilità con logica app](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Effettuare il provisioning di un'app API con un gateway esistente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Effettuare il provisioning di un'app API con un nuovo gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Creare un'app App logica plus API utilizzando un modello](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [App logica che invia un messaggio di testo quando viene generato un avviso](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla struttura del modello, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md).
