<properties
   pageTitle="Distribuzione di più istanze di risorse | Microsoft Azure"
   description="Utilizzare operazione di copia e le matrici in un modello di gestione di risorse Azure per scorrere più volte quando la distribuzione delle risorse."
   services="azure-resource-manager"
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
   ms.date="06/30/2016"
   ms.author="tomfitz"/>

# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Creare più istanze di risorse in Gestione risorse di Azure

In questo argomento viene illustrato come scorrere il modello di gestione risorse Azure per creare più istanze di una risorsa.

## <a name="copy-copyindex-and-length"></a>copia, copyIndex e lunghezza

All'interno della risorsa per creare più volte, è possibile definire un oggetto **Copia** che specifica il numero di volte per scorrere. La copia consente di accedere nel formato seguente:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

È possibile accedere al valore di iterazione corrente con la funzione **copyIndex()** , come illustrato di seguito all'interno della funzione CONCATENA.

    [concat('examplecopy-', copyIndex())]

Quando si creano più risorse da un'ampia gamma di valori, è possibile utilizzare la funzione **lunghezza** per specificare il numero. Fornire la matrice come parametro per la funzione lunghezza.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## <a name="use-index-value-in-name"></a>Usare il valore di indice nella casella nome

È possibile utilizzare la copia operazione creare più istanze di una risorsa in modo univoco sono denominate in base all'indice incremento. È consigliabile, ad esempio, aggiungere un numero univoco alla fine di ogni nome di risorsa che viene distribuito. Per distribuire tre siti web denominato:

- examplecopy 0
- examplecopy-1
- examplecopy-2.

Utilizzare il modello seguente:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Valore di indice scarto

Si noterà dell'esempio precedente che il valore di indice passa da zero a 2. Per spostare il valore di indice, è possibile passare un valore nella funzione **copyIndex()** , ad esempio **copyIndex(1)**. Il numero di iterazioni per l'esecuzione viene comunque specificato nell'elemento copia, ma il valore di copyIndex offset dal valore specificato. Pertanto, con lo stesso modello dell'esempio precedente, ma specificando **copyIndex(1)** trattasse tre siti web denominato:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## <a name="use-copy-with-array"></a>Utilizzo di una copia di matrice
   
L'operazione di copia è particolarmente utile quando si lavora con le matrici quanto è possibile scorrere ogni elemento della matrice. Per distribuire tre siti web denominato:

- examplecopy Contoso
- Fabrikam examplecopy
- examplecopy Coho

Utilizzare il modello seguente:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Naturalmente, impostare il numero di copie su un valore diverso dalla lunghezza della matrice. Ad esempio, può creare una matrice con numero di valori e passare in un valore di parametro che specifica il numero di elementi in forma di matrice per distribuire. In questo caso, impostare il numero di copie come illustrato nell'esempio prima. 

## <a name="depending-on-resources-in-a-loop"></a>A seconda delle risorse in un ciclo

È possibile specificare che una risorsa deve essere distribuito dopo un'altra risorsa utilizzando l'elemento **dependsOn** . Quando si desidera distribuire una risorsa dipende dalla raccolta di risorse in un ciclo, è possibile usare il nome del ciclo copia nell'elemento **dependsOn** . Nell'esempio seguente viene illustrato come distribuire 3 account di archiviazione prima di distribuire la macchina virtuale. Non viene visualizzata la definizione di macchina virtuale completa. Si noti che l'elemento copia ha **nome** impostato su **storagecopy** e l'elemento **dependsOn** per macchine virtuali è impostata su **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                    "name": "storagecopy", 
                    "count": 3 
                }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="looping-on-a-nested-resource"></a>Ciclo continuo in una risorsa annidata

È possibile utilizzare un ciclo di copia di una risorsa annidata. Se è necessario creare più istanze di una risorsa che in genere impostati come nidificato all'interno di un'altra risorsa, è invece necessario creare la risorsa come risorsa principale e definire la relazione con la risorsa padre tramite le proprietà di **tipo** e **il nome** .

Si supponga ad esempio, che è in genere definire un set di dati come una risorsa nidificata all'interno di una Factory di dati.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Per creare più istanze del set di dati, è necessario modificare il modello, come illustrato di seguito. Tipo di avviso relativo all'intero qualificati e il nome include il nome del produttore di dati.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Creare più istanze quando non è possibile usare copia

È possibile utilizzare **Copia** solo sui tipi di risorse, non sulle proprietà all'interno di un tipo di risorsa. Questo può creare problemi dell'utente quando si desidera creare più istanze di un elemento che fa parte di una risorsa. Uno scenario comune consiste nel creare più dischi di dati per una macchina virtuale. È possibile utilizzare **Copia** con dischi dati poiché **dataDisks** è una proprietà del computer virtuale, non il proprio tipo di risorsa. Se, tuttavia, si crea una matrice con tanti dischi di dati come sarà necessario, passare il numero effettivo di dischi di dati per creare. Nella definizione di macchina virtuale, utilizzare la funzione **eseguire** per ottenere solo il numero di elementi che si desidera che dalla matrice.

Un esempio completo di questo modello è indicata nella finestra [Crea una macchina virtuale con una selezione dinamica dei dischi dati](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) modello.

Le sezioni pertinenti del modello di distribuzione sono illustrate di seguito. Molte il modello sono stato rimosso per evidenziare le sezioni per in modo dinamico la creazione di un numero di dischi di dati. Si noti il parametro **numDataDisks** che consente di passare il numero di dischi da creare. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## <a name="next-steps"></a>Passaggi successivi
- Se si desidera conoscere le sezioni di un modello, vedere [Creazione di modelli di Manager delle risorse Azure](./resource-group-authoring-templates.md).
- Per tutte le funzioni che è possibile usare in un modello, vedere [Funzioni di modello di Azure Manager delle risorse](./resource-group-template-functions.md).
- Per informazioni su come distribuire il modello, vedere [distribuire un'applicazione con il modello di gestione risorse Azure](resource-group-template-deploy.md).
