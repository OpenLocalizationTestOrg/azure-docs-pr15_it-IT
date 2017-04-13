<properties
   pageTitle="Creare una tabella, come selezionare (CTAS) in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la codifica con la tabella crea come dell'istruzione select (CTAS) Azure SQL Data warehouse per lo sviluppo di soluzioni."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Creare una tabella come selezionare (CTAS) SQL Data warehouse
Creare una tabella con nome selezionare o `CTAS` corrisponde a uno dei principali funzionalità Transact-SQL disponibili. È un'operazione completamente parallelizzata che crea una nuova tabella in base all'output di un'istruzione SELECT. `CTAS`è il modo più semplice e rapido per creare una copia di una tabella. È possibile utilizzare una versione fino al `SELECT..INTO` se si preferisce. Questo documento offre esempi e procedure consigliate per `CTAS`.

## <a name="using-ctas-to-copy-a-table"></a>Utilizzo di CTAS per copiare una tabella

Ad esempio uno dei più comuni utilizza di `CTAS` crea una copia di una tabella in modo che è possibile modificare il DDL. Se ad esempio creato in origine della tabella come `ROUND_ROBIN` e si desidera impostarlo su una tabella distribuita in una colonna, `CTAS` è come cambiare la colonna di distribuzione. `CTAS`può inoltre essere utilizzato per modificare i tipi di partizioni, l'indicizzazione o colonna.

Supponiamo che è stato creato in questa tabella utilizzando il tipo di distribuzione predefinito di `ROUND_ROBIN` distribuito poiché nessuna colonna distribuzione specificata nel `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

A questo punto si desidera creare una nuova copia di questa tabella con un indice di Columnstore raggruppate in modo che è possibile sfruttare le prestazioni delle tabelle Columnstore raggruppate. Inoltre si desidera distribuire in questa tabella su ProductKey poiché si prevedendo join la colonna e si desidera evitare lo spostamento dei dati durante join su ProductKey. Infine anche da aggiungere partizioni in OrderDateKey in modo che è possibile eliminare rapidamente vecchi dati trascinando le partizioni precedente. Ecco l'istruzione CTAS da copiare la tabella precedente in una nuova tabella.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Infine è possibile rinominare le tabelle per passare alla nuova tabella e quindi eliminare la tabella precedente.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Azure SQL Data Warehouse che non è ancora stato automatico di supporto per creare o statistiche di aggiornamento automatico.  Per ottenere le massime prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o si verificano modifiche significative nei dati.  Per una spiegazione dettagliata delle statistiche, vedere l'argomento [statistiche][] nel gruppo sviluppare degli argomenti.

## <a name="using-ctas-to-work-around-unsupported-features"></a>Utilizzo di CTAS per risolvere le caratteristiche non supportate

`CTAS`può anche essere utilizzato per risolvere un numero di caratteristiche non supportate elencate di seguito. Questa operazione può spesso rivelarsi una situazione positivi/come non solo il codice sarà conforme ma verrà spesso eseguito più velocemente su SQL Data Warehouse. Si tratta in seguito al design completamente parallelizzato. Gli scenari funzionanti intorno con CTAS includono:

- SELEZIONARE. IN
- JOIN ANSI sugli aggiornamenti
- Join ANSI in eliminazioni
- UNIRE istruzione

> [AZURE.NOTE] Provare a pensare "CTAS primo". Se si ritiene che è possibile risolvere un problema utilizzando `CTAS` quindi che è in genere il modo migliore per approccio - anche se si siano scrivendo più dati di conseguenza.
>

## <a name="selectinto"></a>SELEZIONARE. IN
È possibile `SELECT..INTO` viene visualizzato un numero di posizioni in soluzione.

Di seguito è illustrato un esempio di un `SELECT..INTO` istruzione:

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Per convertire le risposte sopra per `CTAS` è molto dirette:

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] CTAS attualmente è necessario che specificare una colonna di distribuzione.  Se non si intenzionalmente si desidera modificare nella colonna di distribuzione, il `CTAS` verranno eseguite più velocemente se si seleziona una colonna di distribuzione che corrisponde a quello della tabella sottostante come questa strategia evita lo spostamento dei dati.  Se si sta creando una tabella in cui le prestazioni non sono un fattore, quindi è possibile specificare `ROUND_ROBIN` per evitare di stabilire una colonna di distribuzione.

## <a name="ansi-join-replacement-for-update-statements"></a>Sostituzione di join ANSI per le istruzioni update

È possibile che avere un aggiornamento complesso che eseguono l'accesso più di due tabelle collaborare usando ANSI aggiungersi sintassi per eseguire l'aggiornamento o Elimina.

Si supponga che era necessario aggiornare questa tabella:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

La query originale potrebbe siano presenti simile al seguente:

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Poiché SQL Data Warehouse non supporta ANSI unisce `FROM` clausola di un `UPDATE` istruzione, non è possibile copiare il codice sopra senza modificare leggermente.

È possibile utilizzare una combinazione di un `CTAS` e un join implicito per sostituire il codice:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Sostituzione di join ANSI per eliminare le istruzioni
In alcuni casi l'approccio ottimale per l'eliminazione dei dati consiste nell'usare `CTAS`. Invece di eliminazione dei dati è sufficiente selezionare i dati che si desidera mantenere. Questo soprattutto per `DELETE` istruzioni che utilizzano ansi sintassi join poiché SQL Data Warehouse non supporta ANSI unisce nel `FROM` clausola di un `DELETE` istruzione.

Esempio di un'istruzione DELETE convertire è riportato di seguito:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Sostituire le istruzioni di stampa unione
Istruzioni di unione possono essere sostituite, almeno in parte, utilizzando `CTAS`. È possibile consolidare il `INSERT` e `UPDATE` in un'unica istruzione. Qualsiasi record eliminato è necessario chiudere disattivare in una seconda istruzione.

Esempio di un `UPSERT` è riportato di seguito:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Suggerimento CTAS: in modo esplicito stato tipo di dati e valori null output

Quando la migrazione di codice è possibile che eseguire su questo tipo di modello di codifica:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Si potrebbe pensare istintivamente deve eseguire la migrazione di questo codice a un CTAS ed è corretto. Tuttavia, esiste un problema di nascosto.

Il codice riportato di seguito non restituisce lo stesso risultato:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Si noti che la colonna "risultato" estende i valori di tipo e valori null dati dell'espressione. Questo può comportare sottili variazioni dei valori in se non si attenzione.

Provare a eseguire le operazioni seguenti ad esempio:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Il valore memorizzato per il risultato è diverso. Come viene utilizzato il valore persistente nella colonna risultato in altre espressioni l'errore diventa ancora più significativo.

![][1]

Questa operazione è particolarmente importante per le migrazioni dei dati. Anche se la seconda query è probabilmente più precisa esiste un problema. I dati è diversi rispetto al sistema di origine e che conduce alle domande dell'integrità per la migrazione. Questo è uno dei rari casi in cui la risposta "errata" è effettivamente quello giusto!

Il motivo che è possibile vedere questa differenza tra i risultati è per difetto il cast implicito tipo. Nel primo esempio nella tabella definisce la definizione della colonna. Quando la riga viene inserita una conversione implicita dei tipi. Nel secondo esempio è una conversione implicita dei come l'espressione definisce il tipo di dati della colonna. Si noti inoltre che la colonna nel secondo esempio è stata definita come una colonna NULLable mentre il cognome al non dispone. Quando la tabella è stata creata in valori null prima colonna di esempio è stata definita in modo esplicito. Nel secondo esempio che è stato solo a sinistra all'espressione e per impostazione predefinita questo comporterebbe una definizione NULL.  

Per risolvere questi problemi è necessario impostare in modo esplicito la conversione di tipi di valori null nel `SELECT` parte il `CTAS` istruzione. Non è possibile impostare queste proprietà nella parte Crea tabella.

Nell'esempio seguente viene illustrato come risolvere il codice:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenere presente quanto segue:
- CAST o CONVERT Impossibile sono stati utilizzati
- ISNULL viene utilizzata per imporre valori null non assegnazione
- ISNULL è la funzione più esterna
- Nella seconda parte di ISNULL è una costante vale a dire 0

> [AZURE.NOTE] Per il supporto essere correttamente impostata è fondamentale per usare `ISNULL` e non `COALESCE`. `COALESCE`non è una funzione deterministica e pertanto il risultato dell'espressione viene mantenuto NULLable. `ISNULL`è diverso. È deterministica. Pertanto quando la seconda parte di `ISNULL` funzione è una costante o un valore letterale, il valore risultante sarà non NULL.

Questo suggerimento non è utile solo per l'integrità dei calcoli. È anche importante per il passaggio partizione tabella. Si supponga che si dispone di questa tabella definita come il fattoriale:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Tuttavia, il campo valore è un'espressione calcolata non fa parte dei dati di origine.

Per creare il set di dati partizionata che è consigliabile procedere come segue:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

La query da eseguire ottimale. Il problema è disponibile quando si tenta di eseguire l'opzione partizione. Le definizioni delle tabelle non corrispondono. Per rendere le definizioni delle tabelle corrispondono il CTAS deve essere modificata.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

È possibile vedere pertanto che la coerenza dei tipi e gestione delle proprietà di valori null in un CTAS è buona norma migliore ingegneristica. Consente di mantenere l'integrità nei calcoli e garantisce inoltre che partizione passaggio è possibile.

Per ulteriori informazioni sull'utilizzo [CTAS][], vedere MSDN. È una delle istruzioni più importanti in Warehouse di dati di SQL Azure. Verificare che si comprendere.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
