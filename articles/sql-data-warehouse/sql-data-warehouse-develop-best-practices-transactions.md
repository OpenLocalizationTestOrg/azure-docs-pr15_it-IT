<properties
   pageTitle="Ottimizzare le transazioni per SQL Data Warehouse | Microsoft Azure"
   description="Procedure consigliate su come scrivere aggiornamenti transazioni efficiente in Warehouse di dati di SQL Azure"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Ottimizzare le transazioni per SQL Data Warehouse

In questo articolo viene spiegato come ottimizzare le prestazioni del codice transazione riducendo al minimo i rischi per lunghi ripristini dello stato precedente.

## <a name="transactions-and-logging"></a>Transazioni e la registrazione

Le transazioni sono un componente importante di un motore di database relazionale. SQL Data Warehouse utilizza le transazioni durante la modifica dei dati. Queste transazioni possono essere espliciti o impliciti. Singolo `INSERT`, `UPDATE` e `DELETE` istruzioni sono tutti gli esempi delle transazioni impliciti. Transazioni esplicite vengono scritte in modo esplicito da uno sviluppatore utilizzando `BEGIN TRAN`, `COMMIT TRAN` o `ROLLBACK TRAN` e vengono in genere usate quando è necessario essere collegati in una singola unità atomica più istruzioni di modifica. 

Azure SQL Data Warehouse apporta le modifiche al database mediante i registri delle transazioni. Ogni distribuzione ha il proprio registro delle transazioni. Transazioni di scrittura è automatiche. Non esiste nessuna configurazione richiesta. Tuttavia, durante questo processo garantisce la scrittura introdurre un sovraccarico del sistema. È possibile ridurre l'impatto scrivere codice tramite transazioni specifico. Codice tramite transazioni efficiente ampiamente può essere suddiviso in due categorie.

- Sfruttare le registrazione minima costrutti laddove possibile
- Elaborazione di dati con ambito batch per evitare transazioni a esecuzione prolungata singolare
- Adottare una partizione cambio motivo per apportare modifiche di grandi dimensioni a una determinata partizione

## <a name="minimal-vs-full-logging"></a>Minimo e la registrazione completa

Diversamente da quanto succede completamente registrate operazioni, che utilizzano il registro delle transazioni per tenere traccia di tutte le modifiche di riga, operazioni a registrazione minima tenere traccia delle allocazioni misura e solo le modifiche dei metadati. Di conseguenza, registrazione minima a altro comporta l'accesso solo le informazioni che sono necessarie ripristinare la transazione in caso di un errore o di una richiesta esplicita (`ROLLBACK TRAN`). Come meno i dati vengono registrati nel registro delle transazioni, un'operazione di registrazione minima prestazioni migliori rispetto a un'operazione di registrazione completa dimensione simili. Inoltre, perché scrive meno passare il registro delle transazioni, viene generata una piccola quantità di dati del registro e pertanto i/o più efficiente.

I limiti di sicurezza transazione vengono applicate solo alle operazioni di registrazione completa.

>[AZURE.NOTE] Operazioni a registrazione minima partecipare transazioni esplicite. Come vengono rilevate tutte le revisioni nel strutture di allocazione, è possibile ripristinare operazioni a registrazione minima. È importante tenere presente che la modifica "minima" ha eseguito l'accesso non è possibile non connesso.

## <a name="minimally-logged-operations"></a>Operazioni a registrazione minima

Le operazioni seguenti sono in grado di minima è in corso la registrazione:

- CREARE selezionare come tabella ([CTAS][])
- INSERIMENTO DI.. SELEZIONARE
- CREARE L'INDICE
- RICOSTRUZIONE DELL'INDICE ALTER
- ELIMINARE L'INDICE
- TRONCARE LA TABELLA
- ELIMINA TABELLA
- PARTIZIONE DI PARAMETRO ALTER TABLE

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] Operazioni di spostamento di dati interno (ad esempio `BROADCAST` e `SHUFFLE`) non sono interessati dal limite di sicurezza transazione.

## <a name="minimal-logging-with-bulk-load"></a>Registrazione minima con caricamento di massa

`CTAS`e `INSERT...SELECT` entrambe blocco operazioni di caricamento. Tuttavia, entrambi influenzate dalla definizione di tabella di destinazione e dipendono da uno scenario di carico. Di seguito è una tabella in cui viene indicato se l'operazione di massa verrà registrato completamente o minima:  

| Indice primario               | Scenario relativo al caricamento                                            | Modalità di registrazione |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Heap                        | Qualsiasi                                                      | **Minima**  |
| Indice cluster             | Tabella di destinazione vuoto                                       | **Minima**  |
| Indice cluster             | Righe caricate non si sovrappongono con pagine esistenti nella destinazione | **Minima**  |
| Indice cluster             | Righe caricate sovrapposte con pagine esistenti nella destinazione        | Completa         |
| Indice Columnstore cluster | Batch dimensioni > = 102,400 per distribuzione allineate | **Minima**  |
| Indice Columnstore cluster | Batch dimensioni < 102,400 per distribuzione allineate  | Completa         |

È importante notare che qualsiasi scrive aggiornare secondari o non cluster indici vengono mantenuto completamente registrate.

> [AZURE.IMPORTANT] SQL Data Warehouse include 60 distribuzioni. Pertanto, presupponendo che tutte le righe sono distribuite uniformemente e posizionate in un'unica partizione, il batch dovranno contenga 6,144,000 righe pollici registrare minima durante la scrittura di un indice di Columnstore a barre raggruppate. Se la tabella è suddiviso e le righe da inserire estesi limiti della partizione, sarà necessario 6,144,000 righe per limite della partizione presupponendo anche la distribuzione dei dati. Ogni partizione di ogni distribuzione in modo indipendente deve superare il limite di 102.400 riga per l'inserimento di minima collegarsi alla distribuzione.

Caricamento di dati in una tabella non vuoto con un indice cluster spesso deve contenere una combinazione di righe di registrazione completa e registrazione minima. Un indice cluster è una struttura non bilanciata (struttura b) delle pagine. Se la pagina scritti contiene già righe da un'altra transazione, quindi questi scrive verranno completamente registrati. Tuttavia, se la pagina non contiene alcun dato quindi la scrittura a tale pagina verrà minima registrata.

## <a name="optimizing-deletes"></a>Ottimizzare le eliminazioni

`DELETE`è un'operazione su una registrazione completa.  Se è necessario eliminare una grande quantità di dati in una tabella o una partizione, è spesso opportuno più `SELECT` i dati che si desidera mantenere, che possono essere eseguiti come un'operazione di registrazione minima.  A tale scopo, creare una nuova tabella con [CTAS][].  Una volta creato, utilizzare [RINOMINA][] sostituire la vecchia tabella con la tabella appena creata.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Ottimizzazione degli aggiornamenti

`UPDATE`è un'operazione su una registrazione completa.  Se è necessario aggiornare un numero elevato di righe in una tabella o una partizione spesso può essere molto più efficace utilizzare un'operazione su una registrazione minima, ad esempio [CTAS][] per farlo.

Nell'esempio seguente una tabella completa aggiornamento è stato convertito in un `CTAS` in modo che sia possibile registrazione minima.

In questo caso posteriori si aggiungono uno sconto per le vendite nella tabella:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Ricreazione di tabelle di grandi dimensioni possono trarre vantaggio da tramite le funzionalità di gestione di SQL Data Warehouse carico di lavoro. Per ulteriori informazioni, consultare la sezione Gestione carico di lavoro nell'articolo [concorrenza][] .

## <a name="optimizing-with-partition-switching"></a>L'ottimizzazione con il passaggio partizione

Elaborare di modifiche su larga scala all'interno di una [partizione di tabella][], una partizione cambio motivo rende molto senso. Se la modifica dei dati è significativa e si estende su più partizioni, quindi è sufficiente scorrere le partizioni raggiunge lo stesso risultato.

I passaggi per eseguire un'opzione di partizione sono i seguenti:
1. Creare un oggetto vuoto fuori partizione
2. Eseguire l'aggiornamento di' ' come un CTAS
3. Cambiare i dati esistenti alla tabella out
4. Passare ai nuovi dati
5. La pulizia dei dati

Tuttavia, per identificare le partizioni per passare prima di tutto è necessario creare una routine di supporto, ad esempio quella riportata di seguito. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Questa procedura ingrandisce codice riutilizzo e mantiene partizione cambio di esempio più compatta.

Il codice seguente illustra i cinque passaggi indicati in precedenza per ottenere una partizione completa cambio routine.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Ridurre a icona la registrazione con piccoli batch

Per le operazioni di modifica di dati di grandi dimensioni, può essere utile dividere l'operazione in blocchi o batch per definire l'ambito l'unità di lavoro.

Di seguito è un esempio di utilizzo. Le dimensioni del batch impostata per un numero complesso per evidenziare la tecnica. In realtà le dimensioni del batch sarebbero significativamente più grande. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Pausa e proporzioni dei caratteri indicazioni

Warehouse di dati di SQL Azure consente di sospendere, riprendere e ridimensionare i data warehouse su richiesta. Quando si posizionare o ridurre il Data Warehouse SQL è importante tenere presente che le transazioni in corso vengono terminate immediatamente. causa transazioni aperte annullare. Se il carico di lavoro ha rilasciato una modifica dei dati di esecuzione prolungata e non completata prima dell'operazione di pausa o scala, sarà necessario il corretto funzionamento da annullare. È possibile configurare il tempo che necessario per posizionare o ridimensionare il database Warehouse di dati di SQL Azure. 

> [AZURE.IMPORTANT] Entrambi `UPDATE` e `DELETE` sono completamente registrate e, in modo annullare e ripristinare queste operazioni molto più tempo del equivalente minima ha eseguito l'accesso operazioni. 

Lo scenario migliore è per consentire a transazioni di modifica dati volo completare prima di sospensione o il ridimensionamento SQL Data Warehouse. Tuttavia, questa potrebbe non essere sempre pratica. Per ridurre il rischio di un ripristino lungo, è possibile utilizzare una delle opzioni seguenti:

- Riscrittura operazioni a esecuzione prolungata utilizzando [CTAS][]
- Suddividere l'operazione in blocchi; operazioni su un sottoinsieme delle righe

## <a name="next-steps"></a>Passaggi successivi

Vedere [le transazioni SQL Data warehouse][] per ulteriori informazioni sui livelli di isolamento e limiti di transazioni.  Per una panoramica delle altre procedure consigliate, vedere [Procedure consigliate Warehouse dati SQL][].

<!--Image references-->

<!--Article references-->
[Transazioni SQL Data warehouse]: ./sql-data-warehouse-develop-transactions.md
[partizione di tabella]: ./sql-data-warehouse-tables-partition.md
[Concorrenza]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Procedure consigliate Warehouse dati SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[RINOMINA]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

