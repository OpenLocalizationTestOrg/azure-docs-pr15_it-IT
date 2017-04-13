<properties
   pageTitle="Progettare decisioni e tecniche per lo sviluppo di SQL Data Warehouse | Microsoft Azure"
   description="Concetti di sviluppo, decisioni relative alla progettazione, suggerimenti e tecniche per SQL Data Warehouse."
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
   ms.date="08/16/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Scelte relative alla progettazione e tecniche per SQL Data Warehouse

Esaminare questi articoli sviluppo per comprendere meglio principali decisioni, suggerimenti e tecniche per SQL Data Warehouse.

## <a name="key-design-decisions"></a>Principali decisioni
Gli articoli seguenti evidenziano alcuni dei concetti chiavi e delle decisioni di progettazione che è necessario conoscere per lo sviluppo della warehouse dati distribuiti utilizzando SQL Data Warehouse:

- [connessioni][]
- [concorrenza][]
- [transazioni][]
- [schemi definiti dall'utente][]
- [distribuzione di tabelle][]
- [indici delle tabelle][]
- [partizioni delle tabelle][]
- [CTAS][]
- [statistiche][]

## <a name="development-recommendations-and-coding-techniques"></a>Sviluppo suggerimenti e tecniche
Questi articoli evidenziare le tecniche specifiche, suggerimenti e consigli per lo sviluppo di SQL Data Warehouse:

- [stored procedure][]
- [etichette][]
- [Visualizzazioni][]
- [tabelle temporanee][]
- [SQL dinamico][]
- [ciclo continuo][]
- [Opzioni raggruppamento per][]
- [assegnazione di variabili][]

## <a name="next-steps"></a>Passaggi successivi
Una volta è stato tramite gli articoli di sviluppo consultare tramite la pagina di [riferimento Transact-SQL][] per ulteriori informazioni sulla sintassi supportata per SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[concorrenza]: ./sql-data-warehouse-develop-concurrency.md
[connessioni]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dinamico]: ./sql-data-warehouse-develop-dynamic-sql.md
[Opzioni raggruppamento per]: ./sql-data-warehouse-develop-group-by-options.md
[etichette]: ./sql-data-warehouse-develop-label.md
[ciclo continuo]: ./sql-data-warehouse-develop-loops.md
[statistiche]: ./sql-data-warehouse-tables-statistics.md
[stored procedure]: ./sql-data-warehouse-develop-stored-procedures.md
[distribuzione di tabelle]: ./sql-data-warehouse-tables-distribute.md
[indici delle tabelle]: ./sql-data-warehouse-tables-index.md
[partizioni delle tabelle]: ./sql-data-warehouse-tables-partition.md
[tabelle temporanee]: ./sql-data-warehouse-tables-temporary.md
[transazioni]: ./sql-data-warehouse-develop-transactions.md
[schemi definiti dall'utente]: ./sql-data-warehouse-develop-user-defined-schemas.md
[assegnazione di variabili]: ./sql-data-warehouse-develop-variable-assignment.md
[Visualizzazioni]: ./sql-data-warehouse-develop-views.md
[Guida di riferimento Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
