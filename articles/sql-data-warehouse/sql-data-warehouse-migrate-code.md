<properties
   pageTitle="Eseguire la migrazione del codice SQL in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la migrazione di codice SQL Azure SQL Data warehouse per lo sviluppo di soluzioni."
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
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Eseguire la migrazione del codice SQL in SQL Data Warehouse

Durante la migrazione del codice da un altro database SQL Data warehouse, è probabile che sarà necessario apportare modifiche alla base del codice. Alcune caratteristiche SQL Data Warehouse in modo significativo migliorare le prestazioni quando sono progettati per essere distribuita. Tuttavia, per mantenere proporzioni e le prestazioni, alcune caratteristiche anche non sono disponibili.

## <a name="common-t-sql-limitations"></a>Limitazioni T-SQL comuni

L'elenco seguente sono riepilogate le funzionalità più comuni che non sono supportate in Warehouse di dati di SQL Azure. I collegamenti è comunque soluzioni alternative per la caratteristica non supportata:

- [Join ANSI sugli aggiornamenti][]
- [Join ANSI in eliminazioni][]
- [istruzione merge][]
- database cross join
- [cursori][]
- [SELEZIONARE. IN][]
- [INSERIMENTO DI.. EXEC][]
- clausola output
- funzioni definite dall'utente all'interno del testo
- funzioni a istruzioni multiple
- [espressioni di tabella comuni](#Common-table-expressions)
- [ricorsiva espressioni di tabella comuni (CTE)] (#Recursive-common-table-expressions-(CTE)
- Procedure e funzioni CLR
- funzione $partition
- variabili di tabella
- parametri dei valori di tabella
- transazioni distribuite
- commit / ripristino lavoro
- salvare transazione
- contesti di esecuzione (EXECUTE AS)
- [con rollup clausola Group by / cubo / set di Opzioni raggruppamento][]
- [livelli di annidamento oltre 8][]
- [aggiornamento di visualizzazioni][]
- [uso delle selezionare assegnazione variabile][]
- [non tipo di dati MAX per stringhe SQL dinamiche][]

Per la maggior parte di queste limitazioni da poterli intorno. Spiegazioni sono vedere gli articoli di sviluppo pertinenti indicati in precedenza.

## <a name="supported-cte-features"></a>Caratteristiche supportate CTE

Espressioni di tabella comuni (CTE) sono parzialmente supportate in SQL Data Warehouse.  Le caratteristiche CTE seguenti sono attualmente supportate:

- È possibile specificare una CTE in un'istruzione SELECT.
- È possibile specificare una CTE in un'istruzione CREATE VIEW.
- È possibile specificare una CTE in un'istruzione di creazione tabella come selezionare (CTAS).
- È possibile specificare una CTE in un'istruzione di creare remoto tabella come selezionare (CRTAS).
- È possibile specificare una CTE in un'istruzione di creare esterni tabella come selezionare (CETAS).
- Possibile fare riferimento a una tabella remota da una CTE.
- Una tabella esterna è possibile fare riferimento da una CTE.
- Più le rispettive definizioni ai query possono essere definite in una CTE.

## <a name="cte-limitations"></a>Limitazioni CTE

Espressioni di tabella comuni presentano alcune limitazioni SQL Data Warehouse, inclusi:

- Una CTE deve essere seguita da una singola istruzione SELECT. Inserisci, Aggiorna, Elimina e istruzioni di stampa unione non sono supportate.
- Un'espressione di tabella comuni che include riferimenti a se stesso (un'espressione comune ricorsiva tabella) non è supportata (vedere la sezione).
- Non è consentito specificare più monitor con clausola in una CTE. Ad esempio, se un CTE_query_definition contiene una sottoquery, tale sottoquery non può contenere un annidate con clausola che definisce un'altra CTE.
- Clausola ORDER BY può essere utilizzata in CTE_query_definition, solo se viene specificata la clausola TOP.
- Quando una CTE viene utilizzata in un'istruzione che fa parte di un batch di istruzione prima che deve essere seguita da un punto e virgola.
- Quando utilizzato nelle istruzioni preparate da sp_prepare, CTE comporterà nello stesso modo delle altre istruzioni SELECT in PDW. Tuttavia, se CTE vengono utilizzate come parte del CETAS preparato da sp_prepare, il comportamento rinviare da SQL Server e altre istruzioni PDW dovuta all'implementazione dell'associazione per sp_prepare. Se i riferimenti CTE utilizzata una colonna errata che non esiste nel CTE selezionare il sp_prepare passerà senza rilevare l'errore, ma verrà generato l'errore durante il sp_execute invece.

## <a name="recursive-ctes"></a>CTE ricorsive

CTE ricorsiva non sono supportate in SQL Data Warehouse.  Migraion di CTE ricorsiva possono essere leggermente completo ed è il modo migliore per suddividere l'in più passaggi. In genere è possibile utilizzare un ciclo e popolare una tabella, mentre si scorre le query provvisorio ricorsiva. Dopo la tabella temporanea viene popolata è quindi possibile restituire i dati come un singolo set di risultati. Un approccio simile è stato utilizzato per risolvere `GROUP BY WITH CUBE` nell'articolo [con rollup clausola group by / cubo / imposta le opzioni di raggruppamento][] .

## <a name="unsupported-system-functions"></a>Funzioni di sistema non supportate

Esistono alcune funzioni di sistema che non sono supportate. Alcune delle quelli principali che è possibile che in genere utilizzati in data warehouse sono:

- NEWSEQUENTIALID () PER
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Alcuni di questi problemi può essere risolto.

## <a name="rowcount-workaround"></a>@@ROWCOUNTsoluzione alternativa

Per ovviare mancanza di supporto per @@ROWCOUNT, creare una stored procedure che verrà recuperare l'ultimo numero di righe da sys.dm_pdw_request_steps e quindi eseguire `EXEC LastRowCount` dopo un'istruzione DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco completo di tutte le istruzioni SQL T supportati, vedere [argomenti Transact-SQL][].

<!--Image references-->

<!--Article references-->
[Join ANSI sugli aggiornamenti]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Join ANSI in eliminazioni]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[istruzione merge]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERIMENTO DI.. EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Argomenti in Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[cursori]: ./sql-data-warehouse-develop-loops.md
[SELEZIONARE. IN]: ./sql-data-warehouse-develop-ctas.md#selectinto
[con rollup clausola Group by / cubo / set di Opzioni raggruppamento]: ./sql-data-warehouse-develop-group-by-options.md
[livelli di annidamento oltre 8]: ./sql-data-warehouse-develop-transactions.md
[aggiornamento di visualizzazioni]: ./sql-data-warehouse-develop-views.md
[uso delle selezionare assegnazione variabile]: ./sql-data-warehouse-develop-variable-assignment.md
[non tipo di dati MAX per stringhe SQL dinamiche]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
