<properties 
    pageTitle="Come utilizzare la Cache di Azure Redis | Microsoft Azure" 
    description="Informazioni su come migliorare le prestazioni delle applicazioni Azure con Azure Redis Cache" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>Come usare Redis Azure Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Questa guida viene illustrato come iniziare a utilizzare **Azure Redis Cache**. Microsoft Azure Redis Cache è in base al popolari aprire origine Redis Cache. Consente l'accesso a una cache Redis sicura e dedicata, gestita da Microsoft. Una cache creata con Azure Redis Cache è possibile accedere da qualsiasi applicazione all'interno di Microsoft Azure.

Microsoft Azure Redis Cache è disponibile in livelli seguenti:

-   **Base** – singolo nodo. Più dimensioni fino a 53 GB.
-   **Standard** : due nodo principale/duplicata. Più dimensioni fino a 53 GB. 99,9 CONTRATTO DI SERVIZIO %.
-   **Premium** : due nodo principale/duplicata con shards fino a 10. Più dimensioni da 6 GB a 530 GB (contatta Microsoft per altre informazioni). Tutte le funzionalità di livello Standard e maggior supporto incluso per [Redis cluster](cache-how-to-premium-clustering.md) [Redis persistenza](cache-how-to-premium-persistence.md)e [Virtuali Azure](cache-how-to-premium-vnet.md). 99,9 CONTRATTO DI SERVIZIO %.

Ogni livello diverso in termini di caratteristiche e prezzi. Per informazioni sui prezzi, vedere [Dettagli prezzi Cache][].

Questa guida viene illustrato come utilizzare il client [StackExchange.Redis][] utilizzando C\# codice. Gli scenari coperti includono **creazione e configurazione di una cache**, **configurazione di client cache**e **aggiunta e rimozione di oggetti dalla cache**. Per ulteriori informazioni sull'uso di Azure Redis Cache, fare riferimento alla sezione [Passaggi successivi][] . Per un'esercitazione dettagliata della creazione di un MVC ASP.NET web app con Redis Cache, vedere [come creare un'App Web con Redis Cache](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>Guida introduttiva di Azure Redis Cache

Guida introduttiva di Azure Redis Cache è un'operazione semplice. Per iniziare, eseguire il provisioning e configurare una cache. Punto, configurare i client di cache in modo che possa accedere la cache. Una volta configurati i client di cache, è possibile iniziare a lavorare con loro.

-   [Creare la cache][]
-   [Configurare i client di cache][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>Creare una cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>Per accedere la cache dopo tale voce viene creato

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Per ulteriori informazioni sulla configurazione della cache, vedere [come configurare Azure Redis Cache](cache-configure.md).

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>Configurare i client di cache

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Dopo aver configurato il progetto di client per la memorizzazione nella cache, è possibile utilizzare le tecniche descritte nelle sezioni seguenti per l'utilizzo con la cache.

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>Utilizzo di cache

La procedura descritta in questa sezione viene descritto come eseguire attività comuni con Cache.

-   [Connettersi alla cache][]
-   [Aggiungere e recuperare gli oggetti dalla cache][]
-   [Utilizzare gli oggetti .NET nella cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>Connettersi alla cache

Per lavorare a livello di programmazione con una cache, è necessario un riferimento alla cache. Aggiungere quanto segue nella parte superiore di qualsiasi file dal quale si desidera utilizzare il client StackExchange.Redis per accedere a una Cache Redis Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] Il client StackExchange.Redis richiede .NET Framework 4 o versioni successive.

La connessione alla Cache Redis Azure viene gestita tramite il `ConnectionMultiplexer` classe. Questa classe è progettata per essere condivisi e riutilizzati in tutta l'applicazione client e non è necessario creare una scala cronologica per operazione. 

Per connettersi a una Cache Redis Azure e da restituire un'istanza di un connesso `ConnectionMultiplexer`, chiamare statica `Connect` metodo e passare il punto finale della cache e la chiave come nell'esempio seguente. Utilizzare la chiave generata dal portale di Azure come il parametro password.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Avviso: Mai archivio credenziali nel codice sorgente. Per rendere più semplice in questo esempio, sta visualizzandoli nel codice sorgente. Per informazioni su come archiviare le credenziali, vedere [come le stringhe dell'applicazione e lavoro stringhe di connessione][] .

Se non si desidera utilizzare SSL, impostare `ssl=false` o omettere il `ssl` parametro.

>[AZURE.NOTE] La porta SSL non è disattivata per impostazione predefinita per nuove cache. Per istruzioni su come abilitare la porta non SSL, vedere [Accesso a porte](cache-configure.md#access-ports)..

Un approccio per la condivisione di un `ConnectionMultiplexer` istanza dell'applicazione deve essere una proprietà statica che restituisce un'istanza connessa, simile al seguente. In questo modo affidabile inizializzare una sola connessa `ConnectionMultiplexer` istanza. In questi esempi `abortConnect` è impostato su false, che indica che la chiamata avrà esito positivo anche se non è possibile stabilire una connessione alla Cache Redis Azure. Una caratteristica di `ConnectionMultiplexer` che automaticamente verrà ripristinare la connessione alla cache una volta il problema di rete o altre cause sono stati risolti.

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

Per ulteriori informazioni sulle opzioni di configurazione di connessione avanzate, vedere [il modello di configurazione StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Dopo la connessione, restituire un riferimento al database cache redis chiamando il `ConnectionMultiplexer.GetDatabase` metodo. L'oggetto restituito dalla `GetDatabase` metodo è un oggetto pass-through semplice e non è necessario essere archiviati.

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

Dopo aver scoperto come connettersi a un'istanza di Azure Redis Cache e restituire un riferimento al database di cache, esaminiamo un uso con la cache.

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>Aggiungere e recuperare gli oggetti dalla cache

Gli elementi possono essere archiviati in e recuperati dalla cache utilizzando il `StringSet` e `StringGet` metodi.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis archivia la maggior parte dei dati come stringhe Redis, ma queste stringhe possono contenere molti tipi di dati, inclusi i dati binari serializzati, che possono essere utilizzati quando l'archiviazione .NET oggetti nella cache.

Quando si chiama `StringGet`, se è presente l'oggetto, viene restituito, e in caso contrario, `null` viene restituito. In questo caso è possibile recuperare il valore dell'origine dati desiderato e memorizzare nella cache per l'uso successivo. Tale valore è noto come criterio di riposo cache.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Per specificare la scadenza di un elemento nella cache, utilizzare la `TimeSpan` parametro di `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>Utilizzare gli oggetti .NET nella cache

Azure Cache Redis possibile memorizzare nella cache oggetti .NET, nonché i tipi di dati di base, ma prima di un oggetto .NET può essere memorizzati nella cache deve essere serializzato. È responsabilità dello sviluppatore di applicazioni e flessibilità per sviluppatori per la scelta del serializzatore.

Un modo semplice per serializzare oggetti consiste nell'utilizzare la `JsonConvert` metodi di serializzazione in [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) e serializzare da e verso JSON. Nell'esempio seguente mostra un get e set utilizzando un `Employee` istanza di oggetto.


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base, seguire questi collegamenti per altre informazioni su Azure Redis Cache.

-   Controllare i provider di servizi di ASP.NET per Azure Redis Cache.
    -   [Lo stato della sessione di Azure Redis Provider](cache-aspnet-session-state-provider.md)
    -   [Azure Redis Provider della Cache di Output ASP.NET Cache](cache-aspnet-output-cache-provider.md)
-   [Attivare la diagnostica cache](cache-how-to-monitor.md#enable-cache-diagnostics) in modo da poter [monitorare](cache-how-to-monitor.md) l'integrità della cache. È possibile visualizzare le metriche nel portale di Azure ed è inoltre possibile [scaricare e rivedere](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) mediante gli strumenti di propria scelta.
-   Consultare la [documentazione relativa al client cache StackExchange.Redis][].
    -   Azure Cache Redis accessibili da molte Redis client e lingue di sviluppo. Per ulteriori informazioni, vedere [http://redis.io/clients][].
-   Azure Cache Redis è utilizzabile anche con servizi di terze parti e strumenti, ad esempio Redsmin e Redis Desktop Manager.
    -   Per ulteriori informazioni su Redsmin, vedere [come recuperare una stringa di connessione Redis Azure e usarlo con Redsmin][].
    -   Accedere ed esaminare i dati nella Cache Redis Azure con un'interfaccia grafica utilizzando [RedisDesktopManager](https://github.com/uglide/RedisDesktopManager).
-   Vedere la documentazione [redis][] e informazioni su [redis tipi di dati][] e [un'introduzione quindici minuti per Redis tipi di dati][].



<!-- INTRA-TOPIC LINKS -->
[Passaggi successivi]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Creare la cache]: #create-cache
[Configure the cache]: #enable-caching
[Configurare i client di cache]: #NuGet
[Working with Caches]: #working-with-caches
[Connettersi alla cache]: #connect-to-cache
[Aggiungere e recuperare gli oggetti dalla cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.IO/Clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Come recuperare una stringa di connessione Redis Azure e usarlo con Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modello di configurazione StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Ulteriori informazioni sui prezzi cache]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentazione del client di cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Redis tipi di dati]: http://redis.io/topics/data-types
[un'introduzione quindici minuti al tipo di dati Redis]: http://redis.io/topics/data-types-intro

[Come funzionano le stringhe dell'applicazione e le stringhe di connessione]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


