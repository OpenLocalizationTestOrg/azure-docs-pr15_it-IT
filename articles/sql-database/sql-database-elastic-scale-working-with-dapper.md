<properties 
    pageTitle="Utilizzo della libreria client di database flessibile con Dapper | Microsoft Azure" 
    description="Utilizzo di una raccolta di client database flessibile con Dapper." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>Utilizzo della libreria client di database flessibile con Dapper 

In questo documento è per gli sviluppatori che si basano su Dapper delle applicazioni, ma si desidera anche adottare [flessibile database utensili](sql-database-elastic-scale-introduction.md) per creare le applicazioni di implementano sharding per scalabilità il livello di dati.  In questo documento sono illustrate le modifiche nelle applicazioni basate su Dapper necessarie per l'integrazione con Strumenti database flessibile. Il nostro obiettivo è la composizione la gestione di flessibile database condiviso e dati dipendenti routing con Dapper. 

**Esempio**: [Strumenti di database flessibile per Database di SQL Azure - integrazione Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Integrazione di **Dapper** e **DapperExtensions** con la raccolta di client database flessibile per il Database di SQL Azure è semplice. Le applicazioni è possono utilizzare dati dipendenti routing modificando la creazione e apertura di nuovi oggetti [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) per utilizzare la chiamata [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) dalla [libreria client](http://msdn.microsoft.com/library/azure/dn765902.aspx). Questo limita le modifiche in un'applicazione a solo in cui vengono create e aperti nuove connessioni. 

## <a name="dapper-overview"></a>Panoramica dapper
**Dapper** è un mapping relazionale a oggetti. Eseguire il mapping di oggetti .NET dall'applicazione di un database relazionale (e viceversa). La prima parte del codice di esempio viene illustrato come è possibile integrare la libreria di client database flessibile con le applicazioni basate su Dapper. Nella seconda parte del codice di esempio viene illustrato come integrare quando si utilizza Dapper e DapperExtensions.  

La funzionalità di mapping in Dapper fornisce i metodi di estensione sulle connessioni al database che semplificano l'invio istruzioni T-SQL per esecuzione o query sul database. Ad esempio, Dapper è semplice per eseguire il mapping tra gli oggetti .NET e i parametri di istruzioni SQL per le chiamate **Execute** o utilizzare i risultati delle query SQL in oggetti .NET utilizzando le chiamate di **Query** da Dapper. 

Quando si utilizza DapperExtensions, non è più necessario fornire le istruzioni SQL. Metodi di estensione, ad esempio **GetList** o **Inserisci** sopra la connessione al database creano le istruzioni SQL dietro le quinte.
 
Un altro vantaggio che Dapper e anche DapperExtensions è che l'applicazione controlla la creazione della connessione al database. Consente di interagire con la libreria client database flessibile Broker le connessioni in base al mapping di shardlets ai database al database.

Per ottenere gli assembly Dapper, vedere [Dapper punto netto](http://www.nuget.org/packages/Dapper/). Per le estensioni Dapper, vedere [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Introduzione alla libreria di client database flessibile

Con la raccolta di client database flessibile, definire le partizioni di dati dell'applicazione denominati *shardlets* , mapparli ai database e identificare in modo sicuro da *chiavi sharding*. È possibile impostare tutti i database sia necessario e distribuisce il shardlets su questi database. Il mapping dei valori di chiave sharding ai database è archiviato da una mappa condiviso fornita dall'API della raccolta. Questa caratteristica è definita **condiviso mappare gestione**. Mappa condiviso funge anche da Gestore connessioni di database per le richieste che contengono una chiave sharding. Questa funzionalità viene definita **il routing dipendente dai dati**.

![Mappe condiviso e il routing dipendente dai dati][1]

Il responsabile di mappa condiviso protegge gli utenti da incoerenze nelle visualizzazioni dati shardlet che possono verificarsi quando si verificano le operazioni di gestione shardlet simultanee sui database. A tale scopo, mappe condiviso del broker le connessioni di database per un'applicazione creata con la raccolta. Quando le operazioni di gestione condiviso potrebbero avere un impatto il shardlet, in questo modo la funzionalità di mappa condiviso automaticamente interrompere una connessione di database. 

Invece di usare la modalità tradizionale di creare connessioni per Dapper, è necessario utilizzare il [metodo OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). In questo modo che tutta la convalida entrerà in vigore e le connessioni vengono gestite correttamente quando si spostano tutti i dati tra shards.

### <a name="requirements-for-dapper-integration"></a>Requisiti per l'integrazione Dapper

Quando si utilizza la libreria di client flessibile database e le API Dapper, si desidera mantenere le proprietà seguenti:

* **Scalabilità**: si desidera aggiungere o rimuovere il livello di dati dell'applicazione sharded in base alle esigenze per i requisiti di capacità dell'applicazione di database. 

-    **La coerenza**: poiché l'applicazione scalabilità utilizzando sharding, è necessario eseguire il routing dei dipendenti dati. Si desidera usare le funzionalità di routing dipendenti di dati della raccolta per farlo. In particolare, si desidera mantenere la convalida e la coerenza garantisce forniti da connessioni negoziate tramite la gestione di mappa condiviso per evitare il danneggiamento o i risultati della query errato. In questo modo che le connessioni a un determinato shardlet rifiutate o interrotta se (ad esempio) la shardlet attualmente viene spostato in una diversa condiviso utilizzando API di divisione/unione.

-    **Mappatura di oggetto**: si desidera mantenere la semplicità di utilizzo dei mapping forniti da Dapper per tradurre tra le classi nell'applicazione e le strutture di database. 

La sezione seguente fornisce indicazioni per tali requisiti per applicazioni basate su **Dapper** e **DapperExtensions**.

## <a name="technical-guidance"></a>Indicazioni tecniche
### <a name="data-dependent-routing-with-dapper"></a>Dati dipendenti routing con Dapper 

Con Dapper, l'applicazione è in genere responsabile per creare e aprire le connessioni al database sottostante. Dato un tipo T dall'applicazione, Dapper restituisce i risultati della query come insiemi di .NET di tipo T. Dapper esegue il mapping delle righe di risultato T-SQL per gli oggetti di tipo T. Analogamente, Dapper esegue il mapping di oggetti .NET in valori SQL o parametri per le istruzioni (Manipulation language) di modifica dei dati. Dapper offre questa funzionalità mediante i metodi di estensione dell'oggetto [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regolare dal raccolte ADO .NET SQL Client. La connessione SQL restituita dalle API scala flessibile per DDR anch ' essi oggetti [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regolari. In questo modo utilizzare direttamente estensioni Dapper sopra il tipo restituito da API DDR della libreria client, in quanto è anche una semplice connessione SQL Client.

Queste osservazioni rendono più semplice da utilizzare connessioni negoziate dalla libreria dei client di database flessibile per Dapper.

In questo esempio di codice (da esempio accompagnamento) illustra l'approccio in cui la chiave sharding viene fornita dall'applicazione per la raccolta al broker la connessione a destra condiviso.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

La chiamata a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API sostituisce la creazione di predefinito e apertura di una connessione SQL Client. La chiamata [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) accetta gli argomenti necessari per il routing dipendente dai dati: 

-    Mappa condiviso per accedere alle interfacce di routing dipendenti di dati
-    Il tasto sharding per identificare il shardlet
-    Le credenziali (nome utente e password) a cui connettersi il condiviso

Oggetto della mappa condiviso consente di creare una connessione a condiviso contenente shardlet per la chiave sharding specificato. Il client di database flessibile API tag anche la connessione per implementare le garanzie di coerenza. Poiché la chiamata a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) restituisce un oggetto di connessione SQL Client normale, la chiamata successiva al metodo di estensione **Execute** da Dapper segue esercitazione Dapper standard.

Utilizzo di query molto simili a quelle, si apre la connessione tramite [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) dal client API. E quindi utilizzare i metodi di estensione Dapper normale per mappare i risultati della query SQL in oggetti .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Si noti che **utilizzando** il blocco con la connessione DDR ambiti tutte le operazioni di database all'interno del blocco a un condiviso nel punto in cui viene mantenuto tenantId1. La query restituirà solo blog archiviato nel condiviso corrente, ma non quelli memorizzati in altre shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Dati dipendenti routing con Dapper e DapperExtensions

Dapper viene fornito con un insieme di estensioni aggiuntive in grado di fornire ulteriori comodità e astrazione dal database quando si sviluppano applicazioni di database offre. DapperExtensions è illustrato un esempio. 

Utilizzo DapperExtensions in un'applicazione non cambia la modalità di creazione e gestione delle connessioni al database. È comunque responsabilità dell'applicazione per aprire le connessioni e regolari SQL Client connessione si prevede gli oggetti, i metodi di estensione. È possibile si basano su [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) come descritto sopra. Come visualizzare esempi di codice, la sola modifica è non è necessario scrivere le istruzioni T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Ed ecco l'esempio di codice per la query: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Gestione problemi temporanei

Il team Microsoft Patterns & Practices pubblicato [Temporaneo guasto gestione Application Block](http://msdn.microsoft.com/library/hh680934.aspx) per ridurre gli sviluppatori di applicazioni comuni condizioni temporanee errore rilevate durante l'esecuzione nel cloud. Per ulteriori informazioni, vedere [Perseverance, segreto tutti luogo: mediante l'applicazione Block gestione temporaneo guasto](http://msdn.microsoft.com/library/dn440719.aspx).

Nell'esempio si basa su raccolta guasto temporaneo in modo da evitare problemi temporanei. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** nel codice precedente è definito come un **SqlDatabaseTransientErrorDetectionStrategy** con un numero di tentativi di 10 e 5 secondi di attesa tra tentativi. Se si usano le transazioni, assicurarsi che l'ambito Riprova torna all'inizio della transazione nel caso di errore temporaneo.

## <a name="limitations"></a>Limitazioni

I metodi descritti in questo documento comportano un paio di limitazioni:

* Il codice di esempio per il documento non viene illustrato come gestire schema tra shards.
* Data una richiesta, si presuppone che tutto l'elaborazione di database è contenuto in un singolo condiviso come identificato dalla chiave sharding illustrata la richiesta. Tuttavia, tale presupposto non sempre mettere in attesa, ad esempio, quando non è possibile rendere disponibile una chiave sharding. Per risolvere questo problema, la raccolta di client database flessibile include la [classe MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). La classe è implementata un'astrazione di connessione per eseguire una query sul shards diversi. L'uso di MultiShardQuery in combinazione con Dapper è lo scopo di questo documento.

## <a name="conclusion"></a>Conclusioni

Le applicazioni che utilizzano Dapper e DapperExtensions facilmente possono trarre vantaggio da Strumenti database flessibile per il Database di SQL Azure. Tramite i passaggi descritti in questo documento, tali applicazioni possono utilizzare la funzionalità dello strumento per dati dipendenti routing modificando la creazione e apertura di nuovi oggetti [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) da utilizzare chiamata [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) della libreria client database flessibile. Questo limita le modifiche di applicazione necessarie per tali posizioni in cui le nuove connessioni vengono create e aperto. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 