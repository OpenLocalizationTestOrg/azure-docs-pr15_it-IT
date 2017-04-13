<properties
   pageTitle="Distribuzione di tabelle in SQL Data Warehouse | Microsoft Azure"
   description="Guida introduttiva di distribuzione tabelle Azure SQL Data Warehouse."
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
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribuzione di tabelle in SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuire][]
- [Indice][]
- [Partizione][]
- [Statistiche][]
- [Temporaneo][]

SQL Data Warehouse rappresenta un'elaborazione market parallela (MPP) sistema di database.  Dividendo dati e funzionalità di elaborazione in più nodi, SQL Data Warehouse può offrire scalabilità enorme - molto avanzata rispetto a qualsiasi singolo sistema.  Scegliere il metodo distribuire i dati all'interno del Data Warehouse SQL corrisponde a uno dei fattori più importanti per ottenere prestazioni ottimali.   Il tasto per ottenere prestazioni ottimali è la riduzione a icona lo spostamento dei dati e, a sua volta il tasto per ridurre al minimo lo spostamento dei dati consiste nel selezionare la strategia di distribuzione appropriata.

## <a name="understanding-data-movement"></a>Informazioni sullo spostamento dei dati

In un sistema MPP, i dati di ogni tabella viene suddiviso tra diversi database sottostanti.  Le query più ottimizzate in un sistema MPP possano semplicemente passate eseguire sui singoli database distribuiti senza interazione tra gli altri database.  Si supponga ad esempio, che si dispone di un database con dati relativi alle vendite che contiene due tabelle, vendite e clienti.  Se si dispone di una query che è necessario aggiungere la tabella sales alla tabella clienti e si divide di vendita e di tabelle clienti alto per numero cliente, inserire ogni cliente in un database separato, è possono risolvere eventuali quesiti cui partecipare a una vendita e assistenza clienti all'interno di ogni database con alcuna conoscenza degli altri database.  Al contrario, se i dati delle vendite diviso per numero dell'ordine e i dati dei clienti tramite il numero cliente, quindi qualsiasi database specificato non avranno i dati corrispondenti per ogni cliente e quindi se si desidera unire i dati delle vendite ai dati del cliente, è necessario ottenere i dati per ogni cliente da altri database.  In questo esempio secondo lo spostamento dei dati da necessario per spostare i dati dei clienti per i dati delle vendite, in modo che è possibile unire due tabelle.  

Spostamento dei dati non è sempre un aspetto negativo, a volte è necessario risolvere una query.  Ma è possibile evitare questa operazione aggiuntiva, naturalmente la query verrà eseguito più velocemente.  Spostamento dei dati viene visualizzato in genere quando il join di tabelle o le aggregazioni vengono eseguite.  Spesso è necessario eseguire entrambe, in modo che può essere ottimizzata per uno scenario, ad esempio un join, è comunque necessario spostamento dei dati per la risoluzione per lo scenario, ad esempio un'aggregazione.  Il trucco scoprire quale è consentono di risparmiare tempo.  Nella maggior parte dei casi, la distribuzione di tabelle di grandi dimensioni dei fatti in una colonna di join in genere è il metodo più efficace per ridurre lo spostamento la maggior parte dei dati.  La distribuzione dei dati nelle colonne di join è un metodo più comune per ridurre lo spostamento dei dati rispetto alla distribuzione di dati nelle colonne correlate in un'aggregazione.

## <a name="select-distribution-method"></a>Selezionare il metodo di distribuzione

Dietro le quinte, SQL Data Warehouse divide i dati in 60 database.  Ogni singolo database viene considerato una **distribuzione**.  Quando i dati vengono caricati in ogni tabella, SQL Data Warehouse sono disponibili informazioni su come dividere i dati tra queste 60 distribuzioni.  

Livello di tabella viene definito il metodo di distribuzione e attualmente sono disponibili due opzioni:

1. **Circolari** cui distribuire i dati in modo uniforme, ma in modo casuale.
2. **Hash distribuito** che distribuisce i dati in base a hashing i valori da una singola colonna

Per impostazione predefinita, quando si definisce un metodo di distribuzione di dati, la tabella verrà distribuita utilizzando il metodo di distribuzione **circolari** .  Dopo avere acquisito più sofisticati nell'implementazione, si verrà comunque si consiglia di utilizzare tabelle **hash distribuita** per ridurre al minimo lo spostamento dei dati che a sua volta consente di ottimizzare le prestazioni delle query.

### <a name="round-robin-tables"></a>Tabelle circolari

Utilizzando il metodo circolari della distribuzione dei dati è molto come sembra.  Quando viene caricati i dati, ogni riga è sufficiente viene inviato per la distribuzione successiva.  Questo metodo di distribuzione dei dati viene sempre in modo casuale equamente i dati molto tra tutte le distribuzioni.  Ovvero non esiste alcun ordinamento fatto durante il processo circolare che inserisce i dati.  Una distribuzione circolari a volte denominata hash casuale per questo motivo.  Con una tabella distribuita circolari non è necessario conoscere i dati.  Per questo motivo, tabelle circolari rendono spesso caricamento efficace i siti di destinazione.

Per impostazione predefinita, se non è selezionato alcun metodo di distribuzione, verrà utilizzato il metodo di distribuzione circolari.  Mentre le tabelle circolari sono facile da usare, in quanto i dati sono distribuiti in modo casuale il sistema che significa che il sistema non garantisce la distribuzione ogni riga è in.  Il sistema di conseguenza, talvolta può essere necessario richiamare un'operazione di spostamento dei dati per organizzare meglio i dati prima di essa può consentire di risolvere una query.  Questa operazione aggiuntiva può rallentare le query.

Considerare l'utilizzo di distribuzione circolari per la tabella negli scenari seguenti:

- Quando introduzione come punto di partenza semplice
- Se nessuna identificabili come chiave di join
- Se non è colonna costituire per distribuire la tabella hash
- Se la tabella non condivide una chiave di join comune con altre tabelle
- Se il join è meno significativo di altri join nella query
- Quando la tabella è impostato su una tabella di gestione temporanea temporanea

Entrambi gli esempi verrà creata una tabella circolari:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Mentre circolari è il tipo di tabella predefinito è esplicita nel DDL una buona norma in modo che siano cancellare ad altri utenti intenzioni del layout tabella.

### <a name="hash-distributed-tables"></a>Hashing tabelle distribuite

Utilizzando un algoritmo **Hash distribuita** per distribuire le tabelle migliorare le prestazioni per molti scenari riducendo lo spostamento dei dati in fase di query.  Distribuito tabelle sono tabelle che diviso tra database distribuiti utilizzando un algoritmo di hashing una singola colonna selezionata.  La colonna di distribuzione è determina la modalità di suddivisione i dati tra i database distribuiti.  La funzione hash utilizza la colonna di distribuzione per assegnare le righe per le distribuzioni.  Algoritmo di distribuzione risultante è deterministico.  Questo è lo stesso valore con lo stesso tipo di dati viene sempre previsti per la distribuzione stessa.    

In questo esempio verrà creata una tabella distribuita su id:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Selezionare la colonna distribuzione

Quando si sceglie di **hash distribuire** una tabella, sarà necessario selezionare una colonna singola distribuzione.  Quando si seleziona una colonna di distribuzione, sono disponibili tre principali fattori da prendere in considerazione.  

Selezionare una singola colonna che sarà:

1. Non verranno aggiornati
2. Distribuire i dati in modo uniforme, evitando dati asimmetria
3. Ridurre a icona lo spostamento dei dati

### <a name="select-distribution-column-which-will-not-be-updated"></a>Selezionare una colonna di distribuzione che non verranno aggiornato

Distribuzione colonne non sono pertanto aggiornabili, selezionare una colonna con valori statici.  Se una colonna dovranno essere aggiornati, in genere non è un candidato distribuzione efficace.  Se esiste un caso in cui è necessario aggiornare una colonna di distribuzione, tale scopo, prima di tutto l'eliminazione della riga e quindi inserire una nuova riga.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Selezionare una colonna di distribuzione che distribuisce uniformemente dati

Poiché un sistema distribuito viene eseguita solo nel modo più veloce la distribuzione più lenta, è importante suddividere il lavoro in modo uniforme tra le distribuzioni per offrire esecuzione non bilanciate attraverso il sistema.  Il modo in cui che il lavoro è diviso in un sistema distribuito si basa su dove si trovano i dati per ogni distribuzione.  In questo modo molto importante selezionare la colonna di distribuzione appropriata per la distribuzione dei dati in modo che ogni distribuzione a uno stesso lavoro richiederà nello stesso momento per completare la parte del lavoro.  Quando si lavoro anche è diviso in tutto il sistema, i dati viene bilanciati tra le distribuzioni.  Quando i dati non è equamente, si chiama questo **inclinare dati**.  

Quando si seleziona la colonna di distribuzione per dividere i dati in modo uniforme ed evitare dati asimmetria, tenere presente quanto segue:

1. Selezionare una colonna che contiene un numero significativo di valori distinti.
2. Evitare la distribuzione dei dati nelle colonne con alcuni valori distinti. 
3. Evitare la distribuzione dei dati nelle colonne con una frequenza elevata di valori null.
4. Evitare la distribuzione dei dati nelle colonne di Data.

Poiché l'hashing ogni valore su 1 delle 60 distribuzioni, per ottenere una distribuzione uniforme possibile selezionare una colonna in cui è altamente univoco e contiene più di 60 valori univoci.  Per illustrare, valutare la possibilità di un caso in cui una colonna contiene solo valori univoci 40.  Se è stata selezionata la colonna come chiave di distribuzione, i dati per la tabella da accedono 40 distribuzioni al massimo, lasciando 20 distribuzioni con nessun dato e alcuna elaborazione da eseguire.  Invece di altri 40 distribuzioni avrebbe carico di lavoro in caso di dati è stati distribuiti uniformemente oltre 60 distribuzioni.  Questo scenario è illustrato un esempio di dati asimmetria.

Nel sistema MPP, ogni passaggio della query che venga per tutte le distribuzioni completare la quota del lavoro.  Se una distribuzione è più operazioni rispetto alle altre, quindi la risorsa delle altre distribuzioni sono essenzialmente inutilizzato solo in attesa di distribuzione carico di lavoro elevata.  Quando si lavoro non è distribuito uniformemente su tutte le distribuzioni, desiderata per l' **elaborazione di inclinazione**.  Elaborazione inclinazione causeranno query da eseguire più lentamente rispetto a se il carico di lavoro può essere distribuito uniformemente nelle distribuzioni.  Dati asimmetria porterà a elaborazione asimmetria.

Evitare di distribuzione nella colonna altamente nullable come valori null verranno inserita nella stessa distribuzione. Distribuzione di una colonna di data può provocare inclinazione elaborazione perché tutti i dati per una data specificata verranno inserita nella stessa distribuzione. Se più utenti sono in esecuzione query tutti i filtri nella stessa data, quindi è necessario effettuare solo 1 delle 60 distribuzioni tutto il lavoro dopo una determinata data solo verrà visualizzato in una distribuzione. In questo scenario, la query verrà eseguita probabilmente 60 volte inferiore se i dati sono stati identico distribuiti tutte le distribuzioni. 

Quando sono presenti colonne non costituire, è consigliabile utilizzare circolari come il metodo di distribuzione.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Selezionare una colonna di distribuzione che ridurrà lo spostamento dei dati

Ridurre lo spostamento dei dati, selezionare la colonna di destra distribuzione corrisponde a uno dei principali strategie per ottimizzare le prestazioni della Warehouse dati SQL.  Spostamento dei dati viene visualizzato in genere quando il join di tabelle o le aggregazioni vengono eseguite.  Colonne utilizzate nei `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` e `HAVING` clausole tutti effettuare per **buona** hashing candidati di distribuzione. 

Invece, le colonne di `WHERE` clausola eseguire **non** per candidati colonna hash buona causa elaborazione inclinare perché limitano le distribuzioni partecipano la query.  Un buon esempio di una colonna che sia per distribuire in, ma spesso può causare l'elaborazione asimmetria è una colonna di date.

In generale, se sono presenti due tabelle di grandi dimensioni dei fatti interessate di frequente da un join, sarà possibile ottenere la maggior parte delle prestazioni distribuendo entrambe le tabelle in una delle colonne di join.  Se si dispone di una tabella in cui è stato mai aggiunto a un'altra tabella dei fatti di grandi dimensioni, quindi cercare alle colonne che si trovano in domande di `GROUP BY` clausola.

Esistono alcuni criteri fondamentali che devono essere soddisfatte per evitare lo spostamento dei dati durante un join:

1. Le tabelle coinvolte nel join devono essere hash distribuito su **una** delle colonne che fanno parte del join.
2. I tipi di dati delle colonne join devono corrispondere tra entrambe le tabelle.
3. Le colonne devono far parte di un operatore uguale a.
4. Il tipo di join potrebbe non essere un `CROSS JOIN`.


## <a name="troubleshooting-data-skew"></a>Risoluzione dei problemi relativi dati asimmetria

Quando i dati della tabella vengono distribuiti utilizzando il metodo di distribuzione hash è probabile che alcune distribuzioni verranno inclinati per presentare in modo non proporzionale più dati rispetto ad altri. Asimmetria quantità eccessiva di dati può influire sulle prestazioni delle query perché il risultato finale di una query distribuita necessario attendere che la distribuzione più lunga in esecuzione alla fine. A seconda del grado di asimmetria dati potrebbe essere necessario risolverlo.

### <a name="identifying-skew"></a>Identificazione asimmetria

Un modo semplice per identificare una tabella come inclinato consiste nell'usare `DBCC PDW_SHOWSPACEUSED`.  Questo è un modo molto semplice e rapido per visualizzare il numero di righe di tabella vengono memorizzati in ognuna delle 60 distribuzioni del database.  Tenere presente che per ottenere prestazioni più bilanciata, le righe nella tabella distribuita devono essere esteso in modo uniforme su tutte le distribuzioni.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Tuttavia, se si esegue una query di viste a gestione dinamica Warehouse di dati di SQL Azure (DMV) è possibile eseguire un'analisi più dettagliata.  Per iniziare, creare la visualizzazione di [dbo.vTableSizes][] visualizzazione tramite il codice SQL da articolo[introduttivo] [Panoramica di tabella].  Dopo aver creata la visualizzazione, eseguire la query per identificare le tabelle più di inclinazione dati 10%.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Risoluzione dei dati asimmetria

Non tutti asimmetria è sufficiente per garantire una soluzione.  In alcuni casi, le prestazioni di una tabella in alcune query possono annullare danni di asimmetria di dati.  Per decidere se è necessario risolvere dati asimmetria di una tabella, è necessario conoscere il più possibile sui volumi di dati e query del carico di lavoro.   È possibile esaminare l'impatto di inclinazione consiste nell'utilizzare la procedura descritta nell'articolo [Monitoraggio delle Query][] per monitorare l'impatto di inclinazione sulle prestazioni di query e in particolare l'impatto alla query il periodo di tempo necessari per completare nelle distribuzioni singoli.

La distribuzione dei dati è questione di trovare il giusto equilibrio tra la riduzione a icona inclinazione dati e la riduzione a icona lo spostamento dei dati. Questi possono essere opposte obiettivi e talvolta potrebbe essere mantenere i dati asimmetria in modo da ridurre lo spostamento dei dati. Ad esempio, quando la colonna di distribuzione è spesso la colonna condivisa in join e aggregazioni, si verrà essere la riduzione a icona lo spostamento dei dati. I vantaggi dello spostamento dei dati minimi potrebbero superano l'impatto dei dati sulla inclinare. 

Normalmente per risolvere dati inclinazione consiste nel ricreare la tabella con una colonna di distribuzione diversa. Poiché non è possibile modificare la colonna di distribuzione per una tabella esistente, il modo per modificare la distribuzione di una tabella per ricreare con una [] [CTAS].  Ecco due esempi di come risolvere asimmetria i dati:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Esempio 1: Creare di nuovo la tabella con una nuova colonna di distribuzione

Questo esempio viene usata [] [CTAS] per ricreare una tabella con una colonna di distribuzione hash diverso. 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Esempio 2: Creare di nuovo la tabella utilizzando distribuzione circolari

Questo esempio viene usata [] [CTAS] per ricreare una tabella con circolari invece di una distribuzione hash. Questa modifica produrranno anche la distribuzione dei dati al costo di spostamento dei dati avanzata. 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla struttura tabella, vedere [Distribuisci][], [indice][], [partizione][], [I tipi di dati][], [statistiche][] e articoli[temporaneo] [Tabelle temporanee].

Per una panoramica delle procedure consigliate, vedere [SQL dati Warehouse procedure consigliate][].


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
[Monitoraggio delle query]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
