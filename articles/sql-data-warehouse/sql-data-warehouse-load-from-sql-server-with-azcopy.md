<properties
   pageTitle="Caricare dati da SQL Server in SQL Azure Data Warehouse (PolyBase) | Microsoft Azure"
   description="Utilizza bcp per esportare i dati da SQL Server in file flat, AZCopy per importare i dati a archiviazione blob Azure e PolyBase per acquisire i dati nell'archivio di dati di SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Caricare dati da SQL Server in SQL Azure Data Warehouse (AZCopy)

Consente di caricare i dati da SQL Server a archiviazione blob Azure bcp e utilità della riga di comando AZCopy. Usare quindi PolyBase o Azure Data Factory per caricare i dati nell'archivio di dati di SQL Azure. 


## <a name="prerequisites"></a>Prerequisiti

Per esaminare in questa esercitazione, è necessario:

- Un database SQL Data Warehouse
- L'utilità della riga di comando bcp installata
- L'utilità della riga di comando SQLCMD installata

>[AZURE.NOTE] È possibile scaricare le utilità bcp e sqlcmd dall' [Area Download Microsoft][].

## <a name="import-data-into-sql-data-warehouse"></a>Importare dati in SQL Data Warehouse

In questa esercitazione verranno creare una tabella in Warehouse di dati di SQL Azure e importare i dati della tabella.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Passaggio 1: Creare una tabella Data warehouse di SQL Azure

Dal prompt dei comandi, utilizzare sqlcmd per eseguire la query seguente per creare una tabella sull'istanza:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] Per ulteriori informazioni sulla creazione di una tabella in SQL Data Warehouse e le opzioni disponibili nella clausola WITH, vedere [Panoramica di tabella][] o [sintassi CREATE TABLE][] .

### <a name="step-2-create-a-source-data-file"></a>Passaggio 2: Creare un file di dati di origine

Aprire il blocco note e copiare le righe di dati in un nuovo file di testo e quindi salvare il file nella directory locale temporanea, C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [AZURE.NOTE] È importante tenere presente che bcp.exe non supporta la codifica UTF-8 del file. Utilizzare file con codificata UTF-16 o ASCII quando si utilizza bcp.exe.

### <a name="step-3-connect-and-import-the-data"></a>Passaggio 3: Connettere e importare i dati
Usa bcp, è possibile connettersi e importare i dati utilizzando il comando seguente sostituendo i valori in base alle esigenze:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

È possibile verificare che i dati sono stati caricati eseguendo la query seguente utilizza sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Questa operazione deve restituire i risultati seguenti:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Passaggio 4: Creare le statistiche sui dati appena caricati

Azure SQL Data Warehouse che non è ancora stato automatico di supporto per creare o statistiche di aggiornamento automatico. Per ottenere le massime prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o si verificano modifiche significative nei dati. Per una spiegazione dettagliata delle statistiche, vedere l'argomento [statistiche][] nel gruppo sviluppare degli argomenti. Di seguito è un rapido esempio come creare le statistiche nella tabella caricato in questo esempio

Eseguire le seguenti istruzioni di creare statistiche da un prompt di sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Esportare dati da SQL Data Warehouse
In questa esercitazione si creerà un file di dati da una tabella di SQL Data Warehouse. Si verranno esportare i dati creato in precedenza in un nuovo file di dati denominato DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Passaggio 1: Esportare i dati

Con l'utilità bcp, è possibile connettersi ed esportare dati tramite il comando seguente sostituendo i valori in base alle esigenze:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
È possibile verificare che i dati esportati correttamente, aprire il nuovo file. I dati nel file devono corrispondere il testo seguente:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] A causa della natura dei sistemi distribuiti, l'ordine di dati potrebbe non essere lo stesso tra database SQL Data Warehouse. È inoltre possibile utilizzare la funzione **queryout** di bcp per scrivere un estratto query invece di esportare l'intera tabella.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del carico, vedere [dati carico in SQL Data Warehouse][].
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Caricare i dati nell'archivio di dati SQL]: ./sql-data-warehouse-overview-load.md
[Panoramica di sviluppo SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Panoramica di tabella]: ./sql-data-warehouse-tables-overview.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Sintassi CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Area Download Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
