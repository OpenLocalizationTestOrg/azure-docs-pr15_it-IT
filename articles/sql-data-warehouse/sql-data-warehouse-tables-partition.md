<properties
   pageTitle="Partizione tabelle SQL Data Warehouse | Microsoft Azure"
   description="Guida introduttiva partizione della tabella Data warehouse di SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partizione tabelle SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuire][]
- [Indice][]
- [Partizione][]
- [Statistiche][]
- [Temporaneo][]

Partizione è supportato in tutti i tipi di tabella di SQL Data Warehouse; inclusi columnstore raggruppate, indice cluster e heap.  Partizione è supportata anche in tutti i tipi di distribuzione, ad esempio hash o circolari distribuito.  Partizione consente di dividere i dati in gruppi più piccoli dei dati e nella maggior parte dei casi, partizione viene effettuati su una colonna di date.

## <a name="benefits-of-partitioning"></a>Vantaggi delle partizioni

Partizione possono trarre vantaggio le prestazioni di manutenzione e query di dati.  Se dei vantaggi entrambi o solo uno dipende dalla modalità di caricamento di dati e se possibile utilizzare la stessa colonna per entrambi gli scopi, poiché partizione può essere eseguita solo in una colonna.

### <a name="benefits-to-loads"></a>Vantaggi caricamento

Il vantaggio principale delle partizioni SQL Data warehouse è migliorare l'efficienza e le prestazioni di caricamento dati dall'uso dell'eliminazione partizione, il passaggio e l'unione.  Nella maggior parte dei casi dati sono suddiviso in una colonna di data che è strettamente collegata alla sequenza che i dati vengono caricati nel database.  Uno dei principali vantaggi dell'utilizzo di partizioni per mantenere i dati evitare registrazione delle transazioni.  Mentre è sufficiente l'inserimento, aggiornamento o eliminazione dei dati può essere l'approccio più semplice, con un piccolo pensiero e fatica, utilizzando partizioni durante il processo di caricamento in modo sostanziale le prestazioni.

Cambio di partizione è utilizzabile per rimuovere o sostituire una sezione di una tabella rapidamente.  Ad esempio, una tabella dei fatti vendite può contenere solo i dati degli ultimi 36 mesi.  Alla fine di ogni mese, il mese di dati di vendita meno recente viene eliminato dalla tabella.  Questi dati possono essere eliminati tramite un'istruzione delete per eliminare i dati per il mese meno recente.  Tuttavia, l'eliminazione di una grande quantità di dati da righe con un'istruzione delete può richiedere molto tempo, nonché creare il rischio di transazioni di grandi dimensioni che potrebbe richiedere molto tempo per eseguire il ripristino in caso di errori.  Un approccio più ottimale consiste nel è sufficiente eliminare la partizione meno recente di dati.  Eliminazione delle singole righe in cui potrebbe richiedere ore, l'eliminazione di un'intera partizione potrebbe richiedere secondi.

### <a name="benefits-to-queries"></a>Vantaggi alle query

Partizione può anche essere utilizzato per migliorare le prestazioni delle query.  Se una query si applica un filtro in una colonna partizionata, questa operazione possibile limitare l'elemento digitalizzato a solo le partizioni idonei che possono essere dei dati, come evitare un'analisi della tabella completo ridotto.  Con l'introduzione degli indici columnstore raggruppate, le prestazioni di eliminazione predicati sono meno utili, ma in alcuni casi può essere dei vantaggi alle query.  Ad esempio, se la tabella dei fatti vendite suddiviso in 36 mesi utilizzando il campo Data di vendita e quindi una query che filtra alla data di vendita possibile ignorare la ricerca in partizioni che non corrispondono al filtro.

## <a name="partition-sizing-guidance"></a>Guida di ridimensionamento partizione

Mentre partizione possono essere utilizzati per migliorare le prestazioni alcuni scenari, creare una tabella con le partizioni **troppi** può influire negativamente sulle prestazioni in alcuni casi.  Questi problemi sono particolarmente veri per le tabelle columnstore raggruppate.  Per partizione per essere utile, è importante tenere presente quando utilizzare partizioni e il numero delle partizioni da creare.  Nessuna regola rapida difficili da quante partizioni sono troppi, dipende i dati e il numero di partizioni vengono caricati a contemporaneamente.  Ma come un generale, dell'aggiunta 10s a 100s delle partizioni, non 1000s.

Quando si creano partizioni nelle tabelle **columnstore raggruppate** , è importante tenere presente il numero di righe verrà inserita in ogni partizione.  Per la compressione ottimale e prestazioni delle tabelle columnstore raggruppate, è necessario un minimo di 1 milione di righe per partizione e distribuzione.  Prima che vengano create partizioni, SQL Data Warehouse divide già tutte le tabelle in 60 database distribuiti.  Qualsiasi partizione aggiunta a una tabella è oltre alle distribuzioni create in background.  Utilizzare questo esempio, se la tabella dei fatti di vendita contenuti 36 partizioni mensile e dato che SQL Data Warehouse include 60 distribuzioni, quindi nella tabella dei fatti di vendita deve contenere 60 milioni di righe al mese o 2,1 miliardi quando vengono inseriti tutti i mesi.  Se una tabella contiene righe in modo significativo inferiore a quello il numero minimo di righe per partizione consigliato, è preferibile partizioni meno per aumentare il numero di righe per partizione.  Vedere anche l'articolo di[indice] di [indicizzazione]che include le query che possono essere eseguite su SQL Data Warehouse per valutare la qualità degli indici columnstore cluster.

## <a name="syntax-difference-from-sql-server"></a>Differenza di sintassi da SQL Server

SQL Data Warehouse introduce una definizione semplificata delle partizioni leggermente diversa da SQL Server.  Partizioni funzioni e gli schemi non vengono utilizzati in SQL Data Warehouse come in SQL Server.  Se, tuttavia, è sufficiente è identificare partizionata colonna e i punti di bordo.  La sintassi delle partizioni potrebbe essere leggermente diversa da SQL Server, i concetti di base sono le stesse.  SQL Server e SQL Data Warehouse supporta una colonna di partizione per tabella, che può essere partizione nell'intervallo.  Per ulteriori informazioni sulle partizioni, vedere [partizioni tabelle e indici][].

L'esempio seguente di un'istruzione SQL Data Warehouse suddiviso [creazione tabella][] , la tabella FactInternetSales sulla colonna OrderDateKey partizioni:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Eseguire la migrazione partizioni da SQL Server

Per eseguire la migrazione le definizioni di partizione di SQL Server SQL Data warehouse semplicemente:

- Eliminare la [schema di partizione][]di SQL Server.
- Aggiungere la definizione di [funzione partition][] alla tabella creare.

Se si esegue la migrazione di una tabella partizionata da un'istanza di SQL Server la sotto SQL può essere utile per analizzare il numero di righe presenti in ogni partizione.  Tenere presente che se la stessa granularità partizione viene utilizzata in SQL Data Warehouse, il numero di righe per partizione ridurrà di un fattore di 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gestione di carico di lavoro

Una considerazione finale tenere in considerazione per la decisione partizione tabella è [del carico di lavoro][].  Gestione di carico di lavoro in SQL Data Warehouse è principalmente la gestione di memoria e concorrenza.  In SQL Data Warehouse la memoria massima allocata per ogni distribuzione durante l'esecuzione di query è le classi di risorse gestite.  Ideale delle partizioni verranno ridimensionate in considerazione altri fattori quali le esigenze di memoria della creazione di indici columnstore raggruppate.  Raggruppate columnstore indici vantaggio notevolmente quando viene assegnata memoria.  Verrà pertanto si desidera assicurarsi che la ricostruzione dell'indice una partizione non è insufficiente di memoria. Aumentare la quantità di memoria disponibile per la query è possibile ottenere passando dal ruolo predefinito, smallrc, a uno degli altri ruoli, ad esempio largerc.

Informazioni sull'assegnazione di memoria per distribuzione sono disponibile eseguendo le viste a gestione dinamica gestore delle risorse. In realtà la concessione di memoria sarà minore di cifre riportata di seguito. Tuttavia, in questo modo un livello di indicazioni utilizzato durante il ridimensionamento delle partizioni per le operazioni di gestione dati.  Tentare di evitare che il ridimensionamento delle partizioni oltre la concessione di memoria disponibili per la classe di risorse molto grandi. Se le partizioni crescita in questa figura si corre il rischio pressione della memoria che a sua conduce ad minore compressione ottimale.

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Cambio di partizione

SQL Data Warehouse supporta partizione divisione, l'unione e passaggio. Ognuna di queste funzioni è excuted con l'istruzione [ALTER TABLE][] .

Per cambiare le partizioni tra due tabelle è necessario assicurarsi che le partizioni allineare ai loro rispettivi limiti e che corrispondono alle definizioni delle tabelle. Come vincoli di controllo non sono disponibili per applicare l'intervallo di valori in una tabella nella tabella di origine deve contenere gli stessi limiti partizione come tabella di destinazione. In questo caso non cambia partizione riuscirà come i metadati partizione non verranno sincronizzati.

### <a name="how-to-split-a-partition-that-contains-data"></a>Come dividere una partizione che contiene dati

Il modo più efficace per dividere una partizione che contiene già dati consiste nell'utilizzare un `CTAS` istruzione. Se la tabella partizionata è un cluster columnstore quindi partizione tabella deve essere vuota prima che possa essere suddiviso.

Tabella di seguito sono esempio columnstore partizionata contenente una riga in ogni partizione:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] Creando l'oggetto statistico è verificare che i metadati della tabella sono più preciso. Se si omette la creazione di statistiche, SQL Data Warehouse utilizzerà i valori predefiniti. Per informazioni dettagliate sulle statistiche esaminare [le statistiche][].

Possiamo quindi eseguire una query per il numero di riga utilizzando i `sys.partitions` visualizzazione catalogo:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Se si tenta di dividere in questa tabella, si verificherà un errore:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Level 15 stato 1, riga 44 divisa clausola dell'istruzione ALTER partizione non riuscita perché la partizione non è vuota.  Solo partizioni vuote possono essere suddivise presenza di un indice columnstore della tabella. È consigliabile disabilitare l'indice columnstore prima istruzione ALTER partizione e quindi ricostruire l'indice columnstore al termine partizione modificare.

Tuttavia, è possibile utilizzare `CTAS` per creare una nuova tabella per contenere i dati.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Come vengono allineati dei limiti delle partizioni è consentito un parametro. Tale etichetta definisce la tabella di origine con una partizione vuota che è possibile successivamente divisa.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tutti gli elementi ancora da svolgere sia per allineare i dati al nuovo dei limiti delle partizioni con `CTAS` e cambia nuovamente i dati alla tabella principale

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Dopo aver completato lo spostamento dei dati è utile anche per aggiornare le statistiche nella tabella di destinazione per assicurarsi che riflettano accuratamente nuova lista di distribuzione dei dati nel loro rispettivi partizioni:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabella suddivisione del controllo origine

Per evitare la definizione di una tabella da **rusting** nel sistema di controllo di origine è consigliabile prendere in considerazione la procedura seguente:

1. Creare la tabella come una tabella partizionata ma senza valori partizione

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`la tabella come parte del processo di distribuzione:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Con questo approccio il codice sorgente rimane in statico e i valori limite partizioni possono essere dinamico; in evoluzione con il warehouse nel tempo.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli nella [Tabella Panoramica][Panoramica]di [Tipi di dati di tabella][Tipi di dati], [la distribuzione di una tabella][Distribuisci], [l'indicizzazione di una tabella di][indice], [Mantenendo le statistiche delle tabelle][statistiche] e [Tabelle temporanee][temporaneo].  Per ulteriori informazioni sulle procedure consigliate, vedere [SQL dati Warehouse procedure consigliate][].

<!--Image references-->

<!--Article references-->
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribuire]: ./sql-data-warehouse-tables-distribute.md
[Indice]: ./sql-data-warehouse-tables-index.md
[Partizione]: ./sql-data-warehouse-tables-partition.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporaneo]: ./sql-data-warehouse-tables-temporary.md
[gestione di carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[Procedure consigliate Warehouse dati SQL]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Gli indici e tabelle partizionate]: https://msdn.microsoft.com/library/ms190787.aspx
[ISTRUZIONE ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREA TABELLA]: https://msdn.microsoft.com/library/mt203953.aspx
[funzione Partition]: https://msdn.microsoft.com/library/ms187802.aspx
[schema di partizione]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
