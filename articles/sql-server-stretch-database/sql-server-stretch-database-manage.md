<properties
    pageTitle="Gestire e risolvere i problemi di zoom Database | Microsoft Azure"
    description="Informazioni su come gestire e risolvere i problemi di Database di zoom."
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
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>Gestire e risolvere i problemi di Database di zoom

Per gestire e risolvere i problemi di Database di zoom, utilizzare gli strumenti e i metodi descritti in questo argomento.

## <a name="manage-local-data"></a>Gestire i dati locali

### <a name="LocalInfo"></a>Ottenere informazioni sui database locali e le tabelle è attivate per il Database di zoom
Aprire il catalogo visualizzazioni **Sys. Databases** e **Tables** per visualizzare informazioni sull'estensione\-abilitato database di SQL Server e le tabelle. Per ulteriori informazioni, vedere [Sys. Databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [Tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Per visualizzare la quantità di spazio di un'estensione\-tabella abilitata utilizza in SQL Server, eseguire l'istruzione seguente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Gestire la migrazione dei dati

### <a name="check-the-filter-function-applied-to-a-table"></a>Controllare la funzione di filtro applicata a una tabella
Aprire la visualizzazione di catalogo **sys.remote\_dati\_archivio\_tabelle** e controllare il valore della **filtro\_predicato** colonna per identificare la funzione Zoom Database utilizza per selezionare le righe per eseguire la migrazione. Se il valore null, l'intera tabella sia idoneo per eseguire la migrazione. Per ulteriori informazioni, vedere [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) e [Selezionare le righe per eseguire la migrazione utilizzando una funzione di filtro](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Controllare lo stato della migrazione dei dati
Selezionare attività **| Zoom | Monitor** per un database di SQL Server Management Studio per controllare la migrazione dei dati in Monitoraggio Database zoom. Per ulteriori informazioni, vedere [Monitor e risolvere i problemi di migrazione dei dati (Database di zoom)](sql-server-stretch-database-monitor.md).

In alternativa, aprire la visualizzazione di gestione dinamica **sys.dm\_db\_accesso a dati remoti\_migrazione\_stato** per visualizzare il numero di righe di dati e batch di migrazione.

### <a name="Firewall"></a>Risolvere i problemi di migrazione dei dati
Per la risoluzione dei problemi suggerimenti, vedere [Monitor e risolvere i problemi di migrazione dei dati (Database di zoom)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Gestire i dati remoti

### <a name="RemoteInfo"></a>Informazioni sui database remoti e tabelle utilizzate dal Database di zoom
Aprire le viste del catalogo **sys.remote\_dati\_archivio\_database** e **sys.remote\_dati\_archivio\_tabelle** per vedere le informazioni sui database remoti e le tabelle in cui sono memorizzati dati migrati. Per ulteriori informazioni, vedere [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Per visualizzare la quantità di spazio Usa una tabella abilitato zoom in Azure, eseguire l'istruzione seguente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Eliminare dati migrati  
Se si desidera eliminare i dati che sono già stata eseguita la migrazione di Azure, seguire i passaggi descritti in [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Gestisci schema della tabella

### <a name="dont-change-the-schema-of-the-remote-table"></a>Non modificare lo schema della tabella remota
Non modificare lo schema di una tabella di Azure remota associato a una tabella di SQL Server configurata per il Database di zoom. In particolare, non modificare il nome o il tipo di dati di una colonna. La caratteristica Zoom Database presuppone varie informazioni sullo schema della tabella remota relazione a schema della tabella di SQL Server. Se si cambia lo schema remoto, zoom Database smette di funzionare per la tabella.

### <a name="reconcile-table-columns"></a>Risolvere le colonne della tabella  
Se è stato eliminato accidentalmente colonne della tabella remoto, eseguire **sp_rda_reconcile_columns** per aggiungere colonne alla tabella remota presenti nell'estensione\-abilitato tabella di SQL Server, ma non nella tabella remota. Per ulteriori informazioni, vedere [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

  > [!IMPORTANT] Quando **sp_rda_reconcile_columns** ricrea colonne che è stata eliminata accidentalmente dalla tabella remota, non ripristina i dati in precedenza alle colonne eliminate.

**sp_rda_reconcile_columns** non eliminare colonne dalla tabella remota sono descritti nella tabella remota ma non nell'estensione\-abilitato tabella di SQL Server. Se sono presenti colonne nella tabella Azure remota che non esistono più nell'estensione\-abilitato SQL Server tabella, queste colonne aggiuntive non impedire l'estensione Database funziona normalmente. Se lo si desidera rimuovere le colonne aggiuntive manualmente.  

## <a name="manage-performance-and-costs"></a>Gestire i costi e prestazioni  

### <a name="troubleshoot-query-performance"></a>Risolvere i problemi di prestazioni delle query
Query che includono zoom\-abilitate tabelle devono eseguire più lentamente prima le tabelle sono state abilitate per zoom. Se le prestazioni di query si riducono in modo significativo, esaminare i possibili problemi seguenti.

-   È il server Azure in un'area geografica diversa rispetto a SQL Server? Configurare il server Azure per essere nell'area geografica di appartenenza stesso come SQL Server per ridurre la latenza di rete.

-   Condizioni della rete potrebbero avere danneggiato. Contattare l'amministratore di rete per informazioni sui problemi recenti o interruzioni.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Aumentare il livello di prestazioni Azure per risorsa\-le operazioni più complesse, ad esempio l'indicizzazione
Quando creare, ricostruire o riorganizzare un indice in una tabella di grandi dimensioni è configurato per il Database di zoom e si prevede di eseguire la ricerca piena di dati migrati in Azure durante questo periodo, è consigliabile aumentare le prestazioni del database di Azure remoto corrispondente per la durata dell'operazione. Per ulteriori informazioni sui livelli di prestazioni e prezzi, vedere [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Non è possibile sospendere il servizio di Database di SQL Server zoom in Azure  
 Assicurarsi di selezionare le prestazioni appropriata e prezzi livello. Se si aumenta il livello di prestazioni temporaneamente per una risorsa\-operazione in modo intensivo ripristinarlo al livello precedente al termine dell'operazione. Per ulteriori informazioni sui livelli di prestazioni e prezzi, vedere [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Modificare l'ambito della query  
 Query su Zoom\-abilitate tabelle restituiscono dati locali e remoti per impostazione predefinita. È possibile modificare l'ambito della query per tutte le query da tutti gli utenti o solo per una singola query da un amministratore.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Modificare l'ambito della query per tutte le query da tutti gli utenti  
 Per modificare l'ambito di tutte le query da tutti gli utenti, eseguire la stored procedure **sys.sp_rda_set_query_mode**. È possibile ridurre l'ambito per eseguire una query solo i dati locali, disattivare tutte le query o ripristinare l'impostazione predefinita. Per ulteriori informazioni, vedere [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="queryHints"></a>Modificare l'ambito della query per una singola query da un amministratore  
 Per modificare l'ambito di una singola query da un membro del ruolo db_owner, aggiungere il * *con \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valore* \)* * suggerimento per la query per l'istruzione SELECT. Il suggerimento REMOTE_DATA_ARCHIVE_OVERRIDE può includere i valori seguenti.  
 -   **LOCAL_ONLY**. Solo i dati locali della query.  

 -   **REMOTE_ONLY**. Query solo dati remoti.  

 -   **STAGE_ONLY**. Solo i dati della tabella in cui fasi zoom Database righe idoneo per la migrazione e conserva migrate righe per il periodo specificato dopo la migrazione della query. Questo suggerimento per la query è l'unico modo per la tabella di gestione temporanea della query.  

Ad esempio, la query seguente restituisce solo risultati locali.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Rendere le eliminazioni e aggiornamenti amministrativi  
 Per impostazione predefinita non è possibile aggiornare o eliminare righe che sono idonee per la migrazione o le righe che hanno già stata eseguita la migrazione, in un'estensione\-abilitato tabella. Quando è necessario risolvere un problema, un membro del ruolo db_owner può eseguire un'operazione di aggiornamento o eliminazione aggiungendo il * *con \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valore* \)* * suggerimento per la query per l'istruzione. Il suggerimento REMOTE_DATA_ARCHIVE_OVERRIDE può includere i valori seguenti.  
 -   **LOCAL_ONLY**. Aggiornare o eliminare solo i dati locali.  

 -   **REMOTE_ONLY**. Aggiornare o eliminare solo dati remoti.  

 -   **STAGE_ONLY**. Aggiornare o eliminare solo i dati della tabella in cui fasi zoom Database righe idoneo per la migrazione e conserva migrate righe per il periodo specificato dopo la migrazione.  

## <a name="see-also"></a>Vedere anche

[Database estensione monitor](sql-server-stretch-database-monitor.md)

[Backup database abilitato zoom](sql-server-stretch-database-backup.md)

[Ripristinare i database abilitato zoom](sql-server-stretch-database-restore.md)
