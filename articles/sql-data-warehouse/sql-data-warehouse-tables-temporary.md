<properties
   pageTitle="Le tabelle temporanee SQL Data warehouse | Microsoft Azure"
   description="Guida introduttiva a tabelle temporanee in Warehouse di dati di SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelle temporanee SQL Data warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuire][]
- [Indice][]
- [Partizione][]
- [Statistiche][]
- [Temporaneo][]

Le tabelle temporanee sono molto utili durante l'elaborazione di dati, in particolare durante la trasformazione nel punto in cui i risultati intermedi sono temporanei. In SQL Data Warehouse tabelle temporanee esistono a livello di sessione.  Visibili solo alla sessione in cui sono stati creati e vengono automaticamente eliminati quando disconnette la sessione.  Le tabelle temporanee offrono prestazioni, perché i risultati vengono scritte in locale invece di archiviazione remota.  Le tabelle temporanee sono leggermente diverse in Azure SQL Data Warehouse Database SQL Azure come è possibile accedervi da un punto qualsiasi all'interno di sessione, inclusi all'interno e all'esterno di una stored procedure.

In questo articolo contiene essenziali indicazioni per l'uso di tabelle temporanee ed evidenziate principi di tabelle temporanee livello sessione. Usando le informazioni in questo articolo consentono di organizzare il codice, migliorando riutilizzo e la manutenzione del codice.

## <a name="create-a-temporary-table"></a>Creare una tabella temporanea

Tabelle temporanee vengono create anteponendo semplicemente il nome di tabella con una `#`.  Per esempio:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

È inoltre possibile creare tabelle temporanee con un `CTAS` utilizzando esattamente lo stesso approccio:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`un comando potente è il vantaggio di essere molto utile in uso di spazio log transazioni. 


## <a name="dropping-temporary-tables"></a>Eliminazione di tabelle temporanee

Quando si crea una nuova sessione, deve essere presente alcun tabelle temporanee.  Tuttavia, se si sta chiamando stessa stored procedure, che consente di creare temporanea con lo stesso nome, per assicurarsi che il `CREATE TABLE` istruzioni hanno esito positivo un semplice controllo esistenza con un `DROP` può essere utilizzato l'esempio seguente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Per la codifica coerenza, è consigliabile utilizzare questo modello per le tabelle e tabelle temporanee.  È anche consigliabile utilizzare `DROP TABLE` per rimuovere le tabelle temporanee quando è stato completato nel codice.  Stored procedure sviluppo è abbastanza comune per visualizzare i comandi dell'elenco a discesa combinati alla fine di una procedura per assicurarsi che gli oggetti vengono cancellate.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Codice modularizing

Poiché le tabelle temporanee sono visibili in un punto qualsiasi in una sessione utente, tale possibile da consentono di organizzare il codice dell'applicazione.  Ad esempio, stored procedure seguente riunisce le procedure consigliate dall'alto per generare DDL che aggiorneranno tutte le statistiche del database in base al nome di statistica.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

In questa fase l'unica azione che si è verificato è la creazione di una stored procedure che sarà sufficiente generato una tabella temporanea #stats_ddl, con le istruzioni DDL.  Questa stored procedure elimina #stats_ddl se esiste già per assicurarsi di che non riuscire se eseguire più volte all'interno di una sessione.  Tuttavia, poiché non esiste alcun `DROP TABLE` alla fine della stored procedure, dopo avere completato la stored procedure, lasciare la tabella creata in modo che possa essere letto all'esterno della stored procedure.  In SQL Data Warehouse, a differenza di altri database di SQL Server, è possibile utilizzare la tabella temporanea all'esterno della procedura di cui è stato creato.  Tabelle temporanee SQL Data Warehouse possono essere utilizzato **in un punto qualsiasi** all'interno della sessione. Questo può comportare più modulare e gestibile codice come l'esempio seguente:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitazioni della tabella temporanea

SQL Data Warehouse stabilire un paio di limitazioni per l'implementazione di tabelle temporanee.  Solo sessione ambite tabelle temporanee sono attualmente supportate.  Le tabelle temporanee globali non sono supportate.  Inoltre, le visualizzazioni non è possibile creare nelle tabelle temporanee.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli [Tabella Panoramica][Panoramica], [Tipi di dati di tabella][Tipi di dati], [la distribuzione di una tabella][Distribuisci], [l'indicizzazione di una tabella di][indice], [partizione di una tabella][partizione] e [Mantenendo le statistiche delle tabelle][statistiche].  Per ulteriori informazioni sulle procedure consigliate, vedere [SQL dati Warehouse procedure consigliate][].

<!--Image references-->

<!--Article references-->
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribuire]: ./sql-data-warehouse-tables-distribute.md
[Indice]: ./sql-data-warehouse-tables-index.md
[Partizione]: ./sql-data-warehouse-tables-partition.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporaneo]: ./sql-data-warehouse-tables-temporary.md
[Procedure consigliate Warehouse dati SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
