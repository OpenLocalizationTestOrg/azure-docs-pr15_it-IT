<properties
    pageTitle="Selezionare le righe per eseguire la migrazione utilizzando una funzione di filtro (estensione Database) | Microsoft Azure"
    description="Informazioni su come selezionare le righe per eseguire la migrazione utilizzando una funzione di filtro."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Selezionare le righe per eseguire la migrazione utilizzando una funzione di filtro (estensione Database)

Se si archiviano freddo dati in un'altra tabella, è possibile configurare il Database di zoom per eseguire la migrazione l'intera tabella. Se la tabella contiene dati caldo e freddo, invece, è possibile specificare una funzione di filtro per selezionare le righe per eseguire la migrazione. Il predicato del filtro è una tabella all'interno del testo\-con valori di funzione. In questo argomento viene descritto come scrivere una tabella all'interno del testo\-con valori di funzione per selezionare le righe per eseguire la migrazione.

>   [AZURE.NOTE] Se viene fornita una funzione di filtro che è scarse, la migrazione dei dati esegue anche in modo non corretto. Database estensione applica la funzione di filtro alla tabella mediante l'operatore CROSS APPLY.

Se non si specifica una funzione di filtro, l'intera tabella viene eseguita la migrazione.

Quando si esegue il Database di abilitare per procedura guidata di zoom, è possibile eseguire la migrazione di un'intera tabella oppure è possibile specificare una funzione di filtro semplice della procedura guidata. Se si desidera utilizzare un tipo diverso della funzione di filtro per selezionare le righe per eseguire la migrazione, eseguire una delle operazioni seguenti.

-   Chiudere la procedura guidata ed eseguire l'istruzione ALTER TABLE per attivare zoom per la tabella e per specificare una funzione di filtro.

-   Eseguire l'istruzione ALTER TABLE per specificare una funzione di filtro dopo l'uscita la procedura guidata.

La sintassi ALTER TABLE per l'aggiunta di una funzione descritto più avanti in questo argomento.

## <a name="basic-requirements-for-the-filter-function"></a>Requisiti di base per la funzione filter
La tabella in linea\-funzione con valori necessari per un predicato di filtro Database zoom è simile nell'esempio seguente.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
I parametri per la funzione devono essere identificatori per le colonne della tabella.

Per evitare che le colonne utilizzate dalla funzione filter venga eliminato o modificato, è necessario associazione allo schema.

### <a name="return-value"></a>Valore restituito
Se la funzione restituisce un non\-risultati vuoto, la riga è idoneo per eseguire la migrazione. In caso contrario \- , ovvero se la funzione non restituisce un risultato \- la riga non è idoneo per eseguire la migrazione.

### <a name="conditions"></a>Condizioni
Il &lt; *predicato* &gt; possono essere composti da una condizione, di più condizioni con l'operatore logico e il join.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Ogni condizione, a sua volta essere costituito di una condizione di base o di più condizioni base uniti tramite join con l'operatore OR logico.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Condizioni di base
Una condizione di base è possibile eseguire una delle seguenti confronti.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Confronto tra un parametro di funzione a un'espressione costante. Ad esempio `@column1 < 1000`.

    Ecco un esempio che consente di verificare se il valore di una colonna *Data* è &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Applicare l'operatore IS NULL o non NULL a un parametro di funzione.

-   Utilizzare l'operatore IN per confrontare un parametro di funzione a un elenco di valori costanti.

    Ecco un esempio che consente di verificare se il valore di una *spedizione\_stato* colonna `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>Operatori di confronto
Gli operatori di confronto seguenti sono supportati.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Espressioni costanti
Le costanti utilizzate in una funzione di filtro possono essere qualsiasi espressione deterministica che può essere valutata quando si definisce la funzione. Espressioni costanti possono contenere quanto segue.

-   Valori letterali. Ad esempio `N’abc’, 123`.

-   Algebriche espressioni. Ad esempio `123 + 456`.

-   Funzioni deterministiche. Ad esempio `SQRT(900)`.

-   Conversioni deterministiche che utilizzano CAST o CONVERT. Ad esempio `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Altre espressioni
È possibile utilizzare la BETWEEN e non gli operatori BETWEEN se la funzione risultante è conforme alle regole descritte in questo documento dopo la sostituzione l'operatore BETWEEN e non tra gli operatori con l'equivalente AND e OR espressioni.

Non è possibile usare le sottoquery o non\-funzioni deterministiche, ad esempio RAND o GETDATE ().

## <a name="add-a-filter-function-to-a-table"></a>Aggiungere una funzione di filtro a una tabella
Aggiungere una funzione di filtro a una tabella, eseguire l'istruzione **ALTER TABLE** e specificare una tabella all'interno del testo esistente\-con valori di funzione come valore della **filtro\_PREDICATI** parametro. Per esempio:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Dopo la funzione è associata alla tabella come predicato, le seguenti condizioni sono vere.

-   La migrazione dei dati volta successiva che si verifica solo le righe per il quale la funzione restituisce un non\-viene eseguita la migrazione di un valore vuoto.

-   Le colonne utilizzate dalla funzione sono dello schema associato. Non è possibile modificare queste colonne come una tabella viene utilizzata la funzione come predicato il filtro.

Non è possibile eliminare la tabella in linea\-con valori di funzione come una tabella viene utilizzata la funzione come predicato il filtro.

>   [AZURE.NOTE] Per migliorare le prestazioni della funzione filter, creare un indice delle colonne utilizzate dalla funzione.

### <a name="passing-column-names-to-the-filter-function"></a>Passare i nomi di colonna per la funzione filter
Quando si assegna una funzione di filtro a una tabella, specificare i nomi delle colonne passato alla funzione di filtro con un nome a una parte. Se si specifica un nome in tre parti quando si passa nella colonna nomi successive query sull'estensione\-abilitata tabella avrà esito negativo.

Ad esempio, se si specifica un nome di colonna in tre parti, come illustrato nell'esempio seguente, l'istruzione verrà eseguita correttamente, ma successive query sulla tabella avrà esito negativo.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

Specificare se, tuttavia, la funzione di filtro con un nome di colonna di una parte come illustrato nell'esempio seguente.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Aggiungere una funzione di filtro dopo aver eseguito la procedura guidata  

Se si desidera utilizzare una funzione che non è possibile creare nella creazione guidata **Database di abilitare per zoom** , è possibile eseguire l'istruzione ALTER TABLE per specificare una funzione dopo l'uscita la procedura guidata. Prima di applicare una funzione, tuttavia, è necessario interrompere la migrazione dei dati che è già in corso e riportare dati migrati. (Per altre informazioni sulla perché questa operazione è necessaria, vedere [sostituire una funzione di filtro esistente](#replacePredicate).  

1. Invertire la direzione di migrazione e porta indietro che i dati già eseguita la migrazione. È possibile annullare l'operazione dopo l'avvio. Si verifica anche i costi in Azure per i trasferimenti di dati in uscita \(uscita\). Per ulteriori informazioni, vedere [come Azure prezzi works](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Attendere il termine della migrazione. È possibile verificare lo stato di **Monitoraggio di zoom Database** da SQL Server Management Studio o è possibile eseguire una query Visualizza **sys.dm_db_rda_migration_status** . Per ulteriori informazioni, vedere [Monitor e risolvere i problemi di migrazione dei dati](sql-server-stretch-database-monitor.md) o [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  

3. Creare la funzione di filtro che si desidera applicare alla tabella.  

4. Aggiungere la funzione alla tabella e riavviare la migrazione dei dati di Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Filtrare le righe per data
Nell'esempio seguente consente di migrare righe in cui la colonna **Data** contiene un valore precedente a 1 gennaio 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrare le righe per il valore di una colonna di stato
Nell'esempio seguente consente di migrare righe in cui la colonna **stato** contiene uno dei valori specificati.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrare le righe mediante una finestra scorrevole
Per filtrare le righe utilizzando una finestra scorrevole, tenere presenti i requisiti seguenti per la funzione filtro.

-   La funzione di. Pertanto non è possibile creare una funzione che viene ricalcolata automaticamente finestra scorrevole col passare del tempo.

-   La funzione utilizza l'associazione allo schema. Pertanto non è possibile semplicemente aggiornare la funzione "posizione" quotidianamente chiamando **ALTER FUNCTION** per spostare la finestra scorrevole.

Iniziare con una funzione di filtro come nell'esempio seguente, che esegue la migrazione delle righe in cui la colonna **systemEndTime** contiene un valore precedente a 1 gennaio 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Applicare la funzione di filtro per la tabella.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Quando si desidera aggiornare la finestra scorrevole, eseguire le operazioni seguenti.

1.  Creare una nuova funzione che specifica la nuova finestra scorrevole. Nell'esempio seguente consente di selezionare le date precedenti al 2 gennaio 2016, anziché il 1 ° gennaio 2016.

2.  Sostituire la funzione filter precedente con quello nuovo dalla chiamata **ALTER TABLE**, come illustrato nell'esempio seguente.

3. Facoltativamente, è possibile eliminare la precedente funzione filter che non si usa dalla chiamata **DROP FUNCTION**. (Questo passaggio non illustrato nell'esempio).

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Ulteriori esempi di funzioni di filtro valido

-   Nell'esempio seguente combina due condizioni di base usando l'operatore logico AND.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Nell'esempio seguente viene usata diverse condizioni e una conversione deterministica con Converti.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   Nell'esempio seguente utilizza le funzioni e operatori matematici.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   Nell'esempio seguente usa la BETWEEN e non BETWEEN operatori. Questo utilizzo è valido poiché la funzione risultante è conforme alle regole descritte in questo documento dopo la sostituzione l'operatore BETWEEN e non tra gli operatori con l'equivalente AND e OR espressioni.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    La funzione precedente è equivalente per la funzione seguente dopo la sostituzione di BETWEEN e non BETWEEN con l'equivalente AND e OR espressioni.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Esempi di funzioni di filtro che non sono validi

-   La funzione seguente non è valida perché contiene un non\-conversione deterministica.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   La funzione seguente non è valida perché contiene un non\-chiamata di funzione deterministica.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   La funzione seguente non è valida perché contiene una sottoquery.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Le funzioni seguenti non sono valide perché espressioni che utilizzano gli operatori algebrico o\-nelle funzioni deve restituire una costante quando si definisce la funzione. Non è possibile includere riferimenti a colonne nelle espressioni algebriche o chiamate.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   La funzione seguente non è valida perché viola le regole descritte di seguito dopo la sostituzione dell'operatore BETWEEN con l'espressione equivalente e.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    La funzione precedente è equivalente per la funzione seguente dopo la sostituzione dell'operatore BETWEEN con l'espressione e equivalente. Questa funzione non è valida perché condizioni base possono utilizzare solo l'operatore OR logico.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>La modalità estesa Database si applica la funzione filter
Database estensione applica la funzione di filtro per la tabella e determina idoneo delle righe utilizzando l'operatore CROSS APPLY. Per esempio:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Se la funzione restituisce un non\-risultati vuoto per la riga, la riga è idoneo per eseguire la migrazione.

## <a name="replacePredicate"></a>Sostituire una funzione di filtro esistente
È possibile sostituire le funzioni di filtro specificato in precedenza, eseguire nuovamente l'istruzione **ALTER TABLE** e specificare un nuovo valore per il **filtro\_PREDICATI** parametro. Per esempio:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
La nuova tabella in linea\-funzione con valori previsti i requisiti seguenti.

-   Nuova funzione deve essere meno restrittivo rispetto alla funzione precedente.

-   Tutti gli operatori che erano presenti nella funzione precedente deve essere presente nella nuova funzione.

-   Nuova funzione non può contenere operatori che non è presente nella funzione precedente.

-   Non è possibile modificare l'ordine degli argomenti di operatore.

-   Solo i valori costanti che fanno parte di un `<, <=, >, >=` confronto può essere modificato in modo che la funzione meno restrittivo.

### <a name="example-of-a-valid-replacement"></a>Esempio di sostituzione valida
Si supponga che la funzione seguente il predicato del filtro corrente.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La funzione seguente costituisce un'alternativa valida semplifica la costante nuova data (che specifica una data in un secondo momento limite) la funzione meno restrittivo.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Esempi di sostituzioni che non sono validi
La funzione seguente non è un'alternativa valida perché la costante nuova data (che specifica una data limita di versioni precedenti) non è la funzione meno restrittivo.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La funzione seguente non è un'alternativa valida perché uno degli operatori di confronto è stata rimossa.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
La funzione seguente non è un'alternativa valida poiché è stata aggiunta una nuova condizione con l'operatore logico AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Rimuovere una funzione di filtro da una tabella
Per eseguire la migrazione l'intera tabella anziché le righe selezionate, rimuovere la funzione esistente mediante l'impostazione **filtro\_PREDICATI** su null. Per esempio:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Dopo aver rimosso la funzione filter, tutte le righe nella tabella sono idonee per la migrazione. Di conseguenza, non è possibile specificare una funzione di filtro per la stessa tabella in un secondo momento a meno che non si riportare tutti i dati remoti per la tabella da Azure prima di tutto. Questa limitazione esistente per evitare la situazione in cui le righe che non sono idonee per la migrazione quando si fornisce una nuova funzione di filtro sono già state migrate in Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Controllare la funzione di filtro applicata a una tabella
Per verificare se la funzione di filtro applicata a una tabella, aprire la visualizzazione di catalogo **sys.remote\_dati\_archivio\_tabelle** e controllare il valore della **filtro\_predicato** colonna. Se il valore null, l'intera tabella sia idoneo per l'archiviazione. Per ulteriori informazioni, vedere [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Note sulla protezione per le funzioni di filtro  
Un account compromesso con privilegi db_owner possono eseguire le operazioni seguenti.  

-   Creare e applicare una funzione con valori di tabella che richiede grandi quantità di risorse del server o è in attesa per un periodo risultante negazione del servizio.  

-   Creare e applicare una funzione con valori di tabella che consente di dedurre il contenuto di una tabella per cui l'utente è stato esplicitamente negato accesso in lettura.  

## <a name="see-also"></a>Vedere anche

[Istruzione ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
