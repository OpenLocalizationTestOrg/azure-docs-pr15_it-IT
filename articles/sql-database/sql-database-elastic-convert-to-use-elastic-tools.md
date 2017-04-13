<properties
   pageTitle="Eseguire la migrazione di database esistenti a scalabilità | Microsoft Azure"
   description="Convertire database sharded per utilizzare gli strumenti di database flessibile creando un condiviso manager mappa"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>Eseguire la migrazione di database esistenti a scalabilità

Gestire facilmente scalabilità orizzontale sharded database esistente con strumenti di database di Database SQL Azure (ad esempio la [raccolta di Database flessibile client](sql-database-elastic-database-client-library.md)). È innanzitutto necessario convertire un set di database che si desidera utilizzare [condiviso mappare manager](sql-database-elastic-scale-shard-map-management.md)esistente. 

## <a name="overview"></a>Panoramica
Per eseguire la migrazione di un database sharded esistente: 

1. Preparare [il database condiviso mapping manager](sql-database-elastic-scale-shard-map-management.md).
2. Creare la mappa condiviso.
3. Preparare shards singoli.  
2. Aggiungere i mapping alla corrispondenza di condiviso.

Queste tecniche possono essere implementate utilizzando la [libreria client .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)o gli script di PowerShell disponibili DB di [SQL Azure - script di strumenti di Database flessibile](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Gli esempi riportati di seguito usare gli script di PowerShell.

Per ulteriori informazioni sul ShardMapManager, vedere [condiviso eseguire il mapping di gestione](sql-database-elastic-scale-shard-map-management.md). Per una panoramica degli strumenti di database flessibile, vedere [Panoramica delle funzionalità Database flessibile](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparare il database di gestione della mappa condiviso

Il responsabile di mappa condiviso è un database speciale che contiene i dati per gestire i database scalabilità orizzontale. È possibile utilizzare un database esistente oppure creare un nuovo database. Si noti che un database funge da gestore mappa condiviso non deve essere dello stesso database come un condiviso. Si noti inoltre che lo script di PowerShell non crea il database dell'utente. 

## <a name="step-1-create-a-shard-map-manager"></a>Passaggio 1: creare un condiviso mappa manager

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Per recuperare il responsabile di mappa condiviso

Dopo la creazione, è possibile recuperare il responsabile di mappa condiviso con questo cmdlet. Questo passaggio è necessario ogni volta che è necessario utilizzare l'oggetto ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>Passaggio 2: creare la mappa condiviso

È necessario selezionare il tipo di corrispondenza condiviso da creare. La scelta dipende l'architettura di database: 

1. Singolo tenant per ogni database (per i termini, vedere il [glossario](sql-database-elastic-scale-glossary.md)). 
2. Più tenant per ogni database (due tipi):
    3. Mapping di elenco
    4. Mapping di intervallo
 

Per un modello singolo tenant, creare una mappa di condiviso **mapping di elenco** . Il modello singolo tenant assegna un solo database per tenant. Si tratta di un modello efficace per gli sviluppatori SaaS come che semplifica la gestione.

![Mapping di elenco][1]

Modello multi-tenant assegna tenant diversi in un solo database e distribuire gruppi di tenant su più database. Utilizzare questo modello quando si prevede che ogni tenant hanno esigenze di dati di piccole dimensioni. In questo modello, viene assegnato un intervallo di tenant a un database utilizzando **il mapping di intervallo**. 
 

![Mapping di intervallo][2]

Oppure è possibile implementare un modello di database multi-tenant mediante un *mapping di elenco* per assegnare più tenant a un solo database. Ad esempio DB1 viene utilizzato per memorizzare le informazioni su id tenant 1 e 5 e DB2 vengono archiviati i dati per tenant 7 e tenant 10. 

![Tenant di più su DB singola][3] 

**A seconda della scelta effettuata, scegliere una delle opzioni seguenti:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opzione 1: creare una mappa condiviso per il mapping di un elenco
Creare una mappa condiviso usando l'oggetto ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opzione 2: creare una mappa condiviso per il mapping di un intervallo

Si noti che per utilizzare questo modello di mapping, id tenant valori deve essere intervalli continui ed è accettabile dispongano di spazio vuoto nella casella intervalli, è sufficiente ignorare l'intervallo quando si creano i database.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Opzione 3: Elenca i mapping su un singolo database
Impostazione di questo modello richiede la creazione di una mappa di elenco come illustrato nel passaggio 2, opzione 1.

## <a name="step-3-prepare-individual-shards"></a>Passaggio 3: Preparare shards singoli

Aggiungere ogni condiviso (database) per il responsabile di mappa condiviso. Consente di preparare singoli database per memorizzare le informazioni di mapping. Eseguire questo metodo su ogni condiviso.
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>Passaggio 4: Aggiungere mapping

L'aggiunta di mapping dipende dal tipo di mappa condiviso che è stato creato. Se è stata creata una mappa di elenco, aggiungere i mapping di elenco. Se è stata creata una mappa di intervallo, aggiungere i mapping di intervallo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opzione 1: eseguire il mapping dei dati per il mapping di un elenco

Eseguire il mapping dei dati mediante l'aggiunta di un mapping di elenco per ogni tenant.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opzione 2: eseguire il mapping dei dati per il mapping di un intervallo

Aggiungere i mapping di intervallo per tutti i tenant intervallo di id-associazioni di database:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Passaggio 4 opzione 3: eseguire il mapping dei dati per più tenant su un singolo database

Per ogni tenant, eseguire il componente ListMapping (opzione 1, sopra). 


## <a name="checking-the-mappings"></a>Controllo mapping

È possibile effettuarvi informazioni shards esistente e i mapping associati ad essi mediante i seguenti comandi:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Riepilogo

Dopo aver completato la configurazione, è possibile iniziare a utilizzare la libreria di client Database flessibile. È anche possibile utilizzare [il routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) e [condiviso con più query](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passaggi successivi


È possibile ottenere gli script di PowerShell dal [sripts strumenti Database DB elastici SQL Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Gli strumenti presenti nella GitHub: [Azure/elastici db strumenti](https://github.com/Azure/elastic-db-tools).

Usare lo strumento di unione di divisione per spostare i dati in o da un modello multi-tenant a un modello singolo tenant. Vedere [strumento di unione divisa](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive

Per informazioni sui modelli di architettura dati comune multi-tenant software come servizio (SaaS) delle applicazioni di database, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant con Database di SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Domande e le richieste di funzionalità

Per eventuali domande, vedere raggiungere Contattaci nel [forum di Database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e le richieste di funzionalità, aggiungerle al [Database SQL di area feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
