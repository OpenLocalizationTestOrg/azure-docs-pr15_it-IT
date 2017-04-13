<properties
    pageTitle="Utilizzo di raccolta di client gestito applicazione servizio Mobile applicazioni (Windows | Xamarin) | Microsoft Azure"
    description="Informazioni su come usare un client .NET Azure App servizio Mobile App per con le applicazioni di Windows e Xamarin."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Come utilizzare il client gestito per App Mobile Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>Panoramica

Questa guida viene illustrato come eseguire gli scenari comuni utilizzando la libreria client gestiti per Azure App servizio Mobile App per Windows e aggiungere App Xamarin. Se ha familiarità con App Mobile, è necessario prendere in considerazione prima di tutto completando la [Guida introduttiva di Azure Mobile App] [ 1] esercitazione. In questa guida è focalizzare l'attenzione su lato client gestito SDK. Per altre informazioni sul SDK sul lato server per le App per dispositivi mobili, vedere la documentazione di [.NET Server SDK] [ 2] o [Node Server SDK][3].

## <a name="reference-documentation"></a>Documentazione di riferimento

La documentazione di riferimento per il client SDK si trova in: [riferimento client Azure Mobile app .NET][4].
Sono disponibili anche numerosi esempi di client [repository esempi di Azure GitHub][5].

## <a name="supported-platforms"></a>Piattaforme supportate

La piattaforma .NET supporta le piattaforme seguenti:

* Versioni di Xamarin Android per API 19 a 24 (KitKat tramite Nougat)
* IOS Xamarin rilascia per iOS 8.0 e versioni successive
* Piattaforma Windows universale
* Windows Phone 8.1
* Windows Phone 8.0 ad eccezione di applicazioni Silverlight

L'autenticazione "server del flusso di cassa" utilizza una visualizzazione Web per l'interfaccia utente presentato.  Se il dispositivo non è in grado di presentare un UI WebView, sono necessari altri metodi di autenticazione.  Questo SDK che quindi non è adatto per tipo di controllo o dispositivi con restrizioni.

##<a name="setup"></a>Il programma di installazione e i prerequisiti

Si presuppone che hanno creato e pubblicare il progetto di back-end App Mobile, che include almeno una tabella.  Nel codice utilizzato in questo argomento, la tabella è denominata `TodoItem` e include le colonne seguenti: `Id`, `Text`, e `Complete`. Questa tabella è la stessa tabella creata al termine [app Mobile Azure Guida introduttiva].

Il tipo di lato client digitato corrispondente in c# è la classe seguente:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

[JsonPropertyAttribute] [ 6] consente di definire il mapping di *PropertyName* tra il tipo di client e la tabella.

Per informazioni su come creare tabelle nel back-end App Mobile, vedere le informazioni nell' [argomento .NET Server SDK] [ 7] o l' [argomento node Server SDK][8]. Se è stato creato il back-end App Mobile nel portale di Azure utilizzando la Guida introduttiva, è possibile usare anche l'impostazione di **facile tabelle** nel [portale di Azure].

###<a name="how-to-install-the-managed-client-sdk-package"></a>Procedura: installare il pacchetto SDK client gestito

Utilizzare uno dei metodi seguenti per installare il pacchetto SDK client gestito per le App per dispositivi mobili da [NuGet][9]:

+ **Visual Studio** Pulsante destro del mouse del progetto, fare clic su **Gestisci pacchetti NuGet**, cercare la `Microsoft.Azure.Mobile.Client` creare un pacchetto, quindi fare clic su **Installa**.

+ **Studio Xamarin** Il pulsante destro del progetto, fare clic su **Aggiungi** > **Aggiungere pacchetti NuGet**, cercare la `Microsoft.Azure.Mobile.Client `creare un pacchetto e quindi fare clic su **Aggiungi pacchetto**.

Nel file di attività principale, ricordarsi di aggiungere l'istruzione **using** seguente:

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>Procedura: utilizzare i simboli di debug in Visual Studio

I simboli per lo spazio dei nomi Microsoft.Azure.Mobile sono disponibili in [SymbolSource][10].  Vedere le [istruzioni SymbolSource] [ 11] di integrare SymbolSource con Visual Studio.

##<a name="create-client"></a>Creare il client App Mobile

Il codice seguente crea [MobileServiceClient] [ 12] oggetto a cui viene utilizzato per accedere il back-end App Mobile.

    var client = new MobileServiceClient("MOBILE_APP_URL");

Nell'esempio precedente, sostituire `MOBILE_APP_URL` con l'URL di back-end App Mobile, disponibile nella e per il back-end App Mobile nel [portale di Azure]. L'oggetto MobileServiceClient deve essere singleton.

## <a name="work-with-tables"></a>Usare le tabelle

La sezione seguente viene illustrato come eseguire ricerche e recuperare record e modificare i dati all'interno della tabella.  Vengono illustrati i seguenti argomenti:

* [Creare un riferimento di tabella](#instantiating)
* [Eseguire query sui dati](#querying)
* [Filtrare i dati restituiti](#filtering)
* [Ordinare i dati restituiti](#sorting)
* [Restituire i dati nelle pagine](#paging)
* [Selezionare colonne specifiche](#selecting)
* [Cercare un record per Id](#lookingup)
* [Gestione delle query senza di](#untypedqueries)
* [Inserimento di dati](#inserting)
* [L'aggiornamento dei dati](#updating)
* [Eliminazione dei dati](#deleting)
* [Risoluzione dei conflitti e ottimistica](#optimisticconcurrency)
* [Associazione di una nuova interfaccia utente di Windows](#binding)
* [Modificare le dimensioni della pagina](#pagesize)

###<a name="instantiating"></a>Procedura: creare un riferimento di tabella

Tutto il codice che consente di accedere o modifica dati in una tabella di back-end chiama funzioni sul `MobileServiceTable` oggetto. Ottenere un riferimento alla tabella utilizzando il metodo [GetTable] , come indicato di seguito:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

L'oggetto restituito viene utilizzato il modello serializzazione digitato. Un modello di serializzazione è supportato anche. Il seguente esempio si [Crea un riferimento a una tabella senza]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Nelle query senza, è necessario specificare la stringa di query OData sottostante.

###<a name="querying"></a>Procedura: eseguire Query sui dati dell'App Mobile

In questa sezione viene descritto come eseguire query nel back-end App Mobile, che include le seguenti funzionalità:

- [Filtrare i dati restituiti](#filtering)
- [Ordinare i dati restituiti](#sorting)
- [Restituire i dati nelle pagine](#paging)
- [Selezionare colonne specifiche](#selecting)
- [Cercare dati in base all'ID](#lookingup)

>[AZURE.NOTE]Per impedire la restituzione di tutte le righe viene applicato un formato di pagina basate su server.  Suddivisione in pagine mantiene le richieste di predefinito per grandi quantità di dati da influenzare il servizio.  Per restituire più di 50 righe, utilizzare la `Skip` e `Take` metodo, come descritto in [restituiscono i dati nelle pagine].

###<a name="filtering"></a>Procedura: filtrare i dati restituiti.

Il codice seguente viene illustrato come filtrare i dati facilmente individuabili perché includono un `Where` clausola in una query. Restituisce tutti gli elementi da `todoTable` cui `Complete` proprietà è uguale a `false`. La funzione [nel punto in cui] si applica una filtro predicato per la query la tabella delle righe.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

È possibile visualizzare l'URI della richiesta inviata a back-end tramite il software del controllo, ad esempio gli strumenti di sviluppo browser o [Fiddler]. Se osserva la richiesta URI, si noterà che la stringa di query viene modificata:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

La richiesta di OData viene convertita in una query SQL da SDK Server:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

La funzione di quello passata alla `Where` metodo può avere un numero non autorizzato di condizioni.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

In questo esempio sarebbe vengano convertito in una query SQL da Server SDK:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Questa query può anche essere suddivisa in più clausole:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Due metodi sono equivalenti e possono essere usati in modo intercambiabile.  L'opzione ex&mdash;di concatenare più predicati in una query&mdash;più compatta e consigliati.

Il `Where` clausola supporta operazioni che è possibile tradurre il subset di OData. Operazioni includono:

* Operatori relazionali (= =,! =, <, < =, >, > =),
* Operatori aritmetici (+, -, /, *, %),
* Numero di precisione (Math.Floor, Math.Ceiling)
* Funzioni di stringa (lunghezza, sottostringa, Sostituisci, IndexOf, StartsWith, EndsWith)
* Proprietà Data (anno, mese, giorno, ora, minuto, secondo)
* Accedere alle proprietà di un oggetto e
* Espressioni combinazione qualsiasi di queste operazioni.

Se si sceglie quali supporta Server SDK, è possibile considerare la [documentazione per OData v3].

###<a name="sorting"></a>Procedura: ordinare i dati restituiti.

Il codice seguente viene illustrato come ordinare i dati facilmente individuabili perché includono una funzione [OrderBy] o [OrderByDescending] nella query. Restituisce gli elementi da `todoTable` ordinamento crescente per il `Text` campo.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Procedura: restituire i dati nelle pagine

Per impostazione predefinita, il back-end restituisce le prime 50 righe. È possibile aumentare il numero di righe restituite chiamando il metodo [eseguire] . Usare `Take` insieme il metodo [Ignora] richiederne uno specifico "pagina" del totale set di dati restituito dalla query. Quando eseguita la query seguente restituisce i primi tre elementi nella tabella.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

La query seguente rivista ignora i primi tre risultati e restituisce tre risultati. La query restituisce la seconda "pagina" dei dati, in cui le dimensioni della pagina sono tre elementi.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

Il metodo [IncludeTotalCount] richiede il totale di _tutti_ i record che sarebbero stati restituiti, ignorando qualsiasi clausola suddivisione in pagine/limite specificato:

    query = query.IncludeTotalCount();

In un'app di scenari reali, è possibile utilizzare query simili all'esempio precedente con un controllo di spostamento o comparabile dell'interfaccia utente per spostarsi tra le pagine.

>[AZURE.NOTE]Per ignorare il limite di 50 riga in un back-end App Mobile, è necessario applicare [EnableQueryAttribute] al metodo GET pubblico e specificare il comportamento di spostamento. Se vengono applicati al metodo, le operazioni seguenti imposta il valore massimo restituito righe a 1000:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Procedura: selezionare colonne specifiche

È possibile specificare l'insieme di proprietà da includere nei risultati aggiungendo una clausola [Select] alla query. Ad esempio, il codice seguente mostra come selezionare un solo campo, nonché come selezionare e formattare più campi:

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Tutte le funzioni finora descritte sono additivi, in modo che è possibile mantenere concatenazione loro. Ogni chiamata concatenato influisce su altro della query. Un esempio di più:

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Procedura: cercare dati in base all'ID

La funzione [LookupAsync] può essere utilizzata per cercare gli oggetti dal database con un particolare ID.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Procedura: eseguire query senza

Quando si esegue una query utilizzando un oggetto di tabella senza, è necessario specificare esplicitamente la stringa di query OData chiamando [ReadAsync], come illustrato nell'esempio seguente:

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Per annullare valori JSON che è possibile utilizzare come un elenco di proprietà. Per ulteriori informazioni su JToken e Newtonsoft Json.NET, visitare il sito [Json.NET] .

### <a name="inserting"></a>Procedura: inserire dati in un back-end App Mobile

Tutti i tipi di client devono contenere un membro denominato **Id**, per impostazione predefinita una stringa. Questo **Id** richiesto per eseguire le operazioni CRUD e per la sincronizzazione offline. Il codice seguente viene illustrato come utilizzare il metodo [InsertAsync] per inserire nuove righe in una tabella. Il parametro contiene i dati vengono inseriti in un oggetto .NET.

    await todoTable.InsertAsync(todoItem);

Se un valore di ID univoco personalizzato non sia il `todoItem` durante l'inserimento, viene generato un GUID dal server.
È possibile recuperare l'Id generato controllando l'oggetto al termine della chiamata.

Per inserire senza dati, è possibile sfruttare Json.NET:

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Ecco un esempio di utilizzo di un indirizzo di posta elettronica come id univoco stringa:

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Utilizzo dei valori degli ID

App Mobile supporta valori stringa personalizzati univoci per colonna **id** della tabella. Un valore stringa consente alle applicazioni di utilizzare valori personalizzati, ad esempio indirizzi di posta elettronica o i nomi utente per l'ID.  ID stringa fornisce i seguenti vantaggi:

* ID generati senza effettuare un round trip al database.
* I record sono più semplice unire dati da tabelle diverse o nei database.
* I valori ID possano migliore integrazione con una logica dell'applicazione.

Quando un valore di ID stringa non è impostato su un record inserito, back-end App Mobile genera un valore univoco per l'ID. È possibile utilizzare il metodo [Guid.NewGuid] per generare i valori di ID sul client o in back-end.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Procedura: modificare i dati in un back-end App Mobile

Il codice seguente viene illustrato come utilizzare il metodo [UpdateAsync] per aggiornare un record esistente con lo stesso ID con le nuove informazioni. Il parametro contiene i dati da aggiornare come oggetto .NET.

    await todoTable.UpdateAsync(todoItem);

Per aggiornare i dati non tipizzati, è possibile sfruttare [Json.NET] come indicato di seguito:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Un `id` campo deve essere specificato durante la creazione di un aggiornamento. Back-end utilizza la `id` campo per identificare la riga da aggiornare. Il `id` campo può essere ottenuto dal risultato della `InsertAsync` chiamata. Un `ArgumentException` viene generato se si tenta di aggiornare un elemento senza fornire il `id` valore.

###<a name="deleting"></a>Procedura: eliminare i dati in un back-end App Mobile

Il codice seguente viene illustrato come utilizzare il metodo [DeleteAsync] per eliminare un'istanza esistente. L'istanza è identificato dalla `id` campo set di `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Per eliminare senza dati, è possibile sfruttare Json.NET come indicato di seguito:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Quando si effettua una richiesta di eliminazione, è necessario specificare un ID. Altre proprietà non vengono passati al servizio o vengono ignorati nel servizio. Il risultato di un `DeleteAsync` chiamata è in genere `null`. Può ottenere l'ID per passare tra il risultato della `InsertAsync` chiamata. A `MobileServiceInvalidOperationException` viene generata quando si tenta di eliminare un elemento senza specificare la `id` campo.

###<a name="optimisticconcurrency"></a>Procedura: usare ottimistica per la risoluzione dei conflitti

Due o più client possono scrivere modifiche allo stesso elemento nello stesso momento. Senza il rilevamento dei conflitti, ultima scrittura da sovrascrivere eventuali aggiornamenti precedenti. **Controllo ottimistica** presuppone che ogni transazione può eseguire il commit e pertanto non utilizzare alcun blocco delle risorse.  Prima di eseguire il commit una transazione, controllo ottimistica verifica che nessun altra transazione ha modificato i dati. Se i dati sono stati modificati, transazione commit verrà riportata.

App Mobile supporta controllo ottimistica il rilevamento delle modifiche per ogni elemento utilizzando il `version` colonna di proprietà di sistema definito per ogni tabella il back-end App Mobile. Ogni volta che viene aggiornato un record, App Mobile imposta la `version` proprietà per tale record per un nuovo valore. Durante la richiesta di ogni aggiornamento, la `version` confrontata proprietà del record incluso con la richiesta per la stessa proprietà per i record del server. Se la versione passato con la richiesta non corrisponde a back-end e quindi la libreria client genera un `MobileServicePreconditionFailedException<T>` eccezione. Il tipo incluso con l'eccezione è il record da back-end che contiene la versione server del record. L'applicazione quindi possibile utilizzare queste informazioni per decidere se eseguire la richiesta di aggiornamento con il corretto `version` valore back-end per applicare le modifiche.

Definire una colonna sulla classe di tabella per la `version` proprietà di sistema per abilitare ottimistica. Per esempio:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Le applicazioni che utilizzano tabelle senza abilitare ottimistica impostando la `Version` contrassegnare la `SystemProperties` della tabella come descritto di seguito.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Oltre ad attivare ottimistica, è necessario intercettare la `MobileServicePreconditionFailedException<T>` eccezione nel codice quando si chiama [UpdateAsync].  Risolvere il conflitto applicando il corretto `version` al record aggiornato e chiamare [UpdateAsync] con il record risolto. Il codice seguente viene illustrato come risolvere un conflitto di scrittura rilevato una volta:

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

Per ulteriori informazioni, vedere l'argomento [Offline sincronizzazione dei dati nell'App Mobile Azure] .

###<a name="binding"></a>Procedura: dati App Mobile associazione a un'interfaccia utente di Windows

In questa sezione viene illustrato come visualizzare gli oggetti dati restituiti con elementi dell'interfaccia utente in un'app di Windows.  Nell'esempio seguente associato all'origine dell'elenco con una query per gli elementi non completati. [MobileServiceCollection] crea un insieme di associazione per dispositivi mobili compatibili con App.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Alcuni controlli in fase di esecuzione gestito supportano un'interfaccia denominata [ISupportIncrementalLoading]. Questa interfaccia consente ai controlli richiedere dati aggiuntivi quando l'utente scorre. Esiste un supporto incorporato per questa interfaccia per le app universale di Windows tramite [MobileServiceIncrementalLoadingCollection], che consente di gestire automaticamente le chiamate dai controlli. Usare `MobileServiceIncrementalLoadingCollection` nelle applicazioni di Windows come descritto di seguito:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Per usare la nuova raccolta sulle applicazioni di Windows Phone 8 e "Silverlight", il `ToCollection` metodi di estensione nel `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Per caricare dati, chiamare `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Quando si utilizza la raccolta creata chiamando `ToCollectionAsync` o `ToCollection`, viene visualizzato un insieme che può essere associato a controlli dell'interfaccia utente.  Questa raccolta è presente suddivisione in pagine.  Poiché la raccolta è il caricamento dei dati dalla rete, a volte il caricamento non riesce. Per gestire tali errori, ignorare il `OnException` metodo su `MobileServiceIncrementalLoadingCollection` per gestire le eccezioni risultanti dalle chiamate a `LoadMoreItemsAsync`.

È consigliabile se la tabella contiene molti campi, ma si desidera visualizzare alcuni di essi nel controllo. È possibile utilizzare le istruzioni nella sezione precedente "[selezionare colonne specifiche](#selecting)" per selezionare colonne specifiche da visualizzare nell'interfaccia utente.

###<a name="pagesize"></a>Modificare le dimensioni della pagina

App Mobile Azure restituisce un massimo di 50 elementi per ogni richiesta per impostazione predefinita.  È possibile modificare le dimensioni di suddivisione in pagine aumentandone la dimensione massima delle pagine nel client e server.  Per aumentare le dimensioni della pagina richieste, specificare `PullOptions` quando si usa `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

Supponendo che sono state la `PageSize` uguale o maggiore di 100 all'interno del server, una richiesta restituisce gli elementi fino a 100.

##<a name="#offlinesync"></a>Usare le tabelle non in linea

Le tabelle non in linea utilizzano un archivio per archiviare i dati SQLite locali per l'utilizzo offline.  Tabella tutte le operazioni vengono eseguite contro locale SQLite memorizzata anziché l'archivio server remoto.  Per creare una tabella non in linea, preparare il progetto:

1. In Visual Studio, fare doppio clic la soluzione > **Gestisci pacchetti NuGet per soluzione...**, quindi cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** per tutti i progetti della soluzione.

2. (Facoltativo) Per supportare dispositivi Windows, installare uno dei pacchetti di runtime SQLite seguenti:

    * **Runtime Windows 8.1:** Installare [SQLite per Windows 8.1][3].
    * **Windows Phone 8.1:** Installare [SQLite per Windows Phone 8.1][4].
    * **Piattaforma Windows universale** Installare [SQLite per Universal Windows universale][5].

3. (Facoltativo). Per i dispositivi di Windows, fare clic su **riferimenti** > **Aggiungi riferimento**, espandere la cartella di **Windows** > **estensioni**e quindi abilitare appropriato **SQLite per Windows** SDK insieme **Visual C++ 2013 Runtime per Windows** SDK.
    I nomi SQLite SDK leggermente con ogni piattaforma Windows.

Prima di creare un riferimento di tabella, è necessario preparare l'archivio locale:

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

Inizializzazione dell'archivio avviene solitamente immediatamente dopo aver creato il client.  **OfflineDbPath** deve essere un nome appropriato per l'utilizzo in tutte le piattaforme supportate.  Se il percorso è il percorso completo (vale a dire inizia con una barra), quindi viene utilizzato il percorso.  Se il percorso non è completo, il file viene inserito in un percorso specifico della piattaforma.

* Per dispositivi iOS e Android, il percorso predefinito è la cartella "File personali".
* Per i dispositivi di Windows, il percorso predefinito è la cartella "AppData" specifici dell'applicazione.

È possibile ottenere un riferimento alla tabella utilizzando il `GetSyncTable<>` metodo:

    var table = client.GetSyncTable<TodoItem>();

Non è necessario eseguire l'autenticazione per l'utilizzo di una tabella non in linea.  È sufficiente eseguire l'autenticazione quando comunica con il servizio di back-end.

###<a name="syncoffline"></a>La sincronizzazione di una tabella non in linea

Tabelle offline non vengono sincronizzate con il back-end per impostazione predefinita.  La sincronizzazione viene suddiviso in due parti.  È possibile inserire le modifiche separatamente il download di nuovi elementi.  Ecco un metodo di sincronizzazione tipica:

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
                "allTodoItems",
                this.todoTable.CreateQuery());
        }
        catch (MobileServicePushFailedException exc)
        {
            if (exc.PushResult != null)
            {
                syncErrors = exc.PushResult.Errors;
            }
        }

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
        if (syncErrors != null)
        {
            foreach (var error in syncErrors)
            {
                if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                {
                    //Update failed, reverting to server's copy.
                    await error.CancelAndUpdateItemAsync(error.Result);
                }
                else
                {
                    // Discard local change.
                    await error.CancelAndDiscardItemAsync();
                }

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Se il primo argomento della funzione `PullAsync` è null, quindi sincronizzazione incrementale non viene utilizzata.  Ogni operazione di sincronizzazione recupera tutti i record.

SDK esegue impliciti `PushAsync()` prima di eseguire il pull dei record.

La gestione dei conflitti accade in un `PullAsync()` metodo.  Gestire i conflitti nello stesso modo tabelle online.  Viene visualizzato il conflitto quando `PullAsync()` chiamato al posto del durante l'inserimento, aggiornamento o Elimina. Se viene eseguita più conflitti, sono inclusi in un unico MobileServicePushFailedException.  Gestire ogni errore separatamente.

##<a name="#customapi"></a>Lavorare con un'API personalizzata

Un'API personalizzata consente di definire personalizzato endpoint che espongono funzionalità del server che non eseguire il mapping a un'istruzione insert, aggiornare, eliminare o operazione di lettura. Tramite un'API personalizzata, è consentito un maggiore controllo messaggistica, inclusa la lettura e impostazione delle intestazioni dei messaggi HTTP e la definizione di un formato corpo del messaggio diverso da JSON.

Chiamare una API personalizzata chiamando uno dei metodi [InvokeApiAsync] sul client. Ad esempio, la riga di codice seguente invia una richiesta POST per il **completeAll** API nel back-end:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Questo modulo è una chiamata al metodo digitato ed è necessario che il tipo restituito **MarkAllResult** è definito. Metodi digitati e non sono supportati.

##<a name="authentication"></a>Autenticazione degli utenti

App Mobile supporta l'autenticazione e autorizzazione degli utenti app con diversi provider di identità esterni: Facebook, Google, Account Microsoft, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni nelle tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È anche possibile utilizzare l'identità degli utenti autenticati per implementare le regole di autorizzazione in script server. Per ulteriori informazioni, vedere l'esercitazione [autenticazione aggiungere all'app].

Due flussi di autenticazione supportati: flusso _gestito client_ e _server gestito_ . Il flusso relativo al server gestito offre l'esperienza di autenticazione più semplice, come si basa sull'interfaccia di autenticazione web del provider. Il flusso relativo al client gestito consente l'integrazione più approfondita con funzionalità specifiche del dispositivo mentre si basa su SDK specifico di dispositivo specifiche del provider.

>[AZURE.NOTE] È consigliabile utilizzare un flusso di client gestite nelle applicazioni produzione.

Per configurare l'autenticazione, è necessario registrare l'app con uno o più provider di identità.  Il provider di identità genera un ID client e un segreto client per l'app.  Questi valori vengono quindi configurati nel back-end per abilitare il servizio di App Azure autenticazione/autorizzazione.  Per ulteriori informazioni, seguire le istruzioni dettagliate nell'esercitazione [autenticazione aggiungere all'app].

In questa sezione vengono trattati i seguenti argomenti:

+ [Autenticazione gestite client](#clientflow)
+ [Autenticazione server gestito](#serverflow)
+ [La memorizzazione nella cache del token di autenticazione](#caching)

###<a name="clientflow"></a>Autenticazione gestite client

L'app può in modo indipendente contattare il provider di identità e quindi specificare il token restituito durante l'accesso con il back-end. Questo flusso di client consente di specificare un'esperienza single sign-on per gli utenti o per recuperare dati aggiuntivi relativi all'utente dal provider di identità. Autenticazione flusso client è preferibile utilizzare un flusso di server come provider di identità SDK consente di mostrato un esempio dell'esperienza utente più nativo e per un'ulteriore personalizzazione.

Esempi sono disponibili per i modelli di autenticazione del flusso di cassa client seguenti:

+ [Active Directory Authentication Library](#adal)
+ [Facebook o Google](#client-facebook)
+ [Live SDK](#client-livesdk)

#### <a name="adal"></a>Autenticazione degli utenti con Active Directory Authentication Library

È possibile utilizzare la libreria di autenticazione Active Directory (ADAL) all'autenticazione dell'utente avvio dal client mediante l'autenticazione di Azure Active Directory.

1. Configurare il back-end di app per dispositivi mobili per AAD sign-on, seguendo l'esercitazione [come configurare il servizio di App per le credenziali Active Directory] . Assicurarsi che completare il passaggio facoltativo di registrazione di un'applicazione client nativi.
2. In Visual Studio o Xamarin Studio, aprire il progetto e aggiungere un riferimento alla `Microsoft.IdentityModel.CLients.ActiveDirectory` pacchetto NuGet. Durante la ricerca, includere versioni non definitive.
3. Aggiungere il codice riportato di seguito per l'applicazione, secondo la piattaforma in uso. In ognuna, effettuare sostituzioni seguenti:

    * Sostituire **Qui di autorità di inserimento** con il nome del tenant in cui è stato effettuato il provisioning dell'applicazione. Il formato deve essere https://login.windows.net/contoso.onmicrosoft.com. Questo valore può essere copiato dalla scheda dominio di Azure Active Directory nel [portale classica Azure].
    * Sostituire **Inserisci-risorsa-ID-qui** con l'ID client per il back-end app per dispositivi mobili. È possibile ottenere l'ID client dalla scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.
    * Sostituire **Inserisci-CLIENT-ID-qui** con l'ID client sono stati copiati dall'applicazione client nativi.
    * Sostituire **Inserisci-reindirizzamento-URI qui** con endpoint _/.auth/login/done_ del sito, utilizzando lo schema HTTPS. Questo valore deve essere simile a _https://contoso.azurewebsites.net/.auth/login/done_.

    Il codice necessario per ogni piattaforma seguente:

    **Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>Single Sign-On utilizzando un token da Facebook o Google

È possibile utilizzare il flusso relativo al client come illustrato in questo frammento di codice per Facebook o Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>Single sign-in con Account Microsoft Live SDK

Per eseguire l'autenticazione degli utenti, è necessario registrare l'app all'account di Microsoft Centro per sviluppatori. Configurare i dettagli della registrazione il back-end App Mobile. Per creare una registrazione dell'account Microsoft e connetterlo al back-end App Mobile, completare i passaggi descritti in [eseguire la registrazione l'app per l'utilizzo di un account di accesso di Microsoft]. Se si dispongono di Windows Store e Windows Phone 8/Silverlight versioni l'app, registrare la versione di Windows Store.

Il codice seguente viene autenticato tramite Live SDK e utilizza il token restituito effettuare l'accesso per il back-end App Mobile.

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Per ulteriori informazioni, vedere la documentazione di [Windows Live SDK] .

###<a name="serverflow"></a>Autenticazione server gestito

Dopo aver registrato il provider di identità, chiamare il metodo [LoginAsync] in [MobileServiceClient] con il valore di [MobileServiceAuthenticationProvider] del provider. Ad esempio, il codice seguente avvia un accesso aggiuntivo del flusso di server con Facebook.

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Se si utilizza un provider di identità diverso da Facebook, impostare il valore di [MobileServiceAuthenticationProvider] al valore relativo al proprio provider.

In un flusso di server, servizio App Azure gestisce il flusso di autenticazione OAuth visualizzando la pagina di accesso del provider selezionato.  Dopo la restituisce provider di identità, servizio App Azure genera un token di autenticazione del servizio di App. Il metodo [LoginAsync] restituisce un [MobileServiceUser], che include l' [ID utente] dell'utente autenticato sia [MobileServiceAuthenticationToken], come token web JSON (JWT). Questo token sono memorizzati nella cache e riutilizzato fino alla scadenza. Per ulteriori informazioni, vedere [memorizzazione nella cache del token di autenticazione](#caching).

###<a name="caching"></a>La memorizzazione nella cache del token di autenticazione

In alcuni casi, è possibile evitare la chiamata al metodo login dopo l'autenticazione prima archiviando il token di autenticazione dal provider.  App di Windows Store e UWP è possibile utilizzare [PasswordVault] per memorizzare nella cache del token di autenticazione corrente dopo una corretta accesso, come indicato di seguito:

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

Il valore di ID utente viene memorizzato il nome utente delle credenziali e il token è archiviato come la Password. Nella successive imprese, è possibile verificare **PasswordVault** le credenziali memorizzate nella cache. Nell'esempio seguente usa le credenziali memorizzate nella cache quando vengono trovati e in caso contrario tenta di eseguire l'autenticazione nuovamente con back-end:

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Quando ci si disconnette da un utente, è necessario rimuovere anche le credenziali archiviate come indicato di seguito:

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

App Xamarin utilizzare [Xamarin.Auth] API per l'archiviazione protetta credenziali in un oggetto **Account** . Per un esempio dell'uso di queste API, vedere il file di codice [AuthStore.cs] della [foto ContosoMoments la condivisione di esempio].

Quando si utilizza l'autenticazione client gestite, è anche possibile memorizzare il token di accesso ottenuto dal provider, ad esempio Facebook o Twitter. Questo token può essere fornito per richiedere un nuovo token di autenticazione back-end, come indicato di seguito:

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>Notifiche push

Negli argomenti seguenti sono illustrate le notifiche Push:

* [Eseguire la registrazione per le notifiche Push](#register-for-push)
* [Ottenere un pacchetto di Windows Store SID](#package-sid)
* [Eseguire la registrazione con i modelli multipiattaforma](#register-xplat)

###<a name="register-for-push"></a>Procedura: eseguire la registrazione per le notifiche Push

Il client App Mobile consente di eseguire la registrazione per le notifiche push con gli hub di notifica di Azure. Quando la registrazione, è possibile ottenere un handle che si ottiene da specifici della piattaforma Push Notification Service (PNS). È anche possibile fornire questo valore insieme a eventuali tag quando si crea la registrazione. Il codice seguente registra l'app di Windows per le notifiche push con il servizio di notifica di Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Se sta conducendo a WNS, è necessario [ottenere un pacchetto di Windows Store SID](#package-sid).  Per ulteriori informazioni sulle applicazioni di Windows, ad esempio come eseguire la registrazione per le registrazioni di modello, vedere [aggiungere le notifiche push per l'app].

Non è possibile che richiede tag dal client.  Le richieste di tag vengono automaticamente eliminate dalla registrazione.
Se si desidera registrare il dispositivo con contrassegni, creare un'API personalizzata che utilizza l'API di hub di notifica per eseguire la registrazione per conto dell'utente.  [Chiamare l'API personalizzata](#customapi) anziché il `RegisterNativeAsync()` metodo.

###<a name="package-sid"></a>Procedura: ottenere un pacchetto di Windows Store SID

Un pacchetto SID è necessarie per l'attivazione delle notifiche push nelle applicazioni di Windows Store.  Per ricevere un pacchetto SID, registrare l'applicazione di Windows Store.

Per ottenere il valore seguente:

1. In Esplora soluzioni Visual Studio, fare clic sul progetto app di Windows Store, fare clic su **Store** > **Associare App con l'archivio...**.
2. Nella procedura guidata, fare clic su **Avanti**, accedere con l'account Microsoft, digitare un nome per l'app riserva **un nuovo nome app**, quindi fare clic su **riserva**.
3. Dopo la registrazione di app è stata creata, selezionare il nome dell'app, fare clic su **Avanti**e quindi fare clic su **Associa**.
4. Accedere a [Centro per sviluppatori di Windows] con l'Account Microsoft. In **App personali**fare clic su registrazione app che è stato creato.
5. Fare clic su **Gestione App** > **identità App**e quindi scorrere l'elenco verso il basso per individuare il **Pacchetto SID**.

Molti scopi del pacchetto SID viene considerano un URI, nel qual caso è necessario utilizzare _ms-app: / /_ come schema. Prendere nota della versione del pacchetto SID concatenando questo valore come prefisso.

App Xamarin Richiedi codice aggiuntivo per poter eseguire la registrazione un'app in esecuzione sulle piattaforme Android o iOS. Per ulteriori informazioni, vedere l'argomento per la piattaforma in uso:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Procedura: modelli push Register per inviare notifiche multipiattaforma

Per registrare i modelli, utilizzare la `RegisterAsync()` metodo con i modelli, come descritto di seguito:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

I modelli dovrebbero essere `JObject` tipi e può contenere più modelli di formato JSON seguente:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

Il metodo **RegisterAsync()** accetta anche i riquadri secondario:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Tutti i contrassegni vengono eliminati durante la registrazione per la protezione. Per aggiungere tag a installazioni o modelli all'interno di installazioni, vedere [lavorare con il server di back-end .NET SDK per App Mobile Azure].

Per inviare notifiche utilizzando questi modelli registrati, fare riferimento alle [API hub di notifica].

##<a name="misc"></a>Argomenti vari

###<a name="errors"></a>Procedura: gestione degli errori

Quando si verifica un errore back-end, il client SDK genera un `MobileServiceInvalidOperationException`.  Nell'esempio seguente viene illustrato come gestire un'eccezione restituito da back-end:

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

Un altro esempio di gestione delle condizioni di errore sono disponibili nel [Mobile App file di esempio]. Nell'esempio [LoggingHandler] fornisce un gestore di delegato di registrazione (seguente) per registrare le richieste di back-end.

###<a name="headers"></a>Procedura: personalizzare le intestazioni di richiesta

Per supportare proprio scenario app specifica, potrebbe essere necessario personalizzare la comunicazione con back-end App Mobile. Ad esempio, si desidera aggiungere un'intestazione personalizzata a ogni richiesta in uscita o modificare anche i codici di stato delle risposte. È possibile utilizzare una personalizzata [DelegatingHandler], come illustrato nell'esempio seguente:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Aggiungere l'autenticazione applicazione in uso.]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronizzazione dei dati non in linea nell'App per dispositivi mobili Azure]: app-service-mobile-offline-data-sync.md
[Aggiungere le notifiche push applicazione in uso.]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registrare l'app per l'utilizzo di un account di accesso di Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Come configurare il servizio di App per le credenziali Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[Crea un riferimento a una tabella senza]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Scrivi]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Selezionare]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Ignora]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[ID utente]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Dove]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Portale di Azure]: https://portal.azure.com/
[Portale classica Azure]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centro per sviluppatori di Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API di hub di notifica]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Esempio di file di App per dispositivi mobili]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 documentazione]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[Esempio di condivisione foto ContosoMoments]: https://github.com/azure-appservice-samples/ContosoMoments