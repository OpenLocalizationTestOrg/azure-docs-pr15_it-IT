<properties
   pageTitle="Modello di Manager delle risorse per un segreto in un archivio chiave | Microsoft Azure"
   description="Mostra lo schema di Manager delle risorse per la distribuzione di informazioni riservate archivio chiave tramite un modello."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-secret-template-schema"></a>Schema di modello segreta archivio chiave

Crea un segreto memorizzati in un archivio di chiave. Questo tipo di risorsa viene spesso distribuito come risorsa figlio [dell'archivio chiave](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Formato di schema

Per creare un segreto archivio chiave, aggiungere il seguente schema al modello. Il segreto può essere definito come una risorsa di figlio di un archivio di chiave o come risorsa principale. È possibile definire il come risorsa figlio quando l'archivio di chiave verrà distribuito nello stesso modello. È necessario definire il segreto come risorsa principale quando l'archivio di chiave non viene distribuito nello stesso modello o quando è necessario creare più informazioni riservate scorrendo sul tipo di risorsa. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Valori

Le tabelle seguenti descrivono i valori che è necessario impostare nello schema.

| Nome | Valore |
| ---- | ---- | 
| tipo | Enumerazione<br />Obbligatorio<br />**informazioni riservate** (quando distribuito come risorsa figlio dell'archivio chiave) o<br /> **Microsoft.KeyVault/vaults/secrets** (quando distribuito come una risorsa di primo livello)<br /><br />Tipo di risorsa da creare. |
| apiVersion | Enumerazione<br />Obbligatorio<br />**2015-06-01** o **Anteprima 19 di 12 2014**<br /><br />La versione dell'API da utilizzare per la creazione della risorsa. | 
| nome | Stringa<br />Obbligatorio<br />Una singola parola quando distribuito come risorsa figlio di un archivio di chiave o nel formato **{nome archivio chiave} / {segreto nome}** quando distribuito come risorsa principale venga aggiunta a un archivio di chiave esistente.<br /><br />Il nome del segreto per creare. |
| proprietà | Oggetto<br />Obbligatorio<br />[oggetto di proprietà](#properties)<br /><br />Oggetto che specifica il valore del segreto per creare. |
| dependsOn | In forma di matrice<br />Facoltativo<br />Un elenco delimitato da virgole di una risorsa nomi o gli identificatori univoci delle risorse.<br /><br />Raccolta di risorse che dipende questo collegamento. Se l'archivio di chiave per il segreto viene distribuito nello stesso modello, includere il nome dell'archivio di chiave in questo elemento per assicurarsi che venga distribuito prima di tutto. |

<a id="properties" />
### <a name="properties-object"></a>oggetto di proprietà

| Nome | Valore |
| ---- | ---- | 
| valore | Stringa<br />Obbligatorio<br /><br />Segreto valore da memorizzare nell'archivio di chiave. Quando si passa a un valore per questa proprietà, utilizzare un parametro di tipo **securestring**.  |

    
## <a name="examples"></a>Esempi

Nel primo esempio distribuisce un segreto come risorsa figlio di un archivio di chiave.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

Il secondo esempio distribuisce il segreto come risorsa principale memorizzati in un archivio di chiave esistente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali su archivi chiave, vedere [Guida introduttiva di Azure chiave archivio](./key-vault/key-vault-get-started.md).
- Per un esempio di un segreto archivio chiave di riferimento per la distribuzione di modelli, vedere [passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md).


