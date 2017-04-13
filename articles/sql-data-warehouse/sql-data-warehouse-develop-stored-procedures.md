<properties
   pageTitle="Stored procedure SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per l'implementazione stored procedure di Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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

# <a name="stored-procedures-in-sql-data-warehouse"></a>Stored procedure SQL Data warehouse

SQL Data Warehouse supporta molte delle funzionalità Transact-SQL di SQL Server. Esistono più importante scalabilità caratteristiche specifiche che si desidera sfruttare per ottimizzare le prestazioni della soluzione.

Tuttavia, per mantenere le proporzioni e le prestazioni di SQL Data Warehouse sono sono alcune caratteristiche e funzionalità che sono le differenze visive e gli altri utenti che non sono supportate.

In questo articolo viene spiegato come implementare stored procedure SQL Data Warehouse.

## <a name="introducing-stored-procedures"></a>Introduzione a stored procedure
Stored procedure sono un ottimo metodo per incorporare il codice SQL. archiviare i dati vicino i dati nel data warehouse. Incapsulando il codice in unità gestibili stored procedure consentendo agli sviluppatori di organizzare le relative soluzioni; facilitare riutilizzo maggiore di codice. Ogni stored procedure può anche accettare parametri per renderli ancora più flessibile.

SQL Data Warehouse fornisce un'implementazione semplificata e semplificata stored procedure. La differenza principale rispetto a SQL Server è che la stored procedure non precompilato. Nei data warehouse sono più interessati con il tempo di compilazione. È più importante che codice della stored procedure è ottimizzato correttamente quando si opera su volumi elevati di dati. L'obiettivo consiste nel salvarlo ore, minuti e secondi non millisecondi. È quindi più utile considerare di stored procedure come contenitori per la logica SQL.     

Quando SQL Data Warehouse esegue la stored procedure le istruzioni SQL vengono analizzate tradotto e ottimizzata in fase di esecuzione. Durante questo processo ogni istruzione viene convertita in query distribuite. Il codice SQL che viene effettivamente eseguito con i dati è diverso da query inviata.

## <a name="nesting-stored-procedures"></a>Annidamento stored procedure
Quando stored procedure chiama altre stored procedure o eseguono sql dinamico quindi la chiamata interna stored procedure o codice detta annidata.

SQL Data Warehouse supporta un massimo di 8 livelli di annidamento. Questo è leggermente diverso da SQL Server. Il livello di annidamento in SQL Server è 32.

Chiamata alla stored procedure di livello superiore considera per nidificare livello 1

```sql
EXEC prc_nesting
```
Se la stored procedure effettua anche EXEC un'altra chiamata questo ne aumenterà il livello di annidamento per 2
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se la seconda routine esegue quindi alcuni sql dinamico questo ne aumenterà il livello di annidamento per 3
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Nota SQL Data Warehouse non supporta attualmente @@NESTLEVEL. È necessario Tieni traccia del livello di annidamento se stessi. Non viene in genere verrà raggiunto il limite di livello 8 nidificare, ma se si è necessario utilizzare nuovamente il codice e "convertirla" in modo da adattarlo all'interno di questo limite.

## <a name="insertexecute"></a>INSERIMENTO DI.. ESEGUIRE
SQL Data Warehouse non consente di utilizzare set di risultati di una stored procedure con un'istruzione INSERT. Tuttavia, esiste un'alternativa che è possibile utilizzare.

Consultare l'articolo seguente su [tabelle temporanee] per un esempio su come eseguire questa operazione.

## <a name="limitations"></a>Limitazioni

Vi sono alcuni aspetti delle procedure Transact-SQL archiviate che non è implementate in SQL Data Warehouse.

Sono:

- stored procedure temporanee
- stored procedure numerate
- stored procedure estese
- Stored procedure CLR
- opzione di crittografia
- opzione di replica
- parametri con valori di tabella
- parametri di sola lettura
- parametri predefiniti
- contesti di esecuzione
- Restituisce l'istruzione

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[tabelle temporanee]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Panoramica di sviluppo]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
