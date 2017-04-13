<properties
   pageTitle="Monitoraggio Database SQL Azure usando le visualizzazioni di gestione dinamica | Microsoft Azure"
   description="Informazioni su come rilevare e diagnosticare i problemi di prestazioni utilizzando le visualizzazioni di gestione dinamica per monitorare Database SQL di Microsoft Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Database di SQL Azure monitoraggio usando le visualizzazioni di gestione dinamica

Database SQL di Microsoft Azure consente un sottoinsieme di viste a gestione dinamica di diagnosticare i problemi di prestazioni, potrebbero essere causati da query bloccate o lunga, bottiglia delle risorse, piani insufficiente della query e così via. In questo argomento vengono fornite informazioni su come rilevare i comuni problemi di prestazioni utilizzando le visualizzazioni di gestione dinamica.

Database SQL parzialmente supporta tre categorie di viste a gestione dinamica:

- Viste a gestione dinamica correlati al database.
- Visualizzazioni per l'esecuzione di gestione dinamica.
- Viste a gestione dinamica transazione correlata.

Per informazioni dettagliate sulle viste a gestione dinamica, vedere [funzioni (Transact-SQL) e viste a gestione dinamica](https://msdn.microsoft.com/library/ms188754.aspx) nella documentazione Online di SQL Server.

## <a name="permissions"></a>Autorizzazioni

Nel Database di SQL e query su una vista di gestione dinamica richiede le autorizzazioni di **Stato di visualizzazione del DATABASE** . L'autorizzazione **VIEW DATABASE STATE** restituisce informazioni relative a tutti gli oggetti nel database corrente.
Per concedere l'autorizzazione di **Visualizzazione dello stato di DATABASE** a un utente di database specifico, eseguire la query seguente:

```GRANT VIEW DATABASE STATE TO database_user; ```

In un'istanza di SQL Server locale, viste a gestione dinamica restituiscono le informazioni sullo stato di server. Nel Database di SQL, restituiscono informazioni relative a solo il database corrente logico.

## <a name="calculating-database-size"></a>Il calcolo delle dimensioni del database

La query seguente restituisce le dimensioni del database (in megabyte):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

La query seguente restituisce le dimensioni dei singoli oggetti (in megabyte) nel database:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Monitoraggio delle connessioni

È possibile utilizzare la visualizzazione [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) per recuperare le informazioni sulle connessioni a un server di Database SQL Azure specifico e i dettagli di ogni connessione. Inoltre, la visualizzazione [exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) è utile quando si recuperano informazioni su tutte le connessioni utente attive e attività interna.
La query seguente recupera informazioni sulla connessione corrente:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Quando si esegue la **sys.dm_exec_requests** e **visualizzazioni exec_sessions**, se si dispone dell'autorizzazione di **Visualizzazione dello stato di DATABASE** nel database, viene visualizzato tutto in esecuzione sessioni nel database. in caso contrario, viene visualizzato solo la sessione corrente.

## <a name="monitoring-query-performance"></a>Monitoraggio delle prestazioni delle query

Lenta o tempo esecuzione di query, è possibile utilizzare risorse di sistema significativa. In questa sezione viene illustrato come utilizzare le visualizzazioni di gestione dinamica per rilevare alcuni problemi di prestazioni di query comuni. Riferimento a un precedente ma comunque utile per la risoluzione dei problemi, è l'articolo di [Risoluzione dei problemi di prestazioni in SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) su Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Ricerca query primi N

Nell'esempio seguente restituisce informazioni sul prime cinque query in base a tempo medio CPU. In questo esempio aggrega le query in base alle loro hashing query in modo che query in modo logico equivalenti raggruppate in base consumo risorsa cumulativo.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Monitorare le query bloccate

Le query lente o lunga possono contribuire a consumo eccessivo delle risorse e dovuto alla query bloccate. La causa del bloccaggio può essere struttura delle applicazioni scadente, piani di query non valido, la mancanza di indici utili e così via. È possibile utilizzare la visualizzazione in dm_tran_locks per ottenere informazioni sull'attività blocco corrente nel Database di SQL Azure. Ad esempio codice, vedere [in dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) nella documentazione Online di SQL Server.

### <a name="monitoring-query-plans"></a>Monitoraggio dei piani di query

Un piano di query inefficace anche aumentare della CPU. Nell'esempio seguente usa la visualizzazione [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) per determinare quali query utilizza la CPU più cumulativa.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Vedere anche

[Introduzione al Database SQL](sql-database-technical-overview.md)
