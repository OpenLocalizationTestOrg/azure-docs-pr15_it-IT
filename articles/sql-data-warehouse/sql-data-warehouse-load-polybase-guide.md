<properties
   pageTitle="Guida per l'uso di PolyBase SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di PolyBase in scenari SQL Data Warehouse e linee guida."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guida per l'uso di PolyBase SQL Data warehouse

Questa guida offre informazioni pratiche per l'uso di PolyBase SQL Data warehouse.

Per iniziare, vedere l'esercitazione [Carica dati con PolyBase][] .


## <a name="rotating-storage-keys"></a>Rotazione delle chiavi dello spazio di archiviazione

Di tanto in tanto è necessario modificare il tasto di scelta per lo spazio di archiviazione blob per motivi di sicurezza.

Il modo più elegante per eseguire questa operazione consiste nell'eseguire un processo noto come "i tasti di rotazione". Si sarà notato hanno due chiavi di spazio di archiviazione per l'account di archiviazione blob. Si tratta in modo che è possibile passare

Ruotare le chiavi di account di archiviazione Azure è semplice tre passaggi

1. Creare seconda credenziali database nell'ambito in base alla chiave di accesso di archiviazione secondaria
2. Creare una seconda origine dati esterna in base a disattivare questo nuove credenziali
3. Eliminare e ricreare le tabelle esterne che punta alla nuova origine dati esterna

Quando è stata eseguita la migrazione tutte esterno tabelle per la nuova origine dati esterna, quindi è possibile eseguire attività di pulizia:

1. Origine dati esterna primo rilascio
2. Il primo database di elenco a discesa ambito delle credenziali in base alla chiave di accesso principale dello spazio di archiviazione
3. Accedere a Azure e rigenerare la chiave primaria accesso pronta per la volta successiva

## <a name="query-azure-blob-storage-data"></a>Query di dati di spazio di archiviazione blob Azure
Query su tabelle esterne utilizzare semplicemente il nome della tabella come se fosse una tabella relazionale.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Una query in una tabella esterna potrebbe non riuscire senza l'errore *"Query interrotto, il numero massimo rifiutare soglia è stato raggiunto durante la lettura da un'origine esterna"*. Indica che i dati esterni contengano record *modificato* . Un record di dati viene considerato "dirty" se i dati effettivi tipi/numero di colonne non corrispondono le definizioni delle colonne della tabella esterna o se i dati non corrispondono al formato di file esterno specificato. Per risolvere il problema, verificare che la tabella esterna e le definizioni di formato di file esterno siano corrette e i dati esterni è conforme a queste definizioni. Nel caso in cui un sottoinsieme dei record di dati esterni vengono modificati, è possibile scegliere di rifiutare questi record per le query utilizzando le opzioni di rifiuta in creare DDL tabella esterna.


## <a name="load-data-from-azure-blob-storage"></a>Caricare dati dallo spazio di archiviazione blob Azure
In questo esempio carica dati dallo spazio di archiviazione blob Azure al database SQL Data Warehouse.

Archiviazione dei dati direttamente rimuove il tempo di trasferimento di dati per le query. Archiviazione dei dati con un indice columnstore consente di migliorare le prestazioni delle query per le query di analisi da fino a 10 volte.

In questo esempio viene usata l'istruzione CREATE TABLE AS SELECT per caricare i dati. La nuova tabella eredita colonne indicate nella query. Eredita i tipi di dati delle colonne dalla definizione di tabelle esterne.

CREATE TABLE AS SELECT è un efficienti altamente istruzione Transact-SQL che carica i dati in parallelo per tutti i nodi di elaborazione del Data Warehouse SQL.  È stato originariamente sviluppato per il motore di elaborazione market parallela (MPP) nel sistema di piattaforma Analitica ed è ora SQL Data warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Vedere [Crea tabella come selezionare (Transact-SQL)][].

## <a name="create-statistics-on-newly-loaded-data"></a>Creare le statistiche sui dati appena caricati

Azure SQL Data Warehouse che non è ancora stato automatico di supporto per creare o statistiche di aggiornamento automatico.  Per ottenere le massime prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o si verificano modifiche significative nei dati.  Per una spiegazione dettagliata delle statistiche, vedere l'argomento [statistiche][] nel gruppo sviluppare degli argomenti.  Di seguito è un rapido esempio illustra come creare caricato in questo esempio statistiche nella tabella.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Esportare dati in archiviazione blob Azure
In questa sezione illustra come esportare dati da SQL Data Warehouse in archiviazione blob Azure. Questo esempio viene usata CREATE esterni TABLE AS SELECT costituito da un efficienti altamente istruzione Transact-SQL per esportare i dati in parallelo da tutti i nodi di elaborazione.

Nell'esempio seguente viene creata una tabella esterna Weblogs2014 utilizzando le definizioni delle colonne e i dati da dbo. Tabella di blog. La definizione di tabelle esterne verrà archiviata in SQL Data Warehouse e alla directory "/ archiviazione/log2014 /" nel contenitore blob specificato dall'origine dati vengono esportati i risultati dell'istruzione SELECT. I dati vengono esportati nel formato di file di testo specificato.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>Aggirare requisito PolyBase UTF-8
In presenta PolyBase supporta il caricamento di file di dati che hanno avuto la codifica UTF-8. In UTF-8 utilizza la stessa codifica dei caratteri come PolyBase ASCII che è possibile ottenere supporto per il caricamento di dati è ASCII con codifica. Tuttavia, PolyBase non supporta codifica dei caratteri, ad esempio UTF-16 / Unicode o caratteri ASCII estesi. ASCII esteso include caratteri con accenti, ad esempio l'umlaut comune in tedesco.

Per risolvere questo requisito la migliore risposta è scrivere nuovamente la codifica UTF-8.

Esistono diversi modi per eseguire questa operazione. Di seguito sono due approcci tramite Powershell:

### <a name="simple-example-for-small-files"></a>Esempio di file di dimensioni ridotte

Di seguito è una riga di un semplice script di Powershell che consente di creare il file.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Tuttavia, mentre si tratta di codificare di nuovo i dati in modo semplice è non più efficiente. Io flusso esempio riportato di seguito è molto più velocemente e raggiunge lo stesso risultato.

### <a name="io-streaming-example-for-larger-files"></a>Esempio IO Streaming per file di grandi dimensioni

Nell'esempio seguente è più complessa ma durante il flusso di righe di dati di origine per impostarlo come destinazione è molto più efficiente. Usare questo approccio per file di dimensioni maggiori.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sullo spostamento dei dati nell'archivio di dati SQL, vedere [Panoramica di migrazione di dati][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Caricare i dati con PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Cenni preliminari sulla migrazione di dati]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[Formato di FILE esterno Crea (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026). aspx [creare una tabella esterna (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREARE selezionare come tabella (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
