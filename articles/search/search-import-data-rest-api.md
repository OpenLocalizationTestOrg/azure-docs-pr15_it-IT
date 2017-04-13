<properties
    pageTitle="Caricamento dei dati di ricerca di Azure tramite l'API REST | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Informazioni su come caricare dati in un indice di ricerca di Azure tramite l'API REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Caricare dati alla ricerca di Azure tramite l'API REST
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTO](search-import-data-rest-api.md)

In questo articolo illustra come usare l' [API REST ricerca Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) per importare dati in un indice di ricerca di Azure.

Prima di iniziare questa procedura dettagliata, è già stato [creato un indice di ricerca di Azure](search-what-is-an-index.md).

Per inserire i documenti nell'indice tramite l'API REST, si verrà inviare una richiesta HTTP POST all'endpoint URL dell'indice. Il corpo del corpo della richiesta HTTP è un oggetto JSON che contiene i documenti per essere aggiunti, modificati o eliminati.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Identificare chiave dell'api amministratore del servizio di ricerca di Azure
Quando si inviano richieste HTTP rispetto al servizio utilizzando l'API REST, *ogni* API richiesta deve includere la chiave dell'api generati per il servizio di ricerca che è stato effettuato il provisioning. Con una chiave valida stabilisce protezione, in base a ogni richiesta, tra l'applicazione di inviare la richiesta e il servizio che viene gestita.

1. Per trovare api tasti di scelta rapida del servizio è necessario accedere al [Portale di Azure](https://portal.azure.com/)
2. Passare a blade del servizio di ricerca di Azure
3. Fare clic sull'icona "tasti visualizzati"

Il servizio avrà *chiavi di amministrazione* e *le query*.

  - Le principali e secondarie *tasti amministratore* diritti completo a tutte le operazioni, compresa la possibilità di gestire il servizio, creare ed eliminare gli indici, indicizzatori e origini dati. Esistono due metodi in modo che è possibile continuare a usare il tasto secondario se si decide di rigenerare la chiave primaria e viceversa.
  - Le *chiavi query* concedere l'accesso in sola lettura per gli indici e i documenti e vengono in genere distribuiti per le applicazioni client che inviano richieste di ricerca.

Per quanto riguarda l'importazione di dati in un indice analitico, è possibile utilizzare la chiave primaria o secondaria amministratore.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decidere quale azione indicizzazione da utilizzare
Quando si utilizza l'API REST, consente di rilasciare richieste POST HTTP con enti richiesta JSON ai endpoint URL dell'indice di ricerca di Azure. L'oggetto JSON nel corpo del richiesta HTTP conterrà una matrice JSON denominata "valore" contenente oggetti JSON che rappresenta i documenti che si desidera aggiungere all'indice, aggiornamento o Elimina.

Ogni oggetto JSON nella matrice "valore" rappresenta un documento da indicizzare. Ciascuno di essi contiene chiave del documento e consente di specificare l'operazione di indicizzazione desiderata (carica, stampa unione, Elimina, e così via). In base di sotto azioni si sceglie, solo determinati campi da includere per ogni documento:

@search.action | Descrizione | Campi necessari per ogni documento | Note
--- | --- | --- | ---
`upload` | Un `upload` azione è simile a un "upsert" nel punto in cui verrà inserito in caso di nuovo e aggiornato/sostituito se esiste il documento. | tasto e tutti gli altri campi che si desidera definire | Durante l'aggiornamento/la sostituzione di un documento esistente, tutti i campi che non è specificato nella richiesta avrà il campo impostato su `null`. Si verifica anche quando il campo stato impostato su un valore non null.
`merge` | Aggiorna un documento esistente con i campi specificati. Se il documento non esiste nell'indice analitico, l'unione avrà esito negativo. | tasto e tutti gli altri campi che si desidera definire | Qualsiasi campo che specificare in una composizione sostituirà il campo esistente nel documento. Sono inclusi i campi di tipo `Collection(Edm.String)`. Ad esempio, se il documento contiene un campo `tags` con valore `["budget"]` e si esegue una stampa unione con valore `["economy", "pool"]` per `tags`, il valore finale della `tags` campo sarà `["economy", "pool"]`. Non sarà `["budget", "economy", "pool"]`.
`mergeOrUpload` | Questa azione si comporta come `merge` se esiste già un documento con il tasto specificato nell'indice analitico. Se il documento non esiste, si comporta come `upload` con un nuovo documento. | tasto e tutti gli altri campi che si desidera definire | -
`delete` | Rimuove il documento specificato dall'indice. | solo chiave | Tutti i campi specificare diverso da quello sul campo chiave verrà ignorato. Se si desidera rimuovere un singolo campo da un documento, utilizzare `merge` invece ed è sufficiente impostare il campo in modo esplicito su null.

## <a name="iii-construct-your-http-request-and-request-body"></a>III. Creare la richiesta HTTP e corpo richiesta
Ora che raccolti i valori dei campi necessari per le azioni di indice, si è pronti creare la richiesta HTTP effettivo e il corpo della richiesta JSON per importare i dati.

#### <a name="request-and-request-headers"></a>Richiesta e intestazioni di richiesta
Nell'URL, sarà necessario fornire il nome del servizio, nome dell'indice ("alberghi" in questo caso), nonché la versione dell'API corretta (la versione corrente di API `2015-02-28` al momento della pubblicazione di questo documento). È necessario definire il `Content-Type` e `api-key` le intestazioni di richiesta. Per informazioni, utilizzare uno dei tasti di amministratore del servizio.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corpo della richiesta

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

In questo caso, si utilizza `upload`, `mergeOrUpload`, e `delete` come le azioni di ricerca.

Si supponga che l'indice "alberghi" di esempio è già compilata con un numero di documenti. Si noti come non è specificare tutti i campi possibili documento quando si usa `mergeOrUpload` e come è specificato solo la chiave di documento (`hotelId`) quando si usa `delete`.

Si noti inoltre che è possibile includere solo fino a 1000 documenti (o 16 MB) in una singola richiesta di indicizzazione.

## <a name="iv-understand-your-http-response-code"></a>IV. Comprendere il codice di risposta HTTP
#### <a name="200"></a>200
Dopo l'invio di una richiesta di indicizzazione riuscita si riceverà una risposta HTTP con codice di stato `200 OK`. Corpo JSON della risposta HTTP sarà come indicato di seguito:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Il codice di stato `207` verrà restituito quando almeno un elemento non è stato indicizzato correttamente. Corpo JSON della risposta HTTP contiene informazioni sui documenti ha esito negativo.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Spesso questo errore indica che il caricamento del servizio di ricerca per raggiungere un punto in cui l'indicizzazione richieste inizierà per restituire `503` le risposte. In questo caso, è consigliabile che il codice client disattivata altamente attesa prima di riprovare. Si otterrà il sistema po' di tempo per recuperare, aumentando le possibilità richieste future avrà esito positivo. Tentativi di rapidamente la richiesta verrà solo prolungare la situazione.

#### <a name="429"></a>429
Il codice di stato `429` verrà restituito quando è stato superato la quota al numero di documenti per indice.

#### <a name="503"></a>503
Il codice di stato `503` verrà restituito se nessun elemento nella richiesta sono stato indicizzato correttamente. Questo errore indica che il sistema è in condizioni di carico elevato e la richiesta non verrà eseguita al momento.

> [AZURE.NOTE] In questo caso, è consigliabile che il codice client disattivata altamente attesa prima di riprovare. Si otterrà il sistema po' di tempo per recuperare, aumentando le possibilità richieste future avrà esito positivo. Tentativi di rapidamente la richiesta verrà solo prolungare la situazione.

Per ulteriori informazioni sul documento azioni e le risposte di successo/errore, vedere [aggiunta, aggiornamento o eliminare documenti](https://msdn.microsoft.com/library/azure/dn798930.aspx). Per ulteriori informazioni su altri codici di stato HTTP che potrebbero essere restituite in caso di errore, vedere [codici di stato HTTP (Azure ricerca)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## <a name="next"></a>Successivo
Dopo avere popolato indice di ricerca di Azure, sarà possibile iniziare a eseguire una query per cercare documenti. Per informazioni dettagliate, vedere [Indice di ricerca Azure alle Query](search-query-overview.md) .
