<properties
pageTitle="L'indicizzazione archiviazione tabelle Azure con ricerca Azure"
description="Informazioni su come indicizzare i dati archiviati in tabelle Azure con ricerca Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>L'indicizzazione archiviazione tabelle Azure con ricerca Azure

In questo articolo viene illustrato come utilizzare la ricerca di Azure indicizzare i dati archiviati in archiviazione tabella Azure. Il nuovo indicizzatore tabella di ricerca di Azure rende questo processo semplice e rapido. 

> [AZURE.IMPORTANT] Questa funzionalità è attualmente in anteprima. È disponibile solo in API REST usando la versione **Anteprima 28 di 02 2015** e nella versione 2.0 per l'anteprima di .NET SDK. Per ricordare, visualizzare in anteprima API vengono utilizzate per la verifica e valutazione di e non deve essere utilizzate in ambienti di produzione.

## <a name="setting-up-azure-table-indexing"></a>La configurazione dell'indicizzazione tabelle Azure

Per installare e configurare un indicizzatore tabelle Azure, è possibile utilizzare l'API REST ricerca Azure per creare e gestire **origini dati** e **indicizzatori** come descritto nelle [operazioni dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946891.aspx). È anche possibile utilizzare [versione 2.0 per l'anteprima](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) di .NET SDK. In futuro, il supporto per l'indicizzazione nella tabella verranno aggiunti al portale di Azure.

Un'origine dati specifica i dati da indicizzare credenziali necessarie per accedere ai dati e dei criteri che consentono di ricerca di Azure identificare in modo efficiente le modifiche dei dati (nuovo, modificare o eliminare righe).

Un indicizzatore legge i dati da un'origine dati e carica in un indice di ricerca di destinazione.

Per impostare l'indice della tabella:

1. Creare un'origine dati
    - Impostare il `type` parametro`azuretable`
    - Passare la stringa di connessione dello spazio di archiviazione account come la `credentials.connectionString` parametro
    - Specificare il nome di tabella mediante la `container.name` parametro
    - Se si desidera, specificare una query utilizzando la `container.query` parametro. Se possibile, utilizzare un filtro in PartitionKey per ottenere prestazioni ottimali; qualsiasi altra query comporta la scansione di tabella completo questo può comportare la riduzione delle prestazioni per tabelle di grandi dimensioni.
2. Creare un indice di ricerca con lo schema che corrisponde alle colonne nella tabella che si desidera indicizzare. 
3. Creare l'indicizzatore collegando l'origine dati per l'indice di ricerca.

### <a name="create-data-source"></a>Creare l'origine dati

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Per ulteriori informazioni sulla creazione di API Datasource, vedere [Creare origine dati](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="create-index"></a>Creare l'indice 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

Per ulteriori informazioni sull'API di indice creare, vedere [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Creare indicizzatore 

Infine, creare indicizzatore che fa riferimento l'origine dati e l'indice di destinazione. Per esempio:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Per ulteriori informazioni sulla creazione di API indicizzatore, vedere [Creare indicizzatore](search-api-indexers-2015-02-28-preview.md#create-indexer).

È tutto occorre - indicizzazione buon!

## <a name="dealing-with-different-field-names"></a>Gestire i nomi dei campi diversi

Spesso, i nomi dei campi dell'indice di esistente sono diversi dai nomi delle proprietà della tabella. È possibile utilizzare **i mapping dei campi** per mappare i nomi delle proprietà della tabella per i nomi dei campi dell'indice di ricerca. Per ulteriori informazioni sui mapping dei campi, vedere [mapping dei campi di ricerca di Azure indicizzatore bridge le differenze tra le origini dati e gli indici di ricerca](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>La gestione delle chiavi di documento

Risultati di ricerca di Azure, la chiave di documento identifica un documento. Ogni indice di ricerca deve avere esattamente un campo chiave di tipo `Edm.String`. Il campo chiave è necessario per ogni documento che si desidera aggiungere all'indice (in realtà è l'unico campo obbligatorio).

Poiché le righe della tabella dispone di una chiave composta, Azure ricerca genera un campo sintetico chiamato `Key` che rappresenta una concatenazione di valori chiave partizione chiave e della riga. Ad esempio, se una riga del PartitionKey è `PK1` e RowKey `RK1`, quindi `Key` valore del campo sarà `PK1RK1`. 

> [AZURE.NOTE] Il `Key` valore può contenere caratteri non validi nei tasti singoli documenti, ad esempio linee tratteggiate. Consente di gestire con caratteri non validi tramite le `base64Encode` [funzione di mapping di campo](search-indexer-field-mappings.md#base64EncodeFunction). Se si esegue questa operazione, tenere presente è anche possibile utilizzare la codifica base 64 attendibili URL quando passando chiavi documento API di chiamata, ad esempio ricerca.

## <a name="incremental-indexing-and-deletion-detection"></a>Rilevamento di indicizzazione e l'eliminazione incrementale
 
Quando si imposta la indicizzatore una tabella per l'esecuzione in base alla pianificazione, esso reindexes righe solo nuove o aggiornate, in base a una riga `Timestamp` valore. Non è necessario specificare un criterio di rilevamento modifiche-incrementale indicizzazione è abilitata per l'utente automaticamente. 

Per indicare che è necessario rimuovere alcuni documenti dall'indice, è possibile utilizzare una strategia Elimina contorni – invece di eliminazione di una riga, aggiungere una proprietà per indicare che venga eliminato e impostare un criterio di rilevamento contorni eliminazione nell'origine dati. Ad esempio, il criterio illustrato di seguito verrà preso in considerazione che una riga viene eliminata se dispone di una proprietà `IsDeleted` con il valore `"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Aiutaci a migliorare ricerca Azure

Se si dispone di richiedere caratteristiche o idee per miglioramenti, per raggiungere i Contattaci sul nostro [sito Uservoicehttp](https://feedback.azure.com/forums/263029-azure-search/).