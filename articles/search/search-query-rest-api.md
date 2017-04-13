<properties
    pageTitle="L'indice di ricerca di Azure tramite l'API REST della query | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Creare una query di ricerca nella ricerca Azure e utilizzare i parametri di ricerca per filtrare e ordinare i risultati di ricerca."
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Indice di ricerca di Azure tramite l'API REST della query
> [AZURE.SELECTOR]
- [Panoramica](search-query-overview.md)
- [Portale](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

In questo articolo illustra come eseguire query su un indice tramite l' [API REST di Azure ricerca](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Prima di iniziare questa procedura dettagliata, è necessario disporre già [creato un indice di ricerca di Azure](search-what-is-an-index.md) e [popolato con dati](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>I. Identificazione query api-chiave del servizio di ricerca di Azure
Un componente chiave di ogni operazione di ricerca in base all'API REST di ricerca Azure è la *chiave dell'api* generati per il servizio che è stato effettuato il provisioning. Con una chiave valida stabilisce protezione, in base a ogni richiesta, tra l'applicazione di inviare la richiesta e il servizio che viene gestita.

1. Per trovare api tasti di scelta rapida del servizio è necessario accedere al [Portale di Azure](https://portal.azure.com/)
2. Passare a blade del servizio di ricerca di Azure
3. Fare clic sull'icona "tasti visualizzati"

Il servizio avrà *chiavi di amministrazione* e *le query*.

 - Le principali e secondarie *tasti amministratore* diritti completo a tutte le operazioni, compresa la possibilità di gestire il servizio, creare ed eliminare gli indici, indicizzatori e origini dati. Esistono due metodi in modo che è possibile continuare a usare il tasto secondario se si decide di rigenerare la chiave primaria e viceversa.
 - Le *chiavi query* concedere l'accesso in sola lettura per gli indici e i documenti e vengono in genere distribuiti per le applicazioni client che inviano richieste di ricerca.

Per quanto riguarda la ricerca di un indice analitico, è possibile usare uno dei tasti query. I tasti di amministrazione possono essere utilizzati anche per le query, ma è necessario utilizzare una chiave di query nel codice dell'applicazione come meglio segue il [principio dei privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Formulare la query
Esistono due modi per [l'indice tramite l'API REST di ricerca](https://msdn.microsoft.com/library/azure/dn798927.aspx). Per è possibile inviare una richiesta HTTP POST in cui verranno definiti i parametri della query in un oggetto JSON nel corpo della richiesta. L'altro consiste per inviare una richiesta HTTP GET nel punto in cui verranno definiti i parametri di query nell'URL della richiesta. Notare che POST ha più [relaxed limiti](https://msdn.microsoft.com/library/azure/dn798927.aspx) alle dimensioni di parametri query rispetto a GET. Per questo motivo, è consigliabile usare POST se non si dispone di circostanze particolari in cui utilizzando GET sarebbe più comodo.

Per i POST e GET, è necessario fornire il *nome del servizio*, *il nome dell'indice*e la corretta *versione dell'API* (la versione corrente di API `2015-02-28` al momento della pubblicazione di questo documento) nell'URL della richiesta. Per ottenere, la *stringa di query* alla fine dell'URL sarà nel punto in cui è fornire i parametri della query. Per il formato dell'URL, vedere di seguito:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

Il formato per il POST è lo stesso, ma con solo api versione, i parametri di stringa di query.



#### <a name="example-queries"></a>Query di esempio

Ecco alcuni query di esempio in un indice denominato "alberghi". Queste query vengono visualizzate in formato GET e POST.

Cercare l'indice intero il termine 'preventivo' e restituisce solo il `hotelName` campo:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Applicare un filtro per l'indice per trovare più 150 dollari notte conveniente alberghi e restituire la `hotelId` e `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Cercare nell'indice intero ordine tramite un campo specifico (`lastRenovationDate`) in ordine decrescente, eseguire i risultati primi due e Mostra solo `hotelName` e `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Inviare la richiesta HTTP
Ora che si dispone di formulare la query come parte dell'URL della richiesta HTTP (per ottenere) o corpo (per POST), è possibile definire le intestazioni di richiesta e inviare la query.

#### <a name="request-and-request-headers"></a>Richiesta e intestazioni di richiesta
È necessario definire due intestazioni di richiesta per scaricare o tre per POST:
1. Il `api-key` intestazione deve essere impostata sulla chiave di query si individuato nel passaggio sono indicati. Si noti che è inoltre possibile utilizzare una chiave di amministrazione come il `api-key` intestazione, ma è consigliabile utilizzare una chiave di query come garantisce l'accesso in sola lettura per gli indici e i documenti in modo esclusivo.
2. Il `Accept` intestazione deve essere impostata su `application/json`.
3. Per i POST solo il `Content-Type` intestazione anche deve essere impostata su `application/json`.

Per una richiesta HTTP GET per la ricerca dell'indice "alberghi" tramite l'API REST di ricerca di Azure, utilizzando una query semplice che consente di cercare il termine "modelli", vedere di seguito:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Ecco la stessa query di esempio, questa volta utilizzando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Una richiesta di query ha esito positivo verrà generato un codice di stato `200 OK` e i risultati della ricerca vengono restituiti come JSON nel corpo della risposta. Ecco quali i risultati per l'aspetto di query indicati, presupponendo che l'indice "alberghi" viene popolata con i dati di esempio nella [Importazione dei dati di ricerca di Azure tramite l'API REST](search-import-data-rest-api.md) (si noti che la JSON è stato formattato per chiarezza).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Per ulteriori informazioni, visitare la sezione "Risposta" dei [Documenti di ricerca](https://msdn.microsoft.com/library/azure/dn798927.aspx). Per ulteriori informazioni su altri codici di stato HTTP che potrebbero essere restituite in caso di errore, vedere [codici di stato HTTP (Azure ricerca)](https://msdn.microsoft.com/library/azure/dn798925.aspx).
