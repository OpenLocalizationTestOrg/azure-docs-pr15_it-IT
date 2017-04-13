<properties
   pageTitle="Gruppo di opzioni in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'implementazione gruppo dalle opzioni di Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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

# <a name="group-by-options-in-sql-data-warehouse"></a>Raggruppare le opzioni in SQL Data Warehouse

La clausola [GROUP BY][] viene utilizzato per aggregare i dati a un set di righe di riepilogo. Include inoltre alcune opzioni che si estendono le funzionalità che devono essere risolto come non è supportati direttamente da Warehouse di dati di SQL Azure.

Queste opzioni sono
- Clausola GROUP BY con ROLLUP
- SET DI RAGGRUPPAMENTO
- Clausola GROUP BY con cubo

## <a name="rollup-and-grouping-sets-options"></a>Opzioni di set di riepilogo e raggruppamento
Opzione più semplice consiste nell'usare `UNION ALL` invece di eseguire il totale complessivo anziché sulla sintassi esplicita. Il risultato è esattamente

Di seguito è illustrato un esempio di un gruppo di istruzione usando la `ROLLUP` opzione:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Utilizzando CUMULATIVO siano abbiamo richieste le aggregazioni seguenti:
- Paese
- Paese
- Totale complessivo

Per sostituire questa operazione è necessario utilizzare `UNION ALL`; specificare le aggregazioni obbligatorio in modo esplicito per restituire gli stessi risultati:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Per i set di raggruppamento tutto è necessario eseguire l'adozione di quello principale ma solo creare unione tutte le sezioni per i livelli di aggregazione che vogliamo visualizzare

## <a name="cube-options"></a>Opzioni di cubo
È possibile creare un gruppo da con cubo utilizzando l'approccio UNION ALL. Il problema è che il codice può risultare difficile e difficile da gestire. Per ovviare a questo è possibile utilizzare questa più avanzata approccio.

Utilizzare l'esempio precedente.

Il primo passaggio consiste nel definire il cubo' ' che definisce tutti i livelli di aggregazione da creare. È importante tenere conto delle CROSS JOIN di due tabelle derivate. Tutti i livelli viene generato automaticamente. Il resto del codice esiste realmente per la formattazione.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Di seguito sono illustrati i risultati del CTAS:

![][1]

Il secondo passaggio è necessario specificare una tabella di destinazione per archiviare risultati intermedi:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Il terzo passaggio consiste nel ciclo sul cubo di colonne eseguendo l'aggregazione. La query verrà eseguito una sola volta per ogni riga nella tabella temporanea #Cube e archiviare i risultati della tabella temp #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

È possibile infine restituire i risultati, semplicemente leggere dalla tabella temporanea #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Suddividere il codice di in sezioni e generando un costrutto di ciclo il codice diventa più gestibili e gestibili.


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[RAGGRUPPA PER]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
