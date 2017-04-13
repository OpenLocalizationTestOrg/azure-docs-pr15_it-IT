<properties
   pageTitle="Eseguire la migrazione dello schema in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la migrazione dello schema nell'archivio di dati di SQL Azure per lo sviluppo di soluzioni."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Eseguire la migrazione dello schema in SQL Data Warehouse#

I riepiloghi seguenti consentono di comprendere le differenze tra SQL Server e SQL Data Warehouse che consentono di eseguire la migrazione del database.

## <a name="table-migration"></a>Migrazione di tabella

Quando la migrazione delle tabelle, è consigliabile acquisire familiarità con le caratteristiche delle tabelle di tabelle di SQL Data Warehouse.  [Panoramica di tabella][] è ideale per iniziare.  In questo articolo vengono descritte le considerazioni più importanti durante la creazione di una tabella, ad esempio le statistiche delle tabelle, distribuzione, partizione e indicizzazione.  Vengono inoltre illustrate alcune [caratteristiche delle tabelle non supportate][] e soluzioni alternative.

SQL Data Warehouse supporta i tipi di dati business comuni.  Vedere l'articolo di [tipi di dati][] per un elenco di supportati e [dei tipi di dati non supportati][].  L'articolo di [tipi di dati][] contiene anche una query per identificare [i tipi di dati non supportate][].  Durante la conversione di tipi di dati, assicurarsi di esaminare le [procedure consigliate del tipo di dati][].

## <a name="next-steps"></a>Passaggi successivi
Una volta completata la migrazione schema del database SQL Data warehouse, andare a uno degli articoli seguenti:

- [Eseguire la migrazione dei dati][]
- [Eseguire la migrazione del codice][]

Per ulteriori informazioni sulle procedure consigliate SQL Data Warehouse, vedere l'articolo [procedure consigliate][] .

<!--Image references-->

<!--Article references-->
[Eseguire la migrazione del codice]: ./sql-data-warehouse-migrate-code.md
[Eseguire la migrazione dei dati]: ./sql-data-warehouse-migrate-data.md
[procedure consigliate]: ./sql-data-warehouse-best-practices.md
[Panoramica di tabella]: ./sql-data-warehouse-tables-overview.md
[caratteristiche delle tabelle non supportate]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[tipi di dati non supportate]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[procedure consigliate del tipo di dati]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
