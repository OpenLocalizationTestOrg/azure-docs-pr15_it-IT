<properties
   pageTitle="Modello di Manager delle risorse per archivio chiave | Microsoft Azure"
   description="Mostra lo schema di Manager delle risorse per la distribuzione di chiavi archivi tramite un modello."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="key-vault-template-schema"></a>Schema del modello di tasti archivio

Crea un archivio di chiave.

## <a name="schema-format"></a>Formato di schema

Per creare un archivio di chiave, aggiungere lo schema di seguito nella sezione risorse del modello.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valori

Le tabelle seguenti descrivono i valori che è necessario impostare nello schema.

| Nome | Valore |
| ---- | ---- | 
| tipo | Enumerazione<br />Obbligatorio<br />**Microsoft.KeyVault/vaults**<br /><br />Tipo di risorsa da creare. |
| apiVersion | Enumerazione<br />Obbligatorio<br />**2015-06-01** o **Anteprima 19 di 12 2014**<br /><br />La versione dell'API da utilizzare per la creazione della risorsa. | 
| nome | Stringa<br />Obbligatorio<br />Un nome univoco tra Azure.<br /><br />Il nome dell'archivio di chiave per creare. Valutare la possibilità di utilizzo della funzione [uniqueString](resource-group-template-functions.md#uniquestring) con la convenzione di denominazione per creare un nome univoco, come illustrato nell'esempio riportato di seguito. |
| posizione | Stringa<br />Obbligatorio<br />Area di validità per archivi chiavi. Per determinare aree valide, vedere [supportati aree geografiche](resource-manager-supported-services.md#supported-regions).<br /><br />Area di ospitare l'archivio di chiave. |
| proprietà | Oggetto<br />Obbligatorio<br />[oggetto di proprietà](#properties)<br /><br />Oggetto che specifica il tipo di archivio di chiave per creare. |
| risorse | In forma di matrice<br />Facoltativo<br />Consentita valori: [risorse segrete archivio chiave](resource-manager-template-keyvault-secret.md)<br /><br />Risorse figlio per l'archivio di chiave. |

<a id="properties" />
### <a name="properties-object"></a>oggetto di proprietà

| Nome | Valore |
| ---- | ---- | 
| enabledForDeployment | Valore booleano<br />Facoltativo<br />**true** o **false**<br /><br />Specifica se l'archivio è abilitata per la distribuzione macchina virtuale o servizio tessuti. |
| enabledForTemplateDeployment | Valore booleano<br />Facoltativo<br />**true** o **false**<br /><br />Specifica se l'archivio è abilitata per l'utilizzo in distribuzioni modello Manager delle risorse. Per ulteriori informazioni, vedere [passare protetti valori durante la distribuzione](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Valore booleano<br />Facoltativo<br />**true** o **false**<br /><br />Specifica se l'archivio è abilitato per la crittografia del volume. |
| tenantId | Stringa<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />Identificatore tenant per la sottoscrizione. È possibile recuperare con il cmdlet di PowerShell [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) o **account azure Mostra** comando CLI Azure. |
| accessPolicies | In forma di matrice<br />Obbligatorio<br />[oggetto accessPolicies](#accesspolicies)<br /><br />Matrice di oggetti fino a 16 che specificare le autorizzazioni per l'utente o principale del servizio. |
| SKU | Oggetto<br />Obbligatorio<br />[oggetto SKU](#sku)<br /><br />SKU per l'archivio di chiave. |

<a id="accesspolicies" />
### <a name="propertiesaccesspolicies-object"></a>oggetto properties.accessPolicies

| Nome | Valore |
| ---- | ---- | 
| tenantId | Stringa<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />L'identificatore tenant del tenant di Azure Active Directory contenente **objectId** in questo criterio di accesso |
| objectId | Stringa<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />Identificatore di oggetto dell'utente di Azure Active Directory o principale del servizio che avrà accesso all'archivio. È possibile recuperare il valore da [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) o i cmdlet di PowerShell [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) o i comandi di Azure CLI **utente di azure Active Directory** o **sp azure Active Directory** . |
| autorizzazioni | Oggetto<br />Obbligatorio<br />[autorizzazioni oggetto](#permissions)<br /><br />Le autorizzazioni concesse per questo archivio all'oggetto Active Directory. |

<a id="permissions" />
### <a name="propertiesaccesspoliciespermissions-object"></a>oggetto properties.accessPolicies.permissions

| Nome | Valore |
| ---- | ---- | 
| chiavi | In forma di matrice<br />Obbligatorio<br />** **tutti**, **backup**, **creare**, **decrittografare**, **eliminare**, **crittografare**, **ottenere**, **importare**, **elenco**, eseguire il ripristino** **accesso**, **unwrapkey**, **aggiornare**, **verificare**, **wrapkey**<br /><br />Le autorizzazioni concesse sulle chiavi di questo archivio all'oggetto Active Directory. Questo valore deve specificato come matrice di uno o più valori consentiti. |
| informazioni riservate | In forma di matrice<br />Obbligatorio<br />**tutti**, **eliminare**, **ottenere**, **elenco**, **impostare**<br /><br />Le autorizzazioni concesse per informazioni riservate in questo archivio all'oggetto Active Directory. Questo valore deve specificato come matrice di uno o più valori consentiti. |

<a id="sku" />
### <a name="propertiessku-object"></a>oggetto Properties.SKU

| Nome | Valore |
| ---- | ---- | 
| nome | Enumerazione<br />Obbligatorio<br />**standard**o **premium** <br /><br />Livello di servizio di KeyVault da utilizzare.  Standard supporta tasti protetto da software e informazioni riservate.  Premium aggiunge il supporto per le chiavi protetta modulo di sicurezza hardware. |
| famiglia | Enumerazione<br />Obbligatorio<br />**RISPOSTE** <br /><br />La famiglia sku da utilizzare. |
 
    
## <a name="examples"></a>Esempi

Nell'esempio seguente viene distribuito un archivio di chiave e segreto.

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

## <a name="quickstart-templates"></a>Guida introduttiva modelli

Il modello di Guida introduttiva seguente distribuisce un archivio di chiave.

- [Creare un archivio chiave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali su archivi chiave, vedere [Guida introduttiva di Azure chiave archivio](./key-vault/key-vault-get-started.md).
- Per un esempio di un segreto archivio chiave di riferimento per la distribuzione di modelli, vedere [passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md).

