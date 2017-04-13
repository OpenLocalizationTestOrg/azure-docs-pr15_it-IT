<properties
    pageTitle="Creazione di report in database cloud scalabilità orizzontale | Microsoft Azure"
    description="come configurare la query flessibile su partizioni orizzontali"    
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
    ms.author="torsteng" />

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Creazione di report in database fuori architetture cloud (preview)

![Query su shards][1]

Database sharded distribuiscono righe in una scalate fuori dati livello. Lo schema è identico su tutti i database partecipanti, noti anche come partizione orizzontale. Utilizzo di una query flessibile, è possibile creare report che si estendono su tutti i database in un database sharded.

Per un avvio rapido, vedere [report tra database cloud scalabilità orizzontale](sql-database-elastic-query-getting-started.md).

Per non sharded database, vedere [Query su database cloud con schemi diversi](sql-database-elastic-query-vertical-partitioning.md). 

 
## <a name="prerequisites"></a>Prerequisiti

* Creare una mappa condiviso mediante la raccolta di client database flessibile. vedere [condiviso eseguire il mapping di gestione](sql-database-elastic-scale-shard-map-management.md). Oppure utilizzare l'app di esempio nella [Guida introduttiva a Strumenti database flessibile](sql-database-elastic-scale-get-started.md).
* In alternativa, vedere [eseguire la migrazione database esistenti ai database scalabilità orizzontale](sql-database-elastic-convert-to-use-elastic-tools.md).
* L'utente deve disporre dell'autorizzazione di modifica di qualsiasi origine di dati esterni. Questa autorizzazione è inclusa l'autorizzazione ALTER DATABASE.
* Per fare riferimento all'origine dati sottostante sono necessarie le autorizzazioni di modifica di qualsiasi origine di dati esterni.

## <a name="overview"></a>Panoramica

Queste istruzioni creano la rappresentazione dei metadati del livello sharded dati nel database di query flessibile. 


1. [CREARE CHIAVE MASTER](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREARE DATABASE NELL'AMBITO DELLE CREDENZIALI](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREARE L'ORIGINE DATI ESTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREAZIONE DI TABELLE ESTERNE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 creare chiave master del database nell'ambito e le credenziali 

Le credenziali viene utilizzata dalla query flessibile per connettersi al database remoti.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] Assicurarsi che la *"\<nome utente\>"* non include alcun *"@servername"* suffisso. 

## <a name="12-create-external-data-sources"></a>1.2 creare origini dati esterne

Sintassi:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Esempio 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
Recuperare l'elenco delle origini dati esterne corrente: 

    select * from sys.external_data_sources; 

L'origine dati esterna fa riferimento la mappa condiviso. Una query flessibile utilizzata l'origine dati esterna e la mappa condiviso sottostante enumerare i database che partecipano a livello di dati. Per leggere la mappa condiviso e per accedere ai dati della shards durante l'elaborazione di una query flessibile, vengono utilizzate le stesse credenziali. 

## <a name="13-create-external-tables"></a>1.3 creare tabelle esterne 
 
Sintassi:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Esempio**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Recuperare l'elenco di tabelle esterne dal database corrente: 

    SELECT * from sys.external_tables; 

Per rimuovere tabelle esterne:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Note

I dati\_origine clausola definisce l'origine dati esterna (una mappa condiviso) utilizzato per la tabella esterna.  

Lo SCHEMA\_nome e l'oggetto\_clausole nome mappare la definizione di tabelle esterne in una tabella in un altro schema. Se omesso, lo schema dell'oggetto remoto verrà considerato uguale a "dbo" e il relativo nome verrà considerato uguale a identico al nome di tabella esterna viene definito. Questo è utile se il nome della tabella remoto è già utilizzato nel database in cui si desidera creare la tabella esterna. Ad esempio, per definire una tabella esterna per ottenere una visualizzazione aggregata di viste del catalogo o viste sui dati in scala fuori a più livelli. Poiché le viste del catalogo e le viste esiste ancora in locale, è possibile utilizzare i nomi per la definizione di tabelle esterne. Invece di usare un nome diverso e utilizzare la visualizzazione del catalogo o la DMV nome nello SCHEMA di\_nome e/o oggetto\_clausole nome. (Vedere nell'esempio seguente). 

La clausola distribuzione specifica la distribuzione di dati utilizzata per questa tabella. Processore query utilizza le informazioni fornite all'interno della clausola di distribuzione per creare i piani di query più efficienti.  

1. **SHARDED** significa orizzontalmente la partizione dati tra i database. Il tasto partizione per la distribuzione dei dati è il parametro **< sharding_column_name >** .
2. **REPLICATO** significa che copie identiche della tabella sono presente in ogni database. È responsabilità dell'utente per garantire che le repliche sono identiche per i database.
3. **Arrotonda\_MARCO** significa che la tabella è suddiviso in senso orizzontale con un metodo di distribuzione dipendenti dall'applicazione. 

**Livello di dati di riferimento**: la tabella esterna DDL fa riferimento a un'origine dati esterna. L'origine dati esterna specifica una mappa condiviso che consente di tabelle esterne con le informazioni necessarie per individuare tutti i database nel livello dati. 


### <a name="security-considerations"></a>Considerazioni sulla protezione 

Gli utenti hanno accesso a tabelle esterne accedere automaticamente alle tabelle remote sottostanti sotto le credenziali fornite nella definizione origine dati esterna. Evitare indesiderati elevazione dei privilegi tramite le credenziali dell'origine dati esterna. Utilizzare GRANT o REVOKE per una tabella esterna come se si trattasse di una tabella normale.  

Dopo aver definito l'origine dati esterna e le tabelle esterne, è ora possibile usare Transact-SQL completo tramite le tabelle esterne.

## <a name="example-querying-horizontal-partitioned-databases"></a>Esempio: esecuzione di query su database partizionati orizzontali 

La query seguente esegue un join di tre elementi tra warehouse, ordini e le righe dell'ordine e utilizza le aggregazioni diverse e un filtro selettivo. Si presuppone partizioni (1) orizzontale (sharding) e (2) che data warehouse, ordini e righe ordine sono sharded in base alla colonna id warehouse e che la query flessibile può posizionare i join nella shards ed elaborare costosa parte della query in shards in parallelo. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Stored procedure per l'esecuzione in Transact-SQL: sp\_execute_remote

Query flessibile è stato inoltre introdotto una stored procedure che fornisce l'accesso diretto per il shards. Stored procedure è denominata [sp\_eseguire \_remoto](https://msdn.microsoft.com/library/mt703714) e può essere utilizzato per eseguire remote stored procedure o codice T-SQL sul database remoti. Accetta i parametri seguenti: 

* Nome dell'origine dati (nvarchar): il nome dell'origine dati esterna di tipo RDBMS. 
* Query (nvarchar): query T-SQL può essere eseguita in ogni condiviso. 
* Dichiarazione di parametro (nvarchar) - facoltativo: stringa con definizione dei tipi di dati per i parametri utilizzati nel parametro di Query (ad esempio sp_executesql). 
* Elenco di valori parametro - facoltativo: elenco delimitato da virgole dei valori di parametro (ad esempio sp_executesql).

Sp\_eseguire\_remota utilizza l'origine dati esterna fornita nei parametri chiamata per eseguire l'istruzione SQL T specificata sul database remoti. Utilizza le credenziali dell'origine dati esterna per la connessione al database di gestione shardmap e i database remoti.  

Esempio: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Connettività di strumenti  

Utilizzare normali stringhe di connessione di SQL Server per connettere l'applicazione, strumenti di integrazione di Business Intelligence e i dati al database con le definizioni di tabelle esterne. Assicurarsi che sia supportato SQL Server come origine dati per lo strumento. Quindi riferimento il database di query flessibile come qualsiasi altro database di SQL Server connessi allo strumento e utilizzo di tabelle esterne dal strumento o applicazione come se si trattasse di tabelle locali. 

## <a name="best-practices"></a>Procedure consigliate 

* Assicurarsi che il database di endpoint flessibile query ha concesso l'accesso al database shardmap e shards tutti attraverso il firewall DB SQL.  

* Convalidare o applicare la distribuzione di dati definita dalla tabella esterna. Se la distribuzione dei dati effettivi è la differenza tra la distribuzione di cui la definizione di una tabella, le query potrebbero restituire risultati imprevisti. 

* Query flessibile attualmente non esegue eliminazione condiviso quando predicati sopra il tasto sharding consenta in modo sicuro escludere determinati shards dall'elaborazione.

* Query flessibile è ideale per le query in cui la maggior parte del calcolo è possibile eseguire il shards. Vengono in genere le massime prestazioni di query con predicati del filtro selettiva che può essere valutata shards nei join sui tasti partizioni che possono essere eseguiti in modo di allineato partizione shards tutti. Altri modelli di query potrebbero essere necessario caricare grandi quantità di dati dal shards per il nodo principale e possono eseguire in modo non corretto

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
