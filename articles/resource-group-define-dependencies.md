<properties
   pageTitle="Relazioni nei modelli di gestione risorse | Microsoft Azure"
   description="Descrive come impostare una risorsa come dipende da un'altra risorsa durante la distribuzione per assicurarsi che le risorse sono distribuite nell'ordine corretto."
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
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Definizione delle dipendenze nei modelli di Manager delle risorse di Azure

Per una determinata risorsa, può essere altre risorse che deve essere presente prima che venga distribuita alla risorsa. Ad esempio, SQL server deve esistere prima di distribuire un database SQL. Per definire la relazione, contrassegnare una risorsa come dipendente da un'altra risorsa. In genere, si definisce una dipendenza con l'elemento **dependsOn** , ma è possibile definire anche tramite la funzione di **riferimento** . 

Manager delle risorse valuta le dipendenze tra le risorse e le distribuisce nell'ordine dipendente. Quando le risorse non sono correlate, Gestione risorse le distribuisce in parallelo.

## <a name="dependson"></a>dependsOn

All'interno del modello, l'elemento dependsOn consente di definire una risorsa come dipendente in una o più risorse. Il valore può essere un elenco delimitato da virgole di nomi di risorsa. 

Nell'esempio seguente mostra un set di scala macchina virtuale dipende un bilanciamento del carico, virtuali e un ciclo che consente di creare più account di archiviazione. Queste altre risorse non vengono visualizzati nell'esempio seguente, ma è necessario sono presenti in un' posizione nel modello.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Per definire una relazione tra una risorsa e le risorse che vengono create tramite un ciclo di copia, impostare l'elemento dependsOn al nome del ciclo. Ad esempio, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

Mentre potrebbe inclinato usare dependsOn mappare le relazioni tra le risorse, è importante conoscere il motivo per cui si sta eseguendo il poiché possono rallentare le prestazioni della distribuzione. Ad esempio, per tenere traccia delle modalità di interconnessi delle risorse, dependsOn non è l'approccio corretto. Non è possibile verificarne le risorse definite nell'elemento dependsOn dopo la distribuzione. Utilizzando dependsOn si influisce fase di distribuzione perché Manager delle risorse non distribuisce in parallelo due risorse che hanno una dipendenza. Relazioni tra le risorse del documento, utilizzare invece [il collegamento delle risorse](resource-group-link-resources.md).

## <a name="child-resources"></a>Risorse figlio

La proprietà di risorse consente di specificare risorse figlio correlati alla risorsa viene definita. Risorse figlio possono essere definiti cinque livelli. È importante tenere presente che una relazione implicita non è stata creata tra una risorsa figlio e la risorsa padre. Se è necessaria la risorsa figlio per essere distribuito dopo la risorsa padre, è necessario definire in modo esplicito tale dipendenza con la proprietà dependsOn. 

Ogni risorsa padre accetta solo alcuni tipi di risorse come risorse figlio. I tipi di risorse accettate vengono specificati nello [schema del modello](https://github.com/Azure/azure-resource-manager-schemas) di risorsa padre. Il nome del tipo di risorsa figlio include il nome del tipo di risorsa padre, ad esempio **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** sono entrambe le risorse figlio di **Microsoft.Web/sites**.

Nell'esempio seguente mostra un server SQL e i database SQL. Si noti che una dipendenza esplicita è definita tra il database SQL e SQL server, anche se il database è un elemento figlio del server.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>funzione di riferimento

La [funzione di riferimento](resource-group-template-functions.md#reference) consente un'espressione a derivano il relativo valore da altri nomi JSON e coppie o risorse di runtime. Le espressioni di riferimento in modo implicito dichiarano che una risorsa dipende da un altro. 

    reference('resourceName').propertyPath

È possibile utilizzare questo elemento o l'elemento dependsOn per specificare le dipendenze, ma non è necessario utilizzare entrambe le opzioni per la stessa risorsa dipendente. Ogni volta che è possibile, utilizzare un riferimento implicito per evitare di aggiungere inavvertitamente una relazione non necessarie.

Per ulteriori informazioni, vedere [funzione riferimento](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla creazione di modelli di Manager delle risorse di Azure, vedere [modelli di creazione condivisa](resource-group-authoring-templates.md). 
- Per un elenco di funzioni disponibili in un modello, vedere [funzioni di modello](resource-group-template-functions.md).

