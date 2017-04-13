<properties
   pageTitle="L'indicizzazione tabelle SQL Data Warehouse | Microsoft Azure"
   description="Guida introduttiva a tabella l'indicizzazione nella Warehouse di dati di SQL Azure."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>L'indicizzazione tabelle SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuire][]
- [Indice][]
- [Partizione][]
- [Statistiche][]
- [Temporaneo][]

SQL Data Warehouse offre diverse opzioni di indicizzazione che includono [gli indici columnstore raggruppate][], [indici cluster e indici][].  Non offre inoltre un'indice opzione Nessun noto anche come [heap][].  In questo articolo vengono illustrati i vantaggi di ogni tipo di indice, nonché suggerimenti per ottenere la maggior parte delle prestazioni fuori degli indici. Vedere [creare una tabella sintassi][] per informazioni dettagliate su come creare una tabella in SQL Data Warehouse.

## <a name="clustered-columnstore-indexes"></a>Indici columnstore raggruppate

Per impostazione predefinita, SQL Data Warehouse viene creato un indice cluster columnstore quando nessuna opzione indice sono specificata in una tabella. Tabelle columnstore cluster offrono il massimo livello di compressione dati sia, nonché le massime prestazioni query complessiva.  Le tabelle columnstore raggruppate in genere ottengano raggruppate tabelle indice o di e sono in genere la soluzione ideale per tabelle di grandi dimensioni.  Per questi motivi, columnstore cluster è il modo migliore per iniziare a quando non si è sicuri di come indicizzare la tabella.  

Per creare una tabella columnstore raggruppate, è sufficiente specificare indice COLUMNSTORE cluster nella clausola WITH o lasciare la clausola WITH disattivata:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Esistono alcuni scenari in cui columnstore cluster potrebbe non essere delle opzioni:

- Tabelle di ColumnStore non supportano secondari indici.  Considerare heap o indice cluster tabelle.
- Tabelle di ColumnStore non supportano varchar (max), nvarchar (max) e varbinary (max).  Considerare heap o indice cluster.
- Tabelle di ColumnStore potrebbero essere meno efficace per i dati temporanei.  Valutare la possibilità di heap e tabelle forse temporanee.
- Tabelle di piccole dimensioni con meno di 100 milioni di righe.  Valutare la possibilità di tabelle heap.

## <a name="heap-tables"></a>Tabelle heap

Quando si sono posizionate temporaneamente dati in SQL Data Warehouse, è possibile che l'utilizzo di una tabella heap apporterà processo più veloce.  In questo modo carichi heap sono più veloci alle tabelle di indice e in alcuni casi che lettura successive può essere eseguita dalla cache.  Se si desidera caricare dati solo alla fase prima di eseguire altre trasformazioni, il caricamento della tabella alla tabella heap sarà molto più rapido il caricamento dei dati a una tabella columnstore raggruppate. Inoltre, il caricamento dei dati a una [tabella temporanea][temporaneo] verrà caricato anche molto più rapidamente il caricamento di una tabella in un archivio permanente.  

Per le tabelle di ricerca small, minore di 100 milioni di righe, spesso tabelle heap avere senso.  Tabelle di columnstore cluster iniziano a raggiungere la compressione ottimale una volta più di 100 milioni di righe.

Per creare una tabella heap, è sufficiente specificare HEAP all'interno della clausola con:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Indici cluster e non cluster

Gli indici cluster potrebbero che columnstore raggruppate tabelle quando una singola riga deve essere recuperati rapidamente.  Per le query in un unico o pochi ricerca riga è tenuto a prestazioni con velocità estreme, valutare la possibilità di un indice cluster o indici secondario.  Tuttavia, per poter utilizzare un indice cluster è che utile solo le query che utilizzano un filtro selettivo sulla colonna indice cluster.  Per migliorare filtro su altre colonne un indice non cluster può essere aggiunti alle altre colonne.  Tuttavia, ogni indice che viene aggiunto a una tabella verrà aggiunta spazio e il tempo di elaborazione carica.

Per creare una tabella di indice cluster, è sufficiente specificare indice cluster nella clausola WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Per aggiungere un indice non cluster in una tabella, è sufficiente utilizzare la sintassi seguente:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Quando viene utilizzata CREATE INDEX, viene creato un indice non cluster per impostazione predefinita. Inoltre, un indice non cluster è consentito solo in una tabella di spazio di archiviazione di riga (HEAP o indice cluster). Gli indici non cluster nella parte superiore di un indice COLUMNSTORE cluster non è consentito in questo momento.


## <a name="optimizing-clustered-columnstore-indexes"></a>Ottimizzazione degli indici columnstore raggruppate

Columnstore raggruppate tabelle sono organizzate nei dati in segmenti.  Disporre di qualità elevata segmento è fondamentale per ottenere prestazioni ottimali della query in una tabella columnstore.  Qualità segmento può essere misurato del numero di righe in un gruppo di righe compressa.  Qualità segmento è ottimale in cui sono presenti almeno 100K righe per ogni riga compresso raggruppino e aumento delle prestazioni come il numero di righe per approccio gruppo riga 1.048.576 righe, ovvero la maggior parte delle righe che può contenere un gruppo di righe.

Di seguito possa creare e utilizzare nel sistema per calcolare la media righe per ogni riga di raggruppino e identificano gli indici columnstore cluster ottimale visualizzazione.  Ultima colonna nella visualizzazione genererà come istruzione SQL che può essere utilizzati per rigenerare gli indici.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Dopo avere creato la visualizzazione, eseguire la query per identificare le tabelle con i gruppi di righe con minore di 100K righe.  Naturalmente, può essere necessario aumentare la soglia di 100 KB se si sta cercando ulteriori qualità segmento ottimale. 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Dopo aver eseguito la query che è possibile iniziare a esaminare i dati e analizzare i risultati. Questa tabella riporta i suggerimenti per l'analisi di gruppo di riga.


| Colonna                             | Come usare questi dati                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | Se la tabella è suddiviso, si prevede di vedere conta gruppo Apri riga superiore. In teoria ogni partizione la distribuzione potrebbe avere un gruppo di riga aperta è associato. Suddividere questo nell'analisi. Una piccola tabella che è state suddiviso può essere ottimizzata per rimuovere completamente la suddivisione come questa migliore per ottimizzare la compressione.                                                                        |
| [row_count_total]                  | Numero totale di righe per la tabella. Ad esempio, è possibile utilizzare questo valore per calcolare una percentuale delle righe in stato compresso.                                                                      |
| [row_count_per_distribution_MAX]   | Se tutte le righe sono distribuite uniformemente il valore è il numero di righe per distribuzione. Confrontare questo valore con la compressed_rowgroup_count.                                 |
| [COMPRESSED_rowgroup_rows]         | Numero totale di righe in formato columnstore per la tabella.                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | Se il numero medio di righe è molto meno il numero massimo di righe per un gruppo di righe, quindi utilizzare CTAS o modificare RICOSTRUZIONE di un indice per ricomprimere i dati                     |
| [COMPRESSED_rowgroup_count]        | Numero di gruppi di righe in formato columnstore. Se il numero è molto elevato in relazione alla tabella è un indicatore che la densità columnstore è bassa.                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | Righe in modo logico vengono eliminate in formato columnstore. Se il numero è elevato une rispetto alle dimensioni della tabella, è consigliabile ricreare la partizione o ricostruire l'indice come si rimuove fisica. |
| [COMPRESSED_rowgroup_rows_MIN]     | Usare in combinazione con colonne Media e MAX per comprendere l'intervallo di valori per i gruppi di righe nel columnstore. Un numero basso superato la soglia di carico (102,400 per distribuzione allineate) indica che le ottimizzazioni sono disponibili nel caricamento dei dati                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | Come sopra                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | Gruppi di righe aperto sono normale. Uno aspetta adeguatamente un gruppo di riga aperta per la distribuzione di tabelle (60). Caricamento in partizioni dati è possibile suggerire un numero eccessivo. Effettuare un doppio la strategia di gestione delle partizione per verificare che sia audio                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | Ogni gruppo di righe possa contenere 1.048.576 righe come valore massimo. Usare questo valore per vedere come completo i gruppi di righe aperto sono attualmente                                                                 |
| [OPEN_rowgroup_rows_MIN]           | Gruppi aperti indicano che i dati siano frequenza caricamento della tabella o che il carico precedente stati inseriti vuoti altre righe in questo gruppo di riga. Utilizzare le funzioni MIN, MAX, gruppi di righe colonne Media per visualizzare la quantità di dati è suo in aperto. Per tabelle di piccole dimensioni può trattarsi di 100% di tutti i dati. In questo caso modificare RICOSTRUZIONE di un indice per forzare i dati a columnstore.                                                                       |
| [OPEN_rowgroup_rows_MAX]           | Come sopra                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | Come sopra                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | Esaminare la riga sottotitoli raggruppare le righe di una verifica dell'integrità.                                                                                                                                       |
| [CLOSED_rowgroup_count]            | Il numero di riga sottotitoli gruppi deve essere basso se qualsiasi siano visibili a tutti. Gruppi di righe sottotitoli possono essere convertiti in roups rowg compresso tramite ALTER INDEX... RIORGANIZZARE comando. Tuttavia, questa non è in genere richiesta. Gruppi di sottotitoli vengono convertiti automaticamente ai gruppi di righe columnstore tramite il processo di "per lo spostamento dei tupla" in background.                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | Gruppi di righe sottotitoli devono avere un tasso di riempimento molto elevato. Se il tasso di riempimento per un gruppo di righe sottotitoli è limitato, un'ulteriore analisi del columnstore è necessaria.                                   |
| [CLOSED_rowgroup_rows_MAX]         | Come sopra                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | Come sopra                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL per ricreare l'indice columnstore per una tabella                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>Motivi della scarsa columnstore indice qualità

Se si sono identificato tabelle con qualità insufficiente della segmento, desiderato identificare la causa principale.  Di seguito sono alcuni altri le cause più comuni della scarsa segmento quaility:

1. Pressione di memoria quando è stato creato l'indice
2. Numero elevato di operazioni DML
3. Small o ambito operazioni di caricamento
4. Troppe partizioni

Questi fattori possono causare un indice columnstore necessario notevolmente minore di ottimale 1 milione di righe per ogni gruppo di righe.  Provocano righe passare al gruppo di riga delta invece di un gruppo di righe compressa. 

### <a name="memory-pressure-when-index-was-built"></a>Pressione di memoria quando è stato creato l'indice

Il numero di righe per ogni gruppo di righe compresso è direttamente correlato alla larghezza della riga e la quantità di memoria disponibile per l'elaborazione del gruppo di righe.  Quando le righe sono stati scritti tabelle columnstore della memoria, potrebbe subire qualità segmento columnstore.  Di conseguenza, la procedura consigliata è fornire la sessione di scrittura per l'accesso di tabelle columnstore indice alla quantità di memoria possibili.  Poiché non esiste un compromesso tra memoria e concorrenza, le indicazioni sull'allocazione di memoria destra dipende dai dati di ogni riga della tabella, la quantità di DWU assegnata al sistema di e la quantità di bande orarie concorrenza che è possibile assegnare alla sessione che la scrittura di dati alla tabella.  Come ottimale, è consigliabile iniziare con xlargerc se si utilizza DW300 o meno largerc se si utilizza DW400 DW600 e mediumrc se si utilizza DW1000 e versioni successive.

### <a name="high-volume-of-dml-operations"></a>Numero elevato di operazioni DML

Una quantità elevata di operazioni DML che aggiornare ed eliminare righe può introdurre inefficienza nel columnstore. Si tratta soprattutto quando la maggior parte delle righe di un gruppo di righe vengono modificati.

- Eliminazione di una riga da un gruppo di righe compresso logicamente solo la riga viene contrassegnata come eliminati. La riga rimane nel gruppo compresso riga fino a ricompilato partizione o tabella.
- Inserimento di una riga aggiunge la riga in a una tabella interna rowstore denominata un gruppo di righe delta. Nella riga inserita non viene convertita in columnstore fino a quando il gruppo di riga delta completo ed è contrassegnato come chiuso. Gruppi di righe vengono chiuse una volta a raggiungere la capacità massima di 1.048.576 righe. 
- Aggiornamento di una riga nel formato columnstore viene elaborato come una logica Elimina e quindi un'istruzione insert. Nella riga inserita può essere archiviata nell'archivio delta.

Aggiornamento in batch e inserire le operazioni che superano la soglia di massa di 102.400 righe per partizione distribuzione allineata verrà scritti direttamente nel formato columnstore. Tuttavia, se la distribuzione uniforme, sarà necessario a modificarli 6.144 milioni di righe in una singola operazione a questo scopo. Se il numero di righe di una partizione allineato distribuzione è minore di 102,400 le righe verranno inoltrate a archivio delta e rimangono sono finché righe sufficienti sono state inserite o modificate per chiudere il gruppo di riga o è stato ricreato l'indice.

### <a name="small-or-trickle-load-operations"></a>Small o ambito operazioni di caricamento

Piccola carica che flusso nell'archivio di dati SQL sono noti anche come ambito carica. In genere che rappresentano un prossimo flusso costante di dati il caricamento dal sistema. Tuttavia, come questo flusso è collocato accanto a continuo il volume di righe non è particolarmente elevato. I dati sono molto spesso in modo significativo in soglia necessaria per il caricamento diretto columnstore formato.

In questi casi, è spesso consigliabile terra innanzitutto i dati in archiviazione blob Azure e consentire si accumulano prima del caricamento. Questa tecnica viene definita *il batch micro*.

### <a name="too-many-partitions"></a>Troppe partizioni

Un altro aspetto da considerare è l'impatto delle partizioni per le tabelle columnstore raggruppate.  Prima di partizione, SQL Data Warehouse divide già i dati in 60 database.  Partizione divide ulteriormente i dati.  Se si partizione i dati, quindi si desidera tenere presente che **ognuna** dovranno disporre almeno 1 milione di righe per trarre vantaggio da un indice columnstore raggruppate.  Se la tabella si suddividere in 100 partizioni, la tabella sarà necessario avere almeno 6 miliardi righe vantaggi offerti da un indice cluster columnstore (righe *100 partizioni* 1 milione di 60 distribuzioni). Se la tabella delle 100 partizioni non ha righe 6 miliardi, ridurre il numero delle partizioni o si consiglia di utilizzare invece di una tabella heap.

Una volta le tabelle sono state caricate con alcuni dati, seguire la seguente procedura per identificare e ricompilare le tabelle con indici columnstore cluster ottimale.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>La ricostruzione degli indici per migliorare la qualità di segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Passaggio 1: Identificare o creare utente che utilizza la classe di risorse corretto

Il più modo rapido per migliorare la qualità di segmento immediatamente consiste nel ricostruire l'indice.  Il codice SQL restituito da una vista precedente restituirà un'istruzione ALTER RICOSTRUZIONE di un indice che può essere utilizzata per rigenerare gli indici.  Quando la ricostruzione degli indici, è necessario allocare memoria insufficiente per la sessione che consente di ricostruire l'indice.  A tale scopo, aumentare la classe di risorse di un utente che dispone delle autorizzazioni per ricostruire l'indice nella tabella seguente per il valore minimo consigliato.  Impossibile modificare la classe di risorse dell'utente proprietario del database, in modo che il sistema non è creato un utente, è necessario farlo prima di tutto.  Il valore minimo che è consigliabile è xlargerc se si utilizza DW300 o meno largerc se si utilizza DW400 DW600 e mediumrc se si utilizza DW1000 e versioni successive.

Di seguito è illustrato un esempio di come allocare più memoria a un utente aumentando loro classe di risorse.  Per ulteriori informazioni sulla risorsa classi e su come creare un nuovo utente sono disponibili per la [gestione della concorrenza e il carico di lavoro] [ Concurrency] articolo.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Passaggio 2: Ricostruire gli indici columnstore raggruppate con maggiore utente di classe di risorse
Accesso come utente nel passaggio 1 (ad esempio LoadUser), che ora utilizzando una classe di risorse superiore, eseguire le istruzioni ALTER INDEX.  Assicurarsi che l'utente dispone dell'autorizzazione di modifica per le tabelle in cui è in corso la ricostruzione dell'indice.  In questi esempi viene spiegato come ricostruire l'indice columnstore intera o ricostruzione una singola partizione. Nelle tabelle di grandi dimensioni, è più pratico per rigenerare gli indici una singola partizione contemporaneamente.

In alternativa, anziché ricostruire l'indice, è Impossibile copiare la tabella in una nuova tabella utilizzando [CTAS][].  Qual è il metodo migliore? Per volumi elevati di dati, [CTAS][] è in genere più veloce [ALTER INDEX][]. Per indicare i volumi minori di dati, [ALTER INDEX][] è più semplice da utilizzare e non è necessario sostituire la tabella.  Per ulteriori informazioni su come ricostruire gli indici con CTAS, vedere **ricostruzione indici con CTAS e i cambi di partizione** sotto.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Creare un indice SQL Data warehouse è un'operazione non in linea.  Per ulteriori informazioni sulla ricostruzione indici, vedere la sezione modificare RICOSTRUZIONE di un indice in [Columnstore indici deframmentazione][] e l'argomento di sintassi [ALTER INDEX][].
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Passaggio 3: Verifica qualità segmento columnstore cluster sono state migliorate
Rieseguire query la tabella identificata con scarso segmento qualità e verificare la qualità segmento è state migliorate.  Se non migliora la qualità segmento, è possibile che le righe nella tabella sono molto grande.  Valutare la possibilità di utilizzo di una classe di risorse superiore o DWU durante la ricostruzione degli indici.

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>La ricostruzione degli indici con CTAS e i cambi di partizione

Questo esempio viene usata [CTAS][] e partizione passaggio per ricostruire una partizione di tabella. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Per ulteriori informazioni sulla ricostruzione partizioni utilizzando `CTAS`, vedere l'articolo [partizione][] .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli [Tabella Panoramica][Panoramica], [Tipi di dati di tabella][Tipi di dati], [la distribuzione di una tabella][Distribuisci], [partizione di una tabella][partizione], [Mantenendo le statistiche delle tabelle][statistiche] e [Tabelle temporanee][temporaneo].  Per ulteriori informazioni sulle procedure consigliate, vedere [SQL dati Warehouse procedure consigliate][].

<!--Image references-->

<!--Article references-->
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribuire]: ./sql-data-warehouse-tables-distribute.md
[Indice]: ./sql-data-warehouse-tables-index.md
[Partizione]: ./sql-data-warehouse-tables-partition.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporaneo]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Procedure consigliate Warehouse dati SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[MODIFICARE INDICE]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[gli indici cluster e indici]: https://msdn.microsoft.com/library/ms190457.aspx
[creare una tabella sintassi]: https://msdn.microsoft.com/library/mt203953.aspx
[ColumnStore indici deframmentazione]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[indici columnstore raggruppate]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
