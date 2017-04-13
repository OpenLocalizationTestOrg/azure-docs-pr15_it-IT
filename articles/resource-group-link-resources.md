<properties 
    pageTitle="Collegamento di risorse in Gestione risorse di Azure | Microsoft Azure" 
    description="Creare un collegamento tra le relative risorse in gruppi di risorse diversi in Gestione risorse di Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/01/2016" 
    ms.author="tomfitz"/>

# <a name="linking-resources-in-azure-resource-manager"></a>Collegamento di risorse in Gestione risorse di Azure

Durante la distribuzione, è possibile contrassegnare una risorsa come dipendente da un'altra risorsa, ma tale ciclo di vita termina in fase di distribuzione. Dopo la distribuzione, è non presente alcuna relazione identificati tra le risorse dipendenti. Manager delle risorse include una caratteristica denominata risorsa il collegamento per stabilire persistente relazioni tra le risorse.

Con il collegamento delle risorse, è possibile documentare relazioni tra i gruppi di risorse. Ad esempio, è comune per un database con il proprio ciclo di vita si trovano in un gruppo di risorse e un'app con un ciclo di vita diverso si trovano in un gruppo di risorse diversi. L'app si connette al database in modo che si desidera contrassegnare un collegamento tra l'app e il database. 

Tutte le risorse collegate devono appartenere allo stesso abbonamento. Ogni risorsa può essere collegato a 50 altre risorse. L'unico modo per risorse correlate della query è tramite l'API REST. Se tutte le risorse collegate eliminati o spostati, il proprietario del collegamento è necessario pulire il collegamento rimanente. Ci si trova **non** viene visualizzato un avviso quando l'eliminazione di una risorsa è collegata ad altre risorse.

## <a name="linking-in-templates"></a>Il collegamento a modelli

Per definire un collegamento in un modello, includere un tipo di risorsa che combina lo spazio dei nomi di risorsa provider e tipo di risorsa origine con **/providers/links**. Il nome deve includere il nome della risorsa origine. Fornire l'id risorsa della risorsa di destinazione. Nell'esempio seguente consente di stabilire un collegamento tra un sito web e un account di archiviazione.

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


Per una descrizione completa del formato del modello, vedere [collegamenti a risorse - schema del modello](resource-manager-template-links.md).

## <a name="linking-with-rest-api"></a>Il collegamento all'API REST

Per definire un collegamento tra le risorse distribuite, eseguire:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Sostituire {id abbonamento} con l'id di sottoscrizione. Sostituire {gruppo risorse}, {provider dello spazio dei nomi, {tipo risorsa}, {nome-risorsa} con i valori che identifica la prima risorsa nella casella collegamento e. Sostituire {nome collegamento} con il nome del collegamento da creare. Usare 2015 01 / 01 per la versione dell'api.

Nella convocazione, includere un oggetto che definisce la seconda risorsa nella casella collegamento:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

L'elemento di proprietà contiene l'identificatore per la seconda risorsa.

È possibile eseguire una query collegamenti nell'abbonamento con:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Per altri esempi, ad esempio come recuperare le informazioni sui collegamenti, vedere [Le risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## <a name="next-steps"></a>Passaggi successivi

- È anche possibile organizzare le risorse con tag. Per informazioni sulle risorse tagging, vedere [usare i contrassegni per organizzare le risorse](resource-group-using-tags.md).
- Per una descrizione di come creare un modello e definire le risorse per la distribuzione, vedere [modelli di creazione condivisa](resource-group-authoring-templates.md).
