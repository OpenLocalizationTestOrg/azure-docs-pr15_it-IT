<properties
   pageTitle="Monitorare il carico di lavoro utilizzando viste | Microsoft Azure"
   description="Informazioni su come controllare il carico di lavoro utilizzando viste."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>Monitorare il carico di lavoro utilizzando viste

In questo articolo viene descritto come utilizzare dinamico Gestione viste per monitorare il carico di lavoro e provare a utilizzare esecuzione della query in Data Warehouse di SQL Azure.

## <a name="permissions"></a>Autorizzazioni

Per eseguire una query di viste in questo articolo, è necessario dell'autorizzazione visualizzazione dello stato di DATABASE o controllo. In genere la concessione del DATABASE è stato di visualizzazione dell'autorizzazione preferito in quanto è molto più restrittivo.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Monitoraggio delle connessioni

Tutti gli accessi SQL Data warehouse sono registrati in [sys.dm_pdw_exec_sessions][].  Questa DMV contiene ultimo 10.000 agli account di accesso.  L'id_sessione è la chiave primaria e viene assegnato in sequenza per ogni nuovo accesso.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Esecuzione di query monitor

Tutte le query eseguite su SQL Data Warehouse sono registrate in [sys.dm_pdw_exec_requests][].  Questa DMV contiene le ultime 10.000 query eseguite.  Il request_id in modo univoco identifica ogni query ed è la chiave primaria per questa DMV.  Il request_id assegnate consecutivamente per ogni nuova query e preceduto da QID, l'acronimo di ID di query.  Query questa DMV per un dato ID_sessione Mostra tutte le query per un accesso specificato.

>[AZURE.NOTE] Stored procedure utilizzano più ID richiesta.  Richiedere che gli ID vengono assegnati in sequenza. 

Ecco i passaggi da seguire per verificare se sono i piani di esecuzione query e le ore per una particolare query.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>PASSAGGIO 1: Identificare la query che si desidera esaminare

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Nei risultati della query precedente, **annotare l'ID richiesta** della query che si desidera esaminare.

Query nello stato **sospeso** vengono vengono accodate a causa di limiti di concorrenza. Tali query vengono visualizzate anche nella query di attesa sys.dm_pdw_waits con un tipo di UserConcurrencyResourceType. Per ulteriori informazioni sui limiti di concorrenza, vedere [gestione della concorrenza e il carico di lavoro][] . Query possono anche attendere che altri motivi ad esempio per i blocchi di oggetto.  Se la query è in attesa di una risorsa, vedere [query Investigating in attesa di risorse][] più in basso in questo articolo.

Per semplificare la ricerca di una query nella tabella sys.dm_pdw_exec_requests, utilizzare [etichetta][] per assegnare un commento alla query che può essere ricercata nella visualizzazione sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>PASSAGGIO 2: Esaminare il piano di query

Utilizzare l'ID richiesta per recuperare piano SQL (DSQL) distribuito della query da [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Quando un piano DSQL richiede più tempo del previsto, la causa può essere un piano complesso con numerosi passaggi DSQL o solo un passaggio impiegando molto tempo.  Se il piano non è più passaggi con diverse operazioni di spostamento, è consigliabile ottimizzare le distribuzioni di tabella per ridurre lo spostamento dei dati. L'articolo [distribuzione tabella][] spiega perché dati devono essere spostati in risolvere una query e vengono illustrate alcune strategie di distribuzione per ridurre al minimo lo spostamento dei dati.

Analizzare ulteriormente i dettagli di un unico passaggio, la colonna *operation_type* del passaggio di query lunga e prendere nota dell' **Indice passo**:

- Procedere con passaggio 3a per **le operazioni di SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Procedere con 3 b passaggio per **le operazioni di spostamento dei dati**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>PASSAGGIO 3: esaminare SQL database distribuito

Utilizzare l'ID richiesta e l'indice passo per recuperare i dettagli da [sys.dm_pdw_sql_requests][], che contiene informazioni di esecuzione del passaggio query su tutti i database distribuiti.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Quando viene eseguito il passaggio di query, [DBCC PDW_SHOWEXECUTIONPLAN][] possono essere utilizzati per recuperare il piano stimato di SQL Server dalla cache di piano di SQL Server per la fase di esecuzione in una distribuzione particolare.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>PASSAGGIO 3 b: esaminare spostamento dei dati nel database distribuiti

Usare l'ID richiesta e l'indice passo per recuperare informazioni su un passaggio di spostamento dei dati in esecuzione su ogni distribuzione della [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Selezionare la colonna *total_elapsed_time* per verificare se una distribuzione particolare sta impiegando molto più di altri utenti per lo spostamento dei dati.
- Per la distribuzione di esecuzione prolungata, selezionare la colonna *rows_processed* per verificare se il numero di righe viene spostato da tale distribuzione è significativamente maggiore di altri utenti. In caso affermativo, è possibile asimmetria dei dati sottostanti.

Se si esegue la query, [DBCC PDW_SHOWEXECUTIONPLAN][] possono essere utilizzati per recuperare piano stimato di SQL Server dalla cache di piano di SQL Server per l'istruzione SQL attualmente in esecuzione all'interno di una distribuzione particolare.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Eseguire il monitoraggio delle query in attesa

Se si scopre che la query non è in corso lo stato di avanzamento perché è in attesa di una risorsa, ecco una query che visualizza tutte le risorse di che una query è in attesa.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Se la query è attivamente in attesa di risorse da un'altra query, lo stato sarà **AcquireResources**.  Se la query include tutte le risorse necessarie, lo stato sarà **consentito**.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle viste, vedere [visualizzazioni di sistema][] .
Per ulteriori informazioni sulle procedure consigliate, vedere [procedure consigliate SQL Data Warehouse][]

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[Procedure consigliate SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Visualizzazioni di sistema]: ./sql-data-warehouse-reference-tsql-system-views.md
[Distribuzione di tabelle]: ./sql-data-warehouse-tables-distribute.md
[Gestione della concorrenza e il carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[In analisi di query in attesa di risorse]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[Sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[Sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[Sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[Sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[Sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[ETICHETTA]: https://msdn.microsoft.com/library/ms190322.aspx
