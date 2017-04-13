<properties 
    pageTitle="Creare o spostare un database SQL Azure in un pool flessibile mediante T-SQL | Microsoft Azure" 
    description="Consente di creare un database SQL Azure in un pool flessibile T-SQL. Oppure utilizzare Transact-SQL per spostare la datbase e disconnettersi pool." 
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Monitorare e gestire un pool di database flessibile con Transact-SQL  

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Utilizzare i comandi [Crea Database (Database di SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Modificare Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) per creare e spostare database e in uscita pool flessibile. Pool flessibile deve esistere prima di poter utilizzare questi comandi. Questi comandi si applicano solo i database. Creazione di nuovi pool e l'impostazione della proprietà del pool (ad esempio eDTUs min e max) non è cambiati con comandi T-SQL.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Creare un nuovo database in un pool di flessibile
Usare il comando Crea DATABASE con l'opzione SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Tutti i database in un pool di flessibile ereditano il livello di servizio del pool di flessibile (Basic, Standard, Premium). 


## <a name="move-a-database-between-elastic-pools"></a>Spostare un database tra i pool flessibile
Utilizzare il comando ALTER DATABASE con la modifica e impostare servizio\_opzione assuma come elastici\_POOL; impostare il nome per il nome del pool di destinazione.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool flessibile 
Utilizzare il comando ALTER DATABASE con la modifica e impostare servizio\_opzione assuma come ELASTIC_POOL; impostare il nome per il nome del pool di destinazione.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Spostare un database da un pool flessibile
Utilizzare il comando ALTER DATABASE e impostare il SERVICE_OBJECTIVE a uno dei livelli di prestazioni (S0, S1 e così via).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Elenca i database in un pool di flessibile
Utilizzare la [sys.database\_servizio \_visualizzazione obiettivi](https://msdn.microsoft.com/library/mt712619) per visualizzare un elenco di tutti i database in un pool di flessibile. Accedere allo schema database per la visualizzazione della query.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Recuperare dati di utilizzo delle risorse per un pool

Utilizzare la [sys.elastic\_pool \_risorse \_visualizzazione Stat](https://msdn.microsoft.com/library/mt280062.aspx) per esaminare le statistiche sull'utilizzo delle risorse di un pool flessibile su un server logico. Accedere allo schema database per la visualizzazione della query.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Ottenere l'uso delle risorse per un database flessibile

Usare il [sys.dm\_ db\_ risorse\_visualizzazione Stat](https://msdn.microsoft.com/library/dn800981.aspx) o [sys.resource \_visualizzazione Stat](https://msdn.microsoft.com/library/dn269979.aspx) per esaminare le statistiche sull'utilizzo delle risorse di un database in un pool di flessibile. Questo processo è simile alla query Uso risorse per ogni singolo database.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un pool di database flessibile, è possibile gestire i database flessibile nel pool di mediante la creazione di processi flessibile. Processi flessibile facilitano l'esecuzione script T-SQL in base a qualsiasi numero di database nel pool. Per ulteriori informazioni, vedere [Panoramica di processi di database flessibile](sql-database-elastic-jobs-overview.md). 

Vedere [proporzioni fuori con Database di SQL Azure](sql-database-elastic-scale-introduction.md): utilizzare strumenti database flessibile per scalabilità, spostare i dati della query o creare le transazioni.
