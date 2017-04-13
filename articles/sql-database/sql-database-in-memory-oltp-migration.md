<properties
    pageTitle="OLTP in memoria consente di migliorare le prestazioni di txn SQL | Microsoft Azure"
    description="OLTP In memoria adottino per migliorare le prestazioni transazione in un database SQL esistente."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>Utilizzo di memoria OLTP (preview) per migliorare le prestazioni dell'applicazione di Database SQL

[In memoria OLTP](sql-database-in-memory.md) può essere utilizzato per migliorare le prestazioni di carico di lavoro OLTP nel database di SQL Azure [Premium](sql-database-service-tiers.md) senza aumentare il livello di prestazioni.

Seguire questi passaggi per l'adozione di OLTP In memoria nel database esistente.

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>Passaggio 1: Assicurarsi che il database Premium supporta OLTP In memoria

Database Premium creati in novembre 2015 o versioni successive supporta la funzionalità In memoria. È possibile verificare se il database Premium supporta la funzionalità di memoria In eseguendo l'istruzione Transact-SQL seguente. È supportata in memoria se il risultato restituito è 1 (non 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* l'acronimo di *Elaborazione delle transazioni estreme*

Se il database esistente deve essere spostato in un nuovo database V12 Premium, è possibile utilizzare le tecniche seguenti per esportare e quindi come importare i dati.

#### <a name="export-steps"></a>Procedura di esportazione

Esportare i database di produzione in un bacpac utilizzando:

- La funzionalità di [esportazione](sql-database-export.md) nel [portale](https://portal.azure.com/).

- La funzionalità **dell'applicazione di livello di dati di esportazione** in un [SSMS.exe aggiornata](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. In **Esplora oggetti**, espandere il nodo **database** .
 2. Pulsante destro del mouse il nodo del database.
 3. Fare clic su **attività** > **esportare dati a livello applicazione**.
 4. Utilizzare la finestra della procedura guidata che viene visualizzata.


#### <a name="import-steps"></a>Procedura di importazione

Importare il bacpac in un nuovo database Premium.

1. Nel [portale](https://portal.azure.com/)di Azure,
 - Passare al server.
 - Selezionare l'opzione [Importa Database](sql-database-import.md) .
 - Selezionare un premio prezzi livello.

2. Usare SQL Server Management Studio per importare le bacpac:
 - In **Esplora oggetti**, fare clic il nodo **database** .
 - Fare clic su **Importa dati a livello applicazione**.
 - Utilizzare la finestra della procedura guidata che viene visualizzata.


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Passaggio 2: Identificare gli oggetti per eseguire la migrazione a OLTP In memoria

SQL Server Management Studio include un rapporto di **Transazione prestazioni Analisi panoramica** che è possibile eseguire su un database con un carico di lavoro attivo. Il report identifica le tabelle e stored procedure che sono candidati per la migrazione a OLTP In memoria.

In SQL Server Management Studio, per generare il report:
- In **Esplora oggetti**, fare clic il nodo del database.
- Fare clic su **report** > **report Standard** > **transazione prestazioni Analisi panoramica**.

Per ulteriori informazioni, vedere [determinazione se una tabella o una Stored Procedure deve essere connesso a OLTP In memoria](http://msdn.microsoft.com/library/dn205133.aspx).


## <a name="step-3-create-a-comparable-test-database"></a>Passaggio 3: Creare un database di test comparabile

Si supponga che il report indica che il database è disponibile una tabella in grado di sfruttare la conversione di una tabella ottimizzata memoria. È consigliabile prima di tutto provare per confermare l'indicazione verificando.

È necessaria una copia di test del database di produzione. Il database di test deve essere allo stesso livello servizio livello del database di produzione.

Per semplificare la verifica, modificare il database di prova come indicato di seguito:

1. Connettersi al database di prova tramite SQL Server Management Studio.

2. Per evitare che richiedono l'opzione con (SNAPSHOT) nelle query, impostare l'opzione di database, come illustrato nell'istruzione Transact-SQL seguente:
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>Passaggio 4: Eseguire la migrazione di tabelle

È necessario creare e inserire una copia della memoria ottimizzata della tabella da testare. È possibile creare utilizzando:

- Ottimizzazione della memoria comodo guidata in SQL Server Management Studio.
- Manuale Transact-SQL.


#### <a name="memory-optimization-wizard-in-ssms"></a>Memoria Ottimizzazione guidata in SQL Server Management Studio

Per usare questa opzione di migrazione:

1. Connettersi al database di test con SQL Server Management Studio.

2. In **Esplora oggetti**, pulsante destro del mouse sulla tabella e quindi fare clic su **Advisor ottimizzazione della memoria**.
 - Viene visualizzata la creazione guidata **Tabella memoria utilità di ottimizzazione Advisor** .

3. Nella procedura guidata, fare clic su **Convalida migrazione** (o sul pulsante **Avanti** ) per verificare se la tabella ha le caratteristiche non supportate non sono supportate nelle tabelle di memoria ottimizzato. Per ulteriori informazioni, vedere:
 - *Elenco di controllo per l'ottimizzazione della memoria* in [Memoria ottimizzazione verificato](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Costrutti transact-SQL non è supportato da OLTP In memoria](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Esecuzione della migrazione a OLTP In memoria](http://msdn.microsoft.com/library/dn247639.aspx).

4. Se la tabella non dispone di alcun caratteristiche non supportate, verificato di eseguire lo schema effettivo e la migrazione dei dati dell'utente.


#### <a name="manual-t-sql"></a>T-SQL manuale

Per usare questa opzione di migrazione:

1. Connettersi al database di test con SQL Server Management Studio (o un'utilità analoga).

2. Ottenere lo script T-SQL completato per la tabella con i relativi indici.
 - In SQL Server Management Studio, fare clic il nodo della tabella.
 - Fare clic su **tabella Script come** > **Crea per** > **nuova finestra Query**.

3. Nella finestra script, aggiungere con (MEMORY_OPTIMIZED = ON) per l'istruzione CREATE TABLE.

4. Se esiste un indice raggruppate, modificarlo in NONCLUSTERED.

5. Rinominare la tabella esistente utilizzando SP_RENAME.

6. Creare la nuova copia ottimizzata memoria della tabella eseguendo lo script CREATE TABLE modificato.

7. Copiare i dati utilizzando Inserisci tabella ottimizzata memoria... SELEZIONARE * IN:
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Passaggio 5 (facoltativo): eseguire la migrazione di stored procedure

La caratteristica In memoria è inoltre possibile modificare una stored procedure per migliorare le prestazioni.


### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerazioni con stored procedure compilate a livello nativo

Una stored procedure a livello nativo compilata deve avere le opzioni seguenti nella relativa clausola Transact-SQL con:

- NATIVE_COMPILATION

- Associazione a schema: indicare le tabelle che stored procedure non può essere modificate in modo tale da comprometterne stored procedure, a meno che non si rilascia stored procedure relative definizioni di colonna.


Un modulo nativo è necessario utilizzare una grande [blocchi ATOMICI](http://msdn.microsoft.com/library/dn452281.aspx) per la gestione delle transazioni. Non esiste alcun ruolo per una transazione esplicita iniziare o per il ripristino transazione. Se viene rilevata una violazione di una regola di business, è possibile terminare il blocco atomico con un'istruzione [che venga GENERATA](http://msdn.microsoft.com/library/ee677615.aspx) .


### <a name="typical-create-procedure-for-natively-compiled"></a>Tipico CREATE PROCEDURE per compilato a livello nativo

In genere T-SQL per creare una stored procedure a livello nativo compilata è simile alla seguente modello:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- Per TRANSACTION_ISOLATION_LEVEL, SNAPSHOT è il valore più comune per stored procedure a livello nativo compilato. È supportata anche un sottoinsieme degli altri valori:
 - LETTURA RIPETIBILE
 - SERIALIZZABILE


- Il valore di lingua deve essere presenta nella visualizzazione sys.languages.


### <a name="how-to-migrate-a-stored-procedure"></a>Come eseguire la migrazione di una stored procedure

La procedura di migrazione è:


1. Ottenere lo script CREATE PROCEDURE per regolare interpretato stored procedure.

2. Riscrittura della propria intestazione in modo che corrisponda il modello precedente.

3. Verificare se la stored procedure codice T-SQL utilizza le caratteristiche non supportate per stored procedure compilate a livello nativo. Implementare soluzioni alternative, se necessario.
 - Per informazioni dettagliate, vedere [Problemi di migrazione per a livello nativo compilato Stored procedure](http://msdn.microsoft.com/library/dn296678.aspx).

4. Rinominare la stored procedure precedente utilizzando SP_RENAME. O semplicemente RILASCIARLO.

5. Eseguire lo script T-SQL CREATE PROCEDURE modificata.


## <a name="step-6-run-your-workload-in-test"></a>Passaggio 6: Eseguire il carico di lavoro test

Eseguire un carico di lavoro nel database di test è simile al carico di lavoro viene eseguito nel database di produzione. Questa operazione deve rivelare il miglioramento delle prestazioni ottenuto dall'utilizzo della caratteristica In memoria per tabelle e stored procedure.

Attributi principali del carico di lavoro sono:

- Numero di connessioni simultanee.

- Rapporto di lettura/scrittura.


Per personalizzare ed eseguire il carico di lavoro test, è possibile utilizzare lo strumento ostress.exe comodo, illustrata di [seguito](sql-database-in-memory.md).


Per ridurre al minimo la latenza di rete, eseguire il test della stessa Azure area geografica in cui è presente il database.


## <a name="step-7-post-implementation-monitoring"></a>Passaggio 7: Monitoraggio post-implementazione

Tenere sotto controllo gli effetti delle prestazioni di implementazioni In memoria in produzione:

- [Lo spazio di archiviazione di monitor In memoria](sql-database-in-memory-oltp-monitoring.md).

- [Database di SQL Azure monitoraggio usando le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>Collegamenti correlati

- [OLTP (In memoria ottimizzazione) in memoria](http://msdn.microsoft.com/library/dn133186.aspx)

- [Introduzione alle Stored procedure compilate a livello nativo](http://msdn.microsoft.com/library/dn133184.aspx)

- [Preparazione di ottimizzazione della memoria](http://msdn.microsoft.com/library/dn284308.aspx)

