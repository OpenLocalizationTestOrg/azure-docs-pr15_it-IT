<properties
   pageTitle="Segreto chiave archivio con Gestione risorse modello | Microsoft Azure"
   description="Viene illustrato come passare un segreto da un archivio di chiave come parametro durante la distribuzione."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# <a name="pass-secure-values-during-deployment"></a>Passare protetti valori durante la distribuzione

Quando è necessario passare un valore sicuro (ad esempio una password) come parametro durante la distribuzione, è possibile archiviare il valore come segreto in un [Archivio di chiave Azure](./key-vault/key-vault-whatis.md) e fare riferimento al valore in altri modelli di Manager delle risorse. Includere solo un riferimento al segreto nel modello scelto per consentire il segreto non viene mai esposto e non è necessario immettere manualmente il valore per il segreto ogni volta che si distribuiscono le risorse. Specificare quali utenti o identità di servizio possono accedere alle informazioni riservate.  

## <a name="deploy-a-key-vault-and-secret"></a>Distribuire un archivio di chiave e segreto

Per creare archivio chiave che si desidera fare riferimento di altri modelli di Manager delle risorse, è necessario impostare la proprietà **enabledForTemplateDeployment** su **true**e deve concedere l'accesso all'utente o principale del servizio che verrà eseguita la distribuzione di cui fa riferimento il segreto.

Per informazioni sulla distribuzione di un insieme di credenziali chiave e segreto, vedere [schema di archivio di chiave](resource-manager-template-keyvault.md) e [chiave archivio segreto schema](resource-manager-template-keyvault-secret.md).

## <a name="reference-a-secret-with-static-id"></a>Fare riferimento a un segreto con id statico

Si fa riferimento il segreto all'interno di un file di parametri che restituisce i valori per il modello. Si fa riferimento il segreto passando l'identificatore di risorsa dell'archivio di chiave e il nome del segreto. In questo esempio, il segreto archivio chiave esista già e si utilizza un valore statico per l'id risorsa.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Un file intero parametro potrebbe essere simile:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

Il parametro che accetta il segreto deve essere **securestring**. Nell'esempio seguente mostra le sezioni pertinenti di un modello che distribuisce un server SQL che richiede una password di amministratore.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## <a name="reference-a-secret-with-dynamic-id"></a>Fare riferimento a un segreto con id dinamico

Nella sezione precedente come passare un id risorsa statica per il segreto archivio chiave. Tuttavia, in alcuni casi, è necessario fare riferimento a un segreto archivio chiave che varia in base alla distribuzione corrente. In questo caso, non è possibile codificare id risorsa nel file di parametri. Purtroppo, in modo dinamico non è possibile generare id risorsa nel file di parametri poiché le espressioni del modello non sono consentite nel file di parametri.

Per generare dinamicamente id risorsa per un segreto archivio chiave, è necessario spostare la risorsa che deve essere il segreto in un modello nidificato. Nel modello di schema, aggiungere al modello nidificato e passare un parametro che contiene l'id risorsa generato in modo dinamico.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni generali su archivi chiave, vedere [Guida introduttiva di Azure chiave archivio](./key-vault/key-vault-get-started.md).
- Per informazioni sull'utilizzo di un archivio di chiave con una macchina virtuale, vedere [Considerazioni sulla sicurezza per gestione di risorse di Azure](best-practices-resource-manager-security.md).
- Per esempi completi per fare riferimento a informazioni riservate chiave, vedere [esempi di archivio di chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

