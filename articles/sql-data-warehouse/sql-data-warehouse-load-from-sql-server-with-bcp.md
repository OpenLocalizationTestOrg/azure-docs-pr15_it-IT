<properties
   pageTitle="Caricare dati da SQL Server in SQL Azure Data Warehouse (bcp) | Microsoft Azure"
   description="Per una dimensione di dati di piccole dimensioni, utilizza bcp per esportare i dati da SQL Server in file flat e importare i dati direttamente nell'archivio di dati di SQL Azure."
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
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Caricare dati da SQL Server in SQL Azure Data Warehouse (file flat)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Per piccoli set di dati, è possibile utilizzare l'utilità della riga di comando bcp per esportare i dati da SQL Server e quindi caricarlo direttamente nell'archivio di dati di SQL Azure.

In questa esercitazione si utilizzerà bcp per:

- Esportare una tabella da SQL Server tramite la scelta di comando (o creare un file di esempio semplice)
- Importare la tabella da un file flat in SQL Data Warehouse.
- Creare statistiche per i dati caricati.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

Per esaminare in questa esercitazione, è necessario:

- Un database SQL Data Warehouse
- L'utilità della riga di comando bcp installato
- L'utilità della riga di comando sqlcmd installato

È possibile scaricare le utilità bcp e sqlcmd dall' [Area Download Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Dati in formato ASCII o UTF-16

Se si sta tentando in questa esercitazione sui propri dati, i dati è necessario ASCII o UTF-16 codifica poiché bcp non supporta UTF-8. 

PolyBase supporta UTF-8 ma non ancora UTF-16. Nota Se si desidera combinare bcp con PolyBase sarà necessario trasformare i dati in UTF-8 dopo l'esportazione da SQL Server. 


## <a name="1-create-a-destination-table"></a>1. creare una tabella di destinazione

Definire una tabella in SQL Data Warehouse sarà la tabella di destinazione per il caricamento. Le colonne della tabella devono corrispondere ai dati di ogni riga del file di dati.

Per creare una tabella, aprire un prompt dei comandi e utilizzare sqlcmd.exe per eseguire il comando seguente:


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


## <a name="2-create-a-source-data-file"></a>2. creare un file di dati di origine

Aprire il blocco note e copiare le righe di dati in un nuovo file di testo e quindi salvare il file nella directory locale temporanea, C:\Temp\DimDate2.txt. Questi dati sono in formato ASCII.

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

(Facoltativo) Per esportare i dati da un database di SQL Server, aprire un prompt dei comandi ed eseguire il comando seguente. Sostituire TableName, nomeserver, NomeDatabase, nome utente e Password con le informazioni personalizzate.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. caricare i dati
Per caricare i dati, aprire un prompt dei comandi ed eseguire il comando seguente, sostituendo i valori per il nome del Server, nome del Database, nome utente e Password con le informazioni personalizzate.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Utilizzare questo comando per verificare che i dati sono stati caricati correttamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

I risultati dovrebbero risultare analoga alla seguente:

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

## <a name="4-create-statistics"></a>4. creare statistiche

SQL Data Warehouse significa non ancora supporto crea automaticamente o aggiornamento automatico statistiche. Per ottenere le massime prestazioni di query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o si verificano modifiche significative nei dati. Per una spiegazione dettagliata delle statistiche, vedere [le statistiche][]. 

Eseguire il comando seguente per creare statistiche per la tabella appena caricata.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. esportare dati da SQL Data Warehouse
Per divertimento, è possibile esportare i dati caricati più SQL Data Warehouse.  Il comando per esportare corrisponde esattamente come l'esportazione da SQL Server.

Tuttavia, è una differenza tra i risultati. Poiché i dati vengono memorizzati in percorsi distribuiti all'interno di SQL Data Warehouse quando si esportano dati ogni nodo di calcolo scrive dati al file di output. L'ordine dei dati nel file di output è probabile che sia diverso da quello l'ordine dei dati nel file di input.

### <a name="export-a-table-and-compare-exported-results"></a>Esportare una tabella e confrontare i risultati esportati

Per visualizzare i dati esportati, aprire un prompt dei comandi ed eseguire questo comando utilizzando parametri definiti dall'utente. NomeServer è il nome del Server SQL Azure logici.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
È possibile verificare che i dati esportati correttamente, aprire il nuovo file. I dati nel file devono corrispondere il testo seguente, ma probabilmente essere ordinati in un ordine diverso:

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

### <a name="export-the-results-of-a-query"></a>Esportare i risultati di una query

È possibile utilizzare la funzione **queryout** di bcp per esportare i risultati di una query invece di esportazione l'intera tabella. 

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del carico, vedere [dati carico in SQL Data Warehouse][].
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse][].
Per ulteriori informazioni sulla creazione di una tabella in SQL Data Warehouse, vedere [Panoramica di tabella][] o [sintassi CREATE TABLE][] .

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
