<properties
    pageTitle="Connette DocumentDB con ricerca Azure usando indicizzatori | Microsoft Azure"
    description="In questo articolo viene illustrato come utilizzare per indicizzatore ricerca Azure con DocumentDB come origine dati."
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>Connette DocumentDB con ricerca Azure usando indicizzatori

Se sta cercando di implementazione di esperienze di ricerca accattivante sui dati DocumentDB, utilizzare indicizzatore ricerca Azure per DocumentDB! In questo articolo verrà illustrato integrare DocumentDB Azure con Azure ricerca senza dover scrivere codice per gestire l'infrastruttura di indicizzazione!

A tale scopo, è necessario [Configurazione account Azure ricerca](../search/search-create-service-portal.md) (non è necessario eseguire l'aggiornamento a ricerca standard) e quindi chiamare l' [API REST di Azure ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx) per creare **un'origine dati** di DocumentDB e un **indicizzatore** per l'origine dati.

In ordine inviano richieste per interagire con le API REST, è possibile utilizzare [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler)o uno strumento di preferenza.


##<a id="Concepts"></a>Concetti di indicizzatore ricerca Azure

Azure supporta ricerca la creazione e gestione dei dati di origini (inclusi DocumentDB) e indicizzatori che funzionano rispetto a tali origini dati.

Un' **origine dati** consente di specificare quali dati devono essere indicizzate, le credenziali per accedere ai dati e i criteri di attivare la ricerca di Azure identificare in modo efficiente le modifiche dei dati (ad esempio modificati o eliminati documenti all'interno della raccolta). L'origine dati è definita come risorsa indipendente in modo che possa essere utilizzato da più indicizzatori.

Un **indicizzatore** descrive il flusso dei dati dell'origine dati in un indice di ricerca di destinazione. È consigliabile creare un indicizzatore per tutte le combinazioni di origine dati e indice di destinazione. Possono essere presenti più indicizzatori scrittura nella stessa posizione di indice, un indicizzatore può scrivere solo in un unico indice. Un indicizzatore viene utilizzato per:

- Eseguire una copia unica dei dati per popolare un indice.
- Sincronizzare un indice con le modifiche apportate nell'origine dati in base alla pianificazione. La pianificazione fa parte della definizione dell'indicizzatore.
- Richiamare aggiornamenti su richiesta a un indice in base alle esigenze.

##<a id="CreateDataSource"></a>Passaggio 1: Creare un'origine dati

Inviare una richiesta HTTP POST per creare una nuova origine dati nel servizio di ricerca di Azure, incluse le intestazioni di richiesta seguenti.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Il `api-version` è necessario. I valori validi includono `2015-02-28` o versione successiva. Visitare [le versioni di API nella ricerca Azure](../search/search-api-versions.md) per visualizzare tutte le versioni API di ricerca.

Il corpo della convocazione contiene la definizione di origine dati, che deve includere i campi seguenti:

- **nome**: scegliere un nome per rappresentare il database DocumentDB.

- **tipo**: usare `documentdb`.

- **le credenziali**:

    - **connectionString**: obbligatorio. Specificare le informazioni di connessione al database di Azure DocumentDB nel formato seguente:`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **contenitore**:

    - **nome**: obbligatorio. Specificare l'id dell'insieme DocumentDB da indicizzare.

    - **query**: facoltativo. È possibile specificare una query per trasformare un documento JSON non autorizzato in uno schema flat Azure ricerca è possibile indicizzare.

- **dataChangeDetectionPolicy**: facoltativo. Visualizzare [dati Modifica criteri di rilevamento](#DataChangeDetectionPolicy) riportata di seguito.

- **dataDeletionDetectionPolicy**: facoltativo. Vedere [i criteri di rilevamento l'eliminazione dei dati](#DataDeletionDetectionPolicy) .

Per un [corpo della richiesta esempio](#CreateDataSourceExample), vedere di seguito.

###<a id="DataChangeDetectionPolicy"></a>Documenti modificati di acquisizione

Lo scopo di un criterio di rilevamento modifiche di dati è in modo efficiente identificare gli elementi di dati modificati. Attualmente, gli unici criteri supportati sono i `High Water Mark` criteri utilizzando la `_ts` timestamp dell'ultima modifica proprietà forniti da DocumentDB - specificato nel modo seguente:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

È anche necessario aggiungere `_ts` nella proiezione e `WHERE` clausola per la query. Per esempio:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Acquisizione documenti eliminati

Quando le righe vengono eliminate dalla tabella di origine, è necessario eliminare le righe da anche l'indice di ricerca. Lo scopo di un criterio di rilevamento l'eliminazione di dati è in modo efficiente identificare gli elementi di dati eliminati. Attualmente, gli unici criteri supportati sono i `Soft Delete` criteri (eliminazione è contrassegnata con un contrassegno di qualche), specificato come indicato di seguito:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] È necessario includere la proprietà softDeleteColumnName nella clausola SELECT se si utilizza una proiezione personalizzata.

###<a id="CreateDataSourceExample"></a>Esempio di corpo richiesta

Nell'esempio seguente crea un'origine dati con un suggerimenti di query e criteri personalizzati:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>Risposta

Si riceverà una risposta HTTP 201 creato l'origine dati è stata creata correttamente.

##<a id="CreateIndex"></a>Passaggio 2: Creare un indice

Creare un indice di ricerca di Azure di destinazione se non è già disponibile. È possibile eseguire questa operazione dall' [Interfaccia utente di Azure portale](../search/search-create-index-portal.md) o tramite l' [API di indice creare](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Assicurarsi che lo schema dell'indice di destinazione sia compatibile con lo schema dei documenti JSON di origine o l'output di proiezione la query personalizzata.

>[AZURE.NOTE] Per gli insiemi partizionati, il tasto di documento predefinito è di DocumentDB `_rid` proprietà, che ottiene rinominato in `rid` risultati di ricerca di Azure. Del inoltre, DocumentDB `_rid` valori contengono caratteri non validi nelle chiavi di ricerca di Azure; di conseguenza, il `_rid` valori sono con codificata base 64.

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figura r: il Mapping tra tipi di dati JSON e tipi di dati ricerca Azure

| TIPO DI DATI JSON|   TIPI DI CAMPI DI DESTINAZIONE COMPATIBILI INDICE|
|---|---|
|Bool|Edm.Boolean, Edm.String|
|Numeri che rappresentano numeri interi|Edm.Int32, Edm.Int64, Edm.String|
|I numeri che rappresentano punti mobile|Double, Edm.String|
|Stringa|Edm.String|
|Tipi di matrici di primitiva ad esempio, "", "b", "c" |Collection(EDM.String)|
|Stringhe che rappresentano date| DateTimeOffset, Edm.String|
|GeoJSON oggetti, ad esempio {"tipo": "Decimale", "coordinate": [lungo, lat]} | Edm.GeographyPoint |
|Altri oggetti JSON|N/D|

###<a id="CreateIndexExample"></a>Esempio di corpo richiesta

Nell'esempio seguente viene creato un indice con un campo id e una descrizione:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>Risposta

Si riceverà una risposta HTTP 201 creato l'indice è stato creato correttamente.

##<a id="CreateIndexer"></a>Passaggio 3: Creare un indicizzatore

È possibile creare un nuovo indicizzatore all'interno di un servizio di ricerca di Azure mediante una richiesta HTTP POST con le intestazioni seguenti.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Il corpo della convocazione contiene la definizione dell'indicizzatore, che deve includere i campi seguenti:

- **nome**: obbligatorio. Il nome dell'indicizzatore.

- **dataSourceName**: obbligatorio. Il nome di un'origine dati esistente.

- **targetIndexName**: obbligatorio. Il nome di un indice esistente.

- **pianificazione**: facoltativo. Vedere la sezione [indicizzazione pianificazione](#IndexingSchedule) riportata di seguito.

###<a id="IndexingSchedule"></a>Esecuzione indicizzatori in una pianificazione

Un indicizzatore possibile specificare una pianificazione. Se è presenta una pianificazione, l'indicizzatore eseguiranno periodicamente in base alla pianificazione. Programmazione sono previsti gli attributi seguenti:

- **intervallo**: obbligatorio. Viene eseguito un valore di durata che specifica un intervallo o un periodo di indicizzatore. Intervallo minimo consentito è 5 minuti; più lunga è un giorno. È necessario essere formattato come un valore di "dayTimeDuration" XSD (un sottoinsieme di un valore di [Durata ISO8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) con restrizioni). Il modello per questa operazione è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` per ogni 15 minuti, `PT2H` per ogni 2 ore.

- **ora di inizio**: obbligatorio. Una valore datetime UTC che specifica l'inizio dell'indicizzatore in esecuzione.

###<a id="CreateIndexerExample"></a>Esempio di corpo richiesta

Nell'esempio seguente viene creato un indicizzatore che consente di copiare i dati dalla raccolta a cui fa riferimento il `myDocDbDataSource` per le origini dati per il `mySearchIndex` indice in una pianificazione che avvia il 1 gennaio 2015 UTC ed esegue ogni ora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>Risposta

Si riceverà una risposta HTTP 201 creato l'indicizzatore è stato creato correttamente.

##<a id="RunIndexer"></a>Passaggio 4: Eseguire un indicizzatore

Oltre a eseguire periodicamente in base alla pianificazione, un indicizzatore può anche essere richiamato su richiesta eseguendo la richiesta HTTP POST seguente:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Risposta

Si riceverà una risposta HTTP 202 accettato l'indicizzatore è stato chiamato correttamente.

##<a name="GetIndexerStatus"></a>Passaggio 5: Visualizzare lo stato dell'indicizzatore

È possibile inviare una richiesta HTTP GET per recuperare la cronologia di esecuzione e lo stato corrente di un indicizzatore:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Risposta

Verrà visualizzata una risposta HTTP 200 OK restituita insieme a una risposta che contiene informazioni sullo stato di integrità complessiva indicizzatore, l'ultima chiamata indicizzatore, nonché la cronologia di recente chiamate indicizzatore (se presente).

La risposta dovrebbe essere simile al seguente:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Cronologia esecuzione contiene tutte le 50 esecuzioni più recenti, completate vengono ordinate in ordine cronologico inverso (per consentire l'esecuzione del più recente per primo nella risposta).

##<a name="NextSteps"></a>Passaggi successivi

Congratulazioni! Sufficiente appreso come integrare DocumentDB Azure con ricerca Azure utilizzando l'indicizzatore per DocumentDB.

 - Per informazioni su come più su Azure DocumentDB, vedere la [pagina del servizio DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Per informazioni su come ulteriori informazioni su Azure ricerca, vedere la [pagina del servizio di ricerca](https://azure.microsoft.com/services/search/).
