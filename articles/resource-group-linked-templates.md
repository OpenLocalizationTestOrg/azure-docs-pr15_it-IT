<properties
   pageTitle="Collegamento modelli di gestione risorse | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare modelli collegati in un modello di gestione di risorse Azure per creare una soluzione di modello modulare. Viene illustrato come passare valori dei parametri, specificare un file di parametri e URL creato in modo dinamico."
   services="azure-resource-manager"
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
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# <a name="using-linked-templates-with-azure-resource-manager"></a>Utilizzare i modelli collegati con Gestione risorse di Azure

Da all'interno di un modello di gestione risorse di Azure, è possibile collegarsi a un altro modello, che consente di scomporre la distribuzione in un set di destinazione, modelli specifici di scopo. Come con la scomposizione di un'applicazione in varie classi di codice, scomposizione vantaggi in termini di testing, riutilizzo e la leggibilità.  

È possibile passare parametri da un modello principale a un modello collegato e i parametri possono direttamente il mapping di parametri o variabili esposte dal modello di chiamata. Il modello collegato può anche passare a una variabile di output al modello origine per consentire un scambio di dati bidirezionale tra i modelli.

## <a name="linking-to-a-template"></a>Collegamento a un modello

Per creare un collegamento tra due modelli mediante l'aggiunta di una risorsa di distribuzione all'interno del modello principale che punta al modello collegato. Impostare la proprietà **templateLink** URI del modello collegato. È possibile specificare i valori dei parametri per il modello collegato specificando valori direttamente in un modello o tramite un collegamento a un file di parametri. Nell'esempio seguente viene utilizzata la proprietà **parametri** per specificare un valore di parametro direttamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Il servizio di gestione risorse deve essere in grado di accedere al modello collegato. È possibile specificare un file locale o un file che è disponibile solo in rete locale per il modello collegato. È possibile fornire solo un valore URI che include **http** o **https**. Un'opzione possibile consiste di inserire il modello collegato in un account di archiviazione e utilizzare l'URI per tale elemento, come illustrato nell'esempio seguente.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Anche se il modello collegato deve essere disponibile esternamente, non è necessario essere generalmente disponibile al pubblico. È possibile aggiungere il modello a un account di archiviazione privato accessibile a solo il proprietario dell'account di archiviazione. Creare quindi un token di firme (SA) accesso condiviso per abilitare l'accesso durante l'installazione. Aggiungere tale token SA URI per il modello collegato. Per istruzioni sulla configurazione di un modello di un account di archiviazione e generando un token SA, vedere [le risorse di distribuzione con i modelli di Manager delle risorse e Azure PowerShell](resource-group-template-deploy.md) o [risorse di distribuzione con i modelli di Manager delle risorse e Azure CLI](resource-group-template-deploy-cli.md). 

Nell'esempio seguente mostra un modello padre collegato a un altro modello. Il modello collegato è possibile accedere con un token SA passato come parametro.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Anche se il token viene passato come stringa sicura, URI del modello di collegate, tra cui token SA ha eseguito l'accesso alle operazioni di distribuzione per il gruppo di risorse. Per limitare l'esposizione, impostare una scadenza per il token.

## <a name="linking-to-a-parameter-file"></a>Collegamento a un file di parametro

Nell'esempio seguente viene utilizzata la proprietà **parametersLink** per creare un collegamento a un file di parametri.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

Il valore URI per il file collegato parametro non può essere un file locale e deve includere **http** o **https**. Il file di parametro può anche essere limitato per l'accesso tramite un token di SA.

## <a name="using-variables-to-link-templates"></a>Utilizzo di variabili per creare un collegamento modelli

Negli esempi precedenti è stato hardcoded URL i valori per i collegamenti di modello. Questo approccio potrebbe funzionare per un modello semplice ma non funziona anche quando si lavora con un elevato numero di modelli modulari. Se, tuttavia, è possibile creare una variabile statica contenente un URL di base per il modello principale e quindi creare dinamicamente URL per i modelli collegati da tale URL di base. Il vantaggio di questo approccio è possibile spostare o divisione il modello perché è necessario modificare la variabile statica nel modello principale facilmente. Il modello principale passa URI corretto in tutto il modello scomposto.

Nell'esempio seguente viene illustrato come utilizzare un URL di base per creare due URL per i modelli collegati (**sharedTemplateUrl** e **vmTemplate**). 

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

È anche possibile utilizzare [distribuzione ()](resource-group-template-functions.md#deployment) per ottenere l'URL di base per il modello corrente e che consente di ottenere l'URL per altri modelli nella stessa posizione. Questo approccio è utile se viene modificato il percorso dei modelli (ad esempio a causa di controllo delle versioni) o che si desidera evitare la codifica URL nel file del modello. 

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## <a name="conditionally-linking-to-templates"></a>In modo condizionale il collegamento ai modelli

È possibile collegare a diversi modelli passando un valore di parametro viene utilizzato per costruire URI del modello collegato. Questo approccio funziona anche quando è necessario specificare durante la distribuzione collegato modello da usare. Ad esempio, è possibile specificare un modello da usare per un account di archiviazione esistente e un altro modello da usare per un nuovo account di archiviazione.

Nell'esempio seguente mostra un parametro per il nome di un account di archiviazione e un parametro per specificare se l'account di archiviazione è nuovo o esistente.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Creare una variabile per il modello URI che include il valore del parametro nuovo o esistente.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Per fornire il valore variabile per risorse per la distribuzione.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

URI risolve in un modello denominato **existingStorageAccount.json** o **newStorageAccount.json**. Creare modelli per tali URI.

Nell'esempio seguente viene illustrato il modello **existingStorageAccount.json** .

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

Nell'esempio seguente viene illustrato il modello **newStorageAccount.json** . Notare che lo spazio di archiviazione esistente modello conto oggetto account di archiviazione verrà restituito nell'output. Il modello di schema funziona con uno dei due modello collegato.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## <a name="complete-example"></a>Esempio completo

I modelli di esempio seguente mostrano una disposizione semplificata dei modelli collegati per illustrare diverse concetti illustrati in questo articolo. Si presuppone che i modelli sono stati aggiunti allo stesso contenitore di un account di archiviazione di pubblico accesso disattivato. Il modello collegato restituisce un valore al modello principale nella sezione **output** .

Il file **parent.json** è costituito da:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

Il file **helloworld.json** è costituito da:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {
        "result": {
            "value": "Hello World",
            "type" : "string"
        }
      }
    }
    
In PowerShell, è possibile aggiungere un token per il contenitore e distribuire i modelli con:

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

In CLI Azure, è possibile aggiungere un token per il contenitore e distribuire i modelli con il codice seguente. Attualmente, è necessario specificare un nome per la distribuzione quando si usa un modello che includa un token di SA.  

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Viene chiesto di fornire token sa come parametro. È necessario anteporre il token con **?**.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sulla definizione l'ordine di distribuzione per le risorse, vedere [definizione delle dipendenze nei modelli di gestione risorse di Azure](resource-group-define-dependencies.md)
- Per informazioni su come definire una risorsa ma creare più istanze, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md)
