<properties
   pageTitle="Creazione di modelli di gestione risorse Azure | Microsoft Azure"
   description="Creare modelli di gestione risorse Azure utilizzando la sintassi JSON dichiarativa per distribuire le applicazioni Azure."
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
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>Creazione di modelli di Manager delle risorse di Azure

Questo argomento illustra la struttura di un modello di gestione di risorse Azure. Presenta sezioni diverse di un modello e le proprietà disponibili in tali sezioni. Il modello è composto da JSON ed espressioni che è possibile utilizzare per creare i valori per la distribuzione. 

Per visualizzare il modello per le risorse che è già stato distribuito, vedere [esportare un modello di gestione risorse di Azure dalle risorse esistenti](resource-manager-export-template.md). Per ulteriori informazioni sulla creazione di un modello, vedere [Procedura dettagliata modello Manager delle risorse](resource-manager-template-walkthrough.md). Per ulteriori informazioni sulla creazione di modelli, vedere [procedure consigliate per la creazione di modelli di gestione risorse di Azure](resource-manager-template-best-practices.md).

Un buon editor JSON consente di semplificare il compito di creazione di modelli. Per informazioni sull'utilizzo di Visual Studio con i modelli, vedere [creazione e la distribuzione di gruppi di risorse Azure tramite Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Per informazioni sull'utilizzo del codice Visual Studio, vedere [utilizzo dei modelli di gestione risorse Azure nel codice Visual Studio](resource-manager-vs-code.md).

Limitare le dimensioni del modello a 1 MB e ogni file di parametro a 64 KB. Il limite di 1 MB si applica allo stato finale del modello dopo che è stato espanso con le definizioni delle risorse iterativo i valori di variabili e parametri. 

## <a name="template-format"></a>Formato di modello

Nella struttura semplice, un modello contiene gli elementi seguenti:

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nome dell'elemento   | Obbligatorio | Descrizione
| :------------: | :------: | :----------
| $schema        |   Sì    | Posizione del file di schema JSON che descrive la versione del linguaggio modello. Utilizzare l'URL indicato nell'esempio precedente.
| contentVersion |   Sì    | Versione del modello (ad esempio 1.0.0.0). È possibile specificare un valore per questo elemento. Quando si distribuisce risorse utilizzando il modello, è possibile utilizzare questo valore per assicurarsi che il modello migliore con viene utilizzato.
| parametri     |   No     | Valori che sono disponibili durante l'esecuzione di distribuzione per personalizzare la distribuzione delle risorse.
| variabili      |   No     | Valori che vengono utilizzati come frammenti JSON nel modello per semplificare le espressioni di lingua del modello.
| risorse      |   Sì    | Tipi di risorse distribuiti o aggiornati in un gruppo di risorse.
| output        |   No     | Valori restituiti dopo la distribuzione.

Esaminiamo le sezioni del modello dettagliatamente più avanti in questo argomento. Per il momento verranno descritte alcune di sintassi che costituiscono il modello.

## <a name="expressions-and-functions"></a>Espressioni e funzioni

La sintassi di base del modello è JSON. Tuttavia, le espressioni e funzioni estendono JSON disponibile nel modello. Con le espressioni, si crea valori che non sono valori letterali strict. Le espressioni sono racchiusi tra parentesi quadre [e] e vengono valutati quando si distribuisce il modello. Espressioni essere visualizzati in un punto qualsiasi in un valore stringa JSON e sempre restituire un altro valore JSON. Se è necessario utilizzare una stringa che inizia con una parentesi [, è necessario utilizzare due parentesi quadre [[.

In genere, usare le espressioni con le funzioni per eseguire operazioni per la configurazione di distribuzione. Proprio come in JavaScript, chiamate di funzione formattate come **functionName(arg1,arg2,arg3)**. Proprietà si fa riferimento, utilizzare gli operatori punto e [index].

Nell'esempio seguente viene illustrato come utilizzare diverse funzioni durante la creazione di valori:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Per un elenco completo delle funzioni di modello, vedere [gestione di risorse di Azure modello funzioni](resource-group-template-functions.md). 


## <a name="parameters"></a>Parametri

Nella sezione parametri del modello, specificare i valori che è possibile inserire quando si distribuisce le risorse. Questi valori di parametro consentono di personalizzare la distribuzione fornendo i valori che sono specifiche per un determinato ambiente (ad esempio sviluppo, test e produzione). Non è necessario fornire parametri nel modello scelto, ma senza parametri il modello sarà sempre distribuire le stesse risorse con nomi, posizioni e proprietà.

Per impostare i valori per le risorse distribuite, è possibile usare questi valori di parametro in tutto il modello. Solo i parametri dichiarati nella sezione parametri possono essere utilizzati in altre sezioni del modello.

Definire i parametri con la struttura seguente:

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| Nome dell'elemento   | Obbligatorio | Descrizione
| :------------: | :------: | :----------
| parameterName  |   Sì    | Nome del parametro. Deve essere un identificatore JavaScript valido.
| tipo           |   Sì    | Tipo del valore del parametro. Vedere l'elenco di sotto dei tipi consentiti.
| defaultValue   |   No     | Valore predefinito per il parametro, se viene specificato alcun valore per il parametro.
| allowedValues  |   No     | Matrice di valori consentiti per il parametro per assicurarsi che il valore destro viene fornito.
| minValue       |   No     | Il valore minimo per i parametri di tipo int, questo valore è estremi inclusivo.
| maxValue       |   No     | Il valore massimo per i parametri di tipo int, questo valore è estremi inclusivo.
| minLength      |   No     | La lunghezza minima di stringa, secureString e parametri di tipo matrice, questo valore è estremi inclusivo.
| maxLength      |   No     | La lunghezza massima della stringa, secureString e parametri di tipo matrice, questo valore è estremi inclusivo.
| Descrizione    |   No     | Descrizione del parametro, viene visualizzato agli utenti del modello tramite l'interfaccia portale modello personalizzato.

I valori e i tipi di consentiti sono:

- **stringa**
- **secureString**
- **int**
- **bool**
- **oggetto** 
- **secureObject**
- **in forma di matrice**

Per specificare un parametro come facoltativo, specificare un valore predefinito (può essere una stringa vuota). 

Se si specifica un nome di parametro che corrisponde a uno dei parametri di comando per distribuire il modello, viene chiesto di specificare un valore per un parametro con il suffisso **FromTemplate**. Ad esempio, se si include un parametro denominato **ResourceGroupName** nel modello scelto che corrisponde al parametro **ResourceGroupName** nel [Nuovo AzureRmResourceGroupDeployment] [ deployment2cmdlet] cmdlet, viene chiesto di specificare un valore per **ResourceGroupNameFromTemplate**. In generale, è consigliabile evitare questa confusione assegnando non parametri con lo stesso nome parametri utilizzati per le operazioni di distribuzione.

>[AZURE.NOTE] Tutte le password, chiavi e altre informazioni riservate devono utilizzare il tipo di **secureString** . Non è possibile leggere i parametri del modello con il tipo di secureString dopo la distribuzione delle risorse. 

Nell'esempio seguente viene illustrato come definire i parametri:

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Per informazioni su come i valori dei parametri di input durante l'installazione, vedere [distribuire un'applicazione con il modello di gestione risorse di Azure](resource-group-template-deploy.md#parameter-file). 

## <a name="variables"></a>Variabili

Sezione variabili, sarà possibile creare i valori che possono essere usati in tutto il modello. In genere variabili basate sui valori forniti dai parametri. Non è necessario definire variabili, ma vengono spesso semplificare il modello riducendo espressioni complesse.

Si definiscono variabili con la struttura seguente:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

Nell'esempio seguente viene illustrato come definire una variabile è costituita da due valori di parametro:

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

Nell'esempio seguente mostra una variabile di tipo JSON complesso e variabili che sono costituite da altre variabili:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>Risorse

Nella sezione risorse si definiscono le risorse distribuite o aggiornate. In questa sezione accessibile complicata perché è necessario comprendere i tipi di che distribuzione per fornire i valori corretti. 

Si definiscono le risorse con la struttura seguente:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nome dell'elemento             | Obbligatorio | Descrizione
| :----------------------: | :------: | :----------
| apiVersion               |   Sì    | Versione dell'API REST da utilizzare per la creazione della risorsa.
| tipo                     |   Sì    | Tipo di risorsa. Questo valore è una combinazione di spazio dei nomi del provider di risorse e il tipo di risorsa (ad esempio **Microsoft.Storage/storageAccounts**).
| nome                     |   Sì    | Nome della risorsa. Il nome deve seguire restrizioni componente URI definite in RFC3986. Inoltre, i servizi di Azure che espongono il nome della risorsa per convalidare il nome per verificare che all'esterno di parti non è un tentativo di spoofing di un'altra identità. Verificare [il nome di risorsa](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| posizione                 |   Variabile  | Posizioni geografico supportati della risorsa fornita. È possibile selezionare una delle posizioni disponibili, ma in genere è opportuno selezionarne uno simile a quello degli utenti. In genere, inoltre opportuno il posizionamento delle risorse che interagiscono tra loro nella stessa regione. La maggior parte dei tipi di risorse richiedono un percorso, ma alcuni tipi (ad esempio un'assegnazione di ruolo) non richiedono un percorso.
| tag                     |   No     | Tag associato alla risorsa.
| commenti                 |   No     | Le note per la documentazione di risorse in un modello
| dependsOn                |   No     | Risorse che dipende la risorsa viene definita. Le dipendenze tra le risorse vengono valutate e risorse vengono distribuite in ordine di dipendenti. Quando le risorse non sono correlate, vengono distribuiti in parallelo. Il valore può essere un elenco delimitato da virgole di una risorsa nomi o gli identificatori univoci delle risorse.
| proprietà               |   No     | Impostazioni di configurazione specifiche delle risorse. I valori per le proprietà sono uguali ai valori che immessi nel corpo della richiesta per l'operazione di API REST (metodo di caricamento) creare la risorsa. Per i collegamenti a risorse schema documentazione o API REST, vedere [provider di gestione delle risorse, aree, API versioni e schemi](resource-manager-supported-services.md).
| copia                     |   No     | Se sono necessari più istanze, il numero di risorse da creare. Per ulteriori informazioni, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md). |
| risorse                |   No     | Risorse figlio che dipendono dalla risorsa viene definita. È possibile fornire solo i tipi di risorse consentite in base allo schema della risorsa padre. Il nome completo del tipo di risorsa figlio include il tipo di risorsa padre, ad esempio **Microsoft.Web/sites/extensions**. Dipendenza da risorsa padre non è implicita; è necessario definire esplicitamente tale dipendenza. 

In base ai valori per specificare per **apiVersion**, **tipo**e il **percorso** non sono immediatamente evidenti. Per fortuna, è possibile determinare questi valori tramite PowerShell Azure o CLI Azure.

Per visualizzare tutti i provider di risorse con **PowerShell**, usare:

    Get-AzureRmResourceProvider -ListAvailable

Dall'elenco restituito, individuare il provider di risorse che interessa. Per ottenere i tipi di risorse per un provider di risorse (ad esempio lo spazio di archiviazione), usare:

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

Per ottenere le versioni di API per un tipo di risorsa (ad esempio account di archiviazione), usare:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

Per ottenere percorsi supportati per un tipo di risorsa, usare:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

Per visualizzare tutti i provider di risorse con **Azure CLI**, usare:

    azure provider list

Dall'elenco restituito, individuare il provider di risorse che interessa. Per ottenere i tipi di risorse per un provider di risorse (ad esempio lo spazio di archiviazione), usare:

    azure provider show Microsoft.Storage

Per ottenere ambienti supportati e le versioni di API, usare:

    azure provider show Microsoft.Storage --details --json

Per ulteriori informazioni sui provider di risorse, vedere [provider di gestione delle risorse, aree, API versioni e schemi](resource-manager-supported-services.md).

La sezione risorse contiene una matrice di risorse per distribuire. All'interno di ogni risorsa, è inoltre possibile definire una matrice di risorse figlio. Di conseguenza, la sezione relativa alle risorse può avere una struttura come:

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


Nell'esempio seguente mostra una risorsa **Microsoft.Web/serverfarms** e una risorsa **Microsoft.Web/sites** con figlio **estensioni** risorsa. Si noti che il sito sia contrassegnato come dipende dalla server farm poiché la server farm deve essere presente prima di poter distribuire il sito. Si noti anche che la risorsa **estensioni** figlio del sito.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>Output

Nella sezione output specificare i valori restituiti da distribuzione. Ad esempio, si può restituire l'URI per accedere a una risorsa distribuita.

Nell'esempio seguente viene illustrata la struttura di una definizione di output:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nome dell'elemento   | Obbligatorio | Descrizione
| :------------: | :------: | :----------
| nomeoutput     |   Sì    | Nome del valore di output. Deve essere un identificatore JavaScript valido.
| tipo           |   Sì    | Tipo di valore di output. Valori di output supportano gli stessi tipi di parametri di input modello.
| valore          |   Sì    | Modello lingua espressione viene valutata e restituito come valore di output.


Nell'esempio seguente mostra un valore che viene restituito nella sezione output.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Per ulteriori informazioni sull'uso di output, vedere [condivisione dello stato in Gestione risorse di Azure modelli](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Passaggi successivi
- Per visualizzare i modelli di completamento per molti tipi diversi di soluzioni, vedere i [Modelli di Guida introduttiva di Azure](https://azure.microsoft.com/documentation/templates/).
- Per informazioni dettagliate sulle funzioni che è possibile utilizzare dall'interno di un modello, vedere [Funzioni di modello di Azure Manager delle risorse](resource-group-template-functions.md).
- Per combinare più modelli durante l'installazione, vedere [uso dei modelli collegati Gestione risorse Azure](resource-group-linked-templates.md).
- Potrebbe essere necessario utilizzare le risorse che si trovano all'interno di un gruppo di risorse diversi. Questo scenario è comune quando si lavora con gli account di archiviazione o reti virtuali che vengono condivisi tra più gruppi di risorse. Per ulteriori informazioni, vedere la [funzione resourceId](resource-group-template-functions.md#resourceid).


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
