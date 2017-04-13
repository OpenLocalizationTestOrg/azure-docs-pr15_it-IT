 <properties
    pageTitle="Come utilizzare il batch per migliorare le prestazioni dell'applicazione di Database SQL Azure"
    description="L'argomento fornisce una prova tale batch operazioni di database imroves notevolmente la velocità e scalabilità delle applicazioni di Database SQL Azure. Sebbene queste tecniche batch funzionano per un database SQL Server, dell'articolo è incentrato Azure."
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Come utilizzare il batch per migliorare le prestazioni dell'applicazione di Database SQL

Batch notevolmente le operazioni di Database SQL Azure consente di migliorare le prestazioni e scalabilità delle applicazioni. Per comprendere i vantaggi, la prima parte di questo articolo vengono illustrati alcuni risultati dei test campione che confrontano le richieste di sequenziale e in batch a un Database SQL. Il resto dell'articolo mostra le tecniche, scenari e considerazioni che consentono di utilizzare il batch correttamente nelle applicazioni Azure.

**Autori**: Jason Roth, Silvano Coriani, Trent Swanson (fondo scala 180 Inc)

**I revisori**: Conor Cunningham, Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>Perché è batch importanti per Database SQL?
Il batch di chiamate a un servizio remoto è una strategia di gestione delle nota per migliorare le prestazioni e scalabilità. Esistono fissi i costi di elaborazione a tutte le interazioni con un servizio remoto, ad esempio serializzazione, trasferimento di rete e deserializzazione. Imballaggio quante transazioni separate in un unico batch riduce tali costi.

In questo articolo si desidera esaminare vari Database di SQL in batch strategie e scenari. Anche se queste strategie sono anche importante per le applicazioni che utilizzano SQL Server locale, esistono diversi motivi per evidenziare l'utilizzo di batch per Database SQL:

- Esiste un potenziale aumento della latenza di rete accesso al Database SQL, in particolare se si accede a Database SQL di dall'esterno la stessa data center Microsoft Azure.
- Le caratteristiche multi-tenant di Database SQL di significa che l'efficienza dei dati access layer è correlata al scalabilità complessiva del database. Database SQL devono evitare qualsiasi tenant o singolo utente monopolizzino le risorse di database a danno degli altri tenant. In risposta a utilizzo che superano le quote di predefiniti, Database SQL di ridurre velocità o rispondere con limitazione eccezioni. Efficienza, ad esempio la divisione in batch, consentono di eseguire altre operazioni sul Database di SQL prima di raggiungere questi limiti. 
- Il batch utile anche per architetture che utilizzano più database (sharding). L'efficienza delle interazioni con ogni unità di database è ancora un fattore chiave per la scalabilità complessiva. 

Uno dei vantaggi dell'utilizzo di Database SQL è che non è necessario gestire i server che ospitano il database. In questa infrastruttura gestita tuttavia indica che è necessario considerare le ottimizzazioni di database in modo leggermente diverso. È non possibile cercare non è più per migliorare l'infrastruttura di rete o hardware database. Microsoft Azure controlla tali ambienti. Area di lavoro principale che è possibile controllare è come l'applicazione interagisce con Database SQL. Il batch corrisponde a uno di questi ottimizzazioni. 

La prima parte del foglio vengono esaminate diverse tecniche di batch per applicazioni .NET che utilizzano Database SQL. Le ultime due sezioni coprono batch scenari e linee guida.

## <a name="batching-strategies"></a>Strategie batch

### <a name="note-about-timing-results-in-this-topic"></a>Nota sui risultati dei tempi in questo argomento
>[AZURE.NOTE] Risultati non sono benchmark ma sono progettati per illustrare **le prestazioni relativa**. Gli intervalli si basano su una media di almeno 10 esecuzioni. Operazioni disponibili viene inserito in una tabella vuota. Questi test sono stati misurati pre-V12 e non corrispondono necessariamente alla velocità che potrebbero verificarsi in un database V12 utilizzando i nuovi [livelli di servizio](sql-database-service-tiers.md). Il vantaggio relativo della tecnica batch dovrebbe essere analogo.

### <a name="transactions"></a>Transazioni
Sembra anomalo per iniziare una revisione di batch esaminando le transazioni. Ma l'utilizzo delle transazioni lato client è un effetto di batch sul lato server sottile che consente di migliorare le prestazioni. E aggiungere operazioni con solo alcune righe di codice, in modo che forniscono un modo rapido per migliorare le prestazioni di esecuzione delle operazioni in sequenza.

Valutare la possibilità di codice c# seguente che contiene una sequenza di inserimento e aggiornare le operazioni su una tabella semplice.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Il seguente codice ADO.NET in sequenza consente di eseguire queste operazioni.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

Il modo migliore per ottimizzare il codice è implementare sorta di lato client in batch delle chiamate. Ma c'è un modo semplice per migliorare le prestazioni del codice inserendo semplicemente la sequenza di chiamate in una transazione. Ecco lo stesso codice che utilizza una transazione.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

Le transazioni sono effettivamente in uso in entrambi gli esempi. Nel primo esempio ogni singolo chiamata è una transazione implicita. Nel secondo esempio, una transazione esplicita include tutte le chiamate. Per la documentazione per la [scrittura in avanti log delle transazioni](https://msdn.microsoft.com/library/ms186259.aspx), i record del log vengono eliminati dal disco, al commit della transazione. In modo da includere altre chiamate in una transazione, la scrittura nel log delle transazioni causare ritardi fino a quando non viene eseguito il commit della transazione. In effetti, si sta abilitando batch per la scrittura nel log delle transazioni del server.

Nella tabella seguente mostra alcune risultati dei test ad hoc. I test eseguiti gli inserimenti sequenziali stessi con e senza transazioni. Per ulteriori informazioni, il primo set di test è stato eseguito in modalità remota da un computer portatile al database di Microsoft Azure. Il secondo set di test è stato eseguito da un servizio cloud e database che entrambi si trova all'interno di Data Center di Microsoft Azure stesso (Ovest Stati Uniti). Nella tabella seguente mostra la durata in millisecondi di inserimenti sequenziali con e senza transazioni.

**In locale in Azure**:

| Operazioni | Nessuna transazione (ms) | Transazione (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Azure a Azure (stesso Data Center)**:

| Operazioni | Nessuna transazione (ms) | Transazione (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] Risultati non sono benchmark. Vedere la [Nota sui risultati dei tempi in questo argomento](#note-about-timing-results-in-this-topic).

Basato sui risultati del test precedente, disposizione una singola operazione in una transazione effettivamente riduzione delle prestazioni. Ma quando si aumenta il numero di operazioni all'interno di una sola transazione, il miglioramento delle prestazioni diventa più contrassegnato. La differenza di prestazioni è più evidente anche quando si verificano tutte le operazioni all'interno del Data Center del Microsoft Azure. L'incremento di latenza dell'uso di Database SQL da di fuori del Data Center del Microsoft Azure leggibile il miglioramento delle prestazioni di utilizzo delle transazioni.

Anche se l'utilizzo delle transazioni è possibile migliorare le prestazioni, continuare a [seguire procedure consigliate per le transazioni e connessioni](https://msdn.microsoft.com/library/ms187484.aspx). Mantenere la transazione più breve e chiudere la connessione al database al termine il lavoro. L'istruzione dell'esempio precedente assicura che la connessione viene chiusa dopo avere completato il blocco di codice successive.

L'esempio precedente illustra che è possibile aggiungere una transazione locale a qualsiasi codice ADO.NET con due righe. Transazioni offrono un modo rapido per migliorare le prestazioni di codice che produca Inserisci sequenza, aggiornare ed eliminare operazioni. Tuttavia, per le prestazioni migliori, provare a modificare il codice per poter usufruire di batch lato client, ad esempio i parametri di valori di tabella.

Per ulteriori informazioni sulle transazioni ADO.NET, vedere [Le transazioni locali in ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### <a name="table-valued-parameters"></a>Parametri con valori di tabella
Valori di tabella parametri supportano tipi di tabella definite dall'utente come parametri nelle istruzioni Transact-SQL, stored procedure e funzioni. Questa tecnica batch lato client consente di inviare più righe di dati all'interno del parametro con valori di tabella. Per utilizzare parametri con valori di tabella, definire un tipo di tabella. L'istruzione Transact-SQL seguente viene creato un tipo di tabella denominato **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

Nel codice, si crea un **DataTable** con l'esatti stessi nomi e tipi di tipo table. Passare questa **DataTable** un parametro in una query di testo o chiamata di stored procedure. Nell'esempio seguente viene illustrata questa tecnica:

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

Nell'esempio precedente, l'oggetto **SqlCommand** inserisce le righe da un parametro di valori di tabella, **@TestTvp**. Oggetto **DataTable** creato in precedenza è assegnato a questo parametro con il metodo di **SqlCommand.Parameters.Add** . Batch viene inserito in una chiamata in modo significativo aumenta le prestazioni in inserimenti sequenziali.

Per migliorare ulteriormente dell'esempio precedente, utilizzare una stored procedure anziché un comando basata su testo. Il comando Transact-SQL seguente crea una stored procedure che accetta il parametro **SimpleTestTableType** valori di tabella.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Modificare la dichiarazione di oggetto **SqlCommand** nell'esempio precedente per le operazioni seguenti.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Nella maggior parte dei casi, parametri con valori di tabella presentano prestazioni migliori o equivalenti rispetto altre tecniche batch. Parametri con valori di tabella sono spesso preferibili, perché sono più flessibili rispetto alle altre opzioni. Altre tecniche, ad esempio la copia di massa SQL, ad esempio, consentono solo l'inserimento di nuove righe. Ma con i parametri con valori di tabella, è possibile utilizzare logica della stored procedure per determinare quali righe sono gli aggiornamenti e quali sono inserisce. Inoltre è possibile modificare il tipo di tabella per contenere una colonna di "Operazione" che indica se la riga specificata deve essere inserita, aggiornata o eliminata.

Nella tabella seguente mostra i risultati dei test ad hoc per l'utilizzo di parametri con valori di tabella in millisecondi.

| Operazioni | In locale in Azure (ms)  | Azure Data Center stesso (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] Risultati non sono benchmark. Vedere la [Nota sui risultati dei tempi in questo argomento](#note-about-timing-results-in-this-topic).

Miglioramento delle prestazioni rispetto batch è immediatamente visibile. In controllo sequenza precedente, 1000 operazioni sono state 129 secondi di fuori del centro dati e 21 secondi nel centro dati. Ma con valori di tabella parametri 1000 operazioni richiedono solo 2.6 secondi di fuori del centro dati e 0,4 secondi nel centro dati.

Per ulteriori informazioni sui parametri con valori di tabella, vedere [Table-Valued Parameters](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Copia di massa SQL
Copia di massa SQL è inoltre possibile inserire grandi quantità di dati in un database di destinazione. Applicazioni .NET è possono utilizzare la classe **SqlBulkCopy** per eseguire operazioni di inserimento in blocco. **SqlBulkCopy** è analogo all'istruzione Transact-SQL, **Inserimento di massa**, **Bcp.exe**o lo strumento della riga di comando. Nell'esempio seguente mostra come copiare le righe in **DataTable**, tabella, tabella di destinazione in SQL Server, tabella di origine.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Esistono alcuni casi in cui copia in blocco, è preferibile parametri con valori di tabella. Vedere la tabella di confronto dei valori di tabella parametri e inserimento di massa operazioni nell'argomento [Table-Valued Parameters](https://msdn.microsoft.com/library/bb510489.aspx).

I risultati dei test reporting ad-hoc seguente mostra le prestazioni della divisione in batch con **SqlBulkCopy** in millisecondi.

| Operazioni | In locale in Azure (ms)  | Azure Data Center stesso (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] Risultati non sono benchmark. Vedere la [Nota sui risultati dei tempi in questo argomento](#note-about-timing-results-in-this-topic).

Di dimensioni inferiori batch, utilizzare valori di tabella parametri ha superato la classe **SqlBulkCopy** . Tuttavia, **SqlBulkCopy** eseguire 12-31% più velocemente di quanto parametri con valori di tabella per i test di 1.000 e 10.000 righe. Come valori di tabella parametri **SqlBulkCopy** è consigliabile per gli inserimenti in batch, soprattutto rispetto alle prestazioni di esecuzione delle operazioni non in blocco.

Per ulteriori informazioni sulla copia di massa ADO.NET, vedere [Operazioni di copia in blocco in SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Istruzioni parametri inserire più righe
Un'alternativa per piccoli batch consiste nel creare un'istruzione INSERT grande parametri che consente di inserire più righe. Nell'esempio seguente viene illustrata questa tecnica.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

In questo esempio è utilizzato per visualizzare il concetto di base. Uno scenario con più realistico da riprodurre a ciclo continuo entità necessari per creare la stringa di query e i parametri del comando contemporaneamente. Sono limitate a un totale 2100 parametri di query, in modo che consente di limitare il numero totale di righe che possono essere elaborati in questo modo.

I risultati dei test ad hoc seguente mostra le prestazioni di questo tipo di istruzione insert in millisecondi.

| Operazioni | Valori di tabella parametri (ms) | Istruzione INSERT (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] Risultati non sono benchmark. Vedere la [Nota sui risultati dei tempi in questo argomento](#note-about-timing-results-in-this-topic).

Questo approccio può essere leggermente più veloce per batch siano meno di 100 righe. Sebbene il miglioramento è piccolo, questa tecnica è un'altra opzione che potrebbe funzionare correttamente in scenario dell'applicazione specifica.

### <a name="dataadapter"></a>DataAdapter
La classe **DataAdapter** consente di modificare un oggetto di **set di dati** e quindi inviare le modifiche come inserire, aggiornare ed eliminare operazioni. Se si usa **DataAdapter** in questo modo, è importante tenere presente che vengono eseguite chiamate distinte per ogni operazione distinti. Per migliorare le prestazioni, utilizzare la proprietà **UpdateBatchSize** al numero di operazioni batch alla volta. Per ulteriori informazioni, vedere [Esecuzione di operazioni Batch utilizzando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Framework entità
Framework entità attualmente non supporta il batch. Gli sviluppatori diversi nella community di sono tentato di dimostrare soluzioni alternative, ad esempio override il metodo **SaveChanges** . Ma le soluzioni sono in genere complesso e personalizzato per l'applicazione e il modello di dati. Il progetto di codeplex entità Framework attualmente dispone di una pagina di discussione su richiesta questa caratteristica. Per visualizzare la discussione, vedere [Le note delle riunioni di progettazione: 2 agosto 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Per completezza, siamo consapevoli che è importante parlare XML come una strategia batch. Tuttavia, l'utilizzo di XML presenta non vantaggi rispetto ad altri metodi e svantaggi. L'approccio è simile a parametri con valori di tabella, ma un file XML o una stringa viene passato a una stored procedure invece di una tabella definite dall'utente. Stored procedure analizza i comandi della stored procedure.

Esistono diversi svantaggi di questo approccio:

- Utilizzo di XML può essere difficile e soggetto a errori.
- Analisi XML per il database può essere CPU che richiede significative attività.
- Nella maggior parte dei casi, questo metodo è inferiore a valori di tabella parametri.

Per questi motivi, non è consigliabile l'utilizzo di XML per query in batch.

## <a name="batching-considerations"></a>Considerazioni sulla divisione in batch
Nelle sezioni seguenti sono disponibili altre indicazioni per l'utilizzo di batch nelle applicazioni di Database SQL.

### <a name="tradeoffs"></a>Compromesso
A seconda dell'architettura, la divisione in batch può comportare un compromesso tra prestazioni e capacità. Si consideri ad esempio lo scenario in cui il proprio ruolo in modo imprevisto è inattivo. Se si perde una riga di dati, l'impatto è inferiore all'impatto di perdere un batch di grandi dimensioni di righe non inviate. Esiste un rischio maggiore quando si buffer righe prima dell'invio al database in un intervallo di tempo specificato.

A causa di compromesso, valutare il tipo di operazioni in batch. In modo più consistente batch (batch di dimensioni maggiori e più tempo windows) con dati meno critici.

### <a name="batch-size"></a>Dimensioni del batch
Nei test eseguiti, si è verificato in genere utile accedendo piccoli batch di grandi dimensioni. Infatti, questa suddivisione spesso condotto alla visualizzazione rallentare le prestazioni rispetto l'invio di un unico batch di grandi dimensioni. Si consideri ad esempio uno scenario in cui si desidera inserire le righe di 1000. Nella tabella seguente mostra la durata di utilizzare parametri con valori di tabella per inserire righe 1000 quando suddivisi in batch più piccoli.

| Dimensioni del batch | Iterazioni | Valori di tabella parametri (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] Risultati non sono benchmark. Vedere la [Nota sui risultati dei tempi in questo argomento](#note-about-timing-results-in-this-topic).

È possibile vedere che le massime prestazioni per le righe di 1000 siano a inviare messaggi in una sola volta. In altri test (non illustrata di seguito) si è verificato un lieve miglioramento delle prestazioni per suddividere un batch di 10000 riga in due batch di 5000. Ma lo schema della tabella per questi test è semplice, pertanto è consigliabile eseguire test su dati specifici e le dimensioni di batch per verificare questi risultati.

Un'altra fattori da prendere in considerazione sono che se il batch totale diventa troppo grande, Database SQL potrebbe limitare e rifiutare eseguire il commit il batch. Per ottenere risultati ottimali, verificare il proprio scenario specifico per determinare se è presente una dimensione batch ideale. Rendere le dimensioni del batch configurabile in fase di esecuzione per abilitare le rettifiche rapide in base alle prestazioni o errori.

Infine, bilanciare le dimensioni del batch con i rischi associati batch. Se sono presenti errori temporanei o il ruolo non riesce, considerare le conseguenze la ripetizione dell'operazione di perdere i dati nel batch.

### <a name="parallel-processing"></a>Elaborazione in parallelo
Cosa fare se si ha l'approccio riduzione delle dimensioni del batch ma utilizzati più thread per eseguire il lavoro? Di nuovo i test è stato più batch più piccoli multithreading in genere eseguiti peggiori di un unico batch più grande. Il test seguente tenta di inserire 1000 righe in una o più batch in parallelo. Questo test Mostra come ulteriori batch simultaneo ridotto effettivamente le prestazioni.

| Dimensioni del batch [iterazioni] | Due thread (ms) | Quattro thread (ms) | Sei thread (ms) |
| -------- | --- | --- | --- |
| [1] 1000 | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] Risultati non sono benchmark. Vedere la [Nota sui risultati dei tempi in questo argomento](#note-about-timing-results-in-this-topic).

Esistono diversi motivi potenziali per la riduzione delle prestazioni a causa di parallelismo:

- Ci sono più chiamate simultanee rete anziché una.
- Più operazioni su una singola tabella causando conflitti e blocco.
- Esistono costi associati multithreading.
- Le spese di apertura di più connessioni compensa il vantaggio di elaborazione in parallelo.

Se la destinazione diverse tabelle o database, è possibile vedere alcune delle prestazioni di ottenere con questa strategia. Sharding di database o federazione sarebbero uno scenario di questo approccio. Sharding vengono usati più database e indirizza dati diversi per ogni database. Se ogni batch di piccole dimensioni agirà da un altro database, quindi eseguire le operazioni in parallelo può essere più efficiente. Tuttavia, il miglioramento delle prestazioni non è sufficientemente significativo da usare come base per una decisione da utilizzare sharding database della soluzione.

In alcuni progetti, esecuzione in parallelo di più piccoli batch causando aumenterà la velocità di richieste di tipo in un sistema in condizioni di carico. In questo caso, anche se è più rapido per l'elaborazione di un unico batch di dimensioni maggiori, l'elaborazione batch multiple in parallelo potrebbe essere più efficiente.

Se si utilizza l'esecuzione in parallelo, è consigliabile controllare il numero massimo di thread di lavoro. Un numero più piccolo può comportare meno conflitti e tempi di esecuzione. Inoltre, prendere in considerazione il carico aggiuntivo che si inserisce nel database di destinazione sia in connessioni e le transazioni.

### <a name="related-performance-factors"></a>Fattori relativi alle prestazioni correlati
Tipico indicazioni sulle prestazioni di database influisce anche sui batch. Ad esempio, inserire le prestazioni ridotte per le tabelle che dispongono di una chiave primaria di grandi dimensioni o molti indici.

Se i valori di tabella parametri utilizzano una stored procedure, è possibile utilizzare il comando **SET NOCOUNT ON** all'inizio della procedura. Questa istruzione elimina la restituzione del conteggio delle righe interessate nella procedura. Tuttavia, nei test eseguiti, l'uso di **SET NOCOUNT ON** non ha alcun effetto o riduzione delle prestazioni. La procedura di prova archiviato è stato semplice con un' unica **Inserisci** dal parametro valori di tabella. È possibile che più complesse stored procedure sarebbero utile dell'istruzione. Ma non presuppongono che aggiunta automatica di **SET NOCOUNT ON** alla stored procedure migliora le prestazioni. Per comprendere l'effetto, verificare la stored procedure con e senza l'istruzione **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Scenari batch
Nelle sezioni seguenti viene descritto come utilizzare parametri con valori di tabella in tre scenari di applicazioni. Il primo scenario mostra come buffer e batch possono collaborare. Il secondo scenario migliora le prestazioni eseguendo le operazioni master / dettaglio in una chiamata singola stored procedure. Lo scenario finale viene illustrato come utilizzare parametri con valori di tabella in un'operazione "UPSERT".

### <a name="buffering"></a>Il buffer di
Anche se sono presenti alcuni scenari in cui sono candidato ottimale per la divisione in batch, sono disponibili molti scenari che sfruttare del batch di elaborazione posticipato. Tuttavia, elaborazione ritardata esegue inoltre un rischio maggiore che i dati vengono persi in caso di un errore imprevisto. È importante valutare il rischio di considerare le conseguenze.

Si consideri ad esempio un'applicazione web che tiene traccia della cronologia di spostamento di ogni utente. Per ogni richiesta di pagina, l'applicazione esegua un database di chiamata per registrare la visualizzazione dell'utente pagina. Ma è possibile ottenere prestazioni e scalabilità, il buffer attività spostamento degli utenti e quindi inviare i dati del database in batch. È possibile attivare l'aggiornamento di database per il tempo trascorso e/o la dimensione del buffer. Ad esempio una regola è possibile specificare che il batch deve essere elaborato dopo 20 secondi o quando il buffer raggiunge gli elementi di 1000.

Nell'esempio seguente usa [Estensioni riattivare - ricezione](https://msdn.microsoft.com/data/gg577609) per l'elaborazione di buffer eventi generati da una classe monitoraggio. Quando il buffer si riempie o viene raggiunto il timeout, il batch dei dati utente verrà inviato al database con un parametro di valori di tabella.

La classe NavHistoryData seguente modelli i dettagli della struttura di spostamento utente. Contiene informazioni di base, ad esempio l'ID utente, l'URL di accesso e l'ora dell'accesso.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

La classe NavHistoryDataMonitor è responsabile per la memorizzazione i dati di spostamento dell'utente al database. Contiene un metodo RecordUserNavigationEntry, risponde generando un evento **OnAdded** . Il codice seguente illustra la logica costruttore che utilizza ricezione per creare un insieme di presenza in base all'evento. Quindi sottoscrive questa raccolta presenza con il metodo di Buffer. L'overload specifica che il buffer deve essere inviato ogni 20 secondi o 1000 voci.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

Il gestore converte tutti gli elementi memorizzati in un tipo di valori di tabella e quindi passa questo tipo a una stored procedure che elabora il batch. Il codice seguente è illustrata la definizione di completamento per le NavHistoryDataEventArgs e le classi NavHistoryDataMonitor.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

Per utilizzare questa classe buffer, viene creato un oggetto NavHistoryDataMonitor statico. Ogni volta che un utente accede a una pagina, l'applicazione chiama il metodo NavHistoryDataMonitor.RecordUserNavigationEntry. La logica di buffer procede a prendersi cura della inviare queste voci al database in batch.

### <a name="master-detail"></a>Dettagli master
Parametri con valori di tabella sono utili per scenari Inserisci semplici. Tuttavia, può essere più complessa per inserimenti batch che includono più di una tabella. Lo scenario "master/dettaglio" è un ottimo esempio. La tabella master identifica l'entità primaria. Uno o più tabelle dettagli memorizzare più dati sull'entità. In questo scenario, relazioni di chiave esterna applicare le relazioni dei dettagli a un'entità master univoco. Valutare la possibilità di una versione semplificata di una tabella PurchaseOrder e la relativa tabella OrderDetail associata. Transact-SQL seguente crea la tabella PurchaseOrder con quattro colonne: OrderID, OrderDate, CustomerID e stato.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Ogni ordine contiene uno o più prodotti acquistati. Queste informazioni vengono acquisite nella tabella PurchaseOrderDetail. Transact-SQL seguente crea tabella PurchaseOrderDetail con cinque colonne: OrderID, OrderDetailID, ProductID, UnitPrice e OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

La colonna OrderID descritti nella tabella PurchaseOrderDetail dovrà fare riferimento a un ordine dalla tabella PurchaseOrder. La seguente definizione di una chiave esterna vengono applicate il vincolo.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Per utilizzare parametri con valori di tabella, è necessario disporre di un tipo di tabella definite dall'utente per ogni tabella di destinazione.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Definire una stored procedure che accetta tabelle questi tipi. Questa procedura consente a un'applicazione in batch localmente un set di ordini e dettagli ordini in un'unica chiamata. Transact-SQL seguente offre la dichiarazione di completamento stored procedure per questo esempio di ordine di acquisto.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

In questo esempio, definito localmente @IdentityLink tabella vengono memorizzati i valori effettivi OrderID da righe appena inserite. Questi identificatori di ordine sono diversi dai valori IDOrdine temporanei il @orders e @details parametri con valori di tabella. Per questo motivo, il @IdentityLink tabella quindi si connette i valori IDOrdine il @orders parametro per i valori IDOrdine reali per le nuove righe nella tabella PurchaseOrder. Dopo il passaggio di @IdentityLink tabella può facilitare l'inserimento dei dettagli dell'ordine con OrderID effettivi che soddisfa il vincolo di chiave esterna.

Codice o da altre chiamate Transact-SQL, è possibile utilizzare questa stored procedure. Vedere la sezione valori di tabella parametri di questo documento per un esempio di codice. Transact-SQL seguente viene illustrato come chiamare il sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

Questa soluzione consente ogni batch di usare un set di valori IDOrdine che inizia da 1. Questi valori IDOrdine temporanei descrivono le relazioni nel batch, ma i valori effettivi OrderID sono determinati al momento dell'operazione di inserimento. È possibile eseguire ripetutamente le stesse istruzioni dell'esempio precedente e generare ordini univoci nel database. Per questo motivo, è consigliabile aggiungere ulteriori logica di codice o database che impedisce gli ordini duplicati quando si usa questo batch tecnica.

In questo esempio viene ancora più complesse operazioni di database, ad esempio operazioni master / dettaglio, possono essere ordinate utilizzando parametri con valori di tabella.

### <a name="upsert"></a>UPSERT
Un altro scenario batch comporta l'aggiornamento simultaneo inserimento nuove righe e le righe esistenti. Questa operazione è definita talvolta un'operazione "UPSERT" (aggiornamento + INS). Invece di effettuare chiamate separate per inserire e aggiornare, l'istruzione unione è ideale per questa operazione. L'istruzione unione è possibile eseguire le opzioni Inserisci e aggiorna le operazioni in un'unica chiamata.

Parametri con valori di tabella è utilizzabile con l'istruzione di stampa unione per eseguire aggiornamenti e inserimenti. Si consideri ad esempio una tabella dei dipendenti semplificata che contiene le colonne seguenti: campo ID dipendente, nome, cognome, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
In questo esempio, è possibile utilizzare il fatto che il SocialSecurityNumber sia univoco per eseguire un'unione di più dipendenti. Creare innanzitutto il tipo di tabella definite dall'utente:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Successivamente, creare una stored procedure o scrivere codice che utilizza l'istruzione di stampa unione per eseguire l'aggiornamento e inserire. Nell'esempio seguente viene utilizzata l'istruzione MERGE su un parametro di valori di tabella @employees, di tipo EmployeeTableType. Il contenuto del @employees tabella non vengono visualizzate qui.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Per ulteriori informazioni, vedere la documentazione ed esempi per l'istruzione di stampa unione. Anche se è possibile eseguire le stesse operazioni in un passaggio più archiviato chiamata di procedura con separare Inserisci e le operazioni di aggiornamento, l'istruzione MERGE risulta più efficiente. Codice di database è inoltre possibile creare le chiamate Transact-SQL che utilizzano l'istruzione MERGE senza due chiamate di database per INSERT e UPDATE.

## <a name="recommendation-summary"></a>Consigli

L'elenco seguente include un riepilogo dei suggerimenti batch descritte in questo argomento:

- Usare il buffer e batch per migliorare le prestazioni e scalabilità delle applicazioni di Database SQL.
- Informazioni sui compromessi nella tra batch buffer e adattabilità. In caso di errore ruolo, il rischio di perdere un batch non elaborato dei dati business critici potrebbe superano la potenza di batch.
- Tentativo di mantenere tutte le chiamate al database di un singolo Data Center per ridurre la latenza.
- Se si sceglie una singola tecnica batch, parametri con valori di tabella offrono prestazioni migliori e flessibilità.
- Per ottenere prestazioni Inserisci più rapida, seguire queste linee guida generali ma test proprio scenario:
    - Per le righe < 100, utilizzare un unico parametri comando Inserisci.
    - Per le righe < 1000, utilizzare i parametri di valori di tabella.
    - Per > = 1000 righe, utilizzare SqlBulkCopy.
- Per aggiornare e le operazioni di eliminazione, utilizzare parametri con valori di tabella con una logica di stored procedure che determina il corretto funzionamento in ogni riga nel parametro table.
- Linee guida per dimensioni batch:
    - Utilizzare le dimensioni del batch più grande che è possibile utilizzare per l'applicazione ed esigenze aziendali.
    - Bilanciare le prestazioni del batch di grandi dimensioni con i rischi di errori temporanei o irreversibili. Qual è la conseguenza di tentativi o perdita dei dati nel batch? 
    - Testare la dimensione massima del batch per verificare che il Database di SQL non rifiuto.
    - Creare le impostazioni di configurazione di tale controllo in batch, ad esempio le dimensioni del batch o l'intervallo di tempo di buffer. Queste impostazioni flessibilità. Senza ridistribuire il servizio cloud, è possibile modificare il comportamento batch di produzione.
- Evitare l'esecuzione in parallelo di batch che funzionano in un'unica tabella in un database. Se si sceglie di suddividere un unico batch tra più thread di lavoro, eseguire test per stabilire il numero di thread ideale. Dopo una soglia sconosciuta più thread influire negativamente sulle prestazioni piuttosto che portarla.
- È consigliabile il buffer su dimensioni e l'ora come un modo di implementare batch per più scenari.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è incentrato sulla modalità progettazione di database e tecniche correlate al batch di codifica può migliorare le prestazioni e scalabilità. Si tratta di un solo fattore di strategia globale. Per altri modi per migliorare le prestazioni e scalabilità, vedere [indicazioni di prestazioni di Database SQL Azure per singoli database](sql-database-performance-guidance.md) e [considerazioni di prezzo e prestazioni per un pool di database flessibile](sql-database-elastic-pool-guidance.md).
