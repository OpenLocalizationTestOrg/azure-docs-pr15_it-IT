<properties
   pageTitle="Caricare dati di esempio in SQL Data Warehouse | Microsoft Azure"
   description="Caricare dati di esempio nell'archivio di dati SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>Caricare dati di esempio nell'archivio di dati SQL

Seguire questa procedura per caricare ed eseguire query sul database di esempio di Adventure Works. Questi script prima di tutto utilizzano sqlcmd per l'esecuzione di SQL in modo da creare tabelle e viste. Dopo avere create tabelle, gli script utilizzerà bcp per caricare i dati.  Se non si dispone già sqlcmd e bcp installato, seguire questi collegamenti per [installare bcp][] e [installare sqlcmd][].

##<a name="load-sample-data"></a>Caricare dati di esempio

1. Scaricare il file di [Script di esempio Adventure Works per SQL Data Warehouse][] CAP.

2. Estrarre i file da zip scaricati in una directory sul computer locale.

3. Modificare aw_create.bat file estratto e impostare le seguenti variabili nella parte superiore del file.  Assicurarsi di non lasciare deve contenere spazi tra le "=" e il parametro.  Di seguito sono illustrati esempi di come potrebbero apparire le modifiche.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. Da un prompt di cmd di Windows, eseguire aw_create.bat modificata.  Assicurarsi di essere nella directory di cui è stato salvato la versione modificata di aw_create.bat.
Questo script verrà...
    * Rilasciare Adventure Works tabelle o viste già esistenti nel database
    * Creare tabelle di Adventure Works e delle visualizzazioni
    * Caricare ogni tabella di Adventure Works tramite bcp
    * Convalidare il conteggio delle righe per ogni tabella di Adventure Works
    * Raccogliere statistiche in ogni colonna per ogni tabella di Adventure Works


##<a name="query-sample-data"></a>Query di dati di esempio

Dopo aver caricato alcuni dati di esempio in un Data Warehouse SQL, è possibile eseguire rapidamente alcune query.  Per eseguire una query, connettersi al database di Adventure Works appena creato in DW di SQL Azure mediante Visual Studio e SSDT, come descritto nel documento [della query con Visual Studio][] .

Esempio di istruzione select semplice per ottenere tutte le informazioni necessarie dei dipendenti:

```sql
SELECT * FROM DimEmployee;
```

Esempio di una query più complessa utilizzando costrutti come RAGGRUPPA per visualizzare l'importo totale per tutte le vendite di ogni giorno:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Esempio di un'istruzione SELECT con una clausola WHERE per filtrare gli ordini dalla prima di una determinata data:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse supporta quasi tutti i costrutti Transact-SQL che supporta SQL Server.  Eventuali differenze sono illustrate della documentazione di [eseguire la migrazione di codice][] .

## <a name="next-steps"></a>Passaggi successivi
Ora che si è avuto la possibilità di provare alcune query con dati di esempio, vedere come [sviluppare][], [caricare][]o [eseguire la migrazione][] a SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[eseguire la migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppare]: sql-data-warehouse-overview-develop.md
[caricare]: sql-data-warehouse-overview-load.md
[query con Visual Studio]: sql-data-warehouse-query-visual-studio.md
[eseguire la migrazione di codice]: sql-data-warehouse-migrate-code.md
[installare bcp]: sql-data-warehouse-load-with-bcp.md
[installare sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Il script di esempio Adventure Works per SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
