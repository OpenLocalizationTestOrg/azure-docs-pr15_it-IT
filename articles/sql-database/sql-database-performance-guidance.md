<properties
    pageTitle="Azure SQL Database e le prestazioni dei singoli database | Microsoft Azure"
    description="In questo articolo consentono di determinare il livello di servizio da scegliere per l'applicazione. Inoltre, si consiglia modi per ottimizzare l'applicazione per ottenere il massimo da Database SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Azure SQL Database e le prestazioni dei singoli database

Database SQL Azure offre tre [livelli di servizio](sql-database-service-tiers.md): Basic, Standard e Premium. Ogni livello di servizio isola strettamente le risorse che può usare il database SQL e garantisce prestazioni prevedibili per quel livello di servizio. In questo articolo sono disponibili indicazioni utili per scegliere il livello di servizio per l'applicazione. Verranno illustrati anche modi che è possibile ottimizzare l'applicazione per ottenere il massimo da Database SQL Azure.

>[AZURE.NOTE] In questo articolo è incentrato indicazioni delle prestazioni per singoli database nel Database di SQL Azure. Per prestazioni indicazioni relative al pool di database flessibile, vedere [Considerazioni prezzo e prestazioni per i pool di database flessibile](sql-database-elastic-pool-guidance.md). Tenere presente però possibile dei consigli regolazione in questo articolo si applicano ai database in un pool di database flessibile e ottenere prestazioni simili.

Questi sono i tre livelli di servizio di Database SQL Azure che è possibile scegliere tra (unità di velocità di database o [DTUs](sql-database-what-is-a-dtu.md)sono misurate le prestazioni:

- **Base**. Servizio di base livello offre prestazioni ottimali prevedibilità per ogni database, ora complessivamente ora. In un database di base, risorse sufficienti supportano le prestazioni ottimali in un database di piccole dimensioni che non dispone di più richieste simultanee.
- **Standard**. Il livello di servizio Standard offre prestazioni più elevate prevedibilità e viene quindi generato sulla barra di database che dispone di più richieste simultanee, ad esempio applicazioni web e gruppo di lavoro. Quando si sceglie un database di livelli di servizio Standard, è possibile ridimensionare l'applicazione di database in base alle prestazioni prevedibili, minuto più minuto.
- **Premium**. Il livello di servizio Premium fornisce prestazioni ottimali prevedibili, secondo il secondo, per ogni database Premium. Quando si sceglie il livello di servizio Premium, è possibile ridimensionare l'applicazione di database in base al caricamento punta per tale database. Il piano rimuove casi le prestazioni varianza può causare small query per richiedere più tempo del previsto in operazioni latenza dipendenti. Questo modello consente di semplificare notevolmente cicli di convalida di sviluppo e prodotto per le applicazioni che è necessario verificare le istruzioni sicura sulla punta risorse necessarie, la variazione di prestazioni o la latenza delle query.

Ogni livello di servizio, impostare il livello di prestazioni, in modo da avere la possibilità di pagare solo per la capacità che necessarie. È possibile [regolare la capacità](sql-database-scale-up.md), verso l'alto o verso il basso, come le modifiche di carico di lavoro. Se, ad esempio, il carico di lavoro di database sia sufficientemente alto durante il ritorno a scuola degli acquisti natalizi, si potrebbe aumentare il livello di prestazioni per il database per un'ora impostate luglio a settembre. È possibile ridurre il termine il season punta. È possibile ridurre a icona pagare per ottimizzare l'ambiente di cloud per la stagionalità dell'azienda. Questo modello è utile anche per cicli di rilascio prodotto software. Un team test può allocare capacità mentre test verrà eseguito e quindi rilasciare tale capacità dopo aver test. In un modello di richiesta capacità pagando per capacità come necessario ed evitare di perdere sulle risorse dedicate raramente utilizzabili.

## <a name="why-service-tiers"></a>Perché i livelli di servizio?

Anche se ogni carico di lavoro di database può essere diverso, lo scopo dei livelli di servizio è fornire prevedibilità prestazioni vari livelli di prestazioni. I clienti con requisiti relativi alle risorse di grandi dimensioni database possono lavorare in un ambiente informatico più dedicato.

### <a name="common-service-tier-use-cases"></a>Livello di servizio comuni use case

#### <a name="basic"></a>Base

- **Devono ancora con Database di SQL Azure**. Le applicazioni di siano spesso inutili livelli di prestazioni. Database di base sono un ambiente ideale per lo sviluppo di database, in un punto allettante.
- **Si dispone di un database con un singolo utente**. Associare un singolo utente a un database in genere le applicazioni non hanno requisiti concorrenza e prestazioni elevati. Queste applicazioni sono candidati per il livello di base del servizio.

#### <a name="standard"></a>Standard

- **Il database ha più richieste simultanee**. Le applicazioni di servizio più utenti contemporaneamente in genere necessitano livelli superiori di prestazioni. Ad esempio, siti Web che riceve il traffico moderato o reparto applicazioni che richiedono ulteriori risorse sono buoni candidati per il livello di servizio Standard.

#### <a name="premium"></a>Premium

La maggior parte dei casi di utilizzo livelli di servizio Premium intrattenere una o più di queste caratteristiche:

- **Caricare punta elevato**. Un'applicazione che richiede molte CPU, memoria o input/output (i) per completare le operazioni richiede un livello dedicato, prestazioni elevate. Ad esempio un'operazione di database che utilizzano più core CPU per un periodo di tempo prolungato è un candidato per il livello di servizio Premium.
- **Richieste simultanee molti**. Alcune applicazioni di database molte richieste simultanee, dei servizi, ad esempio, se che servono di un sito Web con un volume elevato. Base e Standard livelli di servizio limitare il numero di richieste simultanee per database. Le applicazioni che richiedono più connessioni necessario scegliere una dimensione di prenotazione appropriato per gestire il numero massimo di richieste di base alle esigenze.
- **Bassa latenza**. Alcune applicazioni necessitano garantire una risposta dal database nel tempo minimo. Se una stored procedure specifica è chiamata come parte di un'operazione di assistenza clienti esercitare, potrebbe essere un requisito di restituito da tale chiamata in non più di 20 millisecondi, il 99% del tempo. Questo tipo di applicazione dei vantaggi il livello di servizio Premium, per assicurarsi che la potenza di elaborazione richiesta è disponibile.

Il livello di servizio che è necessario per il database SQL dipende dai requisiti di carico punta per ogni dimensione delle risorse. Alcune applicazioni di usare una quantità di una singola risorsa, ma si verificano significative requisiti per le altre risorse.

## <a name="service-tier-capabilities-and-limits"></a>Limiti e le funzionalità di livello di servizio
Ogni livello di livello e le prestazioni del servizio è associata a diversi limiti e le caratteristiche di prestazioni. Questa tabella sono descritte le caratteristiche per un singolo database.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

Nelle sezioni successive offrono maggiori informazioni su come visualizzare relative a questi limiti.

### <a name="maximum-in-memory-oltp-storage"></a>Spazio di archiviazione massimo OLTP In memoria

È possibile utilizzare la visualizzazione **sys.dm_db_resource_stats** per monitorare l'utilizzo di spazio di archiviazione di Azure In memoria. Per ulteriori informazioni sul monitoraggio, vedere [lo spazio di archiviazione di Monitor In memoria OLTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] Elaborazione preview (OLTP) delle transazioni online Azure In memoria sono attualmente supportata solo per i database singoli. È possibile usarla nei database nel pool di database flessibile.

### <a name="maximum-concurrent-requests"></a>Numero massimo di richieste simultaneo

Per visualizzare il numero di richieste simultanee, eseguire la query Transact-SQL sul database di SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Per analizzare il carico di lavoro di un database di SQL Server locale, modificare la query per filtrare il database che si desidera analizzare. Ad esempio, se si dispone di un database locale denominato MyDatabase, questa query Transact-SQL restituisce il numero di richieste simultanee in tale database:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Questo è solo uno snapshot in un unico punto nel tempo. Per comprendere meglio dei requisiti di richieste simultanee carico di lavoro, è necessario raccogliere molti degli esempi nel tempo.

### <a name="maximum-concurrent-logins"></a>Massimo accessi simultanei

È possibile analizzare i modelli utente e applicazione per avere un'idea della frequenza degli accessi. È anche possibile eseguire carichi reali in un ambiente di testing per assicurarsi che sta non raggiungere questo o altri limiti che verranno illustrate in questo articolo. Non è presente una singola query o visualizzazione di gestione dinamica (DMV) che consente di visualizzare simultanee login conta o cronologia.

Se più client utilizzare la stessa stringa di connessione, il servizio autentica ogni account di accesso. Se 10 utenti contemporaneamente connettono a un database con lo stesso nome utente e password, non vi sarà 10 accessi simultanei. Questo limite valida solo per la durata dell'account di accesso e l'autenticazione. Se stesso 10 utenti connettono al database in sequenza, il numero di accessi simultanei mai è maggiore di 1.

>[AZURE.NOTE] Attualmente, questo limite non applicabile ai database nel pool di database flessibile.

### <a name="maximum-sessions"></a>Numero massimo di sessioni

Per visualizzare il numero di sessioni attive corrente, eseguire la query Transact-SQL sul database di SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se si sta analisi un carico di lavoro di SQL Server locale, modificare la query per concentrarsi su un database specifico. Questa query consente di definire le esigenze di sessione possibili per il database se si prevede di spostamento al Database SQL Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Di nuovo queste query restituiscono il numero in un momento. Se si raccolgono più esempi nel tempo, è necessario la migliore conoscenza della sessione di utilizzare.

Per l'analisi di Database SQL, è possibile ottenere dati statistici sulle sessioni. Utilizzare la colonna **active_session_count** **sys.resource_stats**della query. Vedere la sezione successiva per ulteriori informazioni sull'uso di questa visualizzazione.

## <a name="monitor-resource-use"></a>Monitorare l'utilizzo delle risorse
Due visualizzazioni consentono di monitorare l'utilizzo delle risorse per un database SQL relativamente il livello di servizio:

- [Sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>Sys.dm_db_resource_stats
È possibile utilizzare la visualizzazione [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) in ogni database SQL. La visualizzazione **sys.dm_db_resource_stats** Mostra recenti dati di utilizzo delle risorse rispetto al livello di servizio. Media percentuali di CPU, dati i/o, di scrittura e della memoria sono registrate ogni 15 secondi e mantenute per 1 ora.

Poiché questa visualizzazione fornisce un aspetto più granulare all'utilizzo delle risorse, utilizzare **sys.dm_db_resource_stats** prima per l'analisi di qualsiasi stato corrente o sulla risoluzione dei problemi. Ad esempio, la query viene illustrato come utilizzare risorse medio e massimo per il database corrente più ora passata:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Per altre query, vedere gli esempi di [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>Sys.resource_stats

La visualizzazione di [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database **master** contiene informazioni aggiuntive che consentono di eseguire il monitoraggio delle prestazioni del database SQL al livello di livello e prestazioni servizio specifico. I dati sono raccolti ogni 5 minuti e mantenuti per circa 35 giorni. Questa visualizzazione è utile per un'analisi più lungo termine nella cronologia delle modalità di utilizzo delle risorse del database SQL.

Nel grafico seguente mostra l'utilizzo della CPU delle risorse per un database Premium con il livello di prestazioni P2 per ogni ora in una settimana. Questo grafico inizia di lunedì, viene 5 giorni lavorativi e un fine settimana, in caso di molto meno sulla barra dell'applicazione.

![Utilizzo delle risorse database SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Dai dati di questo database ha un carico CPU punta di oltre il 50% utilizzo della CPU rispetto al livello di prestazioni P2 (mezzogiorno martedì). Se CPU è il fattore fondamentale nel profilo di risorse dell'applicazione, è possibile decidere che P2 è il livello di prestazioni appropriato per garantire che si adatta sempre il carico di lavoro. Se si prevede di un'applicazione aumenti nel tempo, è consigliabile avere un buffer di risorse aggiuntive in modo che l'applicazione non viene mai raggiunto il limite di livello di prestazioni. Se si aumenta il livello di prestazioni, è possibile evitare errori cliente visibile che possono verificarsi quando un database non è sufficiente power per elaborare le richieste in modo efficace, in particolare in ambienti basati sulla latenza. Un esempio è un database che supporta un'applicazione che consente di disegnare pagine Web in base ai risultati delle chiamate di database.

Si noti che altri tipi di applicazioni potrebbero interpretare il grafico stesso in modo leggermente diverso. Ad esempio, se un'applicazione tenta di elaborare dati relativi alle retribuzioni ogni giorno e ha lo stesso piano, questo tipo di modello "processo batch" potrebbe eseguire correttamente un livello di prestazioni P1. Il livello di prestazioni P1 include 100 DTUs confrontate con quelle di 200 DTUs a livello di prestazioni P2. Il livello di prestazioni P1 fornisce metà le prestazioni del livello di rendimento P2. Pertanto, il 50% di utilizzo della CPU in P2 è uguale a utilizzo della CPU al 100% in P1. Se l'applicazione non ha timeout, non è rilevante se un processo richiede 2 ore o 2,5 ore alla fine, se si ottiene eseguito il giorno stesso. Un'applicazione in questa categoria probabile che sia possibile usare un livello di prestazioni P1. È possibile sfruttare il fatto che non esistono periodi di tempo durante la giornata quando l'uso delle risorse è inferiore, in modo che qualsiasi "punta grande" potrebbe si sovrapponga in uno dei gestirle in un punto successivo del giorno. Il livello di prestazioni P1 può essere utile per quel tipo di applicazione e risparmiare, purché i processi possono finire ogni giorno.

Espone Database SQL Azure consumate informazioni sulle risorse per ogni database attivo nella visualizzazione **sys.resource_stats** del database **master** in ogni server. I dati nella tabella raggruppati per intervalli di 5 minuti. Con i livelli di servizio Basic, Standard e Premium, i dati possono richiedere più di 5 minuti all'interno della tabella, in modo che i dati sono più utili per analisi cronologici anziché analisi vicino tempo reale. La visualizzazione di **sys.resource_stats** per visualizzare la cronologia recente di un database e per verificare se la si è scelto di prenotazione recapitato le prestazioni da quando è necessario della query.

>[AZURE.NOTE] È necessario essere connessi al database **master** del server di database SQL logico per eseguire query **sys.resource_stats** negli esempi seguenti.

Questo esempio mostra come i dati in questa visualizzazione sono esposti:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La visualizzazione di sys.resource_stats catalogo](./media/sql-database-performance-guidance/sys_resource_stats.png)

Nell'esempio seguente mostra diversi modi che è possibile utilizzare la visualizzazione di catalogo **sys.resource_stats** per ottenere informazioni sull'utilizzo di risorse del database SQL:

1. Per esaminare risorse della settimana precedente per userdb1 database, è possibile eseguire questa query:

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. Per valutare come anche il carico di lavoro si inserisce il livello di prestazioni, è necessario eseguire il drill-in ogni aspetto metriche delle risorse di: CPU, lettura, scrittura, numero di dipendenti e numero di sessioni. Ecco una query modificata con **sys.resource_stats** per segnalare i valori massimo e Media di queste metriche risorsa:

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Con queste informazioni sui valori medi e massimo di unità di misura metriche ogni risorsa, è possibile valutare come anche il carico di lavoro si integra con il livello di prestazioni che si è scelto. In genere, i valori Media da **sys.resource_stats** per ottenere un buon punto di partenza usare rispetto alla dimensione di destinazione. Dovrebbe essere stick la misura principale. Ad esempio, si potrebbe utilizzare il livello di servizio Standard a livello di prestazioni S2. La media utilizzare le percentuali per CPU e i/o di lettura e scrittura sono sotto il 40%, il numero medio di colleghi è minore di 50 e il numero medio di sessioni è inferiore a 200. Il carico di lavoro potrebbe adatti il livello di prestazioni S1. È facile vedere se il database rientra nei limiti di lavoro e sessione. Per verificare se un database si inserisce un livello inferiore delle prestazioni per quanto riguarda la CPU, legge e scrive, dividere il numero DTU del livello di prestazioni inferiore per il numero DTU del livello corrente di prestazioni e quindi moltiplicare il risultato per 100:

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 * *

    Il risultato è la differenza di prestazioni relative tra le due prestazioni in percentuale. Se l'utilizzo delle risorse non supera questo importo, il carico di lavoro potrebbe adatta a livello di prestazioni inferiore. Tuttavia, è necessario eseguire una ricerca in tutti gli intervalli di valori di utilizzo delle risorse e determinare, percentuale, con quale frequenza il carico di lavoro di database da rientrano in basso livello di prestazioni. La query seguente restituisce la percentuale di adattamento per dimensione delle risorse, in base alla soglia del 40% calcolata in questo esempio:

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    In base all'obiettivo di database del livello (OSU), è possibile decidere se il carico di lavoro si integra con il livello di prestazioni inferiore. Se il carico di lavoro di database OSU è 99,9 e la query precedente restituisce valori maggiori di 99,9 per tutte le dimensioni di tre risorse, il carico di lavoro che possano esserne elencate in basso livello di prestazioni.

    Esamina la percentuale di adattamento anche vengono fornite informazioni sulle se è necessario spostare a livello di prestazioni immediatamente superiore per soddisfare le OSU. Ad esempio userdb1 Mostra l'utilizzo della CPU seguente relative alla settimana precedente:

  	| Percentuale di CPU Media | Percentuale di CPU massimo |
  	|---|---|
  	| 24,5 | 100,00 |

    La media CPU è un quarto del limite del livello di prestazioni, si adattarla alle prestazioni del database. Tuttavia, il valore più grande mostra che il database raggiunge il limite del livello di prestazioni. È necessario spostare il successivo livello superiore di prestazioni? È necessario esaminare come numero di volte raggiunge il carico di lavoro al 100% e quindi controllare il carico di lavoro di database OSU.

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Se la query seguente restituisce un valore minore 99,9 per le dimensioni delle tre risorse provare a uno dei due spostare a livello di prestazioni immediatamente superiore o consente di ridurre il carico sul database SQL di tecniche di ottimizzazione dell'applicazione.

4. Questo esercizio considera inoltre aumenta il carico di lavoro previsto in futuro.

## <a name="tune-your-application"></a>Ottimizzare l'applicazione

In locale tradizionale SQL Server, il processo di pianificazione capacità iniziale spesso separato dal processo di esecuzione di un'applicazione di produzione. Licenze hardware e prodotto vengono acquistate prima di tutto e ottimizzazione delle prestazioni viene eseguita in un secondo momento. Quando si utilizza il Database di SQL Azure, è consigliabile interweave il processo di esecuzione di un'applicazione e basta regolare. Con il modello di pagamento per la capacità su richiesta, è possibile ottimizzare l'applicazione di utilizzare le risorse minime necessarie a questo punto, anziché overprovisioning sull'hardware in base a tentativi dei piani di crescita futura per un'applicazione che spesso non sono corretti. Alcuni utenti potrebbero non si desidera ottimizzare un'applicazione e scegliere di overprovision risorse hardware. Questo approccio potrebbe essere utile anche se non si desidera modificare un'applicazione chiave durante un periodo di disponibilità. Tuttavia, un'applicazione di ottimizzazione può ridurre a icona requisiti relativi alle risorse e ridurre i costi mensili quando si utilizzano i livelli di servizio in Database SQL Azure.

### <a name="application-characteristics"></a>Caratteristiche dell'applicazione

Sebbene livelli del servizio di Database SQL Azure sono progettati per migliorare le prestazioni stabilità e prevedibilità per un'applicazione, alcune procedure consigliate consente di ottimizzare l'applicazione per sfruttare al meglio le risorse a un livello di prestazioni. Anche se molte applicazioni sono notevole miglioramento delle prestazioni semplicemente passando a un livello superiore di prestazioni o livello di servizio, alcune applicazioni richiedono ulteriori operazioni di ottimizzazione per trarre vantaggio da un livello superiore del servizio. Per migliorare le prestazioni, valutare la possibilità di ottimizzazione dell'applicazione aggiuntive per le applicazioni che dispongono di queste caratteristiche:

- **Applicazioni che hanno un rallentamento delle prestazioni a causa di comportamento "semplici"**. Applicazioni semplici eseguire operazioni di accesso quantità eccessiva di dati sensibili alle latenza di rete. Potrebbe essere necessario modificare questi tipi di applicazioni per ridurre il numero di operazioni di accesso ai dati al database SQL. Ad esempio, è possibile migliorare le prestazioni dell'applicazione tramite le tecniche come batch query ad hoc o spostare le query alle stored procedure. Per ulteriori informazioni, vedere [query in Batch](#batch-queries).
- **Database con un carico di lavoro in modo intensivo non supportate da un singolo computer intero**. Database che superano le risorse del livello di prestazioni Premium più alto possono trarre vantaggio da scalabilità il carico di lavoro. Per ulteriori informazioni, vedere [sharding tra database](#cross-database-sharding) e [funzionali partizioni](#functional-partitioning).
- **Applicazioni che dispongono di query non ottimale**. Applicazioni, in particolare quelli disponibili nel livello di accesso ai dati, che sono ottimizzati query potrebbe non trarre vantaggio da un livello superiore di prestazioni. Sono incluse le query che non dispongono di una clausola WHERE, hanno indici mancanti o obsolete statistiche. Queste applicazioni trarre vantaggio da tecniche di ottimizzazione delle prestazioni di query standard. Per ulteriori informazioni, vedere [indici mancanti](#missing-indexes) e [ottimizzazione e suggerimenti di Query](#query-tuning-and-hinting).
- **Le applicazioni che contengono dati accedere struttura**. Applicazioni contenenti dati inerente accesso concorrenza problemi, ad esempio il blocco critico, potrebbero non trarre vantaggio da un livello superiore di prestazioni. Considerare la riduzione di round trip nel database di SQL Azure per la memorizzazione dei dati sul lato client con il servizio di memorizzazione nella cache Azure o un'altra tecnologia di memorizzazione nella cache. Vedere [la memorizzazione nella cache di livello applicazione](#application-tier-caching).

## <a name="tuning-techniques"></a>Tecniche di ottimizzazione
In questa sezione, si osserva alcune tecniche per ottimizzare il Database di SQL Azure per ottenere prestazioni ottimali per l'applicazione ed eseguire più basso livello di prestazioni possibili. Alcune di queste tecniche corrisponderanno ottimizzazione procedure consigliate tradizionale di SQL Server, mentre altre sono specifiche per il Database di SQL Azure. In alcuni casi, è possibile esaminare le risorse utilizzate per un database trovare aree per regolare ulteriormente ed estendere i tradizionali tecniche di SQL Server per l'uso di Database SQL Azure.

### <a name="azure-portal-tools"></a>Strumenti portale Azure
Sono disponibili due strumenti nel portale di Azure che consente di analizzare e risolvere i problemi di prestazioni con il database SQL:

- [Comprendere le prestazioni di query](sql-database-query-performance.md)
- [Preparazione di Database SQL](sql-database-advisor.md)

Portale di Azure per ulteriori informazioni sull'entrambe di questi strumenti e sul loro utilizzo. Per fare in modo efficiente diagnosi e correggere i problemi, è consigliabile provare gli strumenti nel portale di Azure. È consigliabile usare manuale di ottimizzazione approcci verranno trattati gli argomenti successivo, per gli indici e ottimizzazione delle query, in casi particolari mancanti.

### <a name="missing-indexes"></a>Indici mancanti
Un problema comune nelle prestazioni del database OLTP correlato alla struttura del database fisico. Spesso, schemi di database sono progettati e spedizione senza eseguire il test scala (caricamento o il volume di dati). Purtroppo, le prestazioni di un piano di query possono accettabile su piccola scala ma diminuire in modo sostanziale in volumi di dati a livello di produzione. L'origine più comune di questo problema è la mancanza di indici appropriati per soddisfare i filtri o altre restrizioni in una query. Spesso, manifesti indici mancanti come tabella digitalizzare durante una ricerca nell'indice potrebbe essere sufficiente.

In questo esempio, il piano di query selezionata utilizza un'analisi durante una ricerca dovrebbe essere sufficiente:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Un piano di query con indici mancanti](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Database SQL Azure consente di individuare e risolvere mancanti comuni indicizzare condizioni. Viste incorporate nel Database di SQL Azure in dettaglio compilazioni di query in cui un indice in modo significativo da ridurre il costo previsto per eseguire una query. Durante l'esecuzione di query, Database SQL di tiene traccia con quale frequenza viene eseguita ogni piano di query e tiene traccia stimata distanza tra il piano di query in esecuzione e quello fittizio nel punto in cui l'indice esistente. È possibile utilizzare le viste di individuare rapidamente le modifiche alla struttura del database fisico potrebbero migliorare costo totale di carico di lavoro per un database e il carico di lavoro reale.

Per valutare potenziali indici mancanti, è possibile utilizzare questa query:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

In questo esempio, la query ha restituito il suggerimento:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Dopo la creazione, la stessa istruzione SELECT seleziona un piano diverso, che utilizza una ricerca invece di un'analisi e quindi esegue in modo più efficiente il piano:

![Un piano di query con indici corretti](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

L'informazione chiave è che la capacità / o di un sistema di prodotto condivisa, è più limitata rispetto a quelle di un computer server dedicato. Esiste un premio sulla riduzione i/o non necessarie per sfruttare al massimo del sistema di DTU di ogni livello di prestazioni dei livelli di servizio di Database SQL Azure. Struttura del database fisico appropriato scelte consente di migliorare notevolmente la latenza per singole query, migliorare la velocità di richieste simultanee gestite per unità in scala e ridurre i costi necessari per soddisfare la query. Per ulteriori informazioni sull'indice mancante viste, vedere [db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Ottimizzazione delle query e suggerimenti
L'utilità di ottimizzazione di query di Database SQL Azure è simile all'utilità di ottimizzazione di query SQL Server tradizionale. La maggior parte delle procedure consigliate per l'ottimizzazione delle query e i motivi di informazioni sulle limitazioni del modello per l'utilità di ottimizzazione di query applicano anche a Database SQL Azure. Se si ottimizzare le query di Database SQL Azure, potrebbe essere visualizzato il vantaggio di riduzione risorse aggregazione. L'applicazione potrebbe essere possibile eseguire un costo inferiore a un equivalente untuned poiché può essere eseguito in un livello inferiore di prestazioni.

Un esempio comune in SQL Server e che si applicano inoltre a Database SQL Azure è come l'utilità di ottimizzazione di query "rileva" parametri. Durante la compilazione, l'utilità di ottimizzazione di query viene valutato il valore corrente di un parametro per determinare se è possibile generare un piano di query più ottimale. Anche se questo strategia spesso può causare a un piano di query in modo significativo superiore a un piano compilato senza valori di parametro noti, attualmente funziona quando entrambe in SQL Server e database di SQL Azure. In alcuni casi il parametro non è individuare e in alcuni casi è individuare il parametro ma il piano generato non ottimale per l'elenco completo dei valori dei parametri in un carico di lavoro. Microsoft include suggerimenti di query (direttive) che è possibile specificare più deliberatamente intento e ignorare il comportamento predefinito del parametro analisi. Spesso, se si usano suggerimenti, è possibile risolvere casi in cui il comportamento predefinito di SQL Server o Database SQL Azure imperfetta per un carico di lavoro cliente specifico.

L'esempio seguente viene illustrato come processore query può generare un piano sia per le prestazioni e requisiti relativi alle risorse. In questo esempio viene anche se si usa un suggerimento per la query, è possibile ridurre i requisiti di tempo e risorse esecuzione query per il database SQL:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

Il codice di configurazione crea una tabella in cui sono previsti inclinato distribuzione dei dati. Il piano di query ottimali varia in base al quale parametro sia selezionato. Purtroppo, il piano di comportamento della cache non è sempre ricompilare la query in base al valore più comune di parametro. Pertanto, è possibile che un piano memorizzare nella cache e utilizzati per numerosi valori, anche quando un altro piano potrebbe essere preferibile piano in Media. Il piano di query crea quindi due stored procedure che sono identiche, tranne per il fatto che un suggerimento per la query speciali.

**Esempio, parte 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Esempio, parte 2**

(È consigliabile attendere almeno 10 minuti prima di iniziare parte 2 di esempio, in modo che i risultati sono distinti nei dati di telemetria risultante).

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Ogni parte dell'esempio tenta di eseguire un'istruzione insert parametri 1000 volte (per generare un carico sufficiente da utilizzare come un set di dati di test). Durante l'esecuzione di stored procedure, processore query esamina il valore del parametro passato alla routine durante la compilazione prima (parametro "analisi"). Processore memorizza il piano risultante e viene utilizzata per le chiamate successive, anche se il valore del parametro è diverso. Non è possibile utilizzare il piano ottimale in tutti i casi. A volte è necessario Guida l'utilità di ottimizzazione per selezionare il piano a cui è migliore per il caso medio anziché il caso specifico da prima di tutto compilata quando la query. In questo esempio, il piano iniziale genera un piano di "analisi" che legge tutte le righe per trovare ogni valore che corrisponde al parametro:

![Ottimizzazione tramite un piano di analisi della query](./media/sql-database-performance-guidance/query_tuning_1.png)

Poiché viene eseguita la procedura utilizzando il valore 1, piano risultante è stata ottimale per il valore 1, ma non non ottimale per tutti gli altri valori nella tabella. Il risultato probabile che non sia che potrebbe desiderare utilizzato per selezionare ogni piano in modo casuale, poiché il piano più lento e utilizza altre risorse.

Se si esegue il test con `SET STATISTICS IO` impostato su `ON`, il lavoro scansione logica in questo esempio viene eseguito in background. È possibile visualizzare sono presenti 1,148 legge il piano, ovvero inefficace, se il caso medio deve restituire una sola riga, è necessario:

![Ottimizzazione tramite un'analisi logica della query](./media/sql-database-performance-guidance/query_tuning_2.png)

Nella seconda parte dell'esempio utilizza un suggerimento per la query per comunicare l'utilità di ottimizzazione da utilizzare un valore specifico durante il processo di compilazione. In questo caso, impone il processore di query per ignorare il valore passato come il parametro e invece di assumere `UNKNOWN`. Questa opzione fa riferimento a un valore che contiene la frequenza media della tabella (ignorando inclinazione). Il piano risultante è un piano basate su ricerca più veloce e che utilizza meno risorse, in Media, rispetto al piano in questo esempio, parte 1:

![Ottimizzazione delle query usando un suggerimento per la query](./media/sql-database-performance-guidance/query_tuning_3.png)

È possibile visualizzare l'effetto della tabella **sys.resource_stats** (si verifica un ritardo dal momento in cui eseguire il test e quando i dati compilano la tabella). Per questo esempio, parte 1 eseguito durante il periodo di tempo 22:25:00 e parte 2 eseguita 22:35:00. Tenere presente che l'intervallo di tempo precedente altre risorse in tale intervallo di tempo rispetto a quella successiva (a causa di efficienza piano).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Risultati dell'esempio all'ottimizzazione delle query](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Anche se il volume in questo esempio è intenzionalmente ridotta, l'effetto di parametri può essere notevole, soprattutto nel database di grandi dimensioni. La differenza, in casi può essere compreso tra secondi per i casi veloci e le ore per i casi lenti.

È possibile esaminare **sys.resource_stats** per determinare se la risorsa per un test utilizza risorse maggiore o minore rispetto a un altro test. Quando si confrontano dati, separare i tempi di test in modo che non sono nella stessa finestra nella visualizzazione **sys.resource_stats** 5 minuti. L'obiettivo dell'esercizio è per ridurre al minimo la quantità totale di risorse utilizzate e non per ridurre al minimo le risorse di punta. Generalmente, ottimizzare una porzione di codice per la latenza riduce il consumo di risorse. Assicurarsi che le modifiche apportate a un'applicazione sono necessari e che le modifiche non influenzano negativamente l'esperienza dei clienti per una persona che potrebbe essere utilizzato per la query nell'applicazione.

Se un carico di lavoro contiene un insieme di ripetizione di query, spesso è opportuno acquisire e convalidare l'ottimizzazione delle scelte piano, poiché unità l'unità di misura risorsa minima necessaria per ospitare il database. Dopo averla convalidata, in alcuni casi riesaminare i piani che consentono di verificare che hanno non ridotto. Sono disponibili ulteriori sui [suggerimenti di query (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Sharding tra database
Poiché il Database di SQL Azure viene eseguito su hardware prodotto, i limiti di capacità per un singolo database sono inferiori per un'installazione di SQL Server locale tradizionale. Alcuni clienti usare tecniche sharding per allontanare le operazioni di database su più database quando le operazioni non corrispondono all'interno dei limiti di un singolo database nel Database di SQL Azure. La maggior parte dei clienti che utilizzano tecniche sharding nel Database di SQL Azure dividere i dati in una singola dimensione su più database. Per questo approccio, è necessario capire che le applicazioni OLTP spesso eseguano le transazioni relative a solo una riga o a un piccolo gruppo di righe nello schema.

>[AZURE.NOTE] Database SQL di ora fornisce una raccolta che faciliteranno con sharding. Per ulteriori informazioni, vedere [Panoramica di raccolta flessibile Database client](sql-database-elastic-database-client-library.md).

Se, ad esempio un database con nome del cliente, ordine e dettagli ordine (ad esempio il database Northwind di esempio tradizionale fornito con SQL Server), si potrebbero dividere questi dati in più database mediante il raggruppamento di un cliente con l'ordine correlati e informazioni sui dettagli ordine. Si è certi che i dati del cliente rimangono in un solo database. L'applicazione da suddividere clienti diversi in database, in modo efficace diffusione il carico su più database. Con sharding, non solo clienti evitare il limite di dimensioni massime database, ma anche il Database di SQL Azure elaborare carichi di lavoro significativamente maggiore i limiti dei livelli di prestazioni diversi, come singoli database si integra con il relativo DTU.

Sebbene sharding database non ridurre la capacità di aggregazione delle risorse per una soluzione, è altamente efficace al supporto di dimensioni molto grandi soluzioni diffusi su più database. Ogni database è possibile eseguire un livello di prestazioni diverso per il supporto di dimensioni molto grandi, "efficienti" database con requisiti relativi alle risorse elevato.

### <a name="functional-partitioning"></a>Partizione funzionale
Utenti di SQL Server spesso combinano molte funzioni in un unico database. Ad esempio, se un'applicazione ha logica di gestione dell'inventario per un punto vendita, tale database potrebbe essere logica associata all'inventario, tenere traccia degli ordini di acquisto, stored procedure e indicizzate o materializzate visualizzazioni per la gestione dei report di fine del mese. Questa tecnica, è facile amministrare il database per le operazioni come copia di backup, ma richiede anche ridimensionare l'hardware per gestire il carico di picco tra tutte le funzioni di un'applicazione.

Se si usa un'architettura di scalabilità nel Database di SQL Azure, è consigliabile dividere disponibili funzioni differenti di un'applicazione in database diversi. In questo modo, ogni applicazione ridimensiona in modo indipendente. Come un'applicazione diventa aumenta e aumenta il carico del database, l'amministratore può scegliere prestazioni indipendente per ogni funzione nell'applicazione. Raggiunto il limite, con questa architettura, un'applicazione può essere superiore a un computer prodotto unico è possibile gestire perché il carico viene distribuito in più computer.

### <a name="batch-queries"></a>Query in batch
Per le applicazioni di accedere ai dati utilizzando volumi elevati, frequenti hoc eseguendo la query, una notevole quantità di tempo di risposta di spesa su comunicazioni di rete tra il livello di applicazione e il Database di SQL Azure. Anche se l'applicazione e Database SQL Azure sono dello stesso data center, la latenza di rete tra i due potrebbe ingrandita da un numero elevato di dati operazioni di accesso. Per ridurre la rete round viaggi per le operazioni di accesso ai dati, si consiglia di utilizzare l'opzione per uno batch query ad hoc oppure compilare come stored procedure. Se si batch query ad hoc, è possibile inviare più query come un batch di grandi dimensioni in un unico trip al Database SQL Azure. Se si esegue la compilazione di query ad hoc in una stored procedure, è possibile ottenere lo stesso risultato come se si batch. Utilizzando una stored procedure consente inoltre il vantaggio di aumentare le possibilità di memorizzazione nella cache i piani di query di Database SQL Azure in modo che è possibile utilizzare la stored procedure nuovamente.

Alcune applicazioni disponibili che richiede significative attività di scrittura. In alcuni casi è possibile ridurre il carico dei / o totale in un database da valutare come batch scrive insieme. Spesso, questa operazione è semplice come tramite le transazioni esplicite invece che con le transazioni commit automatico in stored procedure e batch ad hoc. Per una valutazione delle diverse tecniche utilizzabili, vedere [batch dei tecniche per le applicazioni di Database SQL Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Provare il proprio carico di lavoro per trovare il modello appropriato per il batch. Assicurarsi di comprendere che un modello potrebbe avere garanzie la coerenza leggermente diverso. Trovare il giusto carico di lavoro che viene ridotto a icona utilizzo delle risorse, è necessario individuare la combinazione della coerenza e prestazioni compromessi.

### <a name="application-tier-caching"></a>Livello di applicazione di memorizzazione nella cache
Alcune applicazioni di database sono carichi di lavoro di lettura Incolla. Memorizzazione nella cache livelli potrebbe ridurre il carico sul database e potenzialmente riduzione del livello di prestazioni necessario per supportare un database utilizzando il Database di SQL Azure. Con [Azure Redis Cache](https://azure.microsoft.com/services/cache/), se si dispone di un carico di lavoro di lettura Incolla, è possibile leggere i dati di una volta (o anche in ogni computer a livello di applicazione, a seconda del tipo di configurazione) e quindi memorizzare i dati all'esterno del database SQL. Questo è un modo per ridurre il caricamento database (CPU e lettura i/o), ma non c'è un effetto sulla coerenza perché i dati da leggere dalla cache potrebbero essere aggiornati con i dati nel database. Sebbene in molte applicazioni una certa incoerenze è accettabile, che non è valido per tutti i carichi di lavoro. Prima di implementare una strategia di memorizzazione nella cache del livello di applicazione, è necessario conoscere i requisiti dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui livelli di servizio, vedere [prestazioni e opzioni di Database SQL](sql-database-service-tiers.md)
- Per ulteriori informazioni sui pool di database flessibile, vedere [che cos'è un pool di database flessibile Azure?](sql-database-elastic-pool.md)
- Per informazioni sulle prestazioni e pool di database flessibile, vedere [quando da prendere in considerazione un pool di database flessibile](sql-database-elastic-pool-guidance.md)
