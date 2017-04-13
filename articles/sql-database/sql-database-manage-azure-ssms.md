<properties 
    pageTitle="Gestire un Database SQL con SQL Server Management Studio | Microsoft Azure" 
    description="Informazioni su come usare SQL Server Management Studio per gestire i Database di SQL Server e database." 
    services="sql-database" 
    documentationCenter=".net" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="tysonn"/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sstein"/>


# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Gestione di Database di SQL Azure con SQL Server Management Studio 


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-manage-portal.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

È possibile usare SQL Server Management Studio (Express) per amministrare database e i server di Database SQL Azure. In questo argomento illustra le attività comuni con SQL Server Management Studio. È già un server e un database creato nel Database di SQL Azure prima di iniziare. Per ulteriori informazioni, vedere [creare il primo Database di SQL Azure](sql-database-get-started.md) e [connettersi e Query con SQL Server Management Studio](sql-database-connect-query-ssms.md) .

Si consiglia di utilizzare l'ultima versione di SQL Server Management Studio quando si lavora con Database di SQL Azure. 

> [AZURE.IMPORTANT] Usa sempre l'ultima versione di SQL Server Management Studio poiché viene costantemente migliorato per lavorare con gli aggiornamenti più recenti e Database SQL Azure. Per ottenere la versione più recente, vedere [Scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).



## <a name="create-and-manage-azure-sql-databases"></a>Creare e gestire i database di SQL Azure

Durante la connessione al database **master** , è possibile creare database sul server e modificare o eliminare un database esistente. La procedura seguente viene descritto come eseguire più comuni operazioni di gestione di database tramite Management Studio. Per eseguire queste operazioni, verificare che si è connessi al database **master** con l'account di accesso principale a livello di server creato quando si configura il server.

Per aprire una finestra di query in Management Studio, aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

-   Utilizzare l'istruzione **CREATE DATABASE** per creare un database. Per ulteriori informazioni, vedere [Creazione di DATABASE (Database di SQL)](https://msdn.microsoft.com/library/dn268335.aspx). L'istruzione seguente crea un database denominato **myTestDB** e specifica che si tratti di un database Standard Edition S0 con dimensioni massime predefinite di 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Fare clic su **Esegui** per eseguire la query.

-   Utilizzare l'istruzione **ALTER DATABASE** per modificare un database esistente, ad esempio se si desidera modificare il nome e l'edizione del database. Per ulteriori informazioni, vedere [ALTER DATABASE (Database di SQL)](https://msdn.microsoft.com/library/ms174269.aspx). L'istruzione seguente modifica il database è stato creato nel passaggio precedente per modificare edizione a S1 Standard.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Utilizzare **DROP DATABASE** istruzione per eliminare un database esistente. Per ulteriori informazioni, vedere [DROP DATABASE (Database di SQL)](https://msdn.microsoft.com/library/ms178613.aspx). L'istruzione seguente consente di eliminare il database **myTestDB** , ma non rilasciarla ora perché verrà utilizzato per creare gli account di accesso nel passaggio successivo.

        DROP DATABASE myTestBase;

-   Il database master include la visualizzazione di **Sys. Databases** che è possibile utilizzare per visualizzare i dettagli su tutti i database. Per visualizzare tutti i database esistenti, eseguire l'istruzione seguente:

        SELECT * FROM sys.databases;

-   Nel Database di SQL, l'istruzione **USE** non è supportata per il passaggio tra i database. Se, tuttavia, è necessario stabilire una connessione direttamente al database di destinazione.

>[AZURE.NOTE] Molte delle istruzioni Transact-SQL che creare o modificano un database devono essere eseguiti in propri batch e non possono essere raggruppate con altre istruzioni Transact-SQL. Per ulteriori informazioni, vedere le informazioni specifiche di istruzione.

## <a name="create-and-manage-logins"></a>Creare e gestire gli account di accesso

Il database **master** contiene gli account di accesso e quali account dispone dell'autorizzazione per creare i database o altri account di accesso. Gestire gli account di accesso mediante la connessione al database **master** con l'account di accesso principale a livello di server creato quando si configura il server. Per eseguire query sul database master che gestisce l'account di accesso attraverso l'intero server, è possibile utilizzare le istruzioni **CREATE LOGIN**, **ALTER LOGIN**o **DROP LOGIN** . Per ulteriori informazioni, vedere [gestione dei database e gli account di accesso in Database SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Per creare un account di accesso a livello di server, utilizzare l'istruzione **CREATE LOGIN** . Per ulteriori informazioni, vedere [CREATE LOGIN (Database di SQL)](https://msdn.microsoft.com/library/ms189751.aspx). L'istruzione seguente viene creato un account di accesso chiamato **login1**. Sostituire **password1** con la password di propria scelta.

        CREATE LOGIN login1 WITH password='password1';

-   Utilizzare l'istruzione **CREATE USER** per concedere autorizzazioni a livello di database. Tutti gli accessi devono essere creati nel database **master** . Per un account di accesso per connettersi a un altro database, è necessario concedere l'autorizzazioni a livello di database con l'istruzione **CREATE USER** in tale database. Per ulteriori informazioni, vedere [Creare utente (Database di SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Per concedere autorizzazioni login1 a un database denominato **myTestDB**, completare la procedura seguente:

 1.  Per aggiornare Esplora oggetti per visualizzare il database **myTestDB** che è stato creato, pulsante destro del mouse sul nome del server in Esplora oggetti e quindi fare clic su **Aggiorna**.  

     Se è chiuso la connessione, sarà possibile riconnettersi selezionando **Connettersi Esplora oggetto** dal menu File.

 2. Pulsante destro del mouse **myTestDB** database e selezionare **Nuova Query**.

    3.  Eseguire l'istruzione seguente nel database myTestDB per creare un utente del database denominato **login1User** che corrisponde al server a livello di accesso **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Utilizzare la **sp\_addrolemember** stored procedure per specificare il livello di autorizzazioni per il database appropriato per l'account utente. Per ulteriori informazioni, vedere [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). L'istruzione seguente offre **login1User** autorizzazioni di sola lettura per il database mediante l'aggiunta di **login1User** per il **db\_datareader** ruolo.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Istruzione **ALTER LOGIN** per modificare un account di accesso esistente, ad esempio se si desidera modificare la password per l'accesso. Per ulteriori informazioni, vedere [ALTER LOGIN (Database di SQL)](https://msdn.microsoft.com/library/ms189828.aspx). Istruzione **ALTER LOGIN** deve essere eseguito sul database **master** . Tornare alla finestra della query che sia connesso a tale database. L'istruzione seguente modifica login **login1** per reimpostare la password. Sostituire **newPassword** con la password della scelta e **oldPassword** con la password corrente per l'accesso.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Istruzione **DROP LOGIN** consente di eliminare un account di accesso esistente. Eliminazione di un account di accesso a livello di server elimina anche tutti gli account utente del database associato. Per ulteriori informazioni, vedere [DROP DATABASE (Database di SQL)](https://msdn.microsoft.com/library/ms178613.aspx). Il database **master** è necessario eseguire l'istruzione **DROP LOGIN** . L'istruzione elimina l'account di accesso **login1** .

        DROP LOGIN login1;

-   Il database master viene utilizzato il **sys.sql\_accessi** visualizzare che è possibile utilizzare per visualizzare gli account di accesso. Per visualizzare tutti gli account esistenti, eseguire l'istruzione seguente:

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Eseguire il monitoraggio Database SQL utilizzando viste a gestione dinamica

Database SQL supporta diverse viste a gestione dinamica che è possibile utilizzare per monitorare un singolo database. Seguono alcuni esempi del tipo di dati di monitor, che è possibile recuperare tramite queste visualizzazioni. Per informazioni dettagliate e altri esempi di utilizzo, vedere [Database SQL di monitoraggio utilizzando viste a gestione dinamica](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Query su una vista di gestione dinamica richiede le autorizzazioni di **Stato di visualizzazione del DATABASE** . Per concedere l'autorizzazione di **Visualizzazione dello stato di DATABASE** a un utente di database specifico, connettersi al database ed eseguire l'istruzione seguente nel database:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcolare la dimensione di database utilizzando la **sys.dm\_db\_partizione\_Stat** visualizzazione. Il **sys.dm\_db\_partizione\_Stat** visualizzazione restituisce le informazioni di pagina e numero di righe per ogni partizione nel database, è possibile utilizzare per calcolare le dimensioni del database. La query seguente restituisce le dimensioni del database in megabyte:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Utilizzare la **sys.dm\_exec\_connessioni** e **sys.dm\_exec\_sessioni** visualizzazioni per recuperare le informazioni sulle connessioni utente corrente e le attività interne associate al database. La query seguente restituisce informazioni sulla connessione corrente:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Utilizzare la **sys.dm\_exec\_query\_Stat** visualizzazione per recuperare le statistiche di prestazioni aggregati per piani di query memorizzati nella cache. La query seguente restituisce informazioni sulle principali cinque query in base a tempo medio CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;
 
 
