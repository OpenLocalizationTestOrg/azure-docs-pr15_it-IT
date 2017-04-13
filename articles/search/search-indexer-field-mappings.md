<properties
pageTitle="Mapping dei campi in indicizzatori ricerca Azure"
description="Configurare i mapping dei campi di ricerca di Azure indicizzatore per includere le differenze tra i nomi dei campi e rappresentazioni di dati"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Mapping dei campi in indicizzatori ricerca Azure

Quando si usa indicizzatori ricerca Azure, è possibile trovare occasionalmente personalmente in situazioni in cui i dati di input non abbastanza corrispondono lo schema dell'indice di destinazione. In questi casi, è possibile utilizzare **i mapping dei campi** per trasformare i dati in forma desiderata. 

Alcune situazioni in cui sono utili mapping dei campi:
 
- L'origine dati è contenuto un campo `_id`, ma non consente di Azure ricerca i nomi dei campi a partire da un carattere di sottolineatura. Mapping di un campo consente di "rinominare" un campo. 
- Si desidera inserire più campi nell'indice con gli stessi dati di origine dati, ad esempio perché si desidera applicare diversi analisi a tali campi. Mapping dei campi consentono di "divisione" un campo dell'origine dati.
- È necessario in base 64 codificare o decodificare i dati. Mapping dei campi supporta diverse **funzioni di mapping**, incluse le funzioni di base 64 codifica e decodifica.   


> [AZURE.IMPORTANT] Funzionalità di mapping di questo campo è attualmente in anteprima. È disponibile solo in API REST usando la versione **Anteprima 28 di 02 2015**. Per ricordare, visualizzare in anteprima API vengono utilizzate per la verifica e valutazione di e non deve essere utilizzate in ambienti di produzione.

## <a name="setting-up-field-mappings"></a>Impostazione dei mapping dei campi

Quando si crea un nuovo indicizzatore l'utilizzo dell' [Indicizzatore creare](search-api-indexers-2015-02-28-preview.md#create-indexer) API, è possibile aggiungere mapping dei campi. È possibile gestire i mapping dei campi in un indicizzatore indicizzazione utilizzando l' [Aggiornamento dell'indicizzatore](search-api-indexers-2015-02-28-preview.md#update-indexer) API. 

Mapping di un campo è costituito da 3 parti: 

1. A `sourceFieldName`, che rappresenta un campo nell'origine dati. Questa proprietà è necessaria. 

2. Facoltativo `targetFieldName`, che rappresenta un campo dell'indice di ricerca. Se omesso, viene utilizzato lo stesso nome del database di origine dati. 

3. Facoltativo `mappingFunction`, che possono trasformare i dati usando uno dei diversi funzioni predefinite. L'elenco completo delle funzioni è [inferiore](#mappingFunctions).

Mapping dei campi vengono aggiunti ai `fieldMappings` in forma di matrice nella definizione dell'indicizzatore. 

Ad esempio, ecco come è in grado di soddisfare differenze tra i nomi dei campi: 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Un indicizzatore può contenere più mapping dei campi. Ad esempio, ecco come è possibile "divisione" un campo:

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Ricerca Azure utilizza confronto distinzione tra maiuscole e per risolvere i nomi di campo e funzione nel mapping dei campi. Questa funzionalità è utile (non è necessario impostare appropriato tutte maiuscole e minuscole), ma significa che l'origine dati o indice non è possibile specificare i campi che si differenziano solo per maiuscole/minuscole.  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>Funzioni di mapping di campi

Queste funzioni sono attualmente supportate: 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

Esegue la codifica base 64 *attendibili URL* della stringa di input. Si presuppone che l'input codificata UTF-8. 

#### <a name="sample-use-case"></a>Caso di utilizzo di esempio 

Solo i caratteri attendibili URL possono essere visualizzati in una chiave di ricerca di Azure documento (perché i clienti devono avere la possibilità di inviare il documento tramite l'API di ricerca, ad esempio). Se i dati contengono caratteri non sicuri URL e si desidera utilizzare per inserire un campo chiave dell'indice di ricerca, usare questa funzione.   

#### <a name="example"></a>Esempio 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

Esegue base 64 decodifica della stringa di input. L'input viene considerato come un *URL safe* stringa con codifica base 64. 

#### <a name="sample-use-case"></a>Caso di utilizzo di esempio 

I valori dei metadati personalizzati BLOB devono essere codificata ASCII. È possibile utilizzare la codifica base 64 per rappresentare non autorizzate stringhe Unicode in blob metadati personalizzati. Tuttavia, per rendere ricerca significativo, è possibile utilizzare questa funzione per riconvertire dati codificati in stringhe "regolare" durante la compilazione di indice di ricerca.  

#### <a name="example"></a>Esempio 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

Divide un campo di stringa utilizzando il delimitatore specificato e seleziona il token nella posizione specificata da suddividere risultante.

Ad esempio, se l'input `Jane Doe`, il `delimiter` è `" "`(spazio) e il `position` è uguale a 0, il risultato è `Jane`; Se il `position` è 1, il risultato è `Doe`. Se la posizione si riferisce a un token che non esiste, verrà restituito un errore.

#### <a name="sample-use-case"></a>Caso di utilizzo di esempio 

L'origine dati contiene un `PersonName` campo e si desidera indicizzare come distinta due `FirstName` e `LastName` campi. È possibile utilizzare questa funzione per dividere input utilizzando il carattere di spazio come delimitatore.

#### <a name="parameters"></a>Parametri

- `delimiter`: una stringa da utilizzare come separatore quando si divide la stringa di input.
- `position`: una posizione in base zero all'intero del token scegliere dopo la divisione di stringa di input.    

#### <a name="example"></a>Esempio

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

Trasforma una stringa formattata come matrice JSON di stringhe in una matrice di stringa che può essere utilizzata per popolare una `Collection(Edm.String)` campo nell'indice analitico. 

Se, ad esempio, la stringa di input `["red", "white", "blue"]`, quindi il campo di destinazione di tipo `Collection(Edm.String)` verrà popolata con i tre valori `red`, `white` e `blue`. Per i valori di input non analizzabile come matrici di stringhe JSON, verrà restituito un errore. 

#### <a name="sample-use-case"></a>Caso di utilizzo di esempio

Database SQL Azure non dispone di un tipo di dati incorporato naturalmente mappato a `Collection(Edm.String)` campi di ricerca di Azure. Per compilare i campi di raccolta stringa, formattare i dati di origine come matrice di stringhe JSON e usare questa funzione. 

#### <a name="example"></a>Esempio 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>Aiutaci a migliorare ricerca Azure

Se si dispone di richiedere caratteristiche o idee per miglioramenti, per raggiungere i Contattaci sul nostro [sito Uservoicehttp](https://feedback.azure.com/forums/263029-azure-search/).