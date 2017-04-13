<properties 
    pageTitle="Come creare un'App Web con Cache Redis | Microsoft Azure" 
    description="Informazioni su come creare un'App Web con Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/11/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-a-web-app-with-redis-cache"></a>Come creare un'App Web con Redis Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

In questa esercitazione viene illustrato come creare e distribuire un'applicazione web ASP.NET per un'app web nel servizio App Azure utilizzando Visual Studio 2015. L'applicazione di esempio viene visualizzato un elenco delle statistiche del team da un database e mostra diversi modi per usare Azure Redis Cache per archiviare e recuperare i dati nella cache. Al termine dell'esercitazione sarà necessario un'app web in esecuzione la lettura e scrittura a un database, ottimizzata con Azure Redis Cache e ospitato in Azure.

Si apprenderanno:

-   Informazioni su come creare un'applicazione web ASP.NET MVC 5 in Visual Studio.
-   Come accedere ai dati da un database utilizzando il Framework entità.
-   Come migliorare la trasmissione dei dati e ridurre il caricamento database archiviando e il recupero dei dati utilizzando la Cache Redis Azure.
-   Come usare un Redis ordinati set per recuperare i 5 team superiore.
-   Informazioni su come effettuare il provisioning di Azure risorse per l'applicazione utilizzando un modello di Manager delle risorse.
-   Come pubblicare l'applicazione in Azure utilizzando Visual Studio.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario disporre i prerequisiti seguenti.

-   [Account Azure](#azure-account)
-   [Visual Studio 2015 con Azure SDK per .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Account Azure

È necessario un account Azure per completare l'esercitazione. Si può:

* [Aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=redis_cache_hero). È possibile ottenere crediti che possono essere utilizzati per provare a utilizzare i servizi di Azure a pagamento. Anche dopo il crediti vengono utilizzati, è possibile mantenere l'account e usare le caratteristiche e servizi Azure gratuiti.
* [Vantaggi dell'abbonato attivare Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). L'abbonamento MSDN offre crediti ogni mese che è possibile utilizzare per i servizi di Azure a pagamento.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>Visual Studio 2015 con Azure SDK per .NET

L'esercitazione è scritto per Visual Studio 2015 con [Azure SDK per .NET](../dotnet-sdk.md) 2.8.2 o versione successiva. [Scaricare la versione più recente di Azure SDK Visual Studio, 2015 qui](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio viene installato automaticamente con SDK se non si dispone già.

Se è installato Visual Studio 2013, è possibile [scaricare il più recente di Azure SDK Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Alcune schermate potrebbero apparire diversi da quelli illustrati visualizzate in questa esercitazione.

>[AZURE.NOTE] A seconda di quante delle dipendenze SDK già presenti nel computer in uso, installare il SDK potrebbe richiedere molto tempo, da alcuni minuti a mezz'ora o più.

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

1. Aprire Visual Studio e fare clic su **File**, **Nuovo** **progetto**.

2. Espandere il nodo **c#** nell'elenco **modelli** , selezionare **Cloud**e fare clic su **Applicazione Web ASP.NET**. Verificare che sia selezionato **.NET Framework 4.5.2** .  Digitare **ContosoTeamStats** nella casella di testo **nome** e fare clic su **OK**.
 
    ![Creazione di progetto][cache-create-project]

3. Selezionare **MVC** come tipo di progetto. Deselezionare la casella di controllo **Host nel cloud** . In questa esercitazione, sarà necessario [eseguire il provisioning risorse Azure](#provision-the-azure-resources) e [pubblicare l'applicazione in Azure](#publish-the-application-to-azure) nei passaggi successivi. Per un esempio di provisioning un'app web App servizio da Visual Studio lasciando **Host nel cloud** selezionata, vedere [Introduzione a Web Apps nel servizio App Azure utilizzando ASP.NET e Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![Selezionare modello di progetto][cache-select-template]

4. Fare clic su **OK** per creare il progetto.

## <a name="create-the-aspnet-mvc-application"></a>Creare l'applicazione Asp.net

In questa sezione dell'esercitazione, sarà necessario creare l'applicazione di base che legge e visualizza le statistiche del team da un database.

-   [Aggiungere il modello](#add-the-model)
-   [Aggiungere il controller](#add-the-controller)
-   [Configurare le visualizzazioni](#configure-the-views)

### <a name="add-the-model"></a>Aggiungere il modello

1. Rapida dei **modelli** in **Esplora soluzioni**e scegliere **Aggiungi**, **classe**. 

    ![Aggiungi modello][cache-model-add-class]

2. Immettere `Team` per la classe assegnare un nome e fare clic su **Aggiungi**.

    ![Aggiungi modello classe][cache-model-add-class-dialog]

3. Sostituire il `using` istruzioni nella parte superiore di `Team.cs` file con le operazioni seguenti utilizzando le istruzioni.


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. Sostituire la definizione della `Team` classe con il frammento di codice seguente che contiene una versione aggiornata `Team` classe definizione, nonché altre classi di supporto Framework entità. Per ulteriori informazioni sull'approccio prima del codice a Framework entità utilizzate in questa esercitazione, vedere [codice prima di tutto in un nuovo database](https://msdn.microsoft.com/data/jj193542).


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. In **Esplora soluzioni**fare doppio clic su **config** per aprirlo.

    ![Config][cache-web-config]

3.  Aggiungere la seguente stringa di connessione per la `connectionStrings` sezione. Il nome della stringa di connessione deve corrispondere al nome della classe del contesto database entità Framework ossia `TeamContext`.

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    Dopo aver aggiunto la verifica, la `connectionStrings` sezione avrà un aspetto simile al seguente.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>Aggiungere il controller

1. Premere **F6** per compilare il progetto. 
2. In **Esplora soluzioni**fare clic sulla cartella **controller** e scegliere **Aggiungi**, **Controller**.

    ![Aggiungere controller][cache-add-controller]

3. Scegliere **Controller di 5 MVC con visualizzazioni, utilizzando il Framework entità**e fare clic su **Aggiungi**. Se viene visualizzato un messaggio di errore dopo facendo clic su **Aggiungi**, assicurarsi che siano compilate prima di tutto il progetto.

    ![Aggiungere controller classe][cache-add-controller-class]

5. Selezionare **Team (ContosoTeamStats.Models)** dall'elenco a discesa **classe modello** . Selezionare **TeamContext (ContosoTeamStats.Models)** dall'elenco a discesa **classe contesto dati** . Tipo di `TeamsController` nella casella nome **Controller** di testo (se non è automaticamente popolato). Fare clic su **Aggiungi** per creare la classe controller e aggiungere le visualizzazioni predefinite.

    ![Configurare controller][cache-configure-controller]

4. In **Esplora soluzioni**espandere **asax** e fare doppio clic su **Global.asax.cs** per aprirlo.

    ![Global.asax.cs][cache-global-asax]

5. Aggiungere i due seguenti utilizzando le istruzioni nella parte superiore del file sotto l'altra con le istruzioni.


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. Aggiungere la riga di codice seguente alla fine della `Application_Start` metodo.


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. In **Esplora soluzioni**, espandere `App_Start` e fare doppio clic su `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Sostituire `controller = "Home"` nel codice seguente nel `RegisterRoutes` metodo con `controller = "Teams"` come illustrato nell'esempio seguente.


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>Configurare le visualizzazioni

1. In **Esplora soluzioni**, espandere la cartella di **visualizzazioni** , quindi la cartella **condivisa** e fare doppio clic su **cshtml**. 

    ![Cshtml][cache-layout-cshtml]

2. Modificare il contenuto del `title` elemento e sostituisci `My ASP.NET Application` con `Contoso Team Stats` come illustrato nell'esempio seguente.


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. Nel `body` delle sezioni, aggiornare il primo `Html.ActionLink` istruzione e sostituisci `Application name` con `Contoso Team Stats` e sostituire `Home` con `Teams`.
    -   Prima di:`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   Dopo:`@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Modifiche al codice][cache-layout-cshtml-code]

4. Premere **Ctrl + F5** per generare ed eseguire l'applicazione. Questa versione dell'applicazione legge i risultati direttamente dal database. Nota le azioni **Crea nuovo**, **Modifica**, **Dettagli**ed **eliminare** automaticamente aggiunte all'applicazione da scaffold **MVC 5 Controller con visualizzazioni, utilizzando il Framework entità** . Nella sezione successiva dell'esercitazione si aggiungeranno Redis Cache per ottimizzare l'accesso ai dati e fornire funzionalità aggiuntive per l'applicazione.

![Applicazione iniziale][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurare l'applicazione di utilizzare Redis Cache

In questa sezione dell'esercitazione, è necessario configurare l'applicazione di esempio per archiviare e recuperare le statistiche del team Contoso da un'istanza di Azure Redis Cache utilizzando il client di cache [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

-   [Configurare un'applicazione di utilizzare StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [Aggiornare la classe TeamsController affinché restituisca risultati nella cache oppure il database](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [Aggiornare i metodi di creazione, modifica ed eliminazione per l'uso con la cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [Aggiornare la visualizzazione di indice di team per lavorare con la cache](#update-the-teams-index-view-to-work-with-the-cache)


### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurare un'applicazione di utilizzare StackExchange.Redis

1. Per configurare un'applicazione client mediante il pacchetto StackExchange.Redis NuGet Visual Studio, fare clic sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. 

    ![Gestire pacchetti NuGet][redis-cache-manage-nuget-menu]

2. Digitare **StackExchange.Redis** nella casella di testo di ricerca, selezionare la versione desiderata dalla casella risultati e fare clic su **Installa**.

    ![Pacchetto StackExchange.Redis NuGet][redis-cache-stack-exchange-nuget]

    Pacchetto NuGet Scarica e aggiunge i riferimenti di assembly richiesto per l'applicazione client accedere Azure Redis Cache con il client di cache StackExchange.Redis. Se si preferisce utilizzare una versione sicuro della libreria client **StackExchange.Redis** , scegliere **StackExchange.Redis.StrongName**; in caso contrario scegliere **StackExchange.Redis**.

3. In **Esplora soluzioni**, espandere la cartella **controller** e fare doppio clic su **TeamsController.cs** per aprirlo.

    ![Controller di team][cache-teamscontroller]

4. Aggiungere le due istruzioni **TeamsController.cs**using.

        using System.Configuration;
        using StackExchange.Redis;

5. Aggiungere le due seguenti proprietà per il `TeamsController` classe.

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. Creare un file nel computer denominato `WebAppPlusCacheAppSecrets.config` e inserirla in una posizione in cui non è selezionata con il codice sorgente dell'applicazione di esempio, se si decide di archiviarlo in una posizione. In questo esempio la `AppSettingsSecrets.config` file si trova in `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Modificare il `WebAppPlusCacheAppSecrets.config` file e aggiungere il contenuto seguente. Se si esegue l'applicazione localmente queste informazioni vengono usate per connettersi all'istanza di Azure Redis Cache. In un secondo momento nell'esercitazione verranno effettuare il provisioning di un'istanza di Azure Redis Cache e aggiornare il nome della cache e la password. Se non si prevede di eseguire l'applicazione di esempio in locale è possibile ignorare la creazione di file e i passaggi successivi che fanno riferimento a file, perché quando si distribuisce Azure l'applicazione recupera le informazioni di connessione della cache dall'impostazione di app per l'applicazione Web e non dal file al momento. Poiché il `WebAppPlusCacheAppSecrets.config` non è stata distribuita in Azure con l'applicazione, non è necessaria a meno che non si prevede di eseguire l'applicazione in locale.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. In **Esplora soluzioni**fare doppio clic su **config** per aprirlo.

    ![Config][cache-web-config]

3. Aggiungere quanto segue `file` dell'attributo per la `appSettings` elemento. Se si utilizza un nome di file diverso o posizione, sostituire i valori per quelli illustrati nell'esempio.
    -   Prima di:`<appSettings>`
    -   Dopo:` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    Il runtime ASP.NET unisce il contenuto del file esterno con il markup nel `<appSettings>` elemento. Il runtime ignora l'attributo file se non è possibile trovare il file specificato. Le informazioni riservate (stringa di connessione per la cache) non sono inclusi come parte del codice sorgente per l'applicazione. Se si distribuisce un'app web di Azure, il `WebAppPlusCacheAppSecrests.config` file non distribuito, ovvero quello desiderato. Esistono diversi modi per specificare le informazioni riservate in Azure e in questa esercitazione vengono configurate automaticamente automaticamente quando si esegue il [provisioning di risorse Azure](#provision-the-azure-resources) in un'esercitazione successiva. Per ulteriori informazioni sull'uso di informazioni riservate in Azure, vedere [procedure consigliate per la distribuzione password e altri dati riservati a ASP.NET e servizio App Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Aggiornare la classe TeamsController affinché restituisca risultati nella cache oppure il database

In questo esempio, le statistiche del team possono recuperate dal database o dalla cache. Statistiche del team vengono memorizzate nella cache come un serializzato `List<Team>`e anche come un set ordinato usano tipi di dati Redis. Quando si recuperano gli elementi di un set ordinato, è possibile recuperare alcuni, tutti o eseguire una query per alcuni elementi. In questo esempio è necessario eseguire una query set ordinato per il team di 5 superiore in base a numero di wins.

>[AZURE.NOTE] Non è necessario archiviare le statistiche del team in più formati nella cache per poter utilizzare Azure Redis Cache. In questa esercitazione utilizzata più formati per illustrare le varie opzioni diversi tipi di dati che è possibile utilizzare per i dati nella cache.



1. Aggiungere quanto segue utilizzando le istruzioni per la `TeamsController.cs` file nella parte superiore con l'altro con le istruzioni.

        using System.Diagnostics;
        using Newtonsoft.Json;

2. Sostituire corrente `public ActionResult Index()` metodo con l'implementazione di seguito.


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. Aggiungere i tre metodi seguenti per la `TeamsController` classe per implementare il `playGames`, `clearCache`, e `rebuildDB` tipi di azione estratto parametro aggiunto nel frammento di codice precedente.

    Il `PlayGames` metodo aggiornato le statistiche del team per simulare una campagna di giochi, Salva i risultati al database e consente di cancellare i dati ora non aggiornati dalla cache.


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    Il `RebuildDB` metodo reinizializza il database con il set predefinito di team genera statistiche relative e consente di cancellare i dati ora non aggiornati dalla cache.
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    Il `ClearCachedTeams` metodo rimuove tutte le statistiche del team memorizzati nella cache dalla cache.

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. Aggiungere i quattro metodi seguenti per la `TeamsController` classe per implementare le diverse modalità per recuperare le statistiche del team da cache e il database. Ognuno di questi metodi restituisce un `List<Team>` che viene visualizzato per la visualizzazione.

    Il `GetFromDB` metodo consente di leggere le statistiche del team dal database.

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    Il `GetFromList` metodo consente di leggere le statistiche del team dalla cache come un serializzato `List<Team>`. Se c'è accesso alla cache, le statistiche del team vengono letto dal database e quindi memorizzate nella cache per la volta successiva. In questo esempio viene utilizzata la serializzazione JSON.NET serializzare gli oggetti .NET da e verso la cache. Per ulteriori informazioni, vedere [come utilizzare gli oggetti .NET nella Cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    Il `GetFromSortedSet` metodo consente di leggere le statistiche del team di un set ordinato memorizzati nella cache. Se c'è accesso alla cache, le statistiche del team vengono letto dal database e memorizzate nella cache come un set ordinato.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    Il `GetFromSortedSetTop5` metodo legge i team le prime 5 del set di ordinato memorizzati nella cache. Viene avviato selezionando la cache per l'esistenza del `teamsSortedSet` chiave. Se questo tasto non è presenta, il `GetFromSortedSet` metodo per leggere le statistiche del team e archiviarli nella cache. Successivo set di ordinato memorizzati nella cache viene eseguita una query per il team di 5 superiore che vengono restituiti.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Aggiornare i metodi di creazione, modifica ed eliminazione per l'uso con la cache

Il codice di supporto temporaneo generati come parte di questo esempio include metodi per aggiungere, modificare ed eliminare gruppi. Ogni volta che un team viene aggiunto, modificato o rimosso, i dati nella cache diventano obsoleti. In questa sezione è necessario modificare questi tre metodi per cancellare i team memorizzati nella cache in modo che la cache non è sincronizzata con il database.

1. Individuare il `Create(Team team)` metodo la `TeamsController` classe. Aggiungere una chiamata per il `ClearCachedTeams` metodo, come illustrato nell'esempio seguente.


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. Individuare il `Edit(Team team)` metodo la `TeamsController` classe. Aggiungere una chiamata per il `ClearCachedTeams` metodo, come illustrato nell'esempio seguente.


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. Individuare il `DeleteConfirmed(int id)` metodo la `TeamsController` classe. Aggiungere una chiamata per il `ClearCachedTeams` metodo, come illustrato nell'esempio seguente.


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Aggiornare la visualizzazione di indice di team per lavorare con la cache

1. In **Esplora soluzioni**fare doppio clic su **Index.cshtml**espandere la cartella di **visualizzazioni** , quindi la cartella di **Team** .

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Nella parte superiore del file, cercare l'elemento paragrafo seguente.

    ![Tabella delle azioni][cache-teams-index-table]

    Questo è il collegamento per creare un nuovo team. Sostituire l'elemento paragrafo con la tabella seguente. In questa tabella è disponibili collegamenti di azione per la creazione di un nuovo team, la riproduzione di un nuovo season giochi, cancellazione della cache, recupero i team dalla cache in diversi formati disponibili, il recupero i team dal database e la ricostruzione del database con dati di esempio aggiornato.


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. Scorrere fino alla fine del file **Index.cshtml** e aggiungere le seguenti `tr` elemento in modo che sia l'ultima riga della tabella ultimo nel file.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    La riga corrente viene visualizzato il valore di `ViewBag.Msg` che contiene un rapporto di stato sull'operazione corrente viene impostata quando si fa clic su uno dei collegamenti azione ottenuto nel passaggio precedente.   

    ![Messaggio di stato][cache-status-message]

4. Premere **F6** per compilare il progetto.

## <a name="provision-the-azure-resources"></a>Effettuare il provisioning di risorse Azure

Per ospitare l'applicazione in Azure, è necessario prima di tutto i provisioning Azure dei servizi che richiede l'applicazione. Applicazione di esempio in questa esercitazione utilizza i seguenti servizi Azure.

-   Cache Redis Azure
-   Servizio di App Web App
-   Database SQL

Per distribuire questi servizi a un gruppo di risorse nuovo o esistente di propria scelta, fare clic sul pulsante **Distribuisci in Azure** seguente.

[! [Distribuire Azure] [deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Questo pulsante **Distribuisci in Azure** utilizza il modello di [Guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates) [Crea un'App Web più Redis Cache più Database SQL di](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) effettuare il provisioning di questi servizi e impostare la stringa di connessione per il Database di SQL e impostazione dell'applicazione per la stringa di connessione di Azure Redis Cache.

>[AZURE.NOTE] Se non si dispone di un account Azure, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) in pochi minuti.

Fare clic sul pulsante **Distribuisci in Azure** consente di accedere al portale di Azure e avvia il processo di creazione di risorse descritte dal modello.

![Distribuire Azure][cache-deploy-to-azure-step-1]

1. Scegliere e il **distribuzione personalizzata** , selezionare l'abbonamento Azure per l'utilizzo e selezionare un gruppo di risorse esistente o crearne uno nuovo e specificare il percorso di gruppo di risorse.
2. In e il **parametri** , specificare un nome di account di amministratore (**ADMINISTRATORLOGIN** - non utilizzare **amministrazione**), la password di accesso di amministratore (**ADMINISTRATORLOGINPASSWORD**) e il nome del database (**DATABASENAME**). Gli altri parametri sono configurati per un servizio gratuito di App hosting piano e opzioni di costo inferiore per Database SQL di Azure Redis Cache, che non è dotato di un livello gratuito.
3. Modificare le impostazioni se lo si desidera, o mantenere le impostazioni predefinite e fare clic su **OK**.


![Distribuire Azure][cache-deploy-to-azure-step-2]

1. Fare clic su **note legali revisione**.
2. Leggere le condizioni in e il **fornitore** e fare clic su **Acquista**.
3. Per iniziare il provisioning di risorse, fare clic su **Crea** in e il **distribuzione personalizzata** .

Per visualizzare l'avanzamento della distribuzione, fare clic sull'icona di notifica e fare clic su **distribuzione avviato**.

![Distribuzione avviata][cache-deployment-started]

È possibile visualizzare lo stato della distribuzione in e il **Microsoft.Template** .

![Distribuire Azure][cache-deploy-to-azure-step-3]

Al termine di provisioning, è possibile pubblicare l'applicazione in Azure mediante Visual Studio.

>[AZURE.NOTE] Eventuali errori che possono verificarsi durante il processo di provisioning vengono visualizzati in e il **Microsoft.Template** . Errori comuni sono troppi SQL Server o troppi gratuito App hosting di servizi piani per abbonamento. Risolvere gli eventuali errori e riavviare il processo facendo clic su **ridistribuire** e il **Microsoft.Template** o il pulsante **Distribuisci in Azure** in questa esercitazione.

## <a name="publish-the-application-to-azure"></a>Pubblicare un'applicazione di Azure

In questo passaggio dell'esercitazione, è necessario pubblicare l'applicazione in Azure ed eseguire nel cloud.

1. Fare clic sul progetto **ContosoTeamStats** in Visual Studio e scegliere **pubblica**.

    ![Pubblicare][cache-publish-app]

2. Fare clic su **Servizio di Microsoft Azure App**.

    ![Pubblicare][cache-publish-to-app-service]

3. Selezionare l'abbonamento utilizzato quando si creano le risorse Azure, espandere il gruppo di risorse contenente le risorse, selezionare l'App Web desiderato e fare clic su **OK**. Se è stato utilizzato il pulsante **Distribuisci in Azure** il nome dell'App Web inizia con **sito** seguito da alcuni caratteri aggiuntivi.

    ![Selezionare Web App][cache-select-web-app]

4. Fare clic su **Convalida connessione** per verificare le impostazioni e quindi fare clic su **pubblica**.

    ![Pubblicare][cache-publish]

    Dopo alcuni secondi completeranno il processo di pubblicazione e un browser verrà avviato con l'esecuzione di esempio. Se viene visualizzato un errore DNS per la convalida o la pubblicazione e il processo di provisioning per le risorse per l'applicazione di Azure completato solo recentemente, attendere qualche minuto e riprovare.

    ![Cache aggiunta][cache-added-to-application]

Nella tabella seguente descrive ogni collegamento all'azione dall'applicazione di esempio.

| Azione                  | Descrizione                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Crea nuova              | Creare un nuovo Team.                                                                                                                                               |
| Riprodurre Season             | Riprodurre un season di giochi, aggiornare le statistiche del team e deselezionare le non aggiornate dalla cache di team.                                                                          |
| Cancella Cache             | Deselezionare le statistiche del team dalla cache.                                                                                                                             |
| Elenco dalla Cache         | Recuperare le statistiche del team dalla cache. Se esiste un accesso alla cache, caricare le statistiche dal database e salvare nella cache per la volta successiva.                                        |
| Set ordinato dalla Cache   | Recuperare le statistiche del team dalla cache utilizzando un set ordinato. Se c'è accesso alla cache, caricare le statistiche del database e salvare alla cache utilizzando un set ordinato.  |
| Le prime 5 team dalla Cache  | Recuperare i 5 team superiore dalla cache utilizzando un set ordinato. Se c'è accesso alla cache, caricare le statistiche del database e salvare alla cache utilizzando un set ordinato. |
| Caricare dal database            | Recuperare le statistiche del team dal database.                                                                                                                       |
| Rigenerare DB              | Rigenerare il database e ricaricare con dati di esempio del team.                                                                                                        |
| Modificare / dettagli / Elimina | Modificare un team, visualizzare i dettagli di un team, eliminare un team.                                                                                                             |


Fare clic su alcune delle azioni e sperimentare recuperare i dati da origini diverse. Non differenze tra il tempo che necessario per completare diversi modi per recuperare i dati dal database e la cache.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Eliminare le risorse dopo aver terminato con l'applicazione

Dopo aver terminato con l'applicazione dell'esercitazione di esempio, è possibile eliminare le risorse Azure utilizzate per preservare le risorse costi e. Se si usa il pulsante **Distribuisci in Azure** nella sezione [effettuare il provisioning di risorse Azure](#provision-the-azure-resources) e tutte le risorse sono contenute nello stesso gruppo di risorse, è possibile eliminarle insieme in un'unica operazione eliminando il gruppo di risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **gruppi di risorse**.
2. Digitare il nome del gruppo di risorse nella casella di testo **filtrare gli elementi...** .
3. Fare clic su **…** a destra del gruppo di risorse.
4. Fare clic su **Elimina**.

    ![Elimina][cache-delete-resource-group]

5. Digitare il nome del gruppo di risorse e fare clic su **Elimina**.

    ![Conferma eliminazione][cache-delete-confirm]

Dopo alcuni secondi al gruppo di risorse e tutte le relative risorse contenute verranno eliminate.

>[AZURE.IMPORTANT] Si noti che l'eliminazione di un gruppo di risorse è irreversibile e che il gruppo di risorse e tutte le risorse al suo interno vengono eliminate definitivamente. Verificare che si non elimina per sbaglio errato gruppo di risorse o risorse. Se è stato creato le risorse per l'hosting in questo esempio all'interno di un gruppo di risorse esistente, è possibile eliminare singolarmente ogni risorsa dal loro rispettivi pale.

## <a name="run-the-sample-application-on-your-local-machine"></a>Eseguire l'applicazione di esempio nel computer locale

Per eseguire l'applicazione in locale nel computer in uso, è necessario un'istanza di Azure Redis Cache in cui si desidera memorizzare i dati nella cache. 

-   Se è stata pubblicata all'applicazione di Azure come descritto nella sezione precedente, è possibile utilizzare l'istanza di Azure Redis Cache che è stato effettuato il provisioning durante questo passaggio.
-   Se si dispone di un'altra istanza di Azure Redis Cache esistente, è possibile utilizzare che per eseguire l'esempio in locale.
-   Se è necessario creare un'istanza di Azure Redis Cache, è possibile eseguire la procedura descritta in [creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Dopo aver selezionato o creata la cache da utilizzare, individuare la cache nel portale di Azure e recuperare i [tasti di scelta](cache-configure.md#access-keys) per la cache e il [nome host](cache-configure.md#properties) . Per ulteriori informazioni, vedere [configurare Redis le impostazioni della cache](cache-configure.md#configure-redis-cache-settings).

1. Aprire la `WebAppPlusCacheAppSecrets.config` file creati durante il passaggio [Configura l'applicazione di utilizzare Redis Cache](#configure-the-application-to-use-redis-cache) di questa esercitazione utilizzando la finestra di propria scelta.

2. Modificare il `value` dell'attributo e sostituire `MyCache.redis.cache.windows.net` con il [nome host](cache-configure.md#properties) della cache e specificare la [chiave primaria o secondaria](cache-configure.md#access-keys) della cache come la password.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. Premere **Ctrl + F5** per eseguire l'applicazione.

>[AZURE.NOTE] Si noti che poiché l'applicazione, incluso il database è in esecuzione in locale e la Cache Redis è ospitata in Azure, potrebbe essere visualizzata la cache in eseguire il database. Per ottenere prestazioni ottimali, l'applicazione client e istanza della Cache Redis Azure deve essere nella stessa posizione. 

## <a name="next-steps"></a>Passaggi successivi

-   Ulteriori informazioni sulla [Guida introduttiva di ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) nel sito [ASP.NET](http://asp.net/) .
-   Per altri esempi di creazione di un'applicazione Web ASP.NET nel servizio di App, vedere [creare e distribuire un'app web ASP.NET in Azure App servizio](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) da [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connetti [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
    -   Per altre guide rapide dal demo HealthClinic.biz, vedere [Guide rapide strumenti di sviluppo di Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
-   Ulteriori informazioni su come approccio del [codice prima di tutto in un nuovo database](https://msdn.microsoft.com/data/jj193542) a Framework entità utilizzate in questa esercitazione.
-   Ulteriori informazioni sulle [applicazioni web apps in Azure App servizio](../app-service-web/app-service-web-overview.md).
-   Informazioni su come [monitor](cache-how-to-monitor.md) la cache nel portale di Azure.

-   Esplorare caratteristiche premium di Azure Redis Cache
    -   [Come configurare persistenza per una Redis Cache Premium Azure](cache-how-to-premium-persistence.md)
    -   [Come configurare il servizio cluster per una Redis Cache Premium Azure](cache-how-to-premium-clustering.md)
    -   [Come configurare il supporto di rete virtuale per una Redis Cache Premium Azure](cache-how-to-premium-vnet.md)
    -   Vedere le [Domande frequenti Azure di Cache Redis](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) per ulteriori informazioni sulle dimensioni, velocità e la larghezza di banda con premium cache.



<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

