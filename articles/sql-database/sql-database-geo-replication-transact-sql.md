<properties
    pageTitle="Configurare la replica geografico per Database SQL Azure con Transact-SQL | Microsoft Azure"
    description="Configurare la replica geografico per Database di SQL Azure con Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurare la replica geografico per Database SQL Azure con Transact-SQL

> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-replication-overview.md)
- [Portale di Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

In questo articolo viene illustrato come configurare la replica di geografico Active per un Database di SQL Azure con Transact-SQL.

Per avviare failover utilizzando Transact-SQL, vedere [avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] Replica geografico attiva (leggibile dei secondari) è ora disponibile per tutti i database in tutti i livelli di servizio. In aprile 2017 ritiro il tipo secondario non leggibile e database non leggibile esistenti verranno aggiornati automaticamente a database secondari leggibili.

Per configurare la replica geografico attivo con Transact-SQL, è necessario le operazioni seguenti:

- Un abbonamento Azure.
- Un server di Database SQL Azure logico <MyLocalServer> e un database SQL <MyDB> -il database principale che si desidera replicare.
- Uno o più Database SQL Azure server logici < MySecondaryServer(n) > - server logici da server partner in cui è necessario creare database secondari.
- Un account di accesso che si trova DBManager in primario, hanno db_ownership del database locale verrà geografico replicare, e DBManager nei server partner a cui si configura la replica geografico.
- SQL Server Management Studio (SQL Server Management Studio)

> [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="add-secondary-database"></a>Aggiungere database secondario

È possibile utilizzare l'istruzione **ALTER DATABASE** per creare un database secondario replicato geografico su un server partner. Si esegue questa istruzione nel database master del server contenente il database da replicare. Il database replicato geografico (il "database primario") avrà lo stesso nome del database replicato e avrà, per impostazione predefinita, lo stesso livello di servizio dei database primario. Il database secondario può essere leggibile o non letti e può essere un solo database o un databbase flessibile. Per ulteriori informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md)e [Modificare DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) .
Dopo il database secondario creato, definito dati verranno avviata la replica in modo asincrono dal database principale. La procedura seguente viene descritto come configurare la replica geografico con Management Studio. Procedura per la creazione dei secondari non leggibile e facilitarne la lettura, con un solo database o un database flessibile, è disponibili.

> [AZURE.NOTE] Se esiste un database nel server partner specificato con lo stesso nome del database primario che il comando avrà esito negativo.


### <a name="add-non-readable-secondary-single-database"></a>Aggiungere secondario non leggibile (solo database)

Utilizzare la procedura seguente per creare secondario non leggibile come un unico database.

1. Usa versione 13.0.600.65 o versioni successive di SQL Server Management Studio.

     > [AZURE.IMPORTANT] Scaricare [l'ultima](https://msdn.microsoft.com/library/mt238290.aspx) versione di SQL Server Management Studio. Si consiglia di utilizzare sempre la versione più recente di Management Studio di rimanere aggiornati con gli aggiornamenti al portale di Azure.


2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione **ALTER DATABASE** per convertire un database locale in una replica geografico primaria con un database secondario non leggibile MySecondaryServer1 dove MySecondaryServer1 è il nome del server descrittivo.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Fare clic su **Esegui** per eseguire la query.


### <a name="add-readable-secondary-single-database"></a>Aggiungere secondario leggibile (solo database)
Utilizzare la procedura seguente per creare secondario leggibile come un unico database.

1. In Management Studio connettersi al server logico di Database SQL Azure.

2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione **ALTER DATABASE** seguente per convertire un database locale in una replica geografico primaria con un database secondario leggibile su un server secondario.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Fare clic su **Esegui** per eseguire la query.



### <a name="add-non-readable-secondary-elastic-database"></a>Aggiungere secondario non leggibile (database flessibile)

Utilizzare la procedura seguente per creare secondario non leggibile come database flessibile.

1. In Management Studio connettersi al server logico di Database SQL Azure.

2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione **ALTER DATABASE** per trasformare un database locale in una replica geografico primaria con un database secondario non leggibile su un server secondario in un pool di flessibile.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Fare clic su **Esegui** per eseguire la query.



### <a name="add-readable-secondary-elastic-database"></a>Aggiungere secondario leggibile (database flessibile)
Utilizzare la procedura seguente per creare leggibile secondario come database flessibile.

1. In Management Studio connettersi al server logico di Database SQL Azure.

2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione **ALTER DATABASE** per trasformare un database locale in una replica geografico primaria con un database secondario leggibile su un server secondario in un pool di flessibile.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Fare clic su **Esegui** per eseguire la query.



## <a name="remove-secondary-database"></a>Rimuovere database secondario

È possibile utilizzare l'istruzione **ALTER DATABASE** per interrompere definitivamente la relazione di replica tra un database secondario e il relativo principale. Istruzione nel database master in cui si trova il database primario. Dopo la chiusura di relazione, il database secondario diventa un normale database di sola lettura. Se la connessione al database secondario è interrotta il comando ha avuto esito positivo ma secondaria diventerà in lettura / scrittura dopo il ripristino di integrazione applicativa. Per ulteriori informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md)e [Modificare DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) .

Utilizzare la procedura seguente per rimuovere una relazione di replica geografico replicato geografico secondario.

1. In Management Studio connettersi al server logico di Database SQL Azure.

2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione **ALTER DATABASE** seguente per rimuovere secondario replicato geografico.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Fare clic su **Esegui** per eseguire la query.

## <a name="monitor-geo-replication-configuration-and-health"></a>Monitorare l'integrità e la configurazione della replica geografico

Monitoraggio attività includono il monitoraggio dell'integrità della replica di dati e il monitoraggio della configurazione di replica geografico.  È possibile utilizzare la visualizzazione di gestione dinamica **sys.dm_geo_replication_links** nel database master per restituire tutti i collegamenti di replica esistente per ogni database di informazioni sul server logico Database SQL Azure. Questa visualizzazione contiene una riga per ogni il collegamento tra database primario e secondario. È possibile utilizzare la visualizzazione di gestione dinamica **sys.dm_replication_link_status** per restituire una riga per ogni Database di SQL Azure attualmente impegnati in un collegamento di replica della replica. Sono inclusi database primario e secondario. Se è presente più di un collegamento di replica continua per un determinato database principale, questa tabella contiene una riga per ogni le relazioni. La visualizzazione viene creata in tutti i database, tra cui lo schema logico. Tuttavia, query questa visualizzazione nello schema logico restituisce un set vuoto. È possibile utilizzare la visualizzazione di gestione dinamica **sys.dm_operation_status** per mostrare lo stato per tutte le operazioni di database, tra cui lo stato dei collegamenti di replica. Per ulteriori informazioni, vedere [sys.geo_replication_links (Database di SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Database di SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)e [sys.dm_operation_status (Database di SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Utilizzare la procedura seguente per monitorare una relazione di replica geografico.

1. In Management Studio connettersi al server logico di Database SQL Azure.

2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione seguente per visualizzare tutti i database con collegamenti di replica geografico.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Fare clic su **Esegui** per eseguire la query.
5. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **MyDB**e quindi fare clic su **Nuova Query**.
6. Utilizzare l'istruzione seguente per visualizzare i ritardi della replica e ultima replica il momento del database secondari di MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Fare clic su **Esegui** per eseguire la query.
8. Utilizzare l'istruzione seguente per visualizzare le operazioni di replica di geografico più recente associate al database MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Fare clic su **Esegui** per eseguire la query.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Eseguire l'aggiornamento secondario non leggibile a leggibile

In aprile 2017 ritiro il tipo secondario non leggibile e database non leggibile esistenti verranno aggiornati automaticamente a database secondari leggibili. Se si usano database secondari non leggibile oggi e necessario eseguire l'aggiornamento in modo da essere letto, è possibile utilizzare semplici passaggi seguenti per ogni secondario.

> [AZURE.IMPORTANT] Non esiste alcun metodo modalità self-service per l'aggiornamento sul posto di secondario non leggibile a leggibile. Se si elimina il solo secondario, quindi il database primario rimarrà non protetto fino a quando non sia completamente sincronizzato nel nuovo database secondario. Se contratto di servizio dell'applicazione richiede che primario sempre è protetto, è consigliabile creare secondaria in parallelo in un server diverso prima di applicare la procedura di aggiornamento precedente. Nota che ogni principale può avere fino a 4 database secondari.


1. Prima di tutto, connettersi al server *secondario* e rilasciare il database secondario non leggibile:  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. A questo punto la connessione al server *primario* e aggiungere un nuovo database secondario leggibile

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulle Active geografico replica, vedere - [Replica di geografico Active](sql-database-geo-replication-overview.md)
- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
