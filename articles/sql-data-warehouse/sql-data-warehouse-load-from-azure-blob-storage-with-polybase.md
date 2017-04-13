<properties
   pageTitle="Caricare dati da archiviazione blob Azure in SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Informazioni su come utilizzare PolyBase per caricare dati dallo spazio di archiviazione blob Azure nell'archivio di dati SQL. Caricare lo schema di Contoso Retail Data Warehouse alcune tabelle di dati pubblici."
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
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Caricare dati da archiviazione blob Azure in SQL Data Warehouse (PolyBase)

> [AZURE.SELECTOR]
- [Dati Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Utilizzare i comandi PolyBase e T-SQL per caricare dati dallo spazio di archiviazione blob Azure nell'archivio di dati di SQL Azure. 

Per rendere più semplice, in questa esercitazione carica due tabelle dello spazio di archiviazione Blob Azure pubblico nello schema di Contoso Retail Data Warehouse. Per caricare l'intero set di dati, eseguire l'esempio [caricare completo Contoso Retail Data Warehouse][] dal repository esempi di Microsoft SQL Server.

In questa esercitazione si apprenderà come:

1. Configurare PolyBase caricare dallo spazio di archiviazione blob Azure
2. Caricare dati pubblici nel database
3. Al termine il carico, eseguire le ottimizzazioni.


## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire questa esercitazione, è necessario un account Azure che dispone già di un database SQL Data Warehouse. Se non già, vedere [creare un Data Warehouse SQL][].

## <a name="1-configure-the-data-source"></a>1. configurare l'origine dati

PolyBase utilizza oggetti esterni T-SQL per definire la posizione e gli attributi dei dati esterni. Le definizioni di oggetto esterno vengono archiviate in SQL Data Warehouse. I dati vengono archiviati esternamente.

### <a name="11-create-a-credential"></a>1.1. Creare una credenziale

**Ignorare questo passaggio** se si desidera caricare i dati pubblica Contoso. Perché è già accessibile a tutti gli utenti non è necessario l'accesso sicuro ai dati pubblici.

Se si utilizza questa esercitazione come modello per il caricamento dei propri dati, **non ignorare questo passaggio** . Per accedere ai dati tramite una credenziale, usare il seguente script per creare una credenziale nell'ambito del database e quindi utilizzarla quando si definisce la posizione dell'origine dati.


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
```

Andare al passaggio 2.

### <a name="12-create-the-external-data-source"></a>1.2. Creare l'origine dati esterna

Utilizzare questo comando [Crea origine dati esterna][] per memorizzare la posizione dei dati e il tipo di dati. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Se si scelga di renderlo pubblico contenitori il blob azure, tenere presente che il proprietario di dati viene addebitata per i dati in base alle tariffe uscita abbandona del data center dei dati. 

## <a name="2-configure-data-format"></a>2. configurare formato dati

I dati vengono archiviati in file di testo in archiviazione blob Azure e tutti i campi siano separati da un delimitatore. Eseguire il comando [Crea formato di FILE esterno][] per specificare il formato dei dati nei file di testo. I dati di Contoso sono compressi e delimitato da barra verticale.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. creare tabelle esterne

Dopo avere specificato il formato di origine e file di dati, si è pronti creare le tabelle esterne. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Creare uno schema per i dati. 

Per creare una posizione per archiviare i dati di Contoso nel database, creare uno schema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Creazione di tabelle esterne. 

Eseguire lo script per creare le tabelle esterne DimProduct e FactOnlineSales. Tutto ciò qui è la definizione di nomi di colonna e i tipi di dati e associarli al percorso e il formato di file di archivio blob Azure. La definizione verrà archiviata in SQL Data Warehouse e i dati sono ancora in Azure lo spazio di archiviazione Blob.

Il parametro **percorso** è la cartella nella cartella radice nel Blob Azure lo spazio di archiviazione. Ogni tabella è in una cartella diversa.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. caricare i dati
Non c'è diversi modi per accedere ai dati esterni.  Si query sui dati direttamente dalla tabella esterna, caricare i dati in tabelle di database nuovo o aggiungere dati esterni a tabelle di database esistenti.  


### <a name="41-create-a-new-schema"></a>4.1. Creare un nuovo schema

CTAS crea una nuova tabella contenente i dati.  Creare innanzitutto uno schema per i dati di contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Caricare i dati in nuove tabelle

Per caricare i dati dallo spazio di archiviazione blob Azure e salvarla in una tabella all'interno del database, utilizzare l'istruzione [CREATE TABLE AS SELECT (Transact-SQL)][] . Il caricamento con CTAS consente di sfruttare le tabelle esterne fortemente che appena creata. Per caricare i dati in nuove tabelle, utilizzare un'istruzione [CTAS][] per ogni tabella. 

CTAS crea una nuova tabella e viene compilato con i risultati di un'istruzione select. CTAS definisce la nuova tabella per avere le stesse colonne e tipi di dati come i risultati dell'istruzione select. Se si selezionano tutte le colonne da una tabella esterna, la nuova tabella sarà una replica delle colonne e tipi di dati di tabelle esterne.

In questo esempio viene creata la dimensione e la tabella dei fatti come hashing distribuite tabelle. 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 tenere traccia dello stato di caricamento

È possibile tenere traccia dello stato di avanzamento del carico tramite gestione dinamica viste. 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. ottimizzare columnstore compressione

Per impostazione predefinita, SQL Data Warehouse archivia la tabella come un indice columnstore raggruppate. Al termine di un carico, alcune delle righe di dati potrebbero non compressi nel columnstore.  Esiste una serie di motivi perché questo problema può verificarsi. Per ulteriori informazioni, vedere [gestire gli indici columnstore][].

Per ottimizzare le prestazioni delle query e la compressione columnstore dopo un carico, ricreare la tabella per forzare l'indice columnstore per comprimere tutte le righe. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Per ulteriori informazioni sulla gestione degli indici columnstore, vedere l'articolo [gestire gli indici columnstore][] .

## <a name="6-optimize-statistics"></a>6. ottimizzare statistiche

È consigliabile creare statistiche a colonna singola immediatamente dopo un carico. Sono disponibili alcune opzioni per le statistiche. Ad esempio, se si creano statistiche a colonna singola in ogni colonna di può richiedere molto tempo per ricostruire tutte le statistiche. Se si conosce che alcune colonne non devono essere predicati delle query, è possibile ignorare la creazione delle statistiche nelle colonne.

Se si decide di creare statistiche a colonna singola in ogni colonna di tutte le tabelle, è possibile utilizzare l'esempio di codice stored procedure `prc_sqldw_create_stats` nell'articolo [statistiche][] .

Nell'esempio seguente rappresenta un valido punto di partenza per la creazione di statistiche. Vengono create statistiche a colonna singola in ogni colonna nella tabella delle dimensioni e in ogni colonna join in tabelle dei fatti. È sempre possibile aggiungere unica o più colonne statistiche ad altre colonne di tabella dei fatti in un secondo momento.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Risultato sbloccato!

Dati pubblici nell'archivio di dati di SQL Azure caricate. Ottimo!

È ora possibile avviare query nelle tabelle l'utilizzo delle query simile alla seguente:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Passaggi successivi
Per caricare dati Contoso Retail Data Warehouse completa, utilizzare lo script in per altri suggerimenti di sviluppo, vedere [Panoramica di sviluppo SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Creare un Data Warehouse SQL]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Panoramica di sviluppo SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[gestire gli indici columnstore]: sql-data-warehouse-tables-index.md
[Statistiche]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREARE L'ORIGINE DATI ESTERNA]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CREARE IL FORMATO DI FILE ESTERNI]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[CREARE selezionare come tabella (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Caricare il Data Warehouse completo Contoso al dettaglio]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
