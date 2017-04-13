<properties
   pageTitle="Transazioni SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per l'implementazione delle transazioni Azure SQL Data warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>Transazioni SQL Data warehouse

Come previsto, SQL Data Warehouse supporta le transazioni come parte del carico di lavoro warehouse dati. Tuttavia, per mantenere le prestazioni di SQL Data Warehouse scala alcune caratteristiche sono limitate rispetto a SQL Server. In questo articolo vengono evidenziate le differenze e gli elenchi di altri utenti. 

## <a name="transaction-isolation-levels"></a>Livelli di isolamento
SQL Data Warehouse implementata transazioni ACID. Tuttavia, l'individuazione del supporto transazione è limitato a `READ UNCOMMITTED` e non può essere modificato. È possibile implementare un numero di codifica metodi per evitare dirty lettura dei dati se si tratta di un problema dell'utente. Metodi più popolari sfruttano CTAS e tabella partizione cambio (spesso noto come modello finestra scorrevole) per impedire agli utenti di query sui dati che sono ancora in corso la preparazione. Visualizzazioni che pre-filtrare i dati è anche un approccio più comune.  

## <a name="transaction-size"></a>Dimensioni delle transazioni
Dimensione è limitata un'operazione di modifica di dati single. Il limite viene applicato oggi "per distribuzione". Di conseguenza, l'allocazione totale può essere calcolato moltiplicando il limite per il conteggio di distribuzione. Per approssimativa il numero massimo di righe nella transazione divide le estremità di distribuzione per le dimensioni totali di ogni riga. Per le colonne di lunghezza variabile è consigliabile prendere una lunghezza media colonna anziché la dimensione massima.

Nella tabella seguente ipotesi seguenti sono stati introdotti:

* Si è verificato una distribuzione uniforme dei dati 
* La lunghezza media riga è 250 byte

| [DWU][]    | Chiudere per distribuzione (GiB) | Numero di distribuzioni | Dimensioni delle transazioni MAX (GiB) | # Righe per distribuzione | Numero massimo di righe per ogni transazione |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  1,5                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2,25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  3,75                      | 60                      |  225                       |  15,000,000             |    900,000,000           |
| DW600  |  4.5                       | 60                      |  270 gradi                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30,000,000             |  1,800,000,000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11,25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60.000.000             |  3,600,000,000           |
| DW3000 | 22.5                       | 60                      |  1,350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2,700                     | 180,000,000             | 10,800,000,000           |

Il limite di dimensioni transazione viene applicato per ogni transazione o operazione. Non ha effetto su tutte le transazioni simultanee. Pertanto ogni transazione è possibile scrivere la quantità di dati nel log. 

Per ottimizzare e ridurre la quantità di dati nel Registro di fare riferimento all'articolo [procedure consigliate per le transazioni][] .

> [AZURE.WARNING] È possibile ottenere le dimensioni massime transazioni solo per HASH o anche tabelle ROUND_ROBIN distribuito dove si trova la diffusione dei dati. Se la transazione è la scrittura dei dati in modo inclinato per le distribuzioni il limite è probabile che deve essere raggiunto prima la dimensione massima transazioni.
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Stato transazione
SQL Data Warehouse viene utilizzata la funzione XACT_STATE() per segnalare una transazione non riuscita utilizzando il valore -2. Questo errore indica che l'operazione ha avuto esito negativo e contrassegnato per il ripristino solo

> [AZURE.NOTE] L'uso di -2 per la funzione XACT_STATE per rappresentare una transazione non riuscita rappresenta un comportamento diverso a SQL Server. SQL Server utilizzerà il valore -1 per rappresentare una transazione non possibile eseguire il commit. SQL Server è in grado di tollerare errori all'interno di una transazione senza dover verrà contrassegnato come non possibile eseguire il commit. Ad esempio `SELECT 1/0` potrebbe causare un errore ma non forzare un'operazione in uno stato non possibile eseguire il commit. SQL Server consente inoltre di legge nella transazione non possibile eseguire il commit. Tuttavia, SQL Data Warehouse non consentono di eseguire questa operazione. Se si verifica un errore all'interno di una transazione SQL Data Warehouse non sarà possibile apportare ulteriori istruzioni select fino a quando non è stata annullata l'istruzione passerà automaticamente allo stato-2. Pertanto è importante verificare che il codice dell'applicazione per vedere se utilizza XACT_STATE() come si potrebbe essere necessario apportare modifiche al codice.

In SQL Server, ad esempio, che appare una transazione è simile alla seguente:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Se si omette il codice viene sopra verrà visualizzato il messaggio di errore seguente:

Msg 111233, livello 16, stato 1, riga 1 111233; Transazione corrente è stata interrotta e le modifiche in sospeso sono state annullate. Causa: Una transazione solo ripristino non dell'in modo esplicito annullamento prima DDL, DML un'istruzione SELECT.

Anche non sarà possibile ottenere l'output delle funzioni ERROR_ *.

In SQL Data Warehouse è necessario essere leggermente modificato:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

A questo punto si osserva il comportamento previsto. L'errore nella transazione viene gestito e le funzioni ERROR_ * forniscono i valori come previsto.

Tutti gli elementi modificati è che il `ROLLBACK` della transazione era decidere prima di leggere le informazioni sugli errori nel `CATCH` blocco.

## <a name="errorline-function"></a>Funzione Error_Line()
È anche importante notare che SQL Data Warehouse non implementare o non supporta la funzione ERROR_LINE(). Se hai nel codice che sarà necessario rimuovere in modo da essere compatibile con SQL Data Warehouse. Utilizzare le etichette di query nel codice per implementare funzionalità equivalenti. Vedere l'articolo [etichetta][] per ulteriori informazioni su questa caratteristica.

## <a name="using-throw-and-raiserror"></a>Utilizzo di generazione e RAISERROR
GETTARE è l'implementazione di una versione più recente per la generazione di eccezioni SQL Data warehouse ma è supportata anche RAISERROR. Esistono alcune differenze che valgono prestando attenzione a tuttavia.

- Messaggi di errore non possono essere numeri nell'intervallo 100.000 150.000 per GETTARE definite dall'utente
- Messaggi di errore RAISERROR rimangono fissi 50.000
- Utilizzo di sys.messages non è supportato

## <a name="limitiations"></a>Limitiations
SQL Data Warehouse presentano alcune altre limitazioni relative alle transazioni.

Le seguenti:

- Nessun transazioni distribuite
- Nessun transazioni nidificate consentite
- N Salva punti consentiti
- Nessun transazioni denominate
- Nessun transazioni contrassegnate
- Nessun supporto per DDL come `CREATE TABLE` all'interno di un utente definito transazione

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su come ottimizzare le transazioni, vedere [procedure consigliate per le transazioni][].  Per informazioni su altre procedure consigliate SQL Data Warehouse, vedere [SQL Data Warehouse procedure consigliate][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[Procedure consigliate transazioni]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Procedure consigliate SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[ETICHETTA]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
