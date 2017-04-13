<properties
   pageTitle="SQL dinamico SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per l'utilizzo dinamico SQL Azure SQL Data warehouse per lo sviluppo di soluzioni."
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

# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinamico SQL Data warehouse
Per sviluppare il codice di applicazione per SQL Data Warehouse potrebbe essere necessario usare sql dinamico per offrire soluzioni flessibili, generiche e modulare. SQL Data Warehouse non supporta i tipi di dati blob al momento. Questo potrebbe limitare le dimensioni delle stringhe del come blob tipi includono varchar (max) e nvarchar (max). Se si utilizzano questi tipi di codice dell'applicazione durante la creazione di stringhe di dimensioni molto grandi, sarà necessario separare il codice in blocchi e usare invece l'istruzione EXEC.

Un semplice esempio:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se la stringa breve è possibile utilizzare [sp_executesql][] come di consueto.

> [AZURE.NOTE] Istruzioni eseguita come SQL dinamico continuerà a essere soggetto a tutte le regole di convalida TSQL.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
