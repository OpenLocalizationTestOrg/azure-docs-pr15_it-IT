<properties
    pageTitle="Caricamento dei dati in ricerca Azure con .NET SDK | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Informazioni su come caricare dati in un indice di ricerca di Azure utilizzando .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Caricare dati alla ricerca di Azure utilizzando .NET SDK
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTO](search-import-data-rest-api.md)

In questo articolo illustra come usare [Azure ricerca .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) per importare dati in un indice di ricerca di Azure.

Prima di iniziare questa procedura dettagliata, è già stato [creato un indice di ricerca di Azure](search-what-is-an-index.md). Anche in questo articolo si presuppone che è già stato creato un `SearchServiceClient` oggetto, come illustrato in [creare un indice di ricerca di Azure mediante .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient).

Si noti che tutto il codice di esempio in questo articolo è scritto in c#. È possibile trovare il codice sorgente completo codice [sul GitHub](http://aka.ms/search-dotnet-howto).

Per inserire i documenti nell'indice utilizzando .NET SDK, sarà necessario:

  1. Creare un `SearchIndexClient` oggetto a cui connettersi indice di ricerca.
  2. Creare un `IndexBatch` contenente i documenti da aggiungere, modificare o eliminare.
  3. Chiamare il `Documents.Index` metodo del `SearchIndexClient` per inviare la `IndexBatch` per l'indice di ricerca.

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>I. Creare un'istanza della classe SearchIndexClient
Per importare dati nell'indice con Azure ricerca .NET SDK, sarà necessario creare un'istanza di `SearchIndexClient` classe. È possibile creare questa istanza se stessi, ma è più semplice se si dispone già di un `SearchServiceClient` istanza di chiamare il relativo `Indexes.GetClient` metodo. Ad esempio, ecco come è possibile ottenere un `SearchIndexClient` per l'indice denominato "alberghi" da un `SearchServiceClient` denominata `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] In un'applicazione di ricerca tipica, compilazione e gestione degli indici viene gestita da un componente separato dalla query di ricerca. `Indexes.GetClient`è utile per la compilazione di un indice in quanto salva i problemi di fornire un altro `SearchCredentials`. A tale scopo, passando la chiave di amministrazione utilizzati per creare il `SearchServiceClient` al nuovo `SearchIndexClient`. Tuttavia, nella parte dell'applicazione che esegue le query, è consigliabile creare il `SearchIndexClient` direttamente in modo che è possibile passare in una chiave di query anziché un tasto di amministratore. Si tratta coerente con il [principio dei privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e sarà possibile per rendere più sicura l'applicazione. È possibile trovare ulteriori informazioni sulle chiavi di amministrazione e le query nel [riferimento all'API REST di Azure ricerca su MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient`ha un `Documents` proprietà. Questa proprietà consente di tutti i metodi che è necessario aggiungere, modificare, eliminare o documenti nell'indice della query.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decidere quale azione indicizzazione da utilizzare
Per importare dati tramite .NET SDK, sarà necessario creare un pacchetto backup dei dati in un `IndexBatch` oggetto. Un `IndexBatch` incapsula un insieme di `IndexAction` oggetti, ognuno dei quali contiene un documento e una proprietà che consente di ricerca di Azure l'azione da eseguire sul documento (carica, stampa unione, Elimina, e così via). In base di sotto azioni si sceglie, solo determinati campi da includere per ogni documento:

Azione | Descrizione | Campi necessari per ogni documento | Note
--- | --- | --- | ---
`Upload` | Un `Upload` azione è simile a un "upsert" nel punto in cui verrà inserito in caso di nuovo e aggiornato/sostituito se esiste il documento. | tasto e tutti gli altri campi che si desidera definire | Durante l'aggiornamento/la sostituzione di un documento esistente, tutti i campi che non è specificato nella richiesta avrà il campo impostato su `null`. Si verifica anche quando il campo stato impostato su un valore non null.
`Merge` | Aggiorna un documento esistente con i campi specificati. Se il documento non esiste nell'indice analitico, l'unione avrà esito negativo. | tasto e tutti gli altri campi che si desidera definire | Qualsiasi campo che specificare in una composizione sostituirà il campo esistente nel documento. Sono inclusi i campi di tipo `DataType.Collection(DataType.String)`. Ad esempio, se il documento contiene un campo `tags` con valore `["budget"]` e si esegue una stampa unione con valore `["economy", "pool"]` per `tags`, il valore finale della `tags` campo sarà `["economy", "pool"]`. Non sarà `["budget", "economy", "pool"]`.
`MergeOrUpload` | Questa azione si comporta come `Merge` se esiste già un documento con il tasto specificato nell'indice analitico. Se il documento non esiste, si comporta come `Upload` con un nuovo documento. | tasto e tutti gli altri campi che si desidera definire | -
`Delete` | Rimuove il documento specificato dall'indice. | solo chiave | Tutti i campi specificare diverso da quello sul campo chiave verrà ignorato. Se si desidera rimuovere un singolo campo da un documento, utilizzare `Merge` invece ed è sufficiente impostare il campo in modo esplicito su null.

È possibile specificare l'azione che si desidera utilizzare con i vari metodi statici della `IndexBatch` e `IndexAction` classi, come illustrato nella sezione successiva.

## <a name="iii-construct-your-indexbatch"></a>III. Costruire il IndexBatch
Dopo aver scoperto le azioni da eseguire sui documenti, si è pronti costruire il `IndexBatch`. Nell'esempio seguente viene illustrato come creare un batch con alcune azioni. Nota Questo esempio viene utilizzata una classe personalizzata denominata `Hotel` che esegue il mapping a un documento in corrispondenza dell'indice "alberghi".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

In questo caso, si utilizza `Upload`, `MergeOrUpload`, e `Delete` come le azioni di ricerca, come specificato dai metodi invitati la `IndexAction` classe.

Si supponga che l'indice "alberghi" di esempio è già compilata con un numero di documenti. Si noti come non è specificare tutti i campi possibili documento quando si usa `MergeOrUpload` e come è specificato solo la chiave di documento (`HotelId`) quando si usa `Delete`.

Si noti inoltre che è possibile includere fino a 1000 documenti solo in una singola richiesta di indicizzazione.

> [AZURE.NOTE] In questo esempio abbiamo stiamo applicando diverse azioni documenti diversi. Se si desidera eseguire le stesse operazioni in tutti i documenti in batch, invece di chiamare `IndexBatch.New`, è possibile usare altri metodi statici della `IndexBatch`. Ad esempio, è possibile creare batch chiamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, o `IndexBatch.Delete`. Questi metodi accettano un insieme di documenti (oggetti di tipo `Hotel` in questo esempio) invece di `IndexAction` oggetti.

## <a name="iv-import-data-to-the-index"></a>IV. Importare dati in corrispondenza dell'indice
Dopo aver creato un oggetto inizializzato `IndexBatch` oggetto, è possibile inviare all'indice chiamando `Documents.Index` sul `SearchIndexClient` oggetto. Nell'esempio seguente viene illustrato come chiamare `Index`, così come alcuni passaggi aggiuntivi sarà necessario eseguire:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Nota la `try` / `catch` che circonda la chiamata alla `Index` metodo. Il blocco catture gestisce un caso di errore importanti per l'indicizzazione. Se il servizio di ricerca di Azure viene interrotta indicizzare alcuni dei documenti nel batch di un `IndexBatchException` generata dal `Documents.Index`. Questo problema può verificarsi se si indicizza documenti mentre si trova il servizio in condizioni di carico elevato. **Si consiglia di gestire in modo esplicito questo caso nel codice.** È possibile ritardare e ripetere l'operazione di indicizzazione i documenti che non è riuscito oppure è possibile accedere e continuare come nell'esempio non oppure è possibile eseguire altri a seconda di requisiti la coerenza dei dati dell'applicazione.

Infine, il codice nell'esempio precedente ritardi per due secondi. L'indicizzazione accade in modo asincrono nel servizio di ricerca di Azure, in modo che l'applicazione di esempio è necessario attendere per assicurarsi che i documenti sono disponibili per la ricerca. Ritardi … sono in genere necessari solo demo, test e applicazioni di esempio.

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>Modalità di .NET SDK gestione documenti

Per comprendere come Azure ricerca .NET SDK è in grado di caricare le istanze di una classe definite dall'utente come `Hotel` per l'indice. Per informazioni su come rispondere a questa domanda, esaminiamo il `Hotel` classe, viene eseguito il mapping allo schema indice definito in [creare un indice di ricerca di Azure mediante .NET SDK](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

La prima cosa da notare è che ogni proprietà pubblica di `Hotel` corrisponde a un campo nella definizione di indice, ma con una differenza fondamentale: il nome di ogni campo inizia con una lettera minuscola ("le maiuscole/minuscole"), mentre il nome di ogni proprietà pubblica di `Hotel` inizia con una lettera di lettere maiuscole ("Pascal maiuscole/minuscole"). Si tratta di uno scenario comune nelle applicazioni .NET che eseguire l'associazione dati in cui lo schema di destinazione è all'esterno del controllo dello sviluppatore di applicazioni. Invece di dover violare le leggi .NET indicazioni per la denominazione effettuando proprietà nomi caratterizzati-maiuscole/minuscole, è possibile identificarlo SDK per mappare i nomi delle proprietà in caso di caratterizzati automaticamente con la `[SerializePropertyNamesAsCamelCase]` attributo.

> [AZURE.NOTE] Azure ricerca .NET SDK utilizza la libreria [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) per serializzare e deserializzare gli oggetti di un modello personalizzato da e verso JSON. Se necessario, è possibile personalizzare questa serializzazione. Sono disponibili ulteriori dettagli [aggiornamento a Azure ricerca .NET SDK versione 1.1](search-dotnet-sdk-migration.md#WhatsNew). Un esempio consiste nell'utilizzare la `[JsonProperty]` attributo la `DescriptionFr` proprietà nel codice dell'esempio precedente.

Il secondo importanza di `Hotel` classe sono elencati i tipi di dati delle proprietà pubbliche. Eseguire il mapping i tipi di .NET di queste proprietà per i tipi di campo equivalente alla definizione dell'indice. Ad esempio, il `Category` stringa mappe delle proprietà per il `category` campo di tipo `DataType.String`. Sono disponibili i mapping di tipo simili tra `bool?` e `DataType.Boolean`, `DateTimeOffset?` e `DataType.DateTimeOffset`e così via. Vengono descritte le regole specifiche per il mapping di tipo con il `Documents.Get` metodo su [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

La possibilità di usare le classi come documenti funziona in entrambe le direzioni; È anche possibile recuperare i risultati della ricerca e installato SDK automaticamente deserializzare loro di un tipo di propria scelta, come illustrato nell' [articolo successivo](search-query-dotnet.md).

> [AZURE.NOTE] Azure ricerca .NET SDK supporta anche documenti digitato in modo dinamico utilizzando il `Document` classe, vale a dire/un mapping dei nomi di campo per i valori dei campi chiave. Questo è utile in scenari in cui non si conosce lo schema di indice in fase di progettazione o se conveniente associare a classi modello specifico. Tutti i metodi di SDK che gestiscono documenti dispongono di overload che funzionano con le `Document` classe, nonché overload fortemente tipizzata che accettano un parametro di tipo generico. Nel codice di esempio in questo articolo vengono utilizzata solo quest'ultima. Sono disponibili ulteriori informazioni sul `Document` [su MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx)per la classe.

**Una nota importante sui tipi di dati**

Quando si progettano le classi di modello per eseguire il mapping a un indice di ricerca di Azure, è consigliabile dichiarazione di proprietà dei tipi di valore come `bool` e `int` nullable (ad esempio `bool?` anziché `bool`). Se si utilizza una proprietà non nullable, è necessario **garantire** che non contiene documenti l'indice contengono un valore null per il campo corrispondente. SDK né il servizio di ricerca di Azure consente di attivare la procedura.

Non si tratta di un problema di ipotetico: immaginare uno scenario in cui si aggiunge un nuovo campo a un indice esistente che è di tipo `DataType.Int32`. Dopo aver aggiornato la definizione di indice, tutti i documenti avrà un valore null per il nuovo campo (perché tutti i tipi di sono nullable in Azure ricerca). Se si utilizza una classe modello con un non nullable `int` proprietà del campo corrispondente, verrà visualizzato un `JsonSerializationException` così durante il tentativo di recuperare documenti:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Per questo motivo, è consigliabile utilizzare i tipi nullable nelle classi modello è buona norma.

## <a name="next"></a>Successivo
Dopo avere popolato indice di ricerca di Azure, sarà possibile iniziare a eseguire una query per cercare documenti. Per informazioni dettagliate, vedere [Indice di ricerca Azure alle Query](search-query-overview.md) .
