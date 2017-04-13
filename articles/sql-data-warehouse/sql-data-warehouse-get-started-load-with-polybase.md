<properties
   pageTitle="PolyBase in SQL dati Warehouse esercitazione | Microsoft Azure"
   description="Informazioni su PolyBase e come usarlo per data warehouse scenari."
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
   ms.date="10/10/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Caricare i dati con PolyBase SQL Data warehouse

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Dati Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)

In questa esercitazione viene illustrato come caricare dati SQL Data Warehouse utilizzando AzCopy e PolyBase. Al termine, si saprà come:

- Utilizzare AzCopy per copiare i dati in archiviazione blob Azure
- Creare gli oggetti di database per definire i dati
- Eseguire una query SQL T per caricare i dati

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## <a name="prerequisites"></a>Prerequisiti

Per esaminare in questa esercitazione, è necessario

- Un database SQL Data Warehouse.
- Un account di archiviazione Azure di tipo localmente ridondanti archiviazione Standard (Standard LRS), archiviazione geografico ridondanti Standard (Standard GRS) o archiviazione geografico ridondanti di accesso in lettura Standard (Standard RAGRS).
- Utilità AzCopy. Scaricare e installare la [versione più recente di AzCopy][] installato con gli strumenti di Microsoft Azure lo spazio di archiviazione.

    ![Strumenti di archiviazione Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Passaggio 1: Aggiungere dati di esempio a archiviazione blob Azure

Per caricare i dati, è necessario inserire alcuni dati di esempio in un'archiviazione blob Azure. In questo passaggio si popolare blob Azure archiviazione con dati di esempio. In un secondo momento, PolyBase verrà usata per caricare dati di esempio nel database di SQL Data Warehouse.

### <a name="a-prepare-a-sample-text-file"></a>RISPOSTE. Preparare un file di testo di esempio

Per preparare un file di testo di esempio:

1. Aprire il blocco note e copiare le righe di dati in un nuovo file. Salva nella directory locale temporanea come temp%\DimDate2.txt %.

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

### <a name="b-find-your-blob-service-endpoint"></a>B. Trovare l'endpoint del servizio di blob

Per trovare l'endpoint del servizio di blob:

1. Dal portale di Azure selezionare **Sfoglia** > **Gli account di archiviazione**.
2. Fare clic sull'account di archiviazione che si desidera utilizzare.
3. Selezionare e il conto dello spazio di archiviazione BLOB

    ![Fare clic su BLOB](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. È possibile salvare l'URL endpoint del servizio di blob per in un secondo momento.

    ![Endpoint del servizio di BLOB](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Trovare la chiave di archiviazione Azure

Per trovare la chiave di archiviazione Azure:

1. Dal portale di Azure, selezionare **Sfoglia** > **Gli account di archiviazione**.
2. Fare clic su account di archiviazione che si desidera utilizzare.
3. Selezionare **tutte le impostazioni** > **i tasti di scelta**.
4. Fare clic nella casella copia per uno dei tasti di scelta copiare negli Appunti.

    ![Copiare lo spazio di archiviazione Azure chiave](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Copiare il file di esempio in archiviazione blob Azure

Per copiare i dati in archiviazione blob Azure:

1. Aprire un prompt dei comandi e passare alla directory di installazione AzCopy. Il comando modifica in directory di installazione predefinita su un client di Windows a 64 bit.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Eseguire il seguente comando per caricare il file. Specificare l'URL endpoint del servizio blob per <blob service endpoint URL> e la chiave account Azure dello spazio di archiviazione per < azure_storage_account_key >.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Vedere anche [Guida introduttiva con l'utilità della riga di comando AzCopy][].

### <a name="e-explore-your-blob-storage-container"></a>E. Esplorare il contenitore di spazio di archiviazione blob

Per visualizzare il file è stato caricato in archiviazione blob:

1. Tornare a blade il servizio Blob.
2. In contenitori, fare doppio clic su **datacontainer**.
3. Per esplorare il percorso ai dati, fare clic sulla cartella **datedimension** e i file caricato **DimDate2.txt**verrà visualizzato.
4. Per visualizzare le proprietà, fare clic su **DimDate2.txt**.
5. Si noti che in e il proprietà Blob, è possibile scaricare o eliminare il file.

    ![Blob Azure dello spazio di archiviazione di visualizzazione](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Passaggio 2: Creare una tabella esterna per i dati di esempio

In questa sezione viene creata una tabella esterna che definisce i dati di esempio.

PolyBase utilizza tabelle esterne per accedere ai dati in archiviazione blob Azure. Poiché i dati non vengono archiviati in SQL Data Warehouse, PolyBase gestisce l'autenticazione a dati esterni mediante credenziali nell'ambito del database.

L'esempio in questo passaggio utilizza queste istruzioni Transact-SQL per creare una tabella esterna.

- [Creare chiave Master (Transact-SQL)][] per crittografare il segreto del database nell'ambito delle credenziali.
- [Creare Database nell'ambito delle credenziali (Transact-SQL)][] per specificare le informazioni di autenticazione dell'account di archiviazione Azure.
- [Creazione di origine dati esterna (Transact-SQL)][] per specificare la posizione dello spazio di archiviazione blob Azure.
- [Creare File formato esterno (Transact-SQL)][] per specificare il formato dei dati.
- [Creazione di tabelle esterne (Transact-SQL)][] per specificare la definizione di tabella e la posizione dei dati.

Eseguire la query sul database di SQL Data Warehouse. Crea una tabella esterna denominata DimDate2External nello schema dbo che punti ai dati di esempio DimDate2.txt in archiviazione blob Azure.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


In Esplora oggetti di SQL Server in Visual Studio, è possibile visualizzare il formato di file esterno, origine dati esterna e la tabella DimDate2External.

![Tabella esterna di visualizzazione](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Passaggio 3: Caricare i dati nell'archivio di dati SQL

Dopo aver creata la tabella esterna, è possibile caricare i dati in una nuova tabella o inserire in una tabella esistente.

- Per caricare i dati in una nuova tabella, eseguire l'istruzione [CREATE TABLE AS SELECT (Transact-SQL)][] . La nuova tabella avrà colonne indicate nella query. I tipi di dati delle colonne verranno corrispondono ai tipi di dati nella definizione di tabelle esterne.
- Per caricare i dati in una tabella esistente, utilizzare [INSERT... Selezionare (Transact-SQL)][] istruzione.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Passaggio 4: Creare le statistiche sui dati appena caricati

SQL Data Warehouse non crea automaticamente o aggiornamento automatico statistiche. Per ottenere prestazioni delle query elevato, pertanto è importante creare statistiche per ogni colonna di ogni tabella dopo il primo caricamento. È anche importante aggiornare le statistiche dopo aver apportato modifiche significative nei dati.

In questo esempio Crea statistiche a colonna singola la nuova tabella DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Per ulteriori informazioni, vedere [le statistiche][].  


## <a name="next-steps"></a>Passaggi successivi
Vedere la [Guida PolyBase][] per ulteriori informazioni che è necessario conoscere in fase di sviluppo di una soluzione che utilizza PolyBase.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Guida di PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[Guida introduttiva con l'utilità della riga di comando AzCopy]: ../storage/storage-use-azcopy.md
[versione più recente di AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREARE l'origine dati esterna (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREARE il formato di FILE esterno (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREAZIONE di tabelle esterne (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREARE selezionare come tabella (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERIMENTO DI... Selezionare (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREARE chiave MASTER (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREARE DATABASE nell'ambito CREDENZIALI (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
