<properties
   pageTitle="Caricare i dati da un file CSV in Databaase di SQL Azure (bcp) | Microsoft Azure"
   description="Per una dimensione di dati di piccole dimensioni, utilizza bcp per importare dati nel Database di SQL Azure."
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>


# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>Caricare i dati da file CSV in SQL Azure Data Warehouse (file flat)

È possibile utilizzare l'utilità della riga di comando bcp per importare dati da un file CSV nel Database di SQL Azure.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="prerequisites"></a>Prerequisiti

Per esaminare in questa esercitazione, è necessario:

- Un server di Database SQL Azure logico e database
- L'utilità della riga di comando bcp installato
- L'utilità della riga di comando sqlcmd installato

È possibile scaricare le utilità bcp e sqlcmd dall' [Area Download Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Dati in formato ASCII o UTF-16

Se si sta tentando in questa esercitazione sui propri dati, i dati è necessario ASCII o UTF-16 codifica poiché bcp non supporta UTF-8. 

## <a name="1-create-a-destination-table"></a>1. creare una tabella di destinazione

Definire una tabella nel Database di SQL della tabella di destinazione. Le colonne della tabella devono corrispondere ai dati di ogni riga del file di dati.

Per creare una tabella, aprire un prompt dei comandi e utilizzare sqlcmd.exe per eseguire il comando seguente:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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


## <a name="next-steps"></a>Passaggi successivi

Per eseguire la migrazione di un database SQL Server, vedere informazioni sulla [migrazione di database SQL Server](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Area Download Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
