<properties
    pageTitle="SQL In memoria, iniziare | Microsoft Azure"
    description="Le tecnologie SQL In memoria migliorano notevolmente le prestazioni delle transazioni e carichi di lavoro analitica. Informazioni su come sfruttare le tecnologie."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>Guida introduttiva a (Preview) In memoria in Database SQL

Caratteristiche in memoria notevolmente migliorano le prestazioni delle transazioni e carichi di lavoro di analitica nelle situazioni destra.

In questo argomento viene descritto due dimostrazioni, uno per OLTP In memoria e uno per Analitica In memoria. Ogni demo fornito con i passaggi e codice che è necessario eseguire la demo. È possibile:

- Utilizzare il codice per testare le variazioni per vedere differenze tra i risultati delle prestazioni; o
- Leggere il codice per conoscere lo scenario e per informazioni su come creare e utilizzare gli oggetti In memoria.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [1 di avvio veloce: In memoria OLTP tecnologie per più rapidamente le prestazioni di Transact-SQL](http://msdn.microsoft.com/library/mt694156.aspx) -un altro articolo consentono di iniziare.

#### <a name="in-memory-oltp"></a>OLTP in memoria

Le caratteristiche di [OLTP](#install_oltp_manuallink) In memoria (elaborazione delle transazioni online) sono:

- Tabelle di memoria ottimizzato.
- A livello nativo compilato stored procedure.


Una tabella ottimizzata memoria include una rappresentazione di se stesso in memoria attiva, oltre a rappresentazione standard su un disco rigido. Transazioni aziendali in base alla tabella eseguire più velocemente perché interagiscono direttamente con solo la rappresentazione in memoria attiva.

Con OLTP In memoria, è possibile ottenere la di 30 volte ottenere transazione della velocità di esecuzione in base alle specifiche del carico di lavoro.


Stored procedure a livello nativo compilate richiedono meno istruzioni in fase di esecuzione anziché interpretato tradizionale stored procedure. Risultato di compilazione nativa visto in durate sono 1/100 della durata interpretata.


#### <a name="in-memory-analytics"></a>Analitica in memoria 

La caratteristica di In memoria [Analitica](#install_analytics_manuallink) è:

Gli indici ColumnStore migliorare le prestazioni di analitica e la segnalazione di query. 


#### <a name="real-time-analytics"></a>Analitica in tempo reale

Per [Analitica in tempo reale](http://msdn.microsoft.com/library/dn817827.aspx) è combinare OLTP In memoria e Analitica per:

- Informazioni aziendali in tempo reale in base a dati operativi.


#### <a name="availability"></a>Disponibilità


GA disponibilità generale:

- [Gli indici Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) *su disco*.


Anteprima:

- OLTP in memoria
- Analitica operativi in tempo reale


Considerazioni mentre le caratteristiche In memoria in anteprima sono descritti a [più avanti in questo argomento](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Queste funzioni di anteprima sono disponibili solo per i database [*Premium*](sql-database-service-tiers.md) non in pool flessibile e non è disponibile per tutti i database Basic o Standard.  Supporto per i database Premium flessibile pool sarà presto disponibile. 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>RISPOSTE. Installare l'esempio OLTP In memoria

È possibile creare il database di esempio AdventureWorksLT [V12] tramite pochi clic nel [portale di Azure](https://portal.azure.com/). Quindi la procedura descritta in questa sezione illustra come è possibile migliorare il database AdventureWorksLT con:

- Tabelle in memoria.
- Una stored procedure compilata a livello nativo.


#### <a name="installation-steps"></a>Procedura di installazione

1. Nel [portale di Azure](https://portal.azure.com/), creare un database Premium su un server V12. Impostare l' **origine** al database di esempio AdventureWorksLT [V12].
 - Per informazioni dettagliate, è possibile vedere [creare il primo database di SQL Azure](sql-database-get-started.md).

2. Connetti a database con SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiare lo [script In memoria OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) negli Appunti.
 - Lo script T-SQL crea gli oggetti In memoria necessaria nel database di esempio AdventureWorksLT creato nel passaggio 1.

4. Incollare lo script T-SQL in SQL Server Management Studio e quindi eseguire lo script.
 - È fondamentale, il `MEMORY_OPTIMIZED = ON` istruzioni CREATE TABLE clausola, come in:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Errore 40536


Se viene visualizzato l'errore 40536 quando si esegue lo script T-SQL, eseguire il seguente script T-SQL per verificare se il database è supportato In memoria:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un risultato uguale a **0** indica che non è supportata In memoria e 1 indica che questa caratteristica è supportata. Per individuare il problema:

- Assicurarsi che il database è stato creato dopo le caratteristiche della memoria In OLTP state attive per l'anteprima.
- Assicurarsi che il database si trova il livello di servizio Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Informazioni sugli elementi di memoria ottimizzato creati

**Tabelle**: l'esempio contiene ottimizzata memoria tabelle riportate di seguito:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


È possibile esaminare ottimizzata memoria tabelle tramite **Esplora oggetti** di SQL Server Management Studio per:

- Pulsante destro del mouse **tabelle** > **filtro** > **Le impostazioni del filtro** > **Memoria ottimizzata** è uguale a 1.


Oppure è possibile eseguire una query come le viste del catalogo:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Stored procedure a livello nativo compilata**: SalesLT.usp_InsertSalesOrder_inmem possono essere esaminati tramite una query di visualizzazione catalogo:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Eseguire il carico di lavoro di esempio OLTP

L'unica differenza tra le due seguenti *stored procedure* è che la prima procedura utilizza ottimizzata memoria versioni delle tabelle, mentre la seconda procedura utilizza le normali tabelle su disco:

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


In questa sezione, viene visualizzato come usare l'utilità di comodo **ostress.exe** per eseguire le due stored procedure stressante livelli. È possibile confrontare il tempo necessario completare le esecuzioni due sottolineare.


Quando si esegue ostress.exe, è consigliabile passare progettati per entrambi i valori dei parametri:

- Eseguire un numero elevato di connessioni simultanee, utilizzando - n100.
- Ha by ogni ciclo di connessione centinaia di volte, utilizzando - r500.


Tuttavia, è consigliabile iniziare con più valori più piccoli come - n10 e - r50 per assicurarsi che tutto funzioni.


### <a name="script-for-ostressexe"></a>Script per ostress.exe


In questa sezione Visualizza lo script T-SQL incorporata della linea di comando ostress.exe. Lo script utilizza gli elementi creati da script T-SQL che è stato installato in precedenza.


Il seguente script inserisce un ordine di esempio con cinque voci ottimizzata memoria seguenti *tabelle*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Per rendere la versione _ondisk di Transact-SQL precedente per ostress.exe, è sufficiente da sostituire entrambe le occorrenze della sottostringa *_inmem* con *_ondisk*. Questi sostituisce influenzano i nomi delle tabelle e stored procedure.


### <a name="install-rml-utilities-and-ostress"></a>Installare ostress e utilità RML


Ideale è possibile pianificare eseguire ostress.exe su una macchina virtuale Azure. Creare una [macchina virtuale Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) nella stessa Azure area geografica in cui si trova il database AdventureWorksLT. Ma è possibile eseguire ostress.exe sul portatile.


La macchina virtuale o a qualsiasi elemento è ospitato da scegliere, installare l'utilità di riproduzione Markup Language (RML), che includono ostress.exe.

- Vedere la sezione ostress.exe nel [Database di esempio per OLTP In memoria](http://msdn.microsoft.com/library/mt465764.aspx).
 - Oppure vedere [Database di esempio per OLTP In memoria](http://msdn.microsoft.com/library/mt465764.aspx).
 - O vedere [Blog per l'installazione di ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Eseguire innanzitutto il carico di lavoro sottolineare _inmem


È possibile utilizzare una finestra *Del Prompt di Cmd raccomandati da* per eseguire la riga di comando ostress.exe. Parametri della riga di comando diretto ostress per:

- Esecuzione simultanea 100 connessioni (-n100).
- Ogni connessione eseguire lo script T-SQL 50 volte (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Per eseguire la riga di comando ostress.exe precedente:


1. Reimpostare il contenuto di dati del database eseguendo il comando seguente in SQL Server Management Studio, per eliminare tutti i dati che sono stati inseriti tramite le esecuzioni precedenti:
```
EXECUTE Demo.usp_DemoReset;
```

2. Copiare negli Appunti il testo della riga di comando ostress.exe precedente.

3. Sostituire il `<placeholders>` per i parametri -S - U -P -d con i valori corretti reali.

4. Eseguire la riga di comando modificata in una finestra di RML Cmd.


#### <a name="result-is-a-duration"></a>Risultato è una durata


Al termine dell'ostress.exe, scrive la durata dell'esecuzione come la riga finale di output nella finestra RML Cmd. Esecuzione di un test più breve, ad esempio, la durata di circa 1,5 minuti:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Reimpostare, modificare per _ondisk e quindi eseguire di nuovo


Dopo aver ottenuto il risultato dell'esecuzione di _inmem, eseguire la procedura seguente per _ondisk eseguire:


1. Reimpostare il database eseguendo il comando seguente in SQL Server Management Studio, per eliminare tutti i dati che è stati inseriti in fase di precedente:
```
EXECUTE Demo.usp_DemoReset;
```

2. Modificare la riga di comando ostress.exe per sostituire tutte le *_inmem* con *_ondisk*.

3. Eseguire nuovamente ostress.exe per la seconda volta e acquisire il risultato di durata.

4. Reimpostare nuovamente il database per l'eliminazione responsabile dei quali può essere una grande quantità di dati di test.


#### <a name="expected-comparison-results"></a>Risultati del confronto previsto

I test In memoria sono mostrato un miglioramento delle prestazioni **9 volte** per il carico di lavoro semplice, con ostress in esecuzione in una macchina virtuale Azure nella stessa regione Azure del database.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Installare l'esempio Analitica In memoria


In questa sezione confrontare i risultati IO e le statistiche quando si utilizza un indice columnstore rispetto a un indice tradizionale struttura ad albero b.


Per analitica in tempo reale in un carico di lavoro OLTP, spesso è consigliabile utilizzare un indice non cluster columnstore. Per informazioni dettagliate, vedere [Columnstore indici descritto](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparare il test analitica columnstore


1. Usare il portale di Azure per creare un nuovo database AdventureWorksLT dall'esempio.
 - Usare il nome esatto.
 - Scegliere un livello di servizio Premium.

2. Copia [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) negli Appunti.
 - Lo script T-SQL crea gli oggetti In memoria necessaria nel database di esempio AdventureWorksLT creato nel passaggio 1.
 - Lo script crea la tabella delle dimensioni e due tabelle dei fatti. Le tabelle dei fatti vengano popolate con righe 3,5 milioni.
 - Lo script può richiedere 15 minuti per completare.

3. Incollare lo script T-SQL in SQL Server Management Studio e quindi eseguire lo script.
 - La parola chiave **COLUMNSTORE** in un'istruzione **CREATE INDEX** è fondamentale, come in:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Impostare il livello di compatibilità 130 AdventureWorksLT:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - 130 livello non è correlata direttamente alle caratteristiche In memoria. Ma livello 130 in genere consente di migliorare le prestazioni di query rispetto a 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Fondamentale, tabelle e indici columnstore


- dbo. FactResellerSalesXL_CCI è una tabella che contiene un indice cluster **columnstore** , che ha avanzate compressione al livello di *dati* .

- dbo. FactResellerSalesXL_PageCompressed è una tabella che contiene un equivalente indice regolare raggruppato, compressi solo a livello di *pagina* .


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Query fondamentale per confrontare l'indice columnstore


[Qui](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) sono disponibili i tipi di query T-SQL è possibile eseguire per vedere miglioramenti delle prestazioni. Nel passaggio 2 di script T-SQL, non esiste una coppia di query che si è interessati diretto. Le due query si differenziano solo per una sola riga:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un indice cluster columnstore sia la tabella**_CCI** FactResellerSalesXL.

Estratto script T-SQL seguente stampa statistiche per IO e l'ora per la query di ogni tabella.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>Considerazioni sulla versione di anteprima per OLTP In memoria


Le caratteristiche della memoria In OLTP nel Database di SQL Azure stata [attivata per l'anteprima nella 28 ottobre 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Nel riquadro di anteprima corrente In memoria OLTP è supportata solo per:

- Database in un livello di servizio *Premium* .

- Database creati dopo le caratteristiche della memoria In OLTP state attive.
 - Un nuovo database non è in grado di supportare OLTP In memoria se viene ripristinato da un database creato prima le caratteristiche della memoria In OLTP state attive.


In caso di dubbi, è sempre possibile eseguire il seguente Transact-SQL selezionare questa opzione per verificare se il database è supportato In memoria OLTP. Un risultato di **1** indica che il database supporta OLTP In memoria:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Se la query restituisce **1**, In memoria OLTP è supportata in questo database e le copia del database e il ripristino del database creato in base a questo database.


#### <a name="objects-allowed-only-at-premium"></a>Oggetti è consentiti solo a Premium


Se un database contiene uno dei seguenti tipi di oggetti In memoria OLTP o tipi, il downgrade il livello di servizio del database da Premium a base o Standard non è supportato. Per eseguire il downgrade del database, eliminare questi oggetti:

- Tabelle ottimizzata memoria
- Tipi di memoria ottimizzato tabella
- Moduli compilati a livello nativo


#### <a name="other-relationships"></a>Altre relazioni


- Caratteristiche di uso OLTP In memoria con i database in pool flessibile non è supportata durante l'anteprima.
 - Per spostare un database che possiedono o oggetti OLTP In memoria in un pool di flessibile, procedere come segue:
  - 1. Rimuovere eventuali ottimizzata memoria tabelle, tipi di tabella e moduli T-SQL a livello nativo compilati presenti nel database
  - 2. Modificare il livello di servizio del database a standard
  - 3. Spostare il database nel pool di flessibile

- Utilizzo di memoria OLTP con SQL Data Warehouse non è supportata.
 - La funzione indice columnstore di Analitica In memoria è supportata in SQL Data Warehouse.

- Archivio di Query non acquisisce query interno compilato a livello nativo moduli.

- Alcune funzionalità di Transact-SQL non sono supportate con OLTP In memoria. Si applica a Microsoft SQL Server e Database di SQL Azure. Per informazioni dettagliate, vedere:
 - [Supporto in Transact-SQL per OLTP In memoria](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Costrutti Transact-SQL non è supportati da OLTP In memoria](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>Passaggi successivi


- Provare [usare OLTP In memoria in un'applicazione di SQL Azure esistente.](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>Risorse aggiuntive

#### <a name="deeper-information"></a>Informazioni più dettagliate

- [Informazioni sulle OLTP In memoria, che si applica a Microsoft SQL Server e Database di SQL Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [Informazioni in tempo reale Analitica operativi su MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- White paper su [modelli di carico di lavoro comuni e informazioni sulla migrazione](http://msdn.microsoft.com/library/dn673538.aspx), che vengono descritti i modelli di carico di lavoro in cui In memoria OLTP fornisce normalmente notevole miglioramento delle prestazioni.

#### <a name="application-design"></a>Progettazione di applicazioni

- [OLTP (In memoria ottimizzazione) in memoria](http://msdn.microsoft.com/library/dn133186.aspx)

- [Utilizzo di memoria OLTP in un'applicazione di SQL Azure esistente.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Strumenti

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), per la versione più recente mensile.

- [Descrizione delle utilità di riproduzione Markup Language (raccomandati da) per SQL Server](http://support.microsoft.com/en-us/kb/944837)

- [Lo spazio di archiviazione di monitor In memoria](sql-database-in-memory-oltp-monitoring.md) per OLTP In memoria.

