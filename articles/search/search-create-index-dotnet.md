<properties
    pageTitle="Creare un indice di ricerca di Azure utilizzando .NET SDK | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Creare un indice nel codice utilizzando Azure ricerca .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Creare un indice di ricerca di Azure utilizzando .NET SDK
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-an-index.md)
- [Portale](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)


In questo articolo verrà descritto il processo di creazione di un [indice](https://msdn.microsoft.com/library/azure/dn798941.aspx) di ricerca Azure utilizzando [Azure ricerca .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Prima di seguire questa Guida e creare un indice analitico, è necessario avere già [creato un servizio di ricerca di Azure](search-create-service-portal.md).

Si noti che tutto il codice di esempio in questo articolo è scritto in c#. È possibile trovare il codice sorgente completo codice [sul GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Identificare chiave dell'api amministratore del servizio di ricerca di Azure
Ora che hanno effettuato il provisioning di un servizio di ricerca di Azure, si è pronti praticamente da emettere richieste per l'endpoint del servizio mediante .NET SDK. Prima di tutto, sarà necessario ottenere uno dell'amministrazione api tasti di scelta rapida generati per il servizio di ricerca che è stato effettuato il provisioning. .NET SDK invierà questo tasto api a ogni richiesta del servizio. Con una chiave valida stabilisce protezione, in base a ogni richiesta, tra l'applicazione di inviare la richiesta e il servizio che viene gestita.

1. Per trovare api tasti di scelta rapida del servizio è necessario accedere al [Portale di Azure](https://portal.azure.com/)
2. Passare a blade del servizio di ricerca di Azure
3. Fare clic sull'icona "tasti visualizzati"

Il servizio avrà *chiavi di amministrazione* e *le query*.

  - Le principali e secondarie *tasti amministratore* diritti completo a tutte le operazioni, compresa la possibilità di gestire il servizio, creare ed eliminare gli indici, indicizzatori e origini dati. Esistono due metodi in modo che è possibile continuare a usare il tasto secondario se si decide di rigenerare la chiave primaria e viceversa.
  - Le *chiavi query* concedere l'accesso in sola lettura per gli indici e i documenti e vengono in genere distribuiti per le applicazioni client che inviano richieste di ricerca.

Per creare un indice analitico, è possibile utilizzare la chiave primaria o secondaria amministratore.

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Creare un'istanza della classe SearchServiceClient
Per iniziare a usare Azure ricerca .NET SDK, sarà necessario creare un'istanza di `SearchServiceClient` classe. Questa classe è diversi costruttori. La combinazione di colori desiderata accetta il nome del servizio di ricerca e una `SearchCredentials` oggetto come parametri. `SearchCredentials`include la chiave dell'api.

Il codice seguente crea un nuovo `SearchServiceClient` utilizzando i valori per il nome del servizio di ricerca e la chiave dell'api archiviati in file di configurazione dell'applicazione (`app.config` o `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`ha un `Indexes` proprietà. Questa proprietà consente di tutti i metodi che è necessario creare, elenco, aggiornare o eliminare gli indici di ricerca di Azure.

> [AZURE.NOTE] Il `SearchServiceClient` classe gestisce le connessioni al servizio di ricerca. Per evitare di aprire troppe connessioni, è consigliabile condividere una singola istanza di `SearchServiceClient` nell'applicazione se possibile. I metodi sono thread-safe per abilitare ad esempio la condivisione.

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. Definire l'indice di ricerca di Azure utilizzando il `Index` classe
Una singola chiamata per il `Indexes.Create` metodo creerà l'indice. Questo metodo accetta come parametro un `Index` oggetto che definisce l'indice di ricerca di Azure. È necessario creare un `Index` oggetto e inizializzare come indicato di seguito:

1. Impostare il `Name` proprietà del `Index` oggetto il nome dell'indice.
2. Impostare il `Fields` proprietà del `Index` oggetto in una matrice di `Field` oggetti. Ognuna del `Field` oggetti definisce il comportamento di un campo dell'indice di. È possibile specificare il nome del campo al quale costruttore con il tipo di dati (o analyzer per campi di tipo stringa). È inoltre possibile impostare altre proprietà come `IsSearchable`, `IsFilterable`e così via.

È importante tenere la ricerca utente esperienza e le esigenze aziendali presenti quando si progetta l'indice come ogni `Field` devono essere assegnate le [proprietà appropriate](https://msdn.microsoft.com/library/azure/dn798941.aspx). Queste proprietà controllo quale ricerca caratteristiche (filtro, faceting, l'ordinamento di ricerca full-text e così via) applicare ai campi. Per qualsiasi proprietà non in modo esplicito è, il `Field` le impostazioni predefinite per disabilitare la funzionalità di ricerca corrispondente a meno che non si attiva specificamente per la classe.

Nel caso dell'esempio sono state denominato nostro indice "alberghi" e definita dei campi nel modo seguente:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Avendo scelto con attenzione i valori di proprietà per ogni `Field` in base a come si ritiene che verranno utilizzati in un'applicazione. Ad esempio, è probabile che gli utenti cercare alberghi saranno interessati a parole chiave corrispondenze trovate il `description` di campo, in modo che si attiva ricerca full-text per il campo mediante l'impostazione `IsSearchable` a `true`.

Si noti che esattamente un campo dell'indice del tipo di `DataType.String` deve essere designato come il campo _chiave_ mediante l'impostazione `IsKey` a `true` (vedere `hotelId` nell'esempio precedente).

La definizione dell'indice sopra utilizza un analizzatore del linguaggio personalizzato per il `description_fr` campo perché viene utilizzato per archiviare testo francese. Vedere [la lingua supportata argomento su MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) , nonché il corrispondente [il post di blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) per ulteriori informazioni sull'analisi del linguaggio.

> [AZURE.NOTE]  Si noti che passando `AnalyzerName.FrLucene` nel costruttore, il `Field` saranno automaticamente di tipo `DataType.String` e sarà necessario `IsSearchable` impostato su `true`.

## <a name="iv-create-the-index"></a>IV. Creare l'indice
Dopo aver creato un oggetto inizializzato `Index` oggetto, è possibile creare l'indice è sufficiente chiamando `Indexes.Create` sul `SearchServiceClient` oggetto:

```csharp
serviceClient.Indexes.Create(definition);
```

Per una richiesta ha esito positivo, il metodo restituirà normalmente. Se si è verificato un problema con la richiesta, ad esempio un parametro non valido, il metodo genererà `CloudException`.

Quando si termine con un indice e si desidera eliminarlo, chiamare semplicemente il `Indexes.Delete` metodo sul `SearchServiceClient`. Ad esempio, si tratta come consentirebbe di eliminare l'indice "alberghi":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] Il codice di esempio in questo articolo utilizza l'icona del metodi di Azure ricerca .NET SDK per semplicità. È consigliabile utilizzare i metodi asincroni nelle applicazioni per mantenerle scalable e tempestivo. Negli esempi precedenti, ad esempio, è possibile utilizzare `CreateAsync` e `DeleteAsync` anziché `Create` e `Delete`.

## <a name="next"></a>Successivo
Dopo aver creato un indice di ricerca di Azure, sarà possibile caricare [il contenuto in corrispondenza dell'indice](search-what-is-data-import.md) in modo che è possibile avviare la ricerca dei dati.
