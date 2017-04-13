<properties
   pageTitle="Gestione delle statistiche sulle tabelle di SQL Data Warehouse | Microsoft Azure"
   description="Guida introduttiva a statistiche sulle tabelle Data Warehouse di SQL Azure."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gestione delle statistiche sulle tabelle di SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuire][]
- [Indice][]
- [Partizione][]
- [Statistiche][]
- [Temporaneo][]

SQL Data Warehouse più a conoscenza dei dati, più veloce è possibile eseguire query sui dati.  Il modo indicare SQL Data Warehouse di dati è raccogliere statistiche sui dati.  Statistiche sui dati è una delle operazioni più importanti che è possibile eseguire per ottimizzare le query.  Statistiche utili SQL Data Warehouse per creare il piano ottimale per le query.  In questo modo l'utilità di ottimizzazione di query SQL Data Warehouse è un'utilità di ottimizzazione di costo in base a.  Vale a dire confronta il costo dei vari piani di query e quindi sceglie il piano con costo più basso, che deve essere anche il piano che verrà eseguita la più veloce.

Statistiche possono essere create in una singola colonna, più colonne o in un indice di una tabella.  Statistiche vengono archiviate in un istogramma che acquisisce l'intervallo e selettività dei valori.  Questa operazione è particolarmente utili quando è necessario che l'utilità di ottimizzazione da valutare join, GROUP BY, HAVING e clausola WHERE in una query.  Ad esempio, se l'utilità di ottimizzazione stima che la data che si desidera filtrare la query restituirà 1 riga, potrebbe scegliere molto diversa rispetto a quando in piano stima sono data è selezionato verrà restituito 1 milione di righe.  Durante la creazione delle statistiche è fondamentale, è altrettanto importante riflettere tale statistiche *in modo accurato* lo stato corrente della tabella.  La presenza di statistiche aggiornate garantisce che un buon piano è selezionato per l'utilità di ottimizzazione.  I piani creati tramite l'utilità di ottimizzazione sono solo a quanto previsto le statistiche sui dati.

Il processo di creazione e aggiornamento delle statistiche è un processo manuale, ma è molto semplice.  Si tratta a differenza di SQL Server che viene creato e aggiornato statistiche sulle singole colonne e indici automaticamente.  Utilizzando le informazioni seguenti, è possibile automatizzare notevolmente la gestione delle statistiche sui dati. 

## <a name="getting-started-with-statistics"></a>Guida introduttiva a statistiche

 La creazione di statistiche campionate in ogni colonna è un modo semplice per iniziare a utilizzare le statistiche.  Poiché è altrettanto importante mantenere sempre aggiornata la statistica, può essere un approccio tradizionale di aggiornare le statistiche ogni giorno o dopo ogni caricamento. Ci sono sempre compromesso tra le prestazioni e il costo per creare e aggiornare le statistiche.  Se si trovano che sta impiegando troppo tempo a gestire tutte le statistiche, è consigliabile provare a in maggiore dettaglio statistiche delle colonne o le colonne formato HTML.  È consigliabile, ad esempio, aggiornare colonne data giornaliera, come è possibile aggiungere nuovi valori anziché dopo ogni caricamento. Nuovo, sarà possibile ottenere il massimo vantaggio mediante la ricezione di statistiche su colonne interessate da join, GROUP BY, HAVING e clausola WHERE.  Se si dispone di una tabella con molte colonne che vengono utilizzate solo nella clausola SELECT, statistiche su queste colonne non possono contribuire e spesa leggermente più complessa per identificare solo le colonne in cui le statistiche aiuta a, ridurre il tempo per mantenere le statistiche.

## <a name="multi-column-statistics"></a>Statistiche di più colonne

Oltre a creare statistiche nelle colonne singole, è possibile che le query utile dalle statistiche più colonne.  Statistiche di più colonne sono le statistiche create per un elenco di colonne.  Includono le statistiche di colonna singola sulla prima colonna nell'elenco e alcune informazioni di correlazione tra colonna denominata densità.  Ad esempio, se si dispone di una tabella che eseguono l'accesso a un'altra in due colonne, è possibile che SQL Data Warehouse ottimizzare il piano a condizione che riconosca la relazione tra due colonne.   Statistiche di più colonne migliorare le prestazioni di query per alcune operazioni, ad esempio composte join e raggruppamento.

## <a name="updating-statistics"></a>Aggiornamento delle statistiche

Aggiornamento delle statistiche è una parte importante della routine di gestione di database.  Quando viene modificata la distribuzione dei dati nel database, è necessario aggiornare le statistiche.  Statistiche obsolete causerà prestazioni ottimali secondari della query.

Una procedura consigliata consiste nell'aggiornare statistiche sulle colonne data ogni giorno che si aggiungono nuove date.  Nuove righe ogni ora vengono caricati in data warehouse, vengono aggiunte nuove date di carico o le date delle transazioni. Questi cambiare la distribuzione dei dati e apportare le statistiche non aggiornate. Invece statistiche in una colonna paese in una tabella clienti mai potrebbero essere necessario aggiornare, come la distribuzione dei valori in genere non viene modificati. Supponendo che la distribuzione è costante tra i clienti, aggiungere nuove righe alla variante di tabella non è destinata a cambiare la distribuzione dei dati. Tuttavia, se il proprio data warehouse contiene solo un paese e importare dati da un paese nuovo, risultante nei dati di più paesi che viene archiviati, quindi sicuramente occorre aggiornare le statistiche sulla colonna paese.

Una delle prime domande chiedere queste informazioni quando la risoluzione dei problemi di una query, "Sono le statistiche aggiornate?"

Questa domanda non è quello che è possibile rispondere tramite la validità dei dati. Un oggetto statistiche aggiornati può essere molto vecchio se sono state apportate modifiche materiale ai dati sottostanti. Quando il numero di righe è cambiata sostanzialmente o viene apportata una modifica materiale nella distribuzione dei valori di una colonna specifica *quindi* ha tempo per aggiornare statistiche.  

Per riferimento, **SQL Server** (non SQL Data Warehouse) Aggiorna automaticamente statistiche per situazioni seguenti:

- Se si dispone zero righe nella tabella, quando si aggiungono righe, si riceverà un aggiornamento automatico delle statistiche
- Quando si aggiungono più di 500 righe a una tabella a partire da meno di 500 righe (ad esempio all'avvio avere 499 e aggiungervi 500 righe di un totale di 999 righe), si riceverà un aggiornamento automatico 
- Quando si è maggiore di 500 righe che è necessario aggiungere 500 righe aggiuntive + 20% delle dimensioni della tabella prima viene visualizzato un aggiornamento automatico nelle statistiche

Poiché non esiste alcun DMV per determinare se i dati all'interno della tabella sono cambiato dal momento che sono state aggiornate le statistiche ora ultime, conoscere l'età delle statistiche può fornire con parte dell'immagine.  È possibile utilizzare la query seguente per determinare l'ora dell'ultima le statistiche dove aggiornato in tutte le tabelle.  

> [AZURE.NOTE] Tenere presente che se viene apportata una modifica materiale nella distribuzione dei valori di una colonna specifica, è necessario aggiornare le statistiche indipendentemente dal fatto che ultimo aggiornamento.  

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Colonne di data in un data warehouse, ad esempio, in genere necessitano frequenti aggiornamenti delle statistiche. Nuove righe ogni ora vengono caricati in data warehouse, vengono aggiunte nuove date di carico o le date delle transazioni. Questi cambiare la distribuzione dei dati e apportare le statistiche non aggiornate.  Invece statistiche su una colonna di sesso in una tabella clienti mai potrebbero essere necessario aggiornare. Supponendo che la distribuzione è costante tra i clienti, aggiungere nuove righe alla variante di tabella non è destinata a cambiare la distribuzione dei dati. Tuttavia, se il proprio data warehouse contiene solo un genere e un nuovo requisito dei risultati in un generi più quindi sicuramente occorre aggiornare le statistiche sulla colonna sesso.

Per ulteriori informazioni, vedere [statistiche][] su MSDN.

## <a name="implementing-statistics-management"></a>Implementazione della gestione delle statistiche

Può essere utile anche per estendere i processo per garantire che le statistiche vengono aggiornate alla fine del carico di caricamento di dati. Il caricamento dei dati è quando tabelle più di frequente modificano la dimensione e/o la distribuzione dei valori. Di conseguenza, si tratta di una posizione logica per implementare alcune procedure di gestione.

Alcuni principi vengono fornite di seguito per aggiornare le statistiche durante il processo di caricamento:

- Assicurarsi che ogni tabella caricato sia aggiornato almeno un oggetto di statistiche. Aggiorna le informazioni sulla dimensione (numero di righe e numero totale di pagine) di tabelle nell'ambito dell'aggiornamento Stat.
- Focalizzare l'attenzione su colonne che fanno parte clausole JOIN, GROUP BY, ORDER BY e DISTINCT
- Valutare la possibilità di aggiornare le colonne "crescente chiave", ad esempio transazione date più di frequente se questi valori non verranno inclusi nell'istogramma statistiche.
- Valutare la possibilità di aggiornamento delle colonne di distribuzione statica meno frequente.
- È necessario ricordare che tutti gli oggetti statistico viene aggiornato nella serie. È sufficiente implementazione `UPDATE STATISTICS <TABLE_NAME>` potrebbe non essere ideale - soprattutto per tabelle di grandi dimensioni con numerosi passaggi di oggetti statistiche.

> [AZURE.NOTE] Per informazioni dettagliate sul [crescente chiave], consultare il white paper di SQL Server 2014 cardinalità stima modello.

Per ulteriori informazioni, vedere [Stima cardinalità][] su MSDN.

## <a name="examples-create-statistics"></a>Esempi: Creare le statistiche

In questi esempi viene illustrato come utilizzare diverse opzioni per la creazione di statistiche. Le opzioni utilizzate per ogni colonna che dipendono dalle caratteristiche dei dati e utilizzare la colonna nelle query.

### <a name="a-create-single-column-statistics-with-default-options"></a>RISPOSTE. Creare statistiche a colonna singola con le opzioni predefinite

Per creare le statistiche su una colonna, è sufficiente specificare un nome per l'oggetto statistiche e il nome della colonna.

La sintassi seguente Usa tutte le opzioni predefinite. Per impostazione predefinita, SQL Data Warehouse esempi il 20% della tabella per la creazione di statistiche.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Per esempio:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Creare statistiche a colonna singola esaminando ogni riga

Il tasso di campioni predefinito del 20% è sufficiente per la maggior parte dei casi. Tuttavia, è possibile modificare la frequenza di esempio.

Per visualizzare un esempio nella tabella completa, utilizzare la sintassi seguente:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Per esempio:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Creare statistiche a colonna singola specificando la dimensione del campione

In alternativa, è possibile specificare la dimensione del campione una percentuale:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Creare statistiche a colonna singola solo su alcune delle righe

Un'altra opzione, è possibile creare statistiche su una parte delle righe nella tabella. Questo comando si chiama un dato statistico filtrato.

Ad esempio, è possibile utilizzare statistiche filtrate quando si prevede di eseguire una query una partizione specifica di una tabella partizionata grandi dimensioni. Creando statistiche su solo i valori di partizione, la precisione delle statistiche migliorare e pertanto migliorare le prestazioni delle query.

In questo esempio Crea statistiche in un intervallo di valori. I valori potrebbero facilmente definiti in modo che corrisponda dell'intervallo dei valori in una partizione.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE] Per l'utilità di ottimizzazione query valutare la possibilità di utilizzo delle statistiche filtrate quando sceglie il piano di query distribuita, la query deve essere contenute la definizione dell'oggetto statistiche. Nel caso dell'esempio precedente, la query in cui è necessario specificare col1 valori compresi tra 2000101 e 20001231 clausola.

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Creare statistiche a colonna singola con tutte le opzioni

Naturalmente, è possibile, combinare insieme le opzioni. Nell'esempio seguente crea un oggetto statistiche filtrati con una dimensione del campione personalizzato:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Per il riferimento completo, vedere [Creare statistiche][] su MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Creare le statistiche di più colonne

Per creare una statistica più colonne, è sufficiente usare gli esempi precedenti, ma specificare altre colonne.

> [AZURE.NOTE] Istogramma, che viene utilizzato per stimare numero di righe nel risultato della query, è disponibile solo per la prima colonna elencata nella definizione dell'oggetto statistiche.

In questo esempio, l'istogramma è presente nella *prodotto\_categoria*. Le statistiche di colonna incrociati vengono calcolate in *prodotto\_categoria* e *prodotto\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Poiché non esiste una correlazione tra *prodotto\_categoria* e *prodotto\_sub\_categoria*, un stat più colonne può essere utile se queste colonne sono accessibili nello stesso momento.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Creare statistiche per tutte le colonne in una tabella

È possibile creare statistiche è ai problemi di comandi Crea statistiche dopo la creazione della tabella.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Utilizzare una stored procedure per creare statistiche per tutte le colonne in un database

SQL Data Warehouse non ha una stored procedure di sistema equivalente a [] [sp_create_stats] in SQL Server. Questa stored procedure crea un oggetto di statistiche singola colonna in ogni colonna del database che non include già statistiche.

Questo consente di iniziare a utilizzare la struttura del database. È possibile adattare alle proprie esigenze.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN   sys.[external_tables] e ON  e.[object_id]       = t.[object_id]
WHERE       l.[object_id] IS NULL
AND         e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Per creare le statistiche per tutte le colonne nella tabella con questa procedura, è sufficiente chiamare la procedura.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Esempi: aggiornamento statistiche

Per aggiornare le statistiche, è possibile:

1. Aggiornare un oggetto di statistiche. Specificare il nome dell'oggetto statistiche che si desidera aggiornare.
2. Aggiornare tutti gli oggetti in una tabella. Specificare il nome della tabella anziché un oggetto di statistiche specifici.


### <a name="a-update-one-specific-statistics-object"></a>RISPOSTE. Aggiornare un oggetto di statistiche specifici ###
Utilizzare la sintassi seguente per aggiornare un oggetto di statistiche specifici:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Per esempio:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aggiornando oggetti statistiche specifiche, è possibile ridurre il tempo e risorse necessari per gestire le statistiche. Sono necessari alcuni pensiero, tuttavia, per scegliere gli oggetti statistiche migliori per l'aggiornamento.


### <a name="b-update-all-statistics-on-a-table"></a>B. Aggiornare tutte le statistiche in una tabella ###
Mostra un semplice metodo per l'aggiornamento di tutti gli oggetti statistiche in una tabella.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Per esempio:

```sql
UPDATE STATISTICS dbo.table1;
```

Questa informativa è facile da usare. Ricordare ciò Aggiorna tutte le statistiche sulla tabella e pertanto potrebbe eseguire più attività è necessario. Se le prestazioni non è un problema, questo è il modo più semplice e completo per garantire statistiche siano aggiornate.

> [AZURE.NOTE] Quando si aggiorna tutte le statistiche in una tabella, SQL Data Warehouse indica un'analisi di esempio nella tabella per ogni statistiche. Se la tabella è grande, con numero di colonne e statistiche, potrebbe essere più efficiente per aggiornare le statistiche individuali in base alle esigenze.

Per un'implementazione di un `UPDATE STATISTICS` procedura, vedere l'articolo[temporaneo] di [Tabelle temporanee]. Il metodo di implementazione è leggermente diverso per la `CREATE STATISTICS` procedura riportata sopra, ma il risultato finale è la stessa.

Per la sintassi completa, vedere [Aggiornamento statistiche][] su MSDN.

## <a name="statistics-metadata"></a>Statistiche metadati
Esistono diverse visualizzazione sistema e le funzioni che è possibile utilizzare per trovare informazioni su statistiche. Ad esempio, è possibile vedere se un oggetto statistiche potrebbe essere obsoleto usando la funzione data Stat per visualizzare quando statistiche ultimo create o aggiornate.

### <a name="catalog-views-for-statistics"></a>Visualizzazioni per le statistiche del catalogo
Queste visualizzazioni di sistema sono riportate informazioni statistiche:

| Visualizzazione del catalogo | Descrizione |
| :----------- | :---------- |
| [Sys.Columns][]  | Una riga per ogni colonna. |
| [Sys][]  | Una riga per tutti gli oggetti nel database. |  |
| [Sys.Schemas][]  | Una riga per ogni schema del database. |  |
| [Sys.Stats][] | Una riga per ogni oggetto statistiche. |
| [Sys.stats_columns][] | Una riga per ogni colonna nell'oggetto statistiche. Collegamenti alla sys.columns. |
| [Tables][] | Una riga per ogni tabella (include tabelle esterne). |
| [Sys.table_types][] | Una riga per ogni tipo di dati. |


### <a name="system-functions-for-statistics"></a>Funzioni di sistema per le statistiche
Queste funzioni di sistema sono utili per l'utilizzo delle statistiche:

| Funzione di sistema | Descrizione |
| :-------------- | :---------- |
| [STATS_DATE][]    | Data che ultimo aggiornamento dell'oggetto statistiche. |
| [DBCC SHOW_STATISTICS][] | Fornisce informazioni di riepilogo Livella e informazioni dettagliate sulla distribuzione dei valori come interpretazione per l'oggetto statistiche. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinare statistiche colonne e le funzioni in un'unica visualizzazione

Questa visualizzazione porta le colonne correlate alle statistiche il risultato della funzione [] [STATS_DATE()] insieme.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Esempi di DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() Mostra i dati contenuti in un oggetto statistiche. Questi dati è disponibile in tre parti.

1. Intestazione
2. Densità vettoriale
3. Istogramma

Metadati di intestazione sulle statistiche. L'istogramma visualizza la distribuzione dei valori nella prima colonna della chiave dell'oggetto statistiche. Vettore densità misura correlazione tra colonne. SQLDW calcola le stime di cardinalità, ovvero con i dati dell'oggetto statistiche.

### <a name="show-header-density-and-histogram"></a>Mostra intestazione, densità e istogramma

Questo semplice esempio mostra tutte le tre parti di un oggetto statistiche.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Per esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Visualizzare una o più parti di DBCC SHOW_STATISTICS();

Se si desidera solo visualizzare parti specifiche, utilizzare la `WITH` clausola e specificare le parti che si desidera visualizzare:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Per esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Differenze DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() strettamente è implementata in SQL Data Warehouse confrontate con quelle di SQL Server.

1. Caratteristiche non sono supportate
- Non è possibile usare Stats_stream
- Non è possibile partecipare ad esempio risultati per sottoinsiemi specifici di dati statistici (STAT_HEADER JOIN DENSITY_VECTOR)
2. Non è possibile impostare NO_INFOMSGS per l'eliminazione messaggio
3. Non è possibile utilizzare i nomi delle statistiche parentesi quadre
4. Non è possibile utilizzare i nomi delle colonne per identificare gli oggetti
5. Errore personalizzato 2767 non è supportata

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [DBCC SHOW_STATISTICS][] su MSDN.  Per ulteriori informazioni, vedere gli articoli in [Tabella Panoramica][Panoramica], [Tipi di dati di tabella][Tipi di dati], [la distribuzione di una tabella][Distribuisci], [l'indicizzazione di una tabella di][indice], [partizione di una tabella][partizione] e [Tabelle temporanee][temporaneo].  Per ulteriori informazioni sulle procedure consigliate, vedere [SQL dati Warehouse procedure consigliate][].  

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
[Stima cardinalità]: https://msdn.microsoft.com/library/dn600374.aspx
[CREARE LE STATISTICHE]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistiche]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[Sys.Columns]: https://msdn.microsoft.com/library/ms176106.aspx
[Sys]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.Schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[Sys.Stats]: https://msdn.microsoft.com/library/ms177623.aspx
[Sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[Tables]: https://msdn.microsoft.com/library/ms187406.aspx
[Sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[AGGIORNARE LE STATISTICHE]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
