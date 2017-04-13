<properties
   pageTitle="Modello di Manager delle risorse per blocchi di risorse | Microsoft Azure"
   description="Mostra lo schema di Manager delle risorse per la distribuzione di blocchi di risorse tramite un modello."
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

# <a name="resource-locks-template-schema"></a>Schema del modello blocchi di risorse

Crea un blocco di una risorsa e figlio risorse.

## <a name="schema-format"></a>Formato di schema

Per creare un blocco, aggiungere lo schema di seguito nella sezione risorse del modello.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valori

Le tabelle seguenti descrivono i valori che è necessario impostare nello schema.

| Nome | Obbligatorio | Descrizione |
| ---- | -------- | ----------- |
| tipo | Sì | Tipo di risorsa da creare.<br /><br />Per le risorse:<br />**{spazio dei nomi del} / {digitare} / provider/blocchi**<br /><br/>Per i gruppi di risorse:<br />**Microsoft.Authorization/locks** |
| apiVersion | Sì | La versione dell'API da utilizzare per la creazione della risorsa.<br /><br />Uso:<br />**01 / 01 2015**<br /><br /> |
| nome | Sì | Valore che specifica la risorsa da bloccare e un nome per il blocco. Può contenere fino a 64 caratteri e non può contenere <>, % &,?, o qualsiasi caratteri di controllo.<br /><br />Per le risorse:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />Per i gruppi di risorse:<br />**{lockname}** |
| dependsOn | No | Un elenco delimitato da virgole di una risorsa nomi o gli identificatori univoci delle risorse.<br /><br />Raccolta di risorse che dipende questo blocco. Se la risorsa che blocco viene distribuita nello stesso modello, includere il nome di risorsa in questo elemento per assicurarsi che la risorsa viene distribuita prima di tutto. | 
| proprietà | Sì | Oggetto che identifica il tipo di blocco e note sul blocco.<br /><br />Vedere [proprietà oggetto](#properties-object). |  

### <a name="properties-object"></a>oggetto di proprietà

| Nome | Obbligatorio | Descrizione |
| ---- | -------- | ----------- |
| livello   | Sì | Il tipo di blocco da applicare all'ambito.<br /><br />**CannotDelete** - gli utenti possono modificare risorsa ma non eliminarlo.<br />**Sola lettura** - gli utenti possono leggere da una risorsa, ma non è possibile eliminarlo o eseguire azioni su di esso. |
| note   | No | Descrizione del blocco. Può essere fino a 512 caratteri. |


## <a name="how-to-use-the-lock-resource"></a>Come usare la risorsa di blocco

Aggiungere questa risorsa per iniziare a utilizzare il modello per evitare che le azioni specificate per una risorsa. Il blocco si applica a tutti gli utenti e gruppi.

Per creare o eliminare blocchi management, è necessario avere accesso a **Microsoft.Authorization/** * o * *Microsoft.Authorization/locks/* ** Azioni. Dei ruoli predefiniti, solo **proprietario** e **utente accesso amministratore * * vengono concesse le azioni. Per informazioni sul controllo dell'accesso basato sui ruoli, vedere [Controllo dell'accesso basato sui ruoli di Azure](./active-directory/role-based-access-control-configure.md).

Il blocco viene applicato alla risorsa specificata e tutte le risorse figlio.

È possibile rimuovere un blocco con il comando di PowerShell **Rimuovi AzureRmResourceLock** o con l' [operazione di eliminazione](https://msdn.microsoft.com/library/azure/mt204562.aspx) dell'API REST.

## <a name="examples"></a>Esempi

Nell'esempio seguente si applica un blocco di eliminazione non è possibile a un'app web.

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
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

Nell'esempio seguente viene applicato un blocco di eliminazione non è possibile al gruppo di risorse.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla struttura del modello, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md).
- Per ulteriori informazioni sui blocchi, vedere [risorse di blocco con Gestione risorse di Azure](resource-group-lock-resources.md).
