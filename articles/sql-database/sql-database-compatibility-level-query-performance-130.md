<properties
    pageTitle="Livello di compatibilità, come la valutazione | Microsoft Azure"
    description="Passaggi e strumenti per determinare il livello di compatibilità più adatto per il database nel Database di SQL Azure o Microsoft SQL Server"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Miglioramento delle prestazioni di query con compatibilità 130 livello nel Database di SQL Azure


Database SQL Azure è in esecuzione in modo trasparente centinaia di migliaia di database al numero di livelli diversi compatibilità, conservando e garantire la compatibilità con le versioni precedenti alla versione corrispondente di Microsoft SQL Server per tutti i clienti!

Di conseguenza, non impedisce i clienti che hanno modificano tutti i database esistenti a livello di compatibilità più recente da vantaggi dalla nuova utilità di ottimizzazione di query e le funzionalità del processore query. Come promemoria della cronologia, l'allineamento delle versioni di SQL per i livelli di compatibilità predefiniti sono i seguenti:

- 100: in SQL Server 2008 e SQL Azure V11 del Database.
- 110: in SQL Server 2012 e SQL Azure V11 del Database.
- 120: in SQL Server 2014 e SQL Azure Database V12.
- 130: in SQL Server 2016 e SQL Azure Database V12.


> [AZURE.IMPORTANT] A partire da **2016 mid giugno**nel Database di SQL Azure, il livello di compatibilità predefinito sarà 130 anziché 120 per i database **appena creato** .
> 
> Database creati prima stringa. Estrai giugno 2016 verranno *non* valida e manterrà il livello di compatibilità corrente (100, 110 o 120). Database di cui eseguire la migrazione dalla versione di Database SQL Azure che V11 a V12 non avrà il livello di compatibilità modificato.


In questo articolo vengono illustrati i vantaggi del livello di compatibilità 130 e come sfruttare i vantaggi. Abbiamo indirizzo possibili ripercussioni sulle prestazioni di query per le applicazioni SQL esistente.


## <a name="about-compatibility-level-130"></a>Sul livello di compatibilità 130


Prima di tutto, se si vuole conoscere il livello di compatibilità corrente del database, eseguire l'istruzione Transact-SQL seguente.


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Prima che la modifica a livello di 130 eseguito per i database **appena** creato, verrà verificare quali questa modifica è costituita da tutti sulle tramite alcuni esempi di query molto semplice e vedere come tutti gli utenti possono trarre vantaggio da essa.

Elaborazione delle query di database relazionali può essere molto complessa e può creare numerosi passaggi di informatica e matematica per comprendere le opzioni di progettazione inerenti e i comportamenti. In questo documento, il contenuto è stato semplificato intenzionalmente per garantire che tutti gli utenti con alcuni concetti di base tecnico minimo possibile valutare l'impatto della modifica del livello compatibilità e determinare sui vantaggi offerti applicazioni.

Ecco le opzioni disponibili panoramica sui quali il livello di compatibilità 130 porta la tabella.  È possibile trovare ulteriori dettagli [Modifica](https://msdn.microsoft.com/library/bb510680.aspx)livello di compatibilità DATABASE (Transact-SQL), ma Ecco un breve riepilogo:

- L'operazione di inserimento di un'istruzione Insert selezionare possono essere multithread o può avere un piano in parallelo, mentre prima dell'operazione thread singolo.
- Memoria ottimizzata tabelle e query di variabili di tabella può presentare i piani in parallelo, mentre prima questa operazione è stata inoltre multithread.
- Statistiche di memoria ottimizzato tabella ora possono essere ridotte e vengono aggiornate automaticamente. Vedere [Novità di motore di Database: OLTP In memoria](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) per altri dettagli.
- Batch modalità v/s cambia modalità di riga con gli indici dell'archivio di colonna
  - Ordinamento in una tabella con un indice di colonna archivio è ora in modalità batch.
  - Aggregazioni finestre funzionano ora in modalità batch, ad esempio istruzioni TSQL ritardo/cliente potenziale.
  - Query su tabelle di archivio di colonna con più clausole distinte funzionano in modalità Batch.
  - Query che esegue in Direttore = 1 o con un piano seriale eseguire anche in modalità Batch.
- Ultimo, miglioramenti stima cardinalità sono effettivamente in arrivo con il livello di compatibilità 120, ma se si esegue un livello di compatibilità inferiore (ad esempio 100 o 110), lo spostamento alla compatibilità livello 130 porterà anche questi miglioramenti e anche questi possono trarre vantaggio le prestazioni di query delle applicazioni.


## <a name="practicing-compatibility-level-130"></a>Livello di compatibilità dell'esercitazione 130


Primo Iniziamo alcune tabelle, indici e dati casuali creati per esercitarsi con alcune delle nuove funzionalità. Script di esempio TSQL può essere eseguito in SQL Server 2016 o Database SQL Azure. Tuttavia, quando si crea un database SQL Azure, verificare che si sceglie di almeno un database P2 perché è necessario disporre almeno un paio di core per consentire il multithreading e pertanto trarre vantaggio da queste caratteristiche.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


A questo punto, si ha un aspetto ad alcune delle caratteristiche l'elaborazione di Query con il livello di compatibilità 130.


## <a name="parallel-insert"></a>Inserisci in parallelo


Esecuzione di istruzioni SQL seguente esegue l'operazione di inserimento in livello di compatibilità 120 e 130, che consente di eseguire rispettivamente l'operazione di inserimento in un unico modello thread (120) e un modello multithread (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Richiedendo l'effettivo del piano di query, visualizzazione della propria rappresentazione grafica o il relativo contenuto XML, è possibile determinare quali stima cardinalità funzione è in riproduzione. Esaminando il piano side-by-side nella figura 1, si vede chiaramente che l'esecuzione di colonna archivio inserire passa dalla sequenza 120 in parallelo 130. Si noti inoltre che la modifica dell'icona iteratore nel piano del 130 con due frecce in parallelo, che illustrano il fatto che ora l'esecuzione di iteratore sia effettivamente in parallelo. Se si dispone di grandi dimensioni operazioni di inserimento per completare, l'esecuzione in parallelo, collegato al numero di core che avere a disposizione per il database, funzionano in modo ottimale; tutte le volte 100 superiori a seconda del tipo la propria situazione.


*Figura 1: Operazione di inserimento Cambia sequenza in parallelo con compatibilità livellare 130.*


![Figura 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>Modalità seriale Batch


Analogamente, spostare a livello di compatibilità 130 durante l'elaborazione di righe di dati consente l'elaborazione di modalità batch. Prima di tutto, operazioni in modalità batch sono disponibili solo quando si dispone di un indice di colonna archivio sul posto. In secondo luogo, un batch in genere rappresenta ~ 900 righe e viene utilizzata una logica di codice ottimizzata per CPU multi-core, le prestazioni della memoria superiore e direttamente consente di sfruttare i dati compressi dell'archivio di colonna, ogni volta che è possibile. In queste condizioni, SQL Server 2016 ~ 900 righe elaborare contemporaneamente, anziché 1 riga al momento e di conseguenza, il costo complessivo generale dell'operazione di ora è condivisa tramite l'intero lotto, riducendo il costo per riga globale. Questo importo condiviso di operazioni combinate con la compressione di archivio di colonna in cui sono riduce la latenza in un'operazione di modalità batch selezionare. È possibile trovare altre informazioni relative all'archivio di colonna e modalità nella [Guida di indici Columnstore](https://msdn.microsoft.com/library/gg492088.aspx)batch.


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Come viene visualizzata sotto, osservando la query plan di messaggistica unificata side-by-side nella figura 2, è possibile vedere che con il livello di compatibilità è cambiata la modalità di elaborazione e di conseguenza, quando si esegue la query in sia a livello di compatibilità completamente, è possibile vedere che la maggior parte del tempo di elaborazione di spesa in modalità di riga (86%) rispetto alla modalità batch (14%), in cui sono state elaborate 2 batch. Aumentare il set di dati, il vantaggio aumenterà.


*Figura 2: Selezionare operazione modifiche da seriale alla modalità batch con il livello di compatibilità 130.*


![Figura 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>Modalità batch sull'esecuzione di ordinamento


Simile a quanto sopra menzionato, ma applicato a un'operazione di ordinamento, la transizione dalla modalità di riga (livello di compatibilità 120) alla modalità batch (livello di compatibilità 130) migliora le prestazioni dell'operazione di ordinamento per i motivi stesso.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Visibile side-by-side nella figura 3, è possibile vedere che l'operazione di ordinamento in modalità di riga rappresenta 81% dei costi, mentre la modalità batch rappresenta solo 19% tra il costo (rispettivamente 81% e 56% Ordina stesso).


*Figura 3: Operazione di ordinamento viene modificato riga in modalità batch con il livello di compatibilità 130.*


![Figura 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Naturalmente, questi esempi solo contengono decine di migliaia di righe, che risulta nulla quando si visualizza i dati disponibili nella maggior parte dei server SQL i giorni selezionati. Solo invece del progetto questi contro milioni di righe e questo può essere convertita in alcuni minuti di esecuzione per altri quotidianamente in sospeso natura del carico di lavoro.


## <a name="cardinality-estimation-ce-improvements"></a>Miglioramenti di cardinalità stima (CE)


Introdotta con SQL Server 2014, qualsiasi database che esegue un livello di compatibilità 120 o sopra renderà usare di nuovo stima di cardinalità funzionalità. In pratica, stima cardinalità, ovvero è la logica utilizzata per determinare come SQL server necessario eseguire una query in base al suo costo previsto. La stima viene calcolata utilizzando input dalle statistiche associate agli oggetti coinvolti nella query. In pratica, a livello generale, funzioni di cardinalità stima riga conteggio stima insieme alle informazioni sulla distribuzione dei valori, conteggi dei valori distinti, e conteggi duplicati contenuti in tabelle e oggetti a cui fa riferimento la query. Guida errato tali stime, può condurre a trasmissioni disco a causa di concessioni di memoria insufficiente (ovvero TempDB liquidi), o a una selezione di esecuzione di un piano seriale sull'esecuzione di piano parallelo, per citarne alcune. Conclusione, le stime non corrette possono causare un riduzione delle prestazioni di esecuzione della query. Su altro lato stime migliori, le stime più accurate, dei clienti potenziali per le esecuzioni query meglio!

Come detto in precedenza, le stime e ottimizzazione delle query sono una questione complessa, ma se si desidera ulteriori informazioni sui piani di query e stima cardinalità, è possibile fare riferimento al documento in [Ottimizzazione di Your piani di Query con la stima cardinalità 2014 di SQL Server](https://msdn.microsoft.com/library/dn673537.aspx) per ulteriori approfondimenti sugli.


## <a name="which-cardinality-estimation-do-you-currently-use"></a>La stima di cardinalità si utilizzano attualmente?


Per determinare in quale stima di cardinalità, ovvero le query sono in esecuzione, semplicemente utilizzare gli esempi di query riportata di seguito. Si noti che in questo esempio viene prima verrà eseguiti nel livello di compatibilità 110, per indicare l'utilizzo delle funzioni di cardinalità stima precedente.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Una volta completata l'esecuzione, fare clic sul collegamento XML e osservare le proprietà dell'iteratore prima, come illustrato di seguito. Nota il nome della proprietà denominato CardinalityEstimationModelVersion attualmente impostate su 70. Ciò significa che il livello di compatibilità è impostato per la versione di SQL Server 7.0 (è impostata su 110 come visibile in istruzioni SQL sopra), ma il valore 70 rappresenta semplicemente la funzionalità di cardinalità stima legacy disponibile dal SQL Server 7.0 che non aveva revisioni principali fino a SQL Server 2014 (che include un livello di compatibilità di 120).


*Figura 4: Il CardinalityEstimationModelVersion è impostato su 70 quando si utilizza un livello di compatibilità di 110 o di sotto.*


![Figura 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


In alternativa, è possibile modificare il livello di compatibilità a 130 e disattivare l'utilizzo della nuova funzione di cardinalità stima utilizzando LEGACY_CARDINALITY_ESTIMATION impostata su ON con [ALTER configurazione nell'ambito del DATABASE](https://msdn.microsoft.com/library/mt629158.aspx). Questo sarà esattamente uguale utilizzando 110 da un punto di vista della funzione di cardinalità stima, utilizzando la query più recente di elaborazione livello di compatibilità. In questo modo, è possibile trarre vantaggio da nuova query elaborazione delle funzionalità disponibile a breve con il livello di compatibilità più recente (ad esempio la modalità batch), ma comunque basarsi sulla funzionalità di cardinalità stima precedente se necessario.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Semplicemente passare a livello di compatibilità 120 o 130 consente la nuova funzionalità di cardinalità stima. In questo caso, il valore predefinito CardinalityEstimationModelVersion verrà impostata conseguenza 120 o 130 come viene visualizzata sotto.


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figura 5: Il CardinalityEstimationModelVersion è impostato su 130 quando si utilizza un livello di compatibilità di 130.*


![Figura 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Che le differenze di cardinalità stima


A questo punto, eseguiamo una leggermente più complessa di query che comportano un INNER JOIN con una clausola WHERE con alcuni predicati e osservare il valore stimato conta. numeri di riga dalla funzione cardinalità stima precedente prima di tutto.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Esecuzione della query in modo efficace restituisce 200,704 righe, mentre la stima di riga con la funzionalità di cardinalità stima precedente dichiara 194,284 righe. Naturalmente, come detto prima, questi risultati conteggio righe variano con quale frequenza è stato eseguito degli esempi precedenti, in cui inserisce le tabelle di esempio in ogni esecuzione. Naturalmente, predicati nella query sono disponibili anche influire sulla stima effettiva oltre la forma di tabella, dati, e come tali dati effettivamente correlare tra loro.


*Figura 6: Il valore stimato conta. numeri di riga è 194,284 o 6.000 righe disattivata da 200,704 righe prevede.*


![Figura 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


Nello stesso modo possibile eseguire la stessa query con la nuova funzionalità di cardinalità stima.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Esamina il sotto, a questo punto vediamo la riga è 202,877, o più vicina e superiori alla stima di cardinalità precedente.

*Figura 7: Il valore stimato conta. numeri di riga diventa 202,877 anziché 194,284.*


![Figura 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


In realtà, il risultato è 200,704 righe (ma tutto varia con quale frequenza è stato eseguito le query degli esempi precedenti, ma più importante, poiché TSQL utilizza l'istruzione casuale (), i valori effettivi restituiti possono variare da una sequenza alla successiva). Di conseguenza, in questo esempio, la nuova stima cardinalità consente valutare il numero di righe perché 202,877 è molto più vicino 200,704, rispetto 194,284! Infine, se si cambia la clausola WHERE ai predicati di uguaglianza (anziché ">", ad esempio), questo può effettuare le stime tra i vecchi e nuova funzione di cardinalità ancora più diversi, a seconda di quante corrisponde è accessibile.

Naturalmente, in questo caso, viene ~ 6000 righe disattivato da conteggio effettivo non rappresenta una grande quantità di dati in alcuni casi. A questo punto, Trasponi questa opzione per milioni di righe in più tabelle e query più complesse e in alcuni casi il valore stimato può essere disattivata, milioni di righe e di conseguenza, il rischio della selezione per il piano di esecuzione errato o la richiesta di concessioni di memoria insufficiente zeri TempDB liquidi e i/o maggiore, sono molto superiori.

Se si dispone dell'opportunità, esercitazione il confronto con il set di dati e la query più comune e vedere per se stessi da quanto alcune delle stime sul vecchi e nuovi vengono modificate, mentre solo alcuni può diventare più disattivare da in realtà o alcuni altri appena semplicemente più vicino alla riga effettiva conta effettivamente restituito nei set di risultati. Tutto dipenderà della forma della query, le caratteristiche di database SQL Azure, natura e le dimensioni del set di dati e le statistiche disponibili su di essi. Se l'istanza di Database SQL Azure appena creato, sarà necessario compilare la conoscenza da zero anziché riutilizzo statistiche costituite esecuzioni query precedenti l'utilità di ottimizzazione di query. Pertanto, le stime sono molto contestuali e quasi specifiche per ogni situazione server e applicazione. Strumento prezioso aspetti importanti da tenere in considerazione


## <a name="some-considerations-to-take-into-account"></a>Alcune considerazioni da tenere in considerazione


Anche se la maggior parte dei carichi di lavoro da trarre vantaggio dal livello di compatibilità 130, prima di adottare il livello di compatibilità per l'ambiente di produzione, sono disponibili 3 opzioni:

1. Si passa a livello di compatibilità 130 e vedere come eseguono operazioni. Nel caso in cui si verificano alcune regressione appena è sufficiente impostare la compatibilità livello al livello originale oppure mantenere 130 e di nuovo la stima di cardinalità solo inverso in modalità legacy (come illustrato sopra, questo da soli potrebbe risolvere il problema).
2. Testare le applicazioni esistenti in condizioni di carico produzione simile, regolare con maggiore precisione e convalida delle prestazioni prima del passaggio alla produzione. In caso di problemi, come riportati sopra, è possibile sempre tornare a livello di compatibilità originale o semplicemente inverte la stima di cardinalità, ovvero a modalità precedente.
3. Come un'opzione finale e il modo più recente per rispondere a queste domande, consiste nell'utilizzare l'archivio di Query. È sconsigliata odierna! Per facilitare l'analisi delle query in compatibilità livellare 120 o di sotto e 130, Impossibile consigliamo sufficientemente l'utilizzo di archivio di Query. Archivio di query è disponibile con l'ultima versione di V12 di Database SQL Azure ed è progettato per facilitare la risoluzione dei problemi di prestazioni query. Considerare l'archivio di Query come un registratore di dati di volo per il database raccogliere e presentare informazioni dettagliate a cronologiche tutte le query. Questo semplifica notevolmente legali prestazioni riducendo il tempo necessario per la risoluzione dei problemi. È possibile trovare ulteriori informazioni su [Query Store: un registratore di dati di volo per il database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


AT di alto livello, se già impostato un database che esegue il livello di compatibilità 120 o di sotto e si prevede di spostare alcuni di essi a 130 o perché il carico di lavoro automaticamente effettuato il provisioning nuovi database che saranno presto possibile impostare per impostazione predefinita a 130, è consigliabile seguenti:

- Prima di modificare per il nuovo livello di compatibilità di produzione, abilitare l'archivio di Query. È possibile fare riferimento a [modificare la modalità di compatibilità Database e utilizzare l'archivio di Query](https://msdn.microsoft.com/library/bb895281.aspx) per ulteriori informazioni.
- Testare tutti critiche carichi di lavoro utilizzando representative dati e query di un ambiente di produzione e confrontare le prestazioni con esperienza con e come indicato dall'archivio di Query. Se si verificano alcune regressione, è possibile identificare le query regressioni degli con l'archivio di Query e utilizzare il piano di forzare l'opzione di archivio di Query (piano o il blocco). In questo caso, si definitivamente rimanere con il livello di compatibilità 130 e il piano di query ex suggerita dall'archivio di Query.
- Se si desidera sfruttare le nuove caratteristiche e funzionalità del Database di SQL Azure (che esegue SQL Server 2016), ma sensibili alle modifiche apportate dal livello di compatibilità 130, come ultima risorsa, è possibile forzare il livello di compatibilità indietro al livello che si adatta il carico di lavoro con un'istruzione ALTER DATABASE. Ma prima di tutto, tenere presente che il piano di archiviazione Query bloccare l'opzione è la soluzione migliore perché non si usa 130 sostanzialmente è rimanere al livello di funzionalità di una versione precedente di SQL Server.
- Se si dispone di applicazioni multi-tenant che si estende su più database, potrebbe essere necessario aggiornare la logica di provisioning dei database per garantire un livello di compatibilità coerente in tutti i database; quelli vecchi che nuovi provisioning. Le prestazioni di carico di lavoro dell'applicazione potrebbero essere sensibili al fatto che alcuni database sono in esecuzione livelli di compatibilità diversi e pertanto la coerenza livello di compatibilità tra qualsiasi database potrebbe essere necessaria per fornire la stessa esperienza ai clienti tutti tutti i livelli. Si noti che non è un mandato, tutto dipende come l'applicazione è interessata dal livello di compatibilità.
- Infine, la stima di cardinalità, nonché come modificare il livello di compatibilità, prima di procedere in produzione, è consigliabile per testare il carico di lavoro di produzione condizioni nuovo per determinare se l'applicazione dei vantaggi i miglioramenti di cardinalità stima.


## <a name="conclusion"></a>Conclusioni


Utilizzo di Database SQL Azure per trarre vantaggio da tutti i miglioramenti di SQL Server 2016 chiaramente migliorare le esecuzioni di query. Come-è! Naturalmente, ad esempio qualsiasi nuova caratteristica una corretta valutazione deve essere eseguita per determinare le condizioni esatte in cui il carico di lavoro di database opera appropriato. L'esperienza dimostra che la maggior parte dei carico di lavoro previsto eseguire almeno trasparente sotto il livello di compatibilità 130, sfruttando l'elaborazione di funzioni e nuova stima cardinalità nuova query. Che detto realisticamente, esistono sempre alcune eccezioni eseguire scadenza iniz diligenza è una valutazione importante stabilire quanto possono trarre vantaggio da questi miglioramenti. E di nuovo l'archivio di Query possono essere di grande aiuto in questo modo!

Come si sviluppa SQL Azure, è probabile che un livello di compatibilità 140 in futuro. Quando è appropriato, si inizierà a parlano cosa al livello di compatibilità futuri 140 porterà, così come illustrata brevemente qui il livello di compatibilità 130 è caricarli oggi.

Per il momento di seguito non dimenticare, a partire da giugno 2016, Database SQL Azure cambierà il livello di compatibilità predefinito da 120 a 130 per i database appena creati. Prestare particolare attenzione!


## <a name="references"></a>Riferimenti


- [Novità di motore di Database](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog: Query Store: un registratore di dati di volo per il database da Borko Novakovic, giugno 8 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [Livello di compatibilità ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [MODIFICARE LA CONFIGURAZIONE DELL'AMBITO DI DATABASE](https://msdn.microsoft.com/library/mt629158.aspx)

- [Livello di compatibilità 130 V12 di Database SQL Azure](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Ottimizzare la Query plan di messaggistica unificata con SQL Server stima cardinalità 2014](https://msdn.microsoft.com/library/dn673537.aspx)

- [Guida per gli indici ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog: Migliorate le prestazioni delle Query con livello di compatibilità 130 nel Database SQL Azure, da Alain Lissoir 2016 6 maggio](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
