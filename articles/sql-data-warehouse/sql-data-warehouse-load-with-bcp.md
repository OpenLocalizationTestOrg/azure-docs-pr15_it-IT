<properties
   pageTitle="Utilizzare bcp per caricare i dati in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni su quali bcp e come usarlo per data warehouse scenari."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="mausher;barbkess;sonyama"/>


# <a name="load-data-with-bcp"></a>Caricare i dati con bcp

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Dati Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** è un'utilità di caricamento in blocco della riga di comando che consente di copiare i dati tra SQL Server, file di dati e SQL Data Warehouse. Utilizzare bcp per importare un numero elevato di righe nelle tabelle di SQL Data Warehouse o per esportare i dati dalle tabelle di SQL Server nei file di dati. Tranne quando è utilizzato con l'opzione queryout, bcp richiede alcuna conoscenza di Transact-SQL.

bcp è un modo semplice e rapido per spostare più piccoli set di dati all'interno e disconnettersi da un database SQL Data Warehouse. L'esatta quantità di dati che sono consigliabile carico/estratto tramite bcp dipenderà via la connessione all'interfaccia di Azure dati di rete.  In genere, le tabelle delle dimensioni possono essere caricate ed estratto facilmente con bcp, tuttavia, bcp non è consigliabile per il caricamento o l'estrazione di volumi elevati di dati.  Polybase è lo strumento consigliato per il caricamento e l'estrazione di volumi elevati di dati come avviene ottimizzare il sfruttando l'architettura di elaborazione market parallela di SQL Data Warehouse.

Bcp consente di:

- Utilizzare una semplice utilità della riga di comando per caricare i dati in SQL Data Warehouse.
- Utilizzare una semplice utilità della riga di comando per estrarre dati da SQL Data Warehouse.

In questa esercitazione verrà spiegato come per:

- Importare dati in una tabella utilizzando la scelta di comando
- Esportare dati da un abbonamento tabella la scelta di comando

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

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
