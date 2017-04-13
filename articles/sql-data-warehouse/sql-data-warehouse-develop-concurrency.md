<properties
   pageTitle="Gestione della concorrenza e il carico di lavoro in SQL Data Warehouse | Microsoft Azure"
   description="Comprendere la gestione della concorrenza e carico di lavoro in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gestione della concorrenza e il carico di lavoro in SQL Data Warehouse

Per offrire prestazioni prevedibili proporzioni, consente di Microsoft Azure SQL Data Warehouse è possibile controllare i livelli di concorrenza e assegnazioni di risorse come definizione delle priorità CPU e memoria. In questo articolo viene presentato i concetti di gestione della concorrenza e il carico di lavoro, che spiega come entrambe le caratteristiche sono stati condotti e come è possibile controllare la data warehouse. Gestione di carico di lavoro SQL Data Warehouse consentono di supportare gli ambienti multiutente. Non è destinato per carichi di lavoro multi-tenant.

## <a name="concurrency-limits"></a>Limiti di concorrenza

SQL Data Warehouse consente a un massimo di 1.024 connessioni simultanee. Tutte le 1.024 connessioni possono inviare query contemporaneamente. Tuttavia, per ottimizzare la velocità effettiva, SQL Data Warehouse può accodare alcune query per garantire che ogni query riceva una concessione di memoria minima. Accodamento si verifica in fase di esecuzione query. Per le query di accodamento quando vengono raggiunti i limiti di concorrenza, SQL Data Warehouse possono aumentare prestazioni più elevate query attive garantire l'accesso alle risorse di memoria sono necessarie.  

Limiti di concorrenza dipendono da due concetti: *query simultanee* e *bande orarie concorrenza*. Per una query da eseguire, è necessario eseguire all'interno il limite di concorrenza query e l'assegnazione delle bande orarie concorrenza.

- Query concorrenti sono query esecuzione nello stesso momento. SQL Data Warehouse supporta fino a 32 query simultanee in dimensioni DWU più grandi.
- Bande orarie concorrenza sono assegnate in base a DWU. Ogni DWU 100 fornisce 4 bande orarie concorrenza. Ad esempio un DW100 assegna 4 bande orarie concorrenza e DW1000 assegna 40. Ogni query implica l'uso di uno o più concorrenza bande orarie, dipende dalla [classe di risorse](#resource-classes) della query. Query che esegue nella classe di risorsa smallrc utilizzano un intervallo aperto concorrenza. Query in esecuzione in una classe di risorse superiore utilizzare ulteriori spazi concorrenza.

Nella tabella seguente descrive i limiti per la query simultanee e bande orarie concorrenza varie dimensioni DWU.

### <a name="concurrency-limits"></a>Limiti di concorrenza

|  DWU   | Query simultanee max  | Bande orarie concorrenza assegnate |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

Quando viene soddisfatta una di queste soglie, nuove query vengono accodate ed eseguita su base First Out first-in.  Come termine di una query e il numero di query e bande orarie supera i limiti, vengono rilasciate in coda query. 

> [AZURE.NOTE]  Query *Selezionare* esecuzione esclusivamente in Gestione dinamica viste o viste del catalogo non dipendono da uno qualsiasi dei limiti di concorrenza. È possibile monitorare il sistema indipendentemente dal numero di query in esecuzione su di esso.

## <a name="resource-classes"></a>Classi di risorse

Risorsa classi consentono di che gestire l'allocazione della memoria e cicli assegnati a una query. È possibile assegnare quattro classi di risorse a un utente in forma di *ruoli del database*. Le classi quattro risorse sono **smallrc**, **mediumrc**, **largerc**e **xlargerc**. Gli utenti in smallrc sono indicati una quantità inferiore di memoria e sfruttarne concorrenza maggiore. Invece gli utenti assegnati a xlargerc sono indicati grandi quantità di memoria e pertanto meno le query possono essere eseguiti contemporaneamente.

Per impostazione predefinita, ogni utente è un membro della classe resource small, smallrc. La procedura `sp_addrolemember` viene utilizzato per migliorare la classe di risorse e `sp_droprolemember` utilizzato per ridurre la classe di risorse. Ad esempio, questo comando aumenterebbe la classe di risorse di loaduser a largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Buona norma consiste nel definitivamente assegnare agli utenti per una classe di risorse anziché modificare le classi delle risorse. Carica alle tabelle columnstore raggruppate, ad esempio, crea indici di migliore qualità quando più di memoria. Per garantire che carica abbiano accesso alla memoria superiore, creare un utente specificamente per il caricamento dei dati e assegnare definitivamente l'utente a una classe di risorse superiore.

Esistono alcuni tipi di query che non trarre vantaggio da una maggiore allocazione di memoria. Il sistema ignorerà la loro allocazione di classe di risorse e sempre eseguire invece queste query nella classe small delle risorse. Se queste query eseguire sempre in classe di risorse small, possono essere eseguiti quando bande orarie concorrenza sono sotto pressione e non è possibile utilizzare ulteriori spazi quella necessaria. Per ulteriori informazioni, vedere [eccezioni di classe di risorse](#query-exceptions-to-concurrency-limits) .

Alcuni altri dettagli sulla classe di risorsa:

- Per modificare la classe di risorse di un utente, è necessario *modificare ruolo* autorizzazione.  
- Sebbene sia possibile aggiungere un utente a uno o più delle categorie risorsa superiori, gli utenti avranno gli attributi di classe di risorse più alta a cui sono state assegnate. Ovvero se un utente viene assegnato a mediumrc e largerc, la classe di risorse superiore (largerc) è la classe di risorse che verrà utilizzato il valore.  
- Non è possibile modificare la classe di risorse dell'utente di amministratore di sistema.

Per un esempio dettagliato, vedere [Modifica utente risorsa classe esempio](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Allocazione della memoria

Esistono vantaggi e svantaggi per aumentare la classe di risorse dell'utente. Aumentando una classe di risorse per un utente per ottenere il loro accesso query a più memoria, che potrebbe impedire query eseguire più rapidamente.  Tuttavia, categorie risorsa superiori anche riducono il numero di query concorrenti che è possibile eseguire. Si tratta compromesso tra l'allocazione di grandi quantità di memoria per una singola query o consentire altre query che necessitano anche allocazioni di memoria, per eseguire contemporaneamente. Se un utente viene assegnato elevate allocazioni della memoria per una query, gli altri utenti non avranno accesso alla stessa memoria per eseguire una query.

Nella tabella seguente associa la memoria allocata per ogni distribuzione dalla classe DWU e delle risorse.

### <a name="memory-allocations-per-distribution-mb"></a>Allocazioni di memoria per distribuzione (MB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1.600   |
| DW500  |   100   |    400   |    800  |  1.600   |
| DW600  |   100   |    400   |    800  |  1.600   |
| DW1000 |   100   |    800   |  1.600  |  3.200   |
| DW1200 |   100   |    800   |  1.600  |  3.200   |
| DW1500 |   100   |    800   |  1.600  |  3.200   |
| DW2000 |   100   |  1.600   |  3.200  |  6.400   |
| DW3000 |   100   |  1.600   |  3.200  |  6.400   |
| DW6000 |   100   |  3.200   |  6.400  |  12,800  |

Nella tabella precedente, è possibile vedere che una query in esecuzione su DW2000 nella classe di risorsa xlargerc avrebbe accesso a 6.400 MB di memoria all'interno di ogni i 60 database distribuiti.  In SQL Data Warehouse, esistono 60 distribuzioni. Pertanto, per calcolare l'allocazione di memoria totale per una query in una classe determinata risorsa, i valori indicati devono moltiplicati per 60.

### <a name="memory-allocations-system-wide-gb"></a>Memoria allocazioni sistema (GB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

Da questa tabella allocazioni della memoria di sistema, è possibile vedere che una query in esecuzione su DW2000 nella classe di risorsa xlargerc allocata un totale di 375 GB di memoria (distribuzioni 6.400 MB * 60 / 1.024 da convertire in GB) sopra l'intero della Warehouse dati SQL.

## <a name="concurrency-slot-consumption"></a>Consumo intervallo aperto concorrenza

SQL Data Warehouse concede memoria alle query in esecuzione in categorie risorse superiori. Memoria rappresenta una risorsa fissa.  Di conseguenza, più memoria disponibile per query, possono eseguire la query simultanee meno. Nella tabella seguente reiterates tutti i concetti precedenti in una singola visualizzazione che mostra il numero di sezioni concorrenza disponibili da DWU e le bande consumate da ogni classe di risorse.

### <a name="allocation-and-consumption-of-concurrency-slots"></a>Assegnazione e l'utilizzo di bande orarie concorrenza

|  DWU   | Numero massimo query simultanee  | Bande orarie concorrenza assegnate | Bande orarie utilizzati da smallrc |  Bande orarie utilizzati da mediumrc |  Bande orarie utilizzati da largerc |  Bande orarie utilizzati da xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


Da questa tabella, è possibile vedere che esegue SQL Data Warehouse come DW1000 assegna un massimo di 32 query simultanee e un totale di 40 bande orarie concorrenza. Se tutti gli utenti sono in esecuzione in smallrc, 32 query simultanee sarebbe consentita in quanto ogni query occupano 1 intervallo aperto concorrenza. Se tutti gli utenti di un DW1000 in esecuzione in mediumrc, ogni query da allocare 800 MB per la distribuzione per un'allocazione di memoria totale di 47 GB per ogni query e concorrenza saranno limitata a 5 utenti (40 bande orarie concorrenza / 8 slot per ogni utente mediumrc).

## <a name="query-importance"></a>Importanza di query

SQL Data Warehouse implementato le classi di risorse con i gruppi di carico di lavoro. Esistono un totale di otto gruppi di carico di lavoro che controllano il comportamento delle classi delle risorse tra i vari formati DWU. Per qualsiasi DWU SQL Data Warehouse utilizza solo quattro gruppi di otto carico di lavoro. In questo modo significativo in quanto ogni gruppo di carico di lavoro è assegnato a uno dei quattro classi di risorse: smallrc, mediumrc, largerc, o xlargerc. L'importanza di comprendere i gruppi di carico di lavoro è che alcuni di questi gruppi di carico di lavoro siano impostati superiore *priorità*. Priorità vengono usate per la CPU pianificazione. Query eseguite con priorità alta otterrà tre volte più cicli di CPU rispetto a quelle ottenute con priorità Media. Per i mapping di intervallo aperto concorrenza determinano anche priorità. Quando una query implica l'uso di 16 o più bande orarie, viene eseguito come importante.

Nella tabella seguente sono riportati i mapping di priorità per ogni gruppo di carico di lavoro.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapping di gruppo di carico di lavoro a bande orarie concorrenza e priorità

| Gruppi di carico di lavoro | Mapping di intervallo aperto concorrenza | MB / distribuzione | Mapping di priorità |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       Media       |
| SloDWGroupC01   |            2             |         200       |       Media       |
| SloDWGroupC02   |            4             |         400       |       Media       |
| SloDWGroupC03   |            8             |         800       |       Media       |
| SloDWGroupC04   |           16             |       1.600       |       Elevato         |
| SloDWGroupC05   |           32             |       3.200       |       Elevato         |
| SloDWGroupC06   |           64             |       6.400       |       Elevato         |
| SloDWGroupC07   |          128             |      12,800       |       Elevato         |

Grafico **assegnazione e l'utilizzo di bande orarie concorrenza** è possibile vedere che un DW500 utilizza 1, 4, 8 o bande orarie concorrenza 16 per smallrc, mediumrc, largerc e xlargerc, rispettivamente. È possibile cercare tali valori nel grafico precedente per trovare la priorità per ogni classe di risorse.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapping DW500 di classi delle risorse per priorità

| Classe di risorse | Gruppo di carico di lavoro | Bande orarie concorrenza utilizzati | MB / distribuzione | Importanza |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   Media   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   Media   |
| largerc        | SloDWGroupC03  |           8            |         800       |   Media   |
| xlargerc       | SloDWGroupC04  |          16            |        1.600      |   Elevato     |


È possibile utilizzare la query seguente DMV per esaminare le differenze di assegnazione delle risorse di memoria in modo dettagliato dalla prospettiva dei carichi o per analizzare attivo e cronologico l'uso dei gruppi di carico di lavoro quando la risoluzione dei problemi.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Query che rispettare i limiti di concorrenza

La maggior parte delle query dipendono dalle classi di risorse. Le query devono essere contenute entrambe le simultanee query e concorrenza intervallo aperto soglie. Un utente non è possibile scegliere di escludere una query dal modello di intervallo aperto concorrenza.

Come già detto, le istruzioni seguenti rispettano le classi di risorse:

- SELEZIONARE INSERISCI
- AGGIORNAMENTO
- ELIMINA
- Selezionare (quando si eseguono query tabelle utente)
- RICOSTRUZIONE DELL'INDICE ALTER
- ALTER INDICE RIORGANIZZA
- MODIFICA TABELLA RICOSTRUZIONE
- CREARE L'INDICE
- CREARE L'INDICE COLUMNSTORE RAGGRUPPATE
- CREARE SELEZIONARE COME TABELLA (CTAS)
- Caricamento dei dati
- Operazioni di spostamento dati condotte da dati movimento Service (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Query eccezioni a limiti di concorrenza

Alcune query che non rispettano la classe di risorse a cui è assegnata all'utente. Queste eccezioni per i limiti di concorrenza sono stata progettate quando le risorse di memoria necessari per un determinato comando sono basse, spesso perché il comando non è un'operazione di metadati. L'obiettivo di queste eccezioni consiste nell'evitare allocazioni di memoria più grandi per le query che non saranno mai necessario. In questi casi, la classe di risorse small predefinita (smallrc) viene sempre utilizzata indipendentemente dalla classe effettivo delle risorse assegnata all'utente. Ad esempio `CREATE LOGIN` eseguito sempre in smallrc. Le risorse necessarie per soddisfare questa operazione sono molto bassa, pertanto non risulta utile includere la query nel modello di intervallo aperto concorrenza.  Queste query non sono inoltre limitate per il limite di concorrenza 32 utente, un numero illimitato di tali query può eseguire il limite di 1.024 sessioni.

Le istruzioni seguenti non rispettano le classi di risorse:

- CREARE o eliminare tabella
- ISTRUZIONE ALTER TABLE... Cambia, divisione o UNIRE partizione
- DISABILITA INDICE ALTER
- ELIMINARE L'INDICE
- CREARE, aggiornare o rilasciare statistiche
- TRONCARE LA TABELLA
- AUTORIZZAZIONE DI MODIFICA
- CREARE ACCOUNT DI ACCESSO
- CREAZIONE, modifica o DROP USER
- CREAZIONE, modifica o DROP PROCEDURE
- CREARE o eliminare visualizzazione
- INSERIRE I VALORI
- Selezionare le visualizzazioni di sistema e le viste
- SPIEGARE
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>Modificare un esempio di classe di risorse utente

1. **Accesso crea:** Aprire una connessione al database **master** in SQL server che ospita i database di SQL Data Warehouse ed eseguire i comandi seguenti.

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] È consigliabile creare un utente nel database master per gli utenti di Warehouse di dati di SQL Azure. Creazione di un utente in schema consente all'utente di accesso con strumenti come SQL Server Management Studio senza specificare un nome di database.  Inoltre, consente loro di utilizzare Esplora aree di oggetto per visualizzare tutti i database su un server SQL.  Per ulteriori informazioni sulla creazione e gestione di utenti, vedere [sicura un database SQL Data warehouse][].

2. **Utente creare SQL Data Warehouse:** Aprire una connessione al database **SQL Data Warehouse** ed eseguire il comando seguente.

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **Concedere le autorizzazioni:** Nell'esempio seguente concede `CONTROL` nel database di **SQL Data Warehouse** . `CONTROL`nel database di livello è pari a db_owner in SQL Server.

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **Aumentare classe di risorse:** Utilizzare la query seguente per aggiungere un utente a un ruolo di gestione di carico di lavoro superiore.

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **Diminuire classe di risorse:** Utilizzare la query seguente per rimuovere un utente da un ruolo di gestione di carico di lavoro.

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] Non è possibile rimuovere un utente da smallrc.

## <a name="queued-query-detection-and-other-dmvs"></a>Rilevamento di query in coda e le altre viste

È possibile utilizzare il `sys.dm_pdw_exec_requests` DMV per identificare le query che sono in una coda di concorrenza. Query in attesa di spazio concorrenza avrà lo stato di **sospensione**.

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Ruoli di gestione di carico di lavoro possono essere visualizzati con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La query seguente mostra il ruolo di cui ogni utente viene assegnata a.

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse sono previsti attendere tipi le operazioni seguenti:

- **LocalQueriesConcurrencyResourceType**: query che si trovano all'esterno di framework intervallo aperto concorrenza. Query DMV e sistema funzioni come `SELECT @@VERSION` sono riportati alcuni esempi di query locale.
- **UserConcurrencyResourceType**: query che si trovano all'interno il framework degli intervallo aperto concorrenza. Query su tabelle degli utenti finali sono riportati alcuni esempi utilizzate questo tipo di risorsa.
- **DmsConcurrencyResourceType**: attesa risultante dalla operazioni di spostamento di dati.
- **BackupConcurrencyResourceType**: questo attesa indica che un database viene eseguito il backup. Il valore massimo per questo tipo di risorsa è 1. Se più copie di backup sono state richieste contemporaneamente, gli altri accoderà.

Il `sys.dm_pdw_waits` DMV possono essere usati per vedere le risorse è in attesa una richiesta.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

Il `sys.dm_pdw_resource_waits` DMV Mostra solo di attesa risorsa consumata da una query. Tempo di attesa risorsa misura solo il tempo in attesa di risorse da fornire, anziché tempo di attesa segnale, ossia il tempo che necessario per il server SQL sottostante pianificare la query in CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

Il `sys.dm_pdw_wait_stats` DMV può essere usata per analisi delle tendenze cronologici di attesa.

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come gestire la sicurezza e gli utenti del database, vedere [sicura un database SQL Data warehouse][]. Per ulteriori informazioni sulla risorsa come maggiore classi può migliorare la qualità di indice columnstore raggruppate, vedere [ricostruzione indici per migliorare la qualità di segmento].

<!--Image references-->

<!--Article references-->
[Proteggere un database SQL Data warehouse]: ./sql-data-warehouse-overview-manage-security.md
[La ricostruzione degli indici per migliorare la qualità di segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Proteggere un database SQL Data warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
