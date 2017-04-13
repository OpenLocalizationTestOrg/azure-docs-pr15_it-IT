<properties 
    pageTitle="Usa libreria client database flessibile con entità Framework | Microsoft Azure" 
    description="Utilizzare la raccolta di client Database flessibile che entità Framework per la codifica di database" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>Raccolta di client Database flessibile con entità Framework 
 
In questo documento Mostra le modifiche in un'applicazione di entità Framework necessari per l'integrazione con gli [Strumenti di Database flessibile](sql-database-elastic-scale-introduction.md). Lo stato attivo è la composizione [condiviso mappare gestione](sql-database-elastic-scale-shard-map-management.md) e [del routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) con il Framework entità **Codice primo** approccio. Esercitazione [Code First – Nuovo Database](http://msdn.microsoft.com/data/jj193542.aspx) per EF viene utilizzato come esempio operativo tutto il documento. Codice di esempio in questo documento fa parte degli strumenti di database flessibile set di campioni negli esempi di codice di Visual Studio.
  
## <a name="downloading-and-running-the-sample-code"></a>Download e l'esecuzione del codice
Per scaricare il codice di questo articolo:

* Visual Studio 2012 o versioni successiva è necessario. 
* Avviare Visual Studio. 
* In Visual Studio, selezionare File -> Nuovo progetto. 
* Nella finestra di dialogo "Nuovo progetto", passare a **Esempi in linea** per **c#** e digitare "db flessibile" nella casella di ricerca nell'angolo superiore destro.
    
    ![Framework entità e applicazione di esempio database flessibile][1] 

    Selezionare il campione chiamato **Flessibile strumenti DB per SQL Azure: integrazione con Framework entità**. Dopo aver accettato la licenza, l'esempio viene caricato. 

Per eseguire l'esempio, è necessario creare tre database vuoti in Database SQL Azure:

* Database di mappa Manager condiviso
* Database condiviso 1
* Database condiviso 2

Dopo aver creato i database, inserire il segnaposto in **Program.cs** con il nome del server DB di SQL Azure, i nomi di database e le credenziali per connettersi al database. Compilare la soluzione in Visual Studio. Visual Studio verrà scaricate le NuGet pacchetti necessari per la raccolta di client database flessibile, Framework entità e temporaneo la gestione degli errori come parte del processo di compilazione. Assicurarsi che il ripristino di pacchetti NuGet sia abilitato per la soluzione. È possibile attivare questa impostazione facendo clic sul file soluzione in Esplora soluzioni di Visual Studio. 

## <a name="entity-framework-workflows"></a>Flussi di lavoro di entità Framework 

Gli sviluppatori Framework entità si basano su uno dei seguenti quattro flussi di lavoro delle applicazioni e per garantire la persistenza per gli oggetti applicazione: 

* **Prima di codice (Database nuovo)**: sviluppo di EF crea il modello nel codice dell'applicazione e quindi EF genera il database da essa. 
* **Prima di codice (Database esistente)**: lo sviluppatore consente EF generare il codice dell'applicazione per il modello da un database esistente.
* **Prima di modello**: lo sviluppatore creato il modello nella finestra di progettazione EF e quindi EF il database dal modello.
* **Prima di database**: lo sviluppatore utilizza EF utensili per dedurre il modello da un database esistente. 

Tutti questi approcci si basano sulla classe DbContext per gestire in modo trasparente connessioni al database e schema del database per un'applicazione. La creazione di avvio automatico e schema del database, come illustrato dettagliatamente più avanti nel documento, diverse occorrenze sulla classe di base DbContext consentono per diversi livelli di controllo sulla creazione di connessione. Problemi si verificano principalmente dal fatto che la gestione di connessione di database fornita dall'EF interseca le funzionalità di gestione connessione le interfacce dati dipendenti routing forniti dalla libreria dei client di database flessibile. 

## <a name="elastic-database-tools-assumptions"></a>Ipotesi di strumenti di database flessibile 

Per le definizioni di termini, vedere [il glossario strumenti Database flessibile](sql-database-elastic-scale-glossary.md).

Raccolta di client flessibile database, è possibile definire le partizioni di dati dell'applicazione denominati shardlets. Shardlets identificate da una chiave sharding e sono mappati a database specifici. Un'applicazione potrebbe avere tutti i database in base alle esigenze e distribuire shardlets per fornire sufficiente capacità o prestazioni fornite specifiche esigenze aziendali. Per una mappa condiviso fornita dal database flessibile client API viene archiviato il mapping dei valori di chiave sharding ai database. Questa funzionalità di **Gestione dei mapping condiviso**o SMM si chiama in breve. Mappa condiviso funge anche da Gestore connessioni di database per le richieste che contengono una chiave sharding. Si parla a questo punto **del routing dipendente dai dati**. 
 
Il responsabile di mappa condiviso protegge gli utenti da incoerenze nelle visualizzazioni dati shardlet che possono verificarsi quando si verificano le operazioni di gestione shardlet simultanee (ad esempio spostamento dei dati da un condiviso a un altro). A tale scopo, mappe condiviso gestita da gestore di libreria client le connessioni al database per un'applicazione. In questo modo la funzionalità di mappa condiviso eliminare automaticamente una connessione di database quando le operazioni di gestione condiviso potrebbero avere un impatto shardlet che la connessione è stata creata per. Questo approccio è necessario integrare con alcune delle funzionalità di EF, ad esempio la creazione di nuove connessioni da uno esistente per verificare l'esistenza di database. In generale, il nostro osservazioni sono stato utilizzato costruttori DbContext standard solo aziendale in modo affidabile per le connessioni sottotitoli database che possono essere duplicate in modo sicuro per EF. Seguire il principio di progettazione di database flessibile è invece al solo broker connessioni aperte. Si pensa chiusura della connessione negoziata dalla libreria client prima che delega il a DbContext EF potrebbe risolvere questo problema. Tuttavia, chiudere la connessione e basarsi su EF aprirlo di nuovo, uno foregoes verifiche convalida e la coerenza eseguite dalla raccolta. La funzionalità di migrazioni EF, tuttavia, utilizza queste connessioni per gestire lo schema di database sottostante in modo trasparente all'applicazione. Ideale, si desidera mantenere e combinare tutte queste funzionalità dalla libreria di client flessibile database ed EF nella stessa applicazione. La sezione seguente descrive queste proprietà e i requisiti in modo più dettagliato. 


## <a name="requirements"></a>Requisiti 

Quando si utilizza la libreria client database flessibile e l'API Framework entità, si desidera mantenere le proprietà seguenti: 

* **Scalabilità**: per aggiungere o rimuovere i database dal livello di dati dell'applicazione sharded in base alle esigenze per i requisiti di capacità dell'applicazione. Controllo di conseguenza, sopra la la creazione e l'eliminazione del database e l'utilizzo condiviso database flessibile mappare API di gestione di gestione di database e i mapping di shardlets. 

* **La coerenza**: l'applicazione impiega sharding e utilizza le funzionalità di routing dipendenti di dati della libreria client. Per evitare il danneggiamento o i risultati della query errato, le connessioni vengono negoziate tramite la gestione di mappa condiviso. Questa operazione anche mantiene convalida e la coerenza.
 
* **Prima di codice**: per mantenere la semplicità di utilizzo del paradigma prima di EF codice. Nel primo codice classi nell'applicazione sono mappate in modo trasparente per le strutture di database. Il codice dell'applicazione interagisce con DbSets che la maggior parte dei relativi aspetti l'elaborazione di database sottostante.
 
* **Schema**: entità Framework gestisce la creazione di schemi di database iniziale ed evoluzione schema successive tramite le migrazioni. Conservando queste funzionalità, adeguare l'app è facile con i dati evoluzione. 

Le linee guida seguenti indica come tali requisiti per il primo codice applicazioni che utilizzano strumenti database flessibile. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Dati dipendenti routing utilizzando EF DbContext 

Connessioni a database con entità Framework sono in genere gestite tramite sottoclassi di **DbContext**. Creare tra le, che derivano da **DbContext**. In questo campo definire il **DbSets** che implementare gli insiemi di file sottoposti a database di oggetti CLR per l'applicazione. Nel contesto di routing dipendenti dati, è possibile identificare diverse proprietà utile che non dispongono di necessariamente per altri scenari di applicazione EF prima di codice: 

* Il database esiste già ed è stato registrato nella mappa flessibile database condiviso. 
* Lo schema dell'applicazione è già stato distribuito al database (illustrato di seguito). 
* Dipendenti dai dati routing la connessione al database sono negoziata da mappa condiviso. 

Per integrare **DbContexts** con dipendenti dai dati routing per scalabilità:

1. Creare connessioni di database fisico tramite le interfacce client flessibile database di gestione di mappa condiviso 
2. Disporre la connessione con sottoclasse **DbContext**
3. Passare le classi di base **DbContext** per assicurarsi che tutte le operazioni sul lato EF errore si verifica anche la connessione verso il basso. 

Nell'esempio seguente viene illustrato questo approccio. (Questo codice è anche nel progetto di Visual Studio accompagnamento)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Punti principali
* Costruzione new sostituisce il costruttore predefinito nella sottoclasse DbContext 
* Costruzione new utilizza gli argomenti necessari per il routing dipendenti dati tramite libreria client database flessibile:
    * mappa condiviso per accedere alle interfacce a routing dipendenti dai dati
    * il tasto sharding per identificare il shardlet
    * una stringa di connessione con le credenziali per la connessione di routing dipendenti dai dati per il condiviso. 
 
* La chiamata al costruttore della classe base tiene un detour un metodo statico che consente di eseguire tutti i passaggi necessari per il routing dipendenti dai dati. 
   * Utilizza la chiamata OpenConnectionForKey le interfacce client di database flessibile sulla mappa condiviso per stabilire una connessione aperta.
   * Il mapping dei condiviso Crea connessione aperta a condiviso contenente shardlet per la chiave sharding specificato.
   * Questa connessione aperta viene restituita al costruttore base della DbContext per indicare che la connessione che verrà utilizzato da EF anziché consentire EF creare automaticamente una nuova connessione. In questo modo la connessione è contrassegnato dal client database flessibile API in modo che è possibile garantire la coerenza in operazioni di gestione della mappa condiviso.
 
  
Utilizzare la funzione di costruzione new per sottoclasse DbContext anziché il costruttore predefinito nel codice. Ecco un esempio: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Costruzione new apre la connessione a condiviso contenente i dati per shardlet identificato dal valore di **tenantid1**. Il codice in blocco **tramite** resta invariato per accedere a **elemento DbSet** per i blog in EF il condiviso per **tenantid1**. Questa operazione modificherà semantica per il codice nel tramite bloccarlo in modo che tutte le operazioni di database sono ora limitate all'ambito un condiviso nel punto in cui viene mantenuto **tenantid1** . Ad esempio, una query LINQ su blog **elemento DbSet** solo restituirebbe blog archiviato nel condiviso corrente, ma non quelli memorizzati in altre shards.  

#### <a name="transient-faults-handling"></a>Problemi temporanei gestione
Il team Microsoft Patterns & Practices pubblicato [Nel temporanee Gestione applicazione blocco Fault](https://msdn.microsoft.com/library/dn440719.aspx). La raccolta viene utilizzata con la raccolta di client scalabilità flessibile in combinazione con EF. Tuttavia, assicurarsi che qualsiasi eccezione temporanea restituisce in un punto in cui assicurare che costruzione new viene utilizzata dopo un errore temporaneo, in modo che qualsiasi nuovo tentativo di connessione con costruttori che sono stati modificati. In caso contrario, una connessione a corretto condiviso non è garantita e sono non disponibili garanzie che la connessione viene mantenuta quando si verificano modifiche alla mappa condiviso. 

Nell'esempio seguente viene illustrato come utilizzare un criterio di Riprova SQL intorno costruttori sottoclasse **DbContext** nuovi: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** nel codice precedente è definito come un **SqlDatabaseTransientErrorDetectionStrategy** con un numero di tentativi di 10 e 5 secondi di attesa tra tentativi. Questo approccio è simile alle linee guida per EF e le transazioni avviata dall'utente (vedere [limitazioni con la ripetizione strategie di esecuzione (in poi EF6)](http://msdn.microsoft.com/data/dn307226). Entrambi i casi richiedono che dell'applicazione controlla l'ambito in cui viene restituito l'eccezione temporanea: a riaprire la transazione o ricreare il contesto dal costruttore iniz (come illustrato) che utilizza la libreria di client database flessibile.

La necessità di controllare dove eccezioni temporanee hanno Contattaci tornare nell'ambito impedisce anche l'uso di predefiniti **SqlAzureExecutionStrategy** fornito con EF. **SqlAzureExecutionStrategy** da riaprire una connessione, ma non usare **OpenConnectionForKey** ed evitare pertanto tutta la convalida viene eseguita come parte della chiamata **OpenConnectionForKey** . Se, tuttavia, nell'esempio vengono utilizzati predefiniti **DefaultExecutionStrategy** anche fornito con EF. Anziché **SqlAzureExecutionStrategy**funziona correttamente in combinazione con i criteri di tentativi di gestire guasto temporaneo. Esecuzione della classe **ElasticScaleDbConfiguration** impostati Si noti che deciso di non utilizzare **DefaultSqlExecutionStrategy** poiché suggerisce per usare **SqlAzureExecutionStrategy** se si verificano eccezioni temporanee - che comporterebbe al comportamento errato come descritto. Per ulteriori informazioni sui criteri diversi tentativi ed EF, vedere [La connessione resilienza EF](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Costruttore riscrittura
Negli esempi di codice precedenti viene illustrato l'impostazione predefinita costruttore nuovamente scrive desiderati per l'applicazione per utilizzare i dati dipendenti routing con Framework entità. Nella tabella seguente generalizza questo approccio per altri costruttori. 


Costruttore corrente  | Costruttore riscritto per i dati | Costruttore di base | Note
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |La connessione deve essere una funzione della mappa condiviso e il tasto routing dipendenti dai dati. È necessario per la creazione di connessione automatica by-passare da EF e utilizzare invece la mappa condiviso al broker la connessione. 
MyContext(string)|ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |La connessione è una funzione della mappa condiviso e il tasto routing dipendenti dai dati. Una stringa di connessione o nome del database non funzionerà man mano che si convalida by-passare dalla mappa condiviso. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |La connessione viene visualizzato creata per la chiave di mappa e sharding specificato condiviso con il modello fornito. Il modello compilato verrà passato base c'tor.
MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |La connessione deve corrispondere da mappa condiviso e la chiave. Impossibile fornire come input (a meno che tale input è stata già utilizzando la mappa condiviso e la chiave). Valore booleano verrà trasferito sui. 
MyContext (stringa, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |La connessione deve corrispondere da mappa condiviso e la chiave. Non è specificato come input, (a meno che tale input utilizzava la mappa condiviso e la chiave). Il modello compilato verrà trasferito sui. 
MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, TKey, ObjectContext, bool) |DbContext (ObjectContext, bool) |Per garantire che tutte le connessioni in ObjectContext passato come input viene reindirizzata a una connessione gestita da scalabilità flessibile costruzione new. Una descrizione dettagliata della ObjectContexts è lo scopo di questo documento.
MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, bool)| DbContext (DbConnection, DbCompiledModel, valore booleano). |La connessione deve corrispondere da mappa condiviso e la chiave. La connessione non può essere fornita come input (a meno che tale input è stata già utilizzando la mappa condiviso e la chiave). Modello e Boolean vengono passati al costruttore della classe base. 

## <a name="shard-schema-deployment-through-ef-migrations"></a>Distribuzione dello schema condiviso mediante le migrazioni EF 

Gestione automatica dello schema risulta utile fornite da Framework entità. Nel contesto delle applicazioni che utilizzano strumenti database flessibile, si desidera mantenere questa funzionalità per lo schema shards appena creato il provisioning automatico quando i database vengono aggiunti all'applicazione sharded. Il caso di utilizzo principale è il livello di dati per supportare sharded applicazioni che utilizzano EF. Utilizzare la funzionalità di EF per la gestione dello schema di riduce l'impegno di amministrazione di database con un'applicazione sharded basata sulle EF. 

Distribuzione dello schema tramite le migrazioni EF funziona meglio **connessioni non aperto**. Questa differenza lo scenario per i dati dipendenti routing che si basa su aperta la connessione fornita dal database flessibile client API. Un'altra differenza è il requisito di coerenza: mentre si opportuno per garantire la coerenza per tutte le connessioni routing dipendente dai dati impedire la modifica della mappa simultanee condiviso, non è un problema con la distribuzione di schema iniziale in un nuovo database contenente non è ancora stata registrata nella mappa condiviso e non ancora ricevuto per contenere shardlets. È possibile pertanto si basano sulle connessioni al database regolare per questo scenari, anziché del routing dipendente dai dati.  

Conduce ad un approccio nel punto in cui la distribuzione dello schema tramite le migrazioni EF strettamente con la registrazione del nuovo database come condiviso nella mappa condiviso dell'applicazione. Questa operazione si basa sui prerequisiti seguenti: 

* Il database è già stato creato. 
* Il database non contiene alcun dato, vengono mantenuti nessun utente schema e i dati non utente.
* Il database non sono ancora accessibili tramite il client di database flessibile API per il routing dipendenti dai dati. 

Con questi prerequisiti in posizione, è possibile creare una normale non aperto **SqlConnection** per avviare le migrazioni EF per distribuzione dello schema. Nell'esempio seguente viene illustrato questo approccio. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

In questo esempio viene illustrato il metodo **RegisterNewShard** che registra il condiviso nella mappa condiviso, distribuisce lo schema tramite le migrazioni EF e memorizza il mapping di una chiave sharding per il condiviso. Si basa su un costruttore della sottoclasse **DbContext** (**ElasticScaleContext** nel campione) che entrerà in una stringa di connessione SQL come input. Il codice di questo costruttore è dirette, come illustrato nell'esempio seguente: 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
Uno può hanno implementato la versione del costruttore ereditato dalla classe di base. Ma è necessario assicurarsi che l'inizializzatore predefinito per EF venga utilizzato durante la connessione. Pertanto la breve detour al metodo statico prima di chiamare i il costruttore della classe con la stringa di connessione. Si noti che la registrazione di shards deve essere eseguita in un dominio diverso app o un processo per garantire che le impostazioni di inizializzatore per EF non sono in conflitto. 


## <a name="limitations"></a>Limitazioni 

I metodi descritti in questo documento comportano un paio di limitazioni: 

* Le applicazioni di EF utilizzano **LocalDb** è innanzitutto necessario eseguire la migrazione a un normale database di SQL Server prima di utilizzare una raccolta di client database flessibile. Scalabilità un'applicazione tramite sharding con scalabilità flessibile non è possibile con **LocalDb**. Si noti che sviluppo ancora possibile **LocalDb**. 

* Eventuali modifiche apportate all'applicazione dall'implicano modifiche allo schema di database è necessario eseguire le migrazioni EF su shards tutti. Il codice di esempio per il documento viene illustrato come eseguire questa operazione. Valutare la possibilità di utilizzo del Database di aggiornamento con un parametro ConnectionString per scorrere tutte shards; o estrarre lo script T-SQL per la migrazione in sospeso utilizzando aggiornamento Database con – Script opzione e applicare i shards lo script T-SQL.  

* Data una richiesta, si presuppone che tutti i processi database è contenuto in un singolo condiviso come identificato dalla chiave sharding illustrata la richiesta. Tuttavia, tale presupposto non sempre tenere true. Ad esempio, quando non è possibile rendere disponibile una chiave sharding. Per risolvere questo problema, la libreria client fornisce la classe **MultiShardQuery** implementata un'astrazione di connessione per eseguire una query sul shards diversi. Imparare a usare **MultiShardQuery** in combinazione con EF è lo scopo di questo documento

## <a name="conclusion"></a>Conclusioni

I passaggi descritti in questo documento, è possibile utilizzare applicazioni EF funzionalità della libreria client database flessibile per il routing dipendenti dati tramite il refactoring costruttori delle sottoclassi **DbContext** utilizzate nell'applicazione EF. Questo limita le modifiche necessarie per tali posizioni in cui le classi **DbContext** esistono già. Inoltre, le applicazioni di EF possono continuare a trarre vantaggio dalla distribuzione automatica dello schema combinando i passaggi che richiamano le migrazioni EF necessarie, con la registrazione di nuovo shards e mapping condiviso. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 