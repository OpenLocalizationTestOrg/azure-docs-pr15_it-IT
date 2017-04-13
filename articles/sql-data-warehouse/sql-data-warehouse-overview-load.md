   <properties
   pageTitle="Caricare i dati nell'archivio di dati di SQL Azure | Microsoft Azure"
   description="Informazioni su scenari comuni per il caricamento in SQL Data Warehouse dati. Le quali le PolyBase, archiviazione blob Azure, file flat e spedizione disco. È anche possibile utilizzare gli strumenti di terze parti."
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
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>Caricare i dati nell'archivio di dati di SQL Azure

Riepilogo delle opzioni di scenario e consigli per il caricamento dei dati nell'archivio di dati SQL.

La parte più difficile il caricamento di dati è in genere preparare i dati per il caricamento. Azure consente di semplificare il caricamento usando archiviazione blob Azure come archivio dati comuni per molti dei servizi e utilizzo di dati Azure per poter gestire le comunicazioni e dati spostamento tra i servizi Azure. Questi processi vengano integrati con la tecnologia PolyBase che utilizza elaborazione market parallela (MPP) per caricare i dati in parallelo dallo spazio di archiviazione blob Azure nell'archivio di dati SQL. 

Per esercitazioni che caricano i database di esempio, vedere [caricare i database di esempio][].

## <a name="load-from-azure-blob-storage"></a>Caricare dallo spazio di archiviazione blob Azure
Il modo più rapido per importare dati in SQL Data Warehouse consiste nell'utilizzare PolyBase per caricare i dati dallo spazio di archiviazione blob Azure. PolyBase utilizza Progettazione del SQL Data Warehouse elaborazione market parallela (MPP) per caricare dati in parallelo dallo spazio di archiviazione blob Azure. Per utilizzare PolyBase, è possibile utilizzare i comandi T-SQL o una pipeline di Azure Data Factory.

### <a name="1-use-polybase-and-t-sql"></a>1. utilizzare PolyBase e T-SQL

Riepilogo del processo di caricamento:

2. Formattare i dati come UTF-8, poiché PolyBase non è attualmente supportato UTF-16.
2. Spostare i dati in archiviazione blob Azure e archiviarlo nel file di testo.
3. Configurare gli oggetti esterni SQL Data warehouse per definire il percorso e il formato dei dati
4. Eseguire un comando T-SQL per caricare i dati in parallelo in una nuova tabella di database.

<!-- 5. Schedule and run a loading job. --> 

Per un'esercitazione, vedere [dei dati da archiviazione blob Azure SQL Data warehouse (PolyBase)][].

### <a name="2-use-azure-data-factory"></a>2. utilizzare dati di Azure Factory

Per utilizzare PolyBase in modo più semplice, è possibile creare una pipeline di Azure Data Factory che utilizza PolyBase per caricare dati dallo spazio di archiviazione blob Azure in SQL Data Warehouse. Questo è veloce per configurare poiché non è necessario definire gli oggetti T-SQL. Se è necessario eseguire query su dati esterni senza importarlo, utilizzare Transact-SQL. 

Riepilogo del processo di caricamento:

2. Formattare i dati come UTF-8, poiché PolyBase non è attualmente supportato UTF-16.
2. Spostare i dati in archiviazione blob Azure e archiviarlo nel file di testo.
3. Creare una pipeline di Azure Data Factory per acquisire i dati. Utilizzare l'opzione PolyBase.
4. Pianificare ed eseguire la pipeline.

Per un'esercitazione, vedere [dei dati da archiviazione blob Azure SQL Data warehouse (Azure Data Factory)][].


## <a name="load-from-sql-server"></a>Caricare da SQL Server
Per caricare i dati da SQL Server SQL Data Warehouse è possibile utilizzare Integration Services (SSIS), trasferimento file flat o dischi spedizione a Microsoft. Il caricamento di lettura è possibile visualizzare un riepilogo dei diversi collegamenti alle esercitazioni e processi.

Per pianificare una migrazione completa di dati da SQL Server nell'archivio di dati SQL, vedere [Panoramica di migrazione][]. 

### <a name="use-integration-services-ssis"></a>Utilizzare Integration Services (SSIS)
Se si utilizza già pacchetti Integration Services (SSIS) per caricare in SQL Server, è possibile aggiornare i pacchetti per l'utilizzo di SQL Server come origine e SQL Data Warehouse come destinazione. Si tratta in modo semplice e veloce, ed è un'ottima scelta se non si desidera eseguire la migrazione il processo di caricamento per utilizzare dati già presente nel cloud. Il compromesso è il carico più lento dell'utilizzo PolyBase poiché questa SSIS non esegue il carico in parallelo.

Riepilogo del processo di caricamento:

1. Modificare il pacchetto di Integration Services in modo che puntino a istanza di SQL Server per l'origine e il database di SQL Data Warehouse per la destinazione.
2. Eseguire la migrazione dello schema SQL Data warehouse, se non esiste già.
3. Cambiare il mapping nei pacchetti di utilizzare solo i tipi di dati supportati da SQL Data Warehouse.
3. Pianificare ed eseguire il pacchetto.

Per un'esercitazione, vedere [dei dati da SQL Server per Azure SQL dati Warehouse (SSIS)][].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Utilizzare AZCopy (scelta consigliata per i dati < 10 TB)
Se la dimensione dei dati è < 10 TB, è possibile esportare i dati da SQL Server in file flat, copiarli in archiviazione blob Azure e quindi utilizzare PolyBase per caricare i dati in SQL Data Warehouse

Riepilogo del processo di caricamento:

1. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat.
2. Utilizzare l'utilità della riga di comando AZCopy per copiare i dati da file flat a archiviazione blob Azure.
3. Utilizzare PolyBase per caricare in SQL Data Warehouse.

Per un'esercitazione, vedere [dei dati da archiviazione blob Azure SQL Data warehouse (PolyBase)][].

### <a name="use-bcp"></a>Utilizzare bcp
Se si dispone di una piccola quantità di dati è possibile utilizzare bcp per caricare direttamente nell'archivio di dati di SQL Azure.

Riepilogo del processo di caricamento:
1. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in file flat.
2. Utilizzare bcp per caricare i dati da file flat direttamente nell'archivio di dati SQL.

Per un'esercitazione, vedere [dei dati da SQL Server nell'archivio di dati di SQL Azure (bcp)][].


### <a name="use-importexport-recommended-for--10-tb-data"></a>Utilizzare importazione/esportazione (scelta consigliata per i dati > 10 TB)
Se la dimensione dei dati è > 10 TB e si desidera spostare in Azure, è consigliabile utilizzare il disco spedizione servizio [Importa/Esporta][]. 

Riepilogo del processo di caricamento
2. Usare l'utilità della riga di comando bcp per esportare i dati da SQL Server in un file flat dischi trasferibile.
3. Spedire dischi a Microsoft.
4. Microsoft carica i dati in SQL Data Warehouse

## <a name="load-from-hdinsight"></a>Caricare da HDInsight
SQL Data Warehouse supporta il caricamento di dati da HDInsight tramite PolyBase. Il processo è diverso da quello di caricamento dati dallo spazio di archiviazione Blob Azure - utilizzo PolyBase per connettersi a HDInsight per caricare i dati. 

### <a name="1-use-polybase-and-t-sql"></a>1. utilizzare PolyBase e T-SQL

Riepilogo del processo di caricamento:

2. Formattare i dati come UTF-8, poiché PolyBase non è attualmente supportato UTF-16.
2. Spostare i dati in HDInsight e archiviarlo nel file di testo, formato ORCO o Parquet.
3. Configurare gli oggetti esterni SQL Data warehouse per definire il percorso e il formato dei dati.
4. Eseguire un comando T-SQL per caricare i dati in parallelo in una nuova tabella di database.

Per un'esercitazione, vedere [dei dati da archiviazione blob Azure SQL Data warehouse (PolyBase)][].

## <a name="recommendations"></a>Consigli

Molti dei partner hanno il caricamento di soluzioni. Per maggiori informazioni, vedere un elenco dei [partner di soluzioni][]. 

Se si desidera caricare in SQL Data Warehouse sarà necessario in modo da trasformarlo in righe e colonne prima di caricare i dati provenienti da un'origine non relazionali. I dati trasformati non devono essere archiviati in un database, è possibile memorizzare nei file di testo.

Creare le statistiche sui dati appena caricati. Azure SQL Data Warehouse che non è ancora stato automatico di supporto per creare o statistiche di aggiornamento automatico.  Per ottenere le massime prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o si verificano modifiche significative nei dati.  Per informazioni dettagliate, vedere [le statistiche][].


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[Caricare dati dallo spazio di archiviazione blob Azure SQL Data warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Caricare dati dallo spazio di archiviazione blob Azure SQL Data warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Caricare i dati da SQL Server per Azure SQL dati Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Caricare i dati da SQL Server nell'archivio di dati di SQL Azure (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Caricare i database di esempio]: ./sql-data-warehouse-load-sample-databases.md
[Cenni preliminari sulla migrazione]: ./sql-data-warehouse-overview-migrate.md
[partner di soluzioni]: ./sql-data-warehouse-partner-business-intelligence.md
[Panoramica di sviluppo]: ./sql-data-warehouse-overview-develop.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importazione/esportazione]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
