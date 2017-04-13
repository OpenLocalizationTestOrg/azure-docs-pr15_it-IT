<properties
pageTitle="L'indicizzazione BLOB CSV con indicizzatore blob Azure ricerca | Microsoft Azure"
description="Informazioni su come indicizzare BLOB CSV con ricerca Azure"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>L'indicizzazione BLOB CSV con indicizzatore blob Azure ricerca 

Per impostazione predefinita, analizza [indicizzatore blob Azure ricerca](search-howto-indexing-azure-blob-storage.md) delimitati BLOB di testo come un singolo blocco di testo. Tuttavia, con BLOB contenente dati CSV, spesso scegliere di considerare ogni riga del BLOB come documento separato. Dato, ad esempio, il testo con valori delimitati da seguente: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

è possibile analizzare in 2 documenti, ognuno contenente "id", "Data di pubblicazione" e "tag" campi.

In questo articolo si imparerà analizzare BLOB CSV con indicizzatore blob Azure ricerca. 

> [AZURE.IMPORTANT] Questa funzionalità è attualmente in anteprima. È disponibile solo in API REST usando la versione **Anteprima 28 di 02 2015**. Per ricordare, visualizzare in anteprima API vengono utilizzate per la verifica e valutazione di e non deve essere utilizzate in ambienti di produzione. 

## <a name="setting-up-csv-indexing"></a>La configurazione dell'indicizzazione CSV

Indicizzare BLOB CSV, creare o aggiornare una definizione di indicizzatore con la `delimitedText` modalità di analisi:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Per ulteriori informazioni sulla creazione di API indicizzatore, vedere [Creare indicizzatore](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`indica che la prima riga (non vuoto) di ogni blob contiene intestazioni.
Se BLOB non contengono una riga di intestazione iniziale, è necessario specificare le intestazioni nella configurazione dell'indicizzatore: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Attualmente non è supportata solo la codifica UTF-8. Inoltre, solo il punto e virgola `','` carattere è supportata come delimitatore. Se occorre assistenza per altri codifiche o delimitatori, indicare sul [sito Uservoicehttp](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Quando si usa il testo con valori delimitati da modalità di analisi, Azure ricerca presuppone che tutti i BLOB nell'origine dati saranno CSV. Se è necessario supportare una combinazione di BLOB CSV e non CSV nell'origine dati stesso, indicare sul [sito Uservoicehttp](https://feedback.azure.com/forums/263029-azure-search).

## <a name="request-examples"></a>Esempi di richiesta

Applicazione pratica di questo tutte insieme, ecco gli esempi di payload completata. 

Origine dati: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Aiutaci a migliorare ricerca Azure

Se si dispone di richiedere caratteristiche o idee per miglioramenti, per raggiungere i Contattaci sul nostro [sito Uservoicehttp](https://feedback.azure.com/forums/263029-azure-search/).