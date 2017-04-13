<properties
    pageTitle="Query su database cloud con schema diverso | Microsoft Azure"
    description="come configurare la query tra database su partizioni verticali"    
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

# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Query su database cloud con schemi diversi (preview)

![Query sulle tabelle di database diversi][1]

Database suddiviso verticalmente utilizzano diversi insiemi di tabelle per database diversi. Ciò significa che lo schema è diverso sui database diversi. Ad esempio, tutte le tabelle per inventario sono su un database mentre tutte le tabelle correlate contabilità in un secondo database. 

## <a name="prerequisites"></a>Prerequisiti

* L'utente deve disporre dell'autorizzazione di modifica di qualsiasi origine di dati esterni. Questa autorizzazione è inclusa l'autorizzazione ALTER DATABASE.
* Per fare riferimento all'origine dati sottostante sono necessarie le autorizzazioni di modifica di qualsiasi origine di dati esterni.

## <a name="overview"></a>Panoramica

**Nota**: diversamente da quanto succede con partizione orizzontale, queste istruzioni DDL non dipendono dalla definizione di un livello di dati con una mappa condiviso tramite la raccolta di client database flessibile.

1. [CREARE CHIAVE MASTER](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CREARE DATABASE NELL'AMBITO DELLE CREDENZIALI](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREARE L'ORIGINE DATI ESTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREAZIONE DI TABELLE ESTERNE](https://msdn.microsoft.com/library/dn935021.aspx) 


## <a name="create-database-scoped-master-key-and-credentials"></a>Creare chiave master del database nell'ambito e le credenziali 

Le credenziali viene utilizzata dalla query flessibile per connettersi al database remoti.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
**Nota**    Assicurarsi che la *<username>* non include alcun *"@servername"* suffisso. 

## <a name="create-external-data-sources"></a>Creare origini dati esterne

Sintassi:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

**Importante**   Parametro di tipo deve essere impostato su **RDBMS**. 

### <a name="example"></a>Esempio 

Nell'esempio seguente viene illustrato l'utilizzo dell'istruzione CREATE per le origini dati esterne. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 
 
Per recuperare l'elenco delle origini dati esterne corrente: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelle esterne 

Sintassi:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 
    
    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Esempio  

    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 

Nell'esempio seguente viene illustrato come recuperare l'elenco di tabelle esterne dal database corrente: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Note

Query flessibile estende la sintassi di tabelle esterne esistente per definire tabelle esterne che utilizzano origini dati esterne di tipo RDBMS. Una definizione di tabelle esterne per partizione verticale copre gli aspetti seguenti: 

* **Schema**: la tabella esterna DDL consente di definire uno schema che è possono utilizzare le query. Schema specificato nella definizione della tabella esterna deve corrispondere lo schema delle tabelle del database remoto memorizzazione dei dati effettivi. 

* **Guida di riferimento database remoto**: la tabella esterna DDL fa riferimento a un'origine dati esterna. L'origine dati esterna specifica il nome del server logico e il nome del database del database remoto in cui sono archiviati dati della tabella effettiva. 

Utilizzando un'origine dati esterna, come descritto nella sezione precedente, la sintassi per creare tabelle esterne è come indicato di seguito: 

La clausola DATA_SOURCE definisce l'origine dati esterna (ad esempio il database remoto in caso di partizione verticale) utilizzato per la tabella esterna.  

Le clausole nome_schema e nome_oggetto offrono la possibilità di eseguire il mapping la definizione di tabelle esterne in una tabella in un altro schema nel database remoto o a una tabella con un nome diverso, rispettivamente. Ciò è utile se si desidera definire una tabella esterna per una visualizzazione del catalogo o DMV per il database remoto – o qualsiasi altra situazione in cui il nome della tabella remoto è già utilizzato in locale.  

L'istruzione DDL seguente elimina una definizione di tabelle esterne esistente dal catalogo locale. Non influisce sul database remoto. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Le autorizzazioni per creare/rilascio di tabelle esterne**: modificare qualsiasi origine dati esterna a autorizzazioni sono necessarie per tabella esterna DDL che è necessario anche per fare riferimento all'origine dati sottostante.  

## <a name="security-considerations"></a>Considerazioni sulla protezione
Gli utenti hanno accesso a tabelle esterne accedere automaticamente alle tabelle remote sottostanti sotto le credenziali fornite nella definizione origine dati esterna. Gestire l'accesso alla tabella esterna con attenzione per evitare indesiderati elevazione dei privilegi tramite le credenziali dell'origine dati esterna. Regolare autorizzazioni SQL utilizzabili per concedere o revocare l'accesso a una tabella esterna come se si trattasse di una tabella normale.  


## <a name="example-querying-vertically-partitioned-databases"></a>Esempio: esecuzione di query verticalmente suddiviso database 

La query seguente esegue un join di tre elementi tra due tabelle locali per gli ordini e le righe dell'ordine e la tabella remota per i clienti. Questo è un esempio del caso di utilizzo di dati di riferimento per query flessibile: 

    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100


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

È possibile utilizzare normali stringhe di connessione di SQL Server a cui connettersi database nel server di database SQL che dispone di query flessibile abilitato e tabelle esterne definite strumenti di integrazione di Business Intelligence e i dati. Assicurarsi che sia supportato SQL Server come origine dati per lo strumento. Fare riferimento al database di query flessibile e le relative tabelle esterne come qualsiasi altro database di SQL Server che si connette a con lo strumento. 

## <a name="best-practices"></a>Procedure consigliate 
 
* Assicurarsi che il database di endpoint di query flessibile è consentito l'accesso al database remoto per consentire l'accesso per i servizi di Azure nella configurazione firewall DB SQL. Anche assicurarsi che le credenziali fornite nella definizione origine dati esterni possano accedere senza problemi nel database remoto e disponga delle autorizzazioni per accedere alla tabella remota.  

* Query flessibile è ideale per le query in cui la maggior parte del calcolo è possibile eseguire i database remoti. Vengono in genere le massime prestazioni di query con predicati del filtro selettiva che può essere valutata nel database remoti o join che possono essere eseguite completamente il database remoto. Altri modelli di query potrebbero essere necessario caricare grandi quantità di dati del database remoto e possono eseguire in modo non corretto. 


## <a name="next-steps"></a>Passaggi successivi

Per eseguire query di database partizioni orizzontali (anche detto database sharded), vedere [query su database cloud sharded (orizzontalmente suddivisi)](sql-database-elastic-query-horizontal-partitioning.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
