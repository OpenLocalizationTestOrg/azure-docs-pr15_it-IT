<properties
    pageTitle="Creare uno spazio dei nomi di evento hub con Hub di eventi e abilitare archivio utilizzando un modello di gestione di risorse Azure | Microsoft Azure"
    description="Creare uno spazio dei nomi di evento hub con Hub di eventi e abilitare archivio utilizzando il modello di gestione risorse di Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi di evento hub con Hub di eventi e abilitare archivio utilizzando un modello di gestione risorse di Azure

In questo articolo viene illustrato come utilizzare un modello di gestione risorse Azure consente di creare uno spazio dei nomi di evento hub con un Hub di eventi che consente l'archiviazione con il tuo fulcro evento. Informazioni su come per definire quali risorse vengono distribuite e come definire i parametri specificati quando viene eseguita la distribuzione. È possibile utilizzare questo modello per il proprio distribuzioni o personalizzarlo in base alle esigenze

Per ulteriori informazioni sulla creazione di modelli, vedere [modelli di creazione condivisa Manager delle risorse di Azure][].

Per ulteriori informazioni sull'esercitazione e i motivi nello convenzioni di denominazione Azure risorse, vedere [Convenzioni di denominazione di Azure risorse][].

Per il modello completo, vedere [Hub evento e abilita il modello di archiviazione][] in GitHub.

>[AZURE.NOTE]
>Per cercare modelli più recenti, visitare la raccolta di [Modelli di Guida introduttiva di Azure][] e cercare hub evento.

## <a name="what-you-deploy"></a>Si distribuisce?

Con questo modello, distribuire uno spazio dei nomi di evento hub con un Hub di eventi e abilitare l'archivio.

[Hub di evento](../event-hubs/event-hubs-what-is-event-hubs.md) è un evento servizio utilizzato per fornire eventi e telemetry ingresso in Azure in scala enorme con latenza bassa e affidabilità di elaborazione. Evento hub archivio consentono per l'invio automatico i dati di flusso nell'hub di eventi a archiviazione Blob Azure di propria scelta all'interno di un periodo di tempo specificato o all'intervallo di dimensioni scelta.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuire Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

Azure Gestione risorse, definire i parametri per i valori per specificare quando si distribuisce il modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. È necessario definire un parametro per tali valori che variano in base a project che si distribuisce o in base all'ambiente per distribuire. Non definire i parametri per i valori che restano sempre lo stesso. Ogni valore di parametro viene utilizzato nel modello per definire le risorse che vengono distribuite.

Il modello consente di definire i parametri seguenti.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Il nome dello spazio dei nomi evento hub per creare.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Il nome dell'evento Hub creato in eventi hub dello spazio dei nomi.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Il numero di giorni che si desidera messaggi conservati nell'Hub evento. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Il numero delle partizioni desiderato nell'Hub evento.

```
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="archiveenabled"></a>archiveEnabled

Abilitare l'archiviazione con l'Hub di evento.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat

Il formato di codifica specificate per serializzare i dati dell'evento.

```
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime

L'intervallo di tempo in cui l'archivio inizia archiviazione dei dati di archiviazione blob Azure.

```
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>dimensioni di archiviazione

L'intervallo di dimensioni in cui l'archivio inizia archiviazione dei dati di archiviazione blob Azure.

```
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Nell'archivio richiede un id risorsa Account di archiviazione, abilitare archivio allo spazio di archiviazione di Azure desiderato.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Archiviare il contenitore blob nel punto in cui si vuole che i dati di evento.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion

La versione dell'API del modello.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Risorse per la distribuzione

Crea uno spazio dei nomi del tipo di **EventHubs**, con un Hub di evento e Abilita archiviazione.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }
                  
               }
               
            }
         ]
      }
   ]
```

## <a name="commands-to-run-deployment"></a>Comandi per l'esecuzione di distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Creazione di modelli di Manager delle risorse di Azure]: ../resource-group-authoring-templates.md
[Guida introduttiva di Azure modelli]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Azure risorse convenzioni di denominazione]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Hub di evento e abilita il modello di archiviazione]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive
