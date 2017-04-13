<properties
   pageTitle="Cicli SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per la sostituzione cursori Azure SQL Data warehouse per lo sviluppo di soluzioni e cicli Transact-SQL."
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

# <a name="loops-in-sql-data-warehouse"></a>Cicli SQL Data warehouse
SQL Data Warehouse supporta il ciclo [mentre][] per eseguire ripetutamente blocchi di istruzioni. Continuerà per come le condizioni specificate sono vere o finché il codice termina specificamente dal ciclo utilizzando la `BREAK` parola chiave. Cicli sono particolarmente utili per la sostituzione cursori definiti nel codice SQL. Per fortuna, quasi tutti i cursori scritte in codice SQL di avanzamento rapido lettura solo diversi. Pertanto cicli [mentre] sono un'ottima alternativa se è necessario che sia necessario sostituire uno.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Uso dei cicli e la sostituzione di cursori SQL Data warehouse
Tuttavia, prima testa prima di tutto da porsi la domanda seguente: "Impossibile il cursore nuovamente scrivere utilizzare set basato su operazioni?". In molti casi la risposta sarà Sì e spesso la soluzione migliore. Spesso un'operazione di impostazione in base a più veloce in modo significativo di un approccio iterativo, riga per riga.

Avanzamento rapido cursori di sola lettura possono essere facilmente sostituiti con un costrutto di ciclo. Di seguito è riportato un semplice esempio. In questo esempio aggiorna le statistiche di ogni tabella nel database. Scorrendo le tabelle nel ciclo siamo in grado di eseguire ogni comando in sequenza.

Prima di tutto creare una tabella temporanea contenente un numero di riga univoco utilizzato per identificare le singole istruzioni:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

In secondo luogo, inizializzare le variabili necessari per eseguire il ciclo:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

A questo punto riprodurre a ciclo continuo rispetto alle istruzioni eseguirle uno alla volta:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Infine rilasciare la tabella temporanea creata nel primo passaggio

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[PO' DI TEMPO]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
