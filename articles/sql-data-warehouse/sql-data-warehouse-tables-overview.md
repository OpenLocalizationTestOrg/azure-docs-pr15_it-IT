<properties
   pageTitle="Panoramica delle tabelle di SQL Data Warehouse | Microsoft Azure"
   description="Guida introduttiva a tabelle Warehouse dati di SQL Azure."
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
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>Panoramica delle tabelle di SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuire][]
- [Indice][]
- [Partizione][]
- [Statistiche][]
- [Temporaneo][]

Introduzione alla creazione di tabelle in SQL Data Warehouse è semplice.  La sintassi di [CREATE TABLE][] base nella sintassi comuni si è probabilmente già familiarità con di lavorare con altri database.  Per creare una tabella, è sufficiente denominare la tabella, assegnare un nome delle colonne e definire i tipi di dati per ogni colonna.  Se è stata creare tabelle in altri database, questo avrà un aspetto molto familiare all'utente.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

Nell'esempio precedente consente di creare una tabella chiamata Customers con due colonne, nome e cognome.  Ogni colonna è associato un tipo di dati di VARCHAR(25) che limita i dati da 25 caratteri.  Questi attributi fondamentali di una tabella, nonché gli altri utenti, soprattutto corrispondono altri database.  Tipi di dati definiti per ogni colonna e verificare l'integrità dei dati.  Gli indici possono essere aggiunti per migliorare le prestazioni riducendo i/o.  Partizione possono essere aggiunti per migliorare le prestazioni quando è necessario modificare i dati.

[Ridenominazione di] [ RENAME] una tabella di SQL Data Warehouse è simile alla seguente:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tabelle distribuite

Un nuovo attributo fondamentale introdotto da sistemi distribuiti come SQL Data Warehouse è la **colonna di distribuzione**.  La colonna di distribuzione è molto cosa può sembrare.  È la colonna che determina la modalità di distribuzione o dividere i dati in background.  Quando si crea una tabella senza specificare la colonna di distribuzione, la tabella viene automaticamente distribuita secondo **circolari**.  Tabelle circolari possono essere sufficiente in alcuni casi, la definizione di colonne di distribuzione può notevolmente ridurre lo spostamento dei dati durante le query, pertanto ottimizzazione delle prestazioni.  Vedere [la distribuzione di una tabella][Distribuisci] per altre informazioni su come selezionare una colonna di distribuzione.

## <a name="indexing-and-partitioning-tables"></a>L'indicizzazione e partizione tabelle

Come si desidera ottimizzare le prestazioni diventano più avanzate con SQL Data Warehouse, è consigliabile per ulteriori informazioni sulla struttura tabella.  Per ulteriori informazioni, vedere gli articoli di [Tipi di dati di tabella][Tipi di dati], [la distribuzione di una tabella][Distribuisci], [l'indicizzazione di una tabella][indice] e [partizione una tabella][partizione].

## <a name="table-statistics"></a>Statistiche delle tabelle

Statistiche sono un fondamentale per ottenere prestazioni ottimali dalla Data Warehouse SQL.  Poiché SQL Data Warehouse non ancora crea automaticamente e le statistiche di aggiornamento per l'utente, ad esempio potrebbe ormai si aspettano nel Database di SQL Azure, leggere l'articolo relativo [alle statistiche][] potrebbero non essere uno degli articoli più importanti leggere per ottenere le massime prestazioni dalle query.

## <a name="temporary-tables"></a>Tabelle temporanee

Le tabelle temporanee sono tabelle che solo esistono per la durata del sito di accesso e non possono essere visualizzate da altri utenti.  Le tabelle temporanee possono essere un buon modo per impedire ad altri utenti di visualizzare i risultati temporanei e anche ridurre la necessità di pulizia.  Dal momento che le tabelle temporanee utilizzano anche lo spazio di archiviazione locale, offrono prestazioni migliori per alcune operazioni.  Vedere gli articoli[temporaneo] [Tabella temporanea]per ulteriori informazioni sulle tabelle temporanee.

## <a name="external-tables"></a>Tabelle esterne

Tabelle esterne, noto anche come Polybase tabelle sono tabelle che possono essere una query SQL Data Warehouse ma punto ai dati esterni da SQL Data Warehouse.  Ad esempio, è possibile creare una tabella esterna che fa riferimento al file in archiviazione Blob Azure.  Per ulteriori informazioni su come creare ed eseguire query di una tabella esterna, vedere [caricare dati con Polybase][].  

## <a name="unsupported-table-features"></a>Caratteristiche delle tabelle non supportate

Mentre SQL Data Warehouse contiene molte delle stesse funzioni di tabella disponibili da altri database, esistono alcune caratteristiche che non sono ancora supportate.  Di seguito è riportato un elenco di alcune delle funzionalità di tabella che non sono ancora supportate.

| Caratteristiche non supportate |
| --- |
|[Proprietà Identity][] (vedere [Assegnazione surrogati chiave soluzione][])|
|Chiave primaria, chiavi esterne, univoci e controllo [Vincoli di tabella][]|
|[Indici univoci][]|
|[Colonne calcolate][]|
|[Colonne sparse][]|
|[Tipi definiti dall'utente][]|
|[Sequenza][]|
|[Trigger][]|
|[Viste indicizzate][]|
|[Sinonimi][]|

## <a name="table-size-queries"></a>Query su dimensioni tabella

Un modo semplice per identificare gli spazi e righe consumate da una tabella in ognuna delle 60 distribuzioni, consiste nell'utilizzare [DBCC PDW_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tuttavia, usando i comandi DBCC può essere molto limitazione.  Gestione dinamica viste consentono di visualizzare più dettagli, nonché per ottenere maggiore controllo sui risultati della query.  Iniziare creando verrà indicata da molti degli esempi in questa e altri articoli in questa visualizzazione.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Spazio tabelle riepilogo

La query restituirà le righe e lo spazio dalla tabella.  Si tratta di una query ideale per vedere quali sono le tabelle più grande e che siano circolari o hash distribuita.  Per tabelle distribuito viene inoltre la colonna di distribuzione.  Nella maggior parte dei casi le tabelle più grande dovrebbero essere hash distribuito con un indice columnstore raggruppate.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Spazio di tabella in base al tipo di distribuzione

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Spazio di tabella in base al tipo di indice analitico

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Spazio di distribuzione riepilogo

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli in [Tipi di dati di tabella][Tipi di dati], [la distribuzione di una tabella][Distribuisci], [l'indicizzazione di una tabella di][indice], [partizione di una tabella][partizione], [Mantenendo le statistiche delle tabelle][statistiche] e [Tabelle temporanee][temporaneo].  Per ulteriori informazioni sulle procedure consigliate, vedere [SQL dati Warehouse procedure consigliate][].

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
[Caricare i dati con Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREA TABELLA]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Proprietà Identity]: https://msdn.microsoft.com/library/ms186775.aspx
[Soluzione di chiavi Surrogate assegnazione]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Vincoli di tabella]: https://msdn.microsoft.com/library/ms188066.aspx
[Colonne calcolate]: https://msdn.microsoft.com/library/ms186241.aspx
[Colonne sparse]: https://msdn.microsoft.com/library/cc280604.aspx
[Tipi definiti dall'utente]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequenza]: https://msdn.microsoft.com/library/ff878091.aspx
[Trigger]: https://msdn.microsoft.com/library/ms189799.aspx
[Viste indicizzate]: https://msdn.microsoft.com/library/ms191432.aspx
[Sinonimi]: https://msdn.microsoft.com/library/ms177544.aspx
[Indici univoci]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
