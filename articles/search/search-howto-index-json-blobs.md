<properties
pageTitle="L'indicizzazione BLOB JSON con indicizzatore blob Azure ricerca"
description="L'indicizzazione BLOB JSON con indicizzatore blob Azure ricerca"
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
ms.date="07/26/2016"
ms.author="eugenesh" />

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>L'indicizzazione BLOB JSON con indicizzatore blob Azure ricerca 

In questo articolo viene illustrato come configurare indicizzatore blob Azure ricerca per estrarre il contenuto strutturato da BLOB contenenti JSON.

## <a name="scenarios"></a>Scenari

Per impostazione predefinita, [indicizzatore blob Azure ricerca](search-howto-indexing-azure-blob-storage.md) analizza BLOB JSON come un singolo blocco di testo. Spesso, si desidera mantenere la struttura dei documenti JSON. Ad esempio, data documento JSON 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

è consigliabile analizzare in un documento di Azure ricerca di "testo", "Data di pubblicazione" e "tag" campi.

In alternativa, quando il BLOB contiene una **matrice di oggetti JSON**, è consigliabile ogni elemento della matrice per imparare a un documento di ricerca di Azure separato. Ad esempio, dato un blob con questo JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

è possibile inserire l'indice di ricerca di Azure con 3 documenti distinti, ognuna con i campi "id" e "testo". 

> [AZURE.IMPORTANT] Questa funzionalità è attualmente in anteprima. È disponibile solo in API REST usando la versione **Anteprima 28 di 02 2015**. Per ricordare, visualizzare in anteprima API vengono utilizzate per la verifica e valutazione di e non deve essere utilizzate in ambienti di produzione. 

## <a name="setting-up-json-indexing"></a>La configurazione dell'indicizzazione JSON

Per indicizzare BLOB JSON, impostare il `parsingMode` parametro di configurazione per `json` (indicizzare ogni blob come singolo documento) o `jsonArray` (se il BLOB contengono una matrice JSON): 

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

Se necessario, utilizzare **i mapping dei campi** per selezionare le proprietà del documento di origine JSON utilizzato per popolare l'indice di ricerca di destinazione.  Come descritto in dettaglio di seguito. 

> [AZURE.IMPORTANT] Quando si utilizza `json` o `jsonArray` modalità di analisi, Azure ricerca presuppone che tutti i BLOB nell'origine dati saranno JSON. Se è necessario supportare una combinazione di BLOB JSON e non JSON nella stessa origine dati, indicare sul [sito Uservoicehttp](https://feedback.azure.com/forums/263029-azure-search).

## <a name="using-field-mappings-to-build-search-documents"></a>Utilizzo di mapping dei campi per la creazione di documenti di ricerca 

Al momento Azure ricerca non è possibile indicizzare non autorizzato JSON documenti direttamente, poiché supporta i tipi di dati solo base, matrici di stringhe e GeoJSON punti. Tuttavia, è possibile utilizzare **i mapping dei campi** selezionare parti del documento JSON e "sollevare" loro in campi di livello superiore del documento di ricerca. Per informazioni sulle nozioni fondamentali sul mapping dei campi, vedere [mapping dei campi di ricerca di Azure indicizzatore bridge le differenze tra le origini dati e gli indici di ricerca](search-indexer-field-mappings.md).

Confermata di rifinitura al esempio JSON documento: 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Supponiamo che si dispone di un indice di ricerca con i campi seguenti: `text` di tipo Edm.String, `date` di tipo DateTimeOffset, e `tags` di tipo Collection(Edm.String). Per mappare i JSON nella forma desiderata, utilizzare i mapping dei campi seguenti: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]

I nomi dei campi di origine in mapping vengono specificati utilizzando la notazione [JSON puntatore](http://tools.ietf.org/html/rfc6901) . Iniziare con una barra (/) per fare riferimento alla radice del documento JSON, quindi eseguire il drill-nella casella della proprietà desiderata (livello non autorizzato di annidamento) utilizzando il percorso di separati da barra inoltro. 

È anche possibile fare riferimento agli elementi singoli in forma di matrice con zero. Per selezionare il primo elemento della matrice "tag" dell'esempio precedente, ad esempio, utilizzare il mapping di un campo alla seguente:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Se il nome di un campo di origine in un percorso di mapping campo si riferisce a una proprietà che non esiste nel JSON, tale mapping viene ignorato senza un messaggio di errore. In questo modo possiamo documenti con un altro schema (che è un caso di utilizzo comune). Poiché non vi è alcuna convalida, è necessario prestare attenzione per evitare errori di battitura nella specifica di mapping dei campi. 

Se il documento JSON contiene solo le proprietà di primo livello semplice, potrebbe essere necessario non mapping dei campi del tutto. Ad esempio, se il JSON è simile alla seguente, proprietà di primo livello "testo", "Data di pubblicazione" di "tag" verranno direttamente mappate ai campi corrispondenti nell'indice di ricerca: 
 
    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
    }

## <a name="indexing-nested-json-arrays"></a>L'indicizzazione matrici JSON annidate

Cosa fare se si desidera indicizzare una matrice di oggetti JSON, ma la matrice è nidificato in un punto qualsiasi all'interno del documento? È possibile selezionare la proprietà contiene la matrice utilizzando il `documentRoot` proprietà di configurazione. Ad esempio, se il BLOB aspetto simile al seguente: 

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

Utilizzare questa configurazione per indicizzare la matrice contenuta nella proprietà "level2": 

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


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
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
    }

## <a name="help-us-make-azure-search-better"></a>Aiutaci a migliorare ricerca Azure

Se si dispone di richiedere caratteristiche o idee per miglioramenti, per raggiungere i Contattaci sul nostro [sito Uservoicehttp](https://feedback.azure.com/forums/263029-azure-search/).