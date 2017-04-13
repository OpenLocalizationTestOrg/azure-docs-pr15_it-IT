<properties
   pageTitle="L'aggiornamento a Azure ricerca .NET SDK versione 1.1 | Microsoft Azure | Servizio di ricerca cloud ospitato"
   description="L'aggiornamento a Azure ricerca .NET SDK versione 1.1"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>L'aggiornamento a di Azure ricerca .NET SDK versione 2.0 per l'anteprima

Se si usa la versione 1.1 o versioni precedente di [Azure ricerca .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx), in questo articolo consente di aggiornare l'applicazione per utilizzare la versione principale successiva 2.0 per l'anteprima.

Per una procedura dettagliata più generale di SDK inclusi esempi, vedere [come utilizzare la ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

Versione 2.0 per l'anteprima di Azure ricerca .NET SDK contiene alcune modifiche rispetto alle versioni precedenti. Si tratta prevalentemente secondarie, per consentire la modifica del codice richiede solo agevolmente. Per istruzioni su come modificare il codice per usare la nuova versione SDK, vedere [la procedura di aggiornamento](#UpgradeSteps) .

> [AZURE.NOTE] Se si usa versione preview 0.13 o versioni precedenti, è consigliabile aggiornare alla versione 1.1 prima e quindi eseguire l'aggiornamento alla versione di anteprima 2.0. Vedere [Appendice: procedura per l'aggiornamento alla versione 1.1](#UpgradeStepsV1) per le istruzioni.

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>Quali sono le novità in anteprima di versione 2.0

Versione 2.0 per l'anteprima è la prima versione Azure ricerca .NET SDK di utilizzare una versione di anteprima dell'API REST ricerca Azure, in particolare 2015-02-28-Anteprima. In questo modo è possibile utilizzare molte funzioni di anteprima della ricerca di Azure da un'applicazione .NET, incluse le operazioni seguenti:

- [Analisi personalizzati](https://aka.ms/customanalyzers)
- Supporto dell'indicizzatore [Archiviazione Blob Azure](search-howto-indexing-azure-blob-storage.md) e [Archivio tabelle Azure](search-howto-indexing-azure-tables.md)
- Personalizzazione dell'indicizzatore tramite [mapping dei campi](search-indexer-field-mappings.md)
- Supporto tecnico eTag per attivare l'aggiornamento simultaneo attendibili di definizioni di indice, indicizzatori e origini dati
- Supporto per .NET Core e il profilo Portable .NET 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>Procedura di aggiornamento

Prima di tutto, aggiornare il riferimento NuGet per `Microsoft.Azure.Search` tramite la Console di gestione di pacchetti NuGet o mouse su riferimenti del progetto e selezionando "Gestisci pacchetti NuGet..." in Visual Studio. Verificare che si attiva versione non definitiva pacchetti selezionando "Includere non definitiva" NuGet "Gestisci pacchetti" finestra in Visual Studio oppure utilizzando il `-IncludePrerelease` passare se si usa Console di gestione di pacchetti NuGet.

Una volta NuGet scaricato i nuovi pacchetti e le relative dipendenze, rigenerare il progetto. A seconda di strutturazione di codice, potrebbe ricreare correttamente. In caso affermativo, si è pronti a passare!

Se la compilazione non riesce, verrà visualizzato un errore di generazione simile al seguente:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Il passaggio successivo consiste nel correggere l'errore build. Per informazioni dettagliate su come risolvere il problema e la causa dell'errore, vedere [ultime modifiche nella versione 2.0 per l'anteprima](#ListOfChanges) .

Si possono vedere gli avvisi di generazione aggiuntivi correlati a metodi obsoleti o proprietà. Gli avvisi includerà istruzioni sulle operazioni da utilizzare invece la funzionalità deprecata. Ad esempio, se l'applicazione utilizza la `SearchRequestOptions.RequestId` proprietà, viene visualizzato un avviso che indica che`"This property is deprecated. Please use ClientRequestId instead."`

Dopo aver risolto gli errori di generazione, è possibile apportare modifiche all'applicazione per sfruttare le nuove funzionalità di se lo si desidera. Nuove caratteristiche di SDK in dettaglio nelle [che cos'è nuovo in anteprima di versione 2.0](#WhatsNew).

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>Ultime modifiche nella versione 2.0 per l'anteprima

Esiste solo una modifica nel versione 2.0 per l'anteprima che potrebbe richiedere modifiche al codice oltre la ricostruzione dell'applicazione.

### <a name="indexesgetclient-return-type"></a>Tipo restituito Indexes.GetClient

Il `Indexes.GetClient` metodo presenta un nuovo tipo restituito. In precedenza, restituito `SearchIndexClient`, ma questo è stato modificato in `ISearchIndexClient` nella versione 2.0 per l'anteprima. Si tratta per supportare i clienti che desidera simulare la `GetClient` metodo per unit test restituendo un'implementazione fittizia del `ISearchIndexClient`.

#### <a name="example"></a>Esempio

Se il codice è simile alla seguente:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

È possibile modificarlo per questa opzione per correggere gli errori di compilazione:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>Conclusioni
Se sono necessarie altre informazioni sull'uso di Azure ricerca .NET SDK, vedere le [procedure](search-howto-dotnet-sdk.md)aggiornati.

Invitiamo i commenti in SDK. In caso di problemi, è possibile Contattaci chiedere aiuto nel [forum MSDN di ricerca di Azure](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch). Se si trova un errore, è possibile archiviare un problema nell' [archivio di Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues). Assicurarsi che per il titolo del problema con prefisso "ricerca SDK:".

Grazie per aver scelto ricerca Azure!

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>Appendice: Procedura per l'aggiornamento alla versione 1.1 

> [AZURE.NOTE] Questa sezione viene applicata solo per gli utenti della versione Azure ricerca .NET SDK 0.13 anteprima e versioni precedente.

Prima di tutto, aggiornare il riferimento NuGet per `Microsoft.Azure.Search` tramite la Console di gestione di pacchetti NuGet o mouse su riferimenti del progetto e selezionando "Gestisci pacchetti NuGet..." in Visual Studio.

Una volta NuGet scaricato i nuovi pacchetti e le relative dipendenze, rigenerare il progetto.

Se in precedenza si utilizzava versione 1.0.0-preview, 1.0.1-preview o 1.0.2-preview, la compilazione dovrebbe essere eseguita correttamente e si è pronti a passare!

Se in precedenza si utilizzava versione 0.13.0-preview o versioni precedenti, dovrebbero essere visualizzati generare errori simile al seguente:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Il passaggio successivo consiste nel correggere gli errori di compilazione uno alla volta. La maggior parte sarà necessario modificare alcuni nomi di classe e metodo che sono stati rinominati in SDK. [Elenco delle ultime modifiche nella versione 1.1](#ListOfChangesV1) contiene un elenco di queste modifiche nome.

Se si usano le classi personalizzate per i documenti del modello e tali classi dispongono di proprietà dei tipi di base non nullable (ad esempio `int` o `bool` in c#), esiste una correzione nella versione 1.1 di SDK di cui tenere presente. Per ulteriori informazioni, vedere [correzioni di Bug nella versione 1.1](#BugFixesV1) .

Infine, dopo aver risolto gli errori di generazione, è possibile apportare modifiche all'applicazione per sfruttare le nuove funzionalità di se lo si desidera.

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>Elenco delle ultime modifiche nella versione 1.1

L'elenco seguente è ordinato nella probabilità che la modifica interesserà il codice dell'applicazione.

#### <a name="indexbatch-and-indexaction-changes"></a>Modifiche apportate IndexBatch e IndexAction

`IndexBatch.Create`è stato rinominato in `IndexBatch.New` e non ha un `params` argomento. È possibile utilizzare `IndexBatch.New` batch che combinare diversi tipi di azioni (unioni, eliminazioni e così via). Inoltre, sono disponibili nuovi metodi statici per la creazione di batch in tutte le azioni sono gli stessi: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`.

`IndexAction`non ha più costruttori pubblici e le relative proprietà a questo punto sono modificabili. Utilizzare i nuovi metodi statici per la creazione di azioni per diversi scopi: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`. `IndexAction.Create`è stata rimossa. Se è stata utilizzata l'overload che accetta solo un documento, assicurarsi di usare `Upload` invece.

##### <a name="example"></a>Esempio

Se il codice è simile alla seguente:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

È possibile modificarlo per questa opzione per correggere gli errori di compilazione:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se si desidera, è possibile semplificare ulteriormente l'alla seguente:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Modifiche IndexBatchException

Il `IndexBatchException.IndexResponse` proprietà è stato rinominato in `IndexingResults`, e il tipo è ora `IList<IndexingResult>`.

##### <a name="example"></a>Esempio

Se il codice è simile alla seguente:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

È possibile modificarlo per questa opzione per correggere gli errori di compilazione:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>Modifiche apportate al metodo operazione

Ogni operazione in Azure ricerca .NET SDK è esposto come un set di overload per i chiamanti icona del o asincroni. Le firme e separazione di questi overload è stato modificato nella versione 1.1.

Ad esempio, l'operazione "Ottenere le statistiche di indice" nelle versioni precedenti di SDK esposto tali firme:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Le firme del metodo per la stessa operazione nella versione 1.1 è simile al seguente:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

A partire dalla versione 1.1 Azure ricerca .NET SDK consente di organizzare i metodi di operazione in modo diverso:

 - Parametri facoltativi ora vengono creati come predefinito parametri invece di overload aggiuntivi. Questo riduce il numero di overload, a volte notevolmente.
 - I metodi di estensione nascondono ora numerose i dettagli dell'HTTP estranei dal chiamante. Ad esempio le versioni precedenti di SDK restituivano un oggetto di risposta con un codice di stato HTTP, spesso non è necessario verificare in quanto i metodi di operazione che venga generata `CloudException` per il codice di stato che indica un errore. I metodi di estensione nuovo restituiscono solo gli oggetti del modello, senza che sia necessario di dover annullare la disposizione del loro nel codice.
 - Invece di core interfacce espongono ora i metodi che forniscono un maggiore controllo al livello HTTP se è necessario. A questo punto è possibile passare le intestazioni HTTP da includere nel richieste e la visualizzazione della nuova `AzureOperationResponse<T>` tipo restituito offre accesso diretto al `HttpRequestMessage` e `HttpResponseMessage` per l'operazione. `AzureOperationResponse`viene definita nel `Microsoft.Rest.Azure` spazio dei nomi e sostituisce `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Modifiche ScoringParameters

Una nuova classe denominata `ScoringParameter` è stato aggiunto in SDK più recente per rendere più semplice fornire parametri al punteggio profili in una query di ricerca. In precedenza il `ScoringProfiles` proprietà del `SearchParameters` classe è stato digitato come `IList<string>`; A questo punto viene indicato come `IList<ScoringParameter>`.

##### <a name="example"></a>Esempio

Se il codice è simile alla seguente:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

È possibile modificarlo per questa opzione per correggere gli errori di compilazione: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Modifiche apportate al modello classe

Per la firma assume descritto nella [metodo operazione modifica](#OperationMethodChanges), molte classi di `Microsoft.Azure.Search.Models` spazio dei nomi sono stati rinominati o rimossi. Per esempio:

 - `IndexDefinitionResponse`è stata sostituita da`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`è stata rinominata`DocumentSearchResult`
 - `IndexResult`è stata rinominata`IndexingResult`
 - `Documents.Count()`viene ora restituita una `long` con numero di documenti al posto di un`DocumentCountResponse`
 - `IndexGetStatisticsResponse`è stata rinominata`IndexGetStatisticsResult`
 - `IndexListResponse`è stata rinominata`IndexListResult`

Per riepilogare, `OperationResponse`-derivate presenti solo ritorno a capo un oggetto modello sono state rimosse. Le classi rimanenti hanno avuto loro suffisso modificato da `Response` a `Result`.

##### <a name="example"></a>Esempio

Se il codice è simile alla seguente:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

È possibile modificarlo per questa opzione per correggere gli errori di compilazione:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Le classi di risposta e IEnumerable

Una modifica aggiuntiva che può influire sul codice è che le classi di risposta che contengono raccolte non implementano non è più `IEnumerable<T>`. Se, tuttavia, è possibile accedere direttamente la proprietà dell'insieme. Ad esempio, se il codice è simile alla seguente:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

È possibile modificarlo per questa opzione per correggere gli errori di compilazione:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>Nota importante per le applicazioni web

Se si dispone di un'applicazione web che serializza `DocumentSearchResponse` direttamente per inviare i risultati della ricerca nel browser, è necessario modificare il codice o i risultati non serializzerà correttamente. Ad esempio, se il codice è simile alla seguente:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

È possibile modificare ottenendo il `.Results` proprietà della risposta della ricerca per risolvere il rendering di risultati di ricerca:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

È necessario cercare questi casi nel codice manualmente. **Il compilatore non segnalerà** perché `JsonResult.Data` è di tipo `object`.

#### <a name="cloudexception-changes"></a>Modifiche CloudException

Il `CloudException` classe è stata spostata la `Hyak.Common` spazio dei nomi per il `Microsoft.Rest.Azure` spazio dei nomi. Inoltre, il relativo `Error` proprietà è stato rinominato in `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Modifiche apportate SearchServiceClient e SearchIndexClient

Il tipo del `Credentials` proprietà è stata modificata da `SearchCredentials` alla classe base `ServiceClientCredentials`. Se è necessario accedere la `SearchCredentials` di un `SearchIndexClient` o `SearchServiceClient`, utilizzare la visualizzazione della nuova `SearchCredentials` proprietà.

Nelle versioni precedenti di SDK, `SearchServiceClient` e `SearchIndexClient` era costruttori che ha un `HttpClient` parametro. Sono state sostituite con costruttori che accettano un `HttpClientHandler` e una matrice di `DelegatingHandler` oggetti. Rende più facile da installare gestori personalizzati per pre-elaborare le richieste HTTP, se necessario.

Infine, i costruttori che ha un `Uri` e `SearchCredentials` sono stati modificati. Ad esempio, se si dispone di codice è simile alla seguente:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

È possibile modificarlo per questa opzione per correggere gli errori di compilazione:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Si noti inoltre che viene impostato sul tipo di parametro credenziali `ServiceClientCredentials`. Si tratta probabilmente influire sul codice dal `SearchCredentials` dipende dal `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Passando un ID richiesta

Nelle versioni precedenti di SDK, è possibile impostare un ID richiesta nella `SearchServiceClient` o `SearchIndexClient` e verrà incluso in ogni richiesta all'API REST. Ciò è utile per la risoluzione dei problemi relativi al servizio di ricerca se è necessario contattare il supporto tecnico. Tuttavia, è più utile per impostare un ID univoco richiesta per ogni operazione invece di usare lo stesso ID per tutte le operazioni. Per questo motivo, il `SetClientRequestId` metodi di `SearchServiceClient` e `SearchIndexClient` sono state rimosse. Se, tuttavia, è possibile passare un ID richiesta per ogni metodo operazione tramite facoltativa `SearchRequestOptions` parametro.

> [AZURE.NOTE] Nelle versioni future di SDK, verrà aggiunto un nuovo meccanismo per impostare un ID richiesta a livello globale sul client oggetti coerente con l'approccio utilizzato da altri SDK Azure.

#### <a name="example"></a>Esempio

Se si dispone di codice è simile alla seguente:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

È possibile modificarlo per questa opzione per correggere gli errori di compilazione:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Nome modifiche all'interfaccia

I nomi dell'interfaccia di gruppo operazione sono cambiati per garantire la coerenza con i nomi di proprietà corrispondenti:

 - Il tipo di `ISearchServiceClient.Indexes` è stato rinominato da `IIndexOperations` a `IIndexesOperations`.
 - Il tipo di `ISearchServiceClient.Indexers` è stato rinominato da `IIndexerOperations` a `IIndexersOperations`.
 - Il tipo di `ISearchServiceClient.DataSources` è stato rinominato da `IDataSourceOperations` a `IDataSourcesOperations`.
 - Il tipo di `ISearchIndexClient.Documents` è stato rinominato da `IDocumentOperations` a `IDocumentsOperations`.

Questa modifica è difficilmente influire sul codice a meno che non è stato creato simulazioni queste interfacce a scopo di testing.

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>Correzioni di bug nella versione 1.1

Si è verificato un errore in versioni precedenti di Azure ricerca .NET SDK relativo alla serializzazione le classi modello personalizzato. L'errore può verificarsi se una classe modello personalizzato creato con una proprietà di un tipo di valore non nullable.

#### <a name="steps-to-reproduce"></a>Procedura per riprodurre

Creare una classe modello personalizzato con una proprietà del tipo di valore non nullable. Ad esempio, aggiungere un pubblico `UnitCount` proprietà di tipo `int` anziché `int?`.

Se si indicizza un documento con il valore predefinito di quel tipo (ad esempio, 0 per `int`), il campo potrà essere null nella ricerca Azure. Se esegue la ricerca successivamente per il documento, la `Search` chiamata genererà `JsonSerializationException` lamentano che non è possibile convertire `null` a `int`.

Inoltre, i filtri potrebbero non funzionare come previsto poiché null è stato scritto da indicizzare anziché il valore desiderato.

#### <a name="fix-details"></a>Risolvere i dettagli

È stato risolto questo problema nella versione 1.1 di SDK. Ora, se si dispone di una classe modello come segue:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e impostare `IntValue` a 0, il valore è ora correttamente serializzato come 0 nella rete e memorizzato come 0 nell'indice analitico. Anche round trip funziona come previsto.

Esiste un potenziale problema da tenere in considerazione con questo approccio: se si utilizza un tipo di modello con una proprietà non nullable, è necessario **garantire** che non contiene documenti l'indice contengono un valore null per il campo corrispondente. SDK né all'API REST di ricerca Azure consente di attivare la procedura.

Non si tratta di un problema di ipotetico: immaginare uno scenario in cui si aggiunge un nuovo campo a un indice esistente che è di tipo `Edm.Int32`. Dopo aver aggiornato la definizione di indice, tutti i documenti avrà un valore null per il nuovo campo (perché tutti i tipi di sono nullable in Azure ricerca). Se si utilizza una classe modello con un non nullable `int` proprietà del campo corrispondente, verrà visualizzato un `JsonSerializationException` così durante il tentativo di recuperare documenti:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Per questo motivo, è comunque consigliabile utilizzare i tipi nullable nelle classi modello è buona norma.

Per ulteriori informazioni su questo errore e la correzione, vedere [questo problema in GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).
