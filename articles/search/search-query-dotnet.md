<properties
    pageTitle="Query l'indice di ricerca Azure utilizzando .NET SDK | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Creare una query di ricerca nella ricerca Azure e utilizzare i parametri di ricerca per filtrare e ordinare i risultati di ricerca."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Indice di ricerca di Azure utilizzando .NET SDK della query
> [AZURE.SELECTOR]
- [Panoramica](search-query-overview.md)
- [Portale](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

In questo articolo illustra come eseguire query su un indice usando [Azure ricerca .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Prima di iniziare questa procedura dettagliata, è necessario disporre già [creato un indice di ricerca di Azure](search-what-is-an-index.md) e [popolato con dati](search-what-is-data-import.md).

Si noti che tutto il codice di esempio in questo articolo è scritto in c#. È possibile trovare il codice sorgente completo codice [sul GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>I. Identificazione query api-chiave del servizio di ricerca di Azure
Una volta creato un indice di ricerca di Azure, si è pronti quasi eseguire query utilizzando .NET SDK. Prima di tutto, sarà necessario ottenere uno della query api tasti di scelta rapida generati per il servizio di ricerca che è stato effettuato il provisioning. .NET SDK invierà questo tasto api a ogni richiesta del servizio. Con una chiave valida stabilisce protezione, in base a ogni richiesta, tra l'applicazione di inviare la richiesta e il servizio che viene gestita.

1. Per trovare api tasti di scelta rapida del servizio è necessario accedere al [Portale di Azure](https://portal.azure.com/)
2. Passare a blade del servizio di ricerca di Azure
3. Fare clic sull'icona "tasti visualizzati"

Il servizio avrà *chiavi di amministrazione* e *le query*.

  - Le principali e secondarie *tasti amministratore* diritti completo a tutte le operazioni, compresa la possibilità di gestire il servizio, creare ed eliminare gli indici, indicizzatori e origini dati. Esistono due metodi in modo che è possibile continuare a usare il tasto secondario se si decide di rigenerare la chiave primaria e viceversa.
  - Le *chiavi query* concedere l'accesso in sola lettura per gli indici e i documenti e vengono in genere distribuiti per le applicazioni client che inviano richieste di ricerca.

Per quanto riguarda la ricerca di un indice analitico, è possibile usare uno dei tasti query. I tasti di amministrazione possono essere utilizzati anche per le query, ma è necessario utilizzare una chiave di query nel codice dell'applicazione come meglio segue il [principio dei privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II. Creare un'istanza della classe SearchIndexClient
Per eseguire query con Azure ricerca .NET SDK, sarà necessario creare un'istanza di `SearchIndexClient` classe. Questa classe è diversi costruttori. Quella che si desidera accetta il nome del servizio di ricerca, il nome di indice e un `SearchCredentials` oggetto come parametri. `SearchCredentials`include la chiave dell'api.

Il codice seguente crea un nuovo `SearchIndexClient` per l'indice "alberghi" (creato in [creare un indice di ricerca di Azure mediante .NET SDK](search-create-index-dotnet.md)) utilizzando i valori per il nome del servizio di ricerca e la chiave dell'api archiviati in file di configurazione dell'applicazione (`app.config` o `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`ha un `Documents` proprietà. Questa proprietà consente di tutti i metodi che è necessario eseguire query su indici di ricerca di Azure.

## <a name="iii-query-your-index"></a>III. L'indice della query
La ricerca con .NET SDK è semplice come si chiama la `Documents.Search` metodo sul `SearchIndexClient`. Questo metodo accetta alcuni parametri, incluso il testo di ricerca, insieme ad un `SearchParameters` oggetto che può essere utilizzato per perfezionare ulteriormente la query.

#### <a name="types-of-queries"></a>Tipi di query
I due principali [tipi di query](search-query-overview.md#types-of-queries) si userà sono `search` e `filter`. A `search` Cerca in tutti i campi _che supportano le ricerche_ dell'indice di uno o più termini di query. A `filter` query viene valutata un'espressione booleana tutti _possibile filtrare_ campi in un indice.

Ricerche e applicare filtri vengono eseguiti tramite il `Documents.Search` metodo. Una query di ricerca può essere passata nel `searchText` parametro, mentre un'espressione di filtro può essere passata nel `Filter` proprietà del `SearchParameters` classe. Per filtrare senza eseguire ricerche, è sufficiente passare `"*"` per il `searchText` parametro. Per eseguire ricerche senza filtro, lasciare la `Filter` proprietà, o non passare un `SearchParameters` istanza tutto.

#### <a name="example-queries"></a>Query di esempio

Il codice di esempio seguente mostra diversi modi per l'indice "alberghi" definito in [creare un indice di ricerca di Azure mediante .NET SDK](search-create-index-dotnet.md#DefineIndex)della query. Si noti che i documenti restituiti con i risultati della ricerca sono istanze di `Hotel` classe, che è stata definita [nell'Importazione dei dati in ricerca Azure con .NET SDK](search-import-data-dotnet.md#HotelClass). Il codice di esempio utilizza un `WriteDocuments` metodo per restituire i risultati di ricerca alla console. Questo metodo è descritto nella sezione successiva.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV. Gestire i risultati di ricerca
Il `Documents.Search` restituisce un `DocumentSearchResult` oggetto che contiene i risultati della query. Nell'esempio nella sezione precedente utilizzato un metodo denominato `WriteDocuments` per restituire i risultati di ricerca alla console:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Ecco l'aspetto dei risultati per le query nella sezione precedente, presupponendo che l'indice "alberghi" viene popolata con i dati di esempio di [Importazione dei dati in ricerca Azure con .NET SDK](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

Codice dell'esempio precedente utilizza la console per generare l'output dei risultati di ricerca. Analogamente, è necessario visualizzare i risultati della ricerca in un'applicazione. Vedere [questo esempio sul GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) per un esempio di come visualizzare i risultati della ricerca in un'applicazione web basata su MVC ASP.NET.
