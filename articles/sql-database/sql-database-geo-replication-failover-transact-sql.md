<properties 
    pageTitle="Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con Transact-SQL | Microsoft Azure" 
    description="Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con Transact-SQL" 
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
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con Transact-SQL


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


In questo articolo viene illustrato come avviare il controllo a un Database SQL secondario con Transact-SQL. Per configurare la replica geografico, vedere [Configurare geografico-replica di Database SQL Azure](sql-database-geo-replication-transact-sql.md).



Per avviare failover, è necessario quanto segue:

- Un account di accesso che si trova DBManager in primario, hanno db_ownership del database locale verrà geografico replicare, e DBManager nei server partner a cui si configura la replica geografico.
- SQL Server Management Studio (SQL Server Management Studio)


> [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Avviare pianificato in caso di errore promuovere un database secondario per diventare nel nuovo database primario

È possibile utilizzare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario per diventare il nuovo database primario in modo pianificato, abbassare di livello principale esistente per diventare secondario. Istruzione nel database master sul server logico Database SQL Azure in cui si trova il database replicato geografico secondario che si desidera innalzare il livello. Questa funzionalità è progettata per failover pianificato, ad esempio durante le esercitazioni DR e richiede che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente replica alla modalità icona del, causando tutte le transazioni in sospeso a scaricati secondario e blocco tutte le transazioni nuove;

2. Consente di attivare i ruoli dei due database in collaborazione replica geografico.  

Questa sequenza garantisce che i due database vengono sincronizzati prima di passare i ruoli e pertanto non verrà eseguita alcuna perdita di dati. Esiste un breve periodo in cui entrambi i database non sono disponibili (sull'ordine di secondi da 0 a 25) mentre i ruoli sono cambiati. Se il database primario include più database secondari, il comando verrà automaticamente riconfigurare altri database secondari per connettersi al nuovo principale.  L'intera operazione dovrebbe eseguire meno di un minuto per completare in condizioni normali. Per ulteriori informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md)e [Modificare DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) .


Utilizzare la procedura seguente per avviare pianificato in caso di errore.

1. Connettersi al server di Database SQL Azure logico in cui si trova un database secondario replicato geografico Management Studio.

2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione **ALTER DATABASE** seguente per attivare il database secondario il ruolo principale.

        ALTER DATABASE <MyDB> FAILOVER;

4. Fare clic su **Esegui** per eseguire la query.

>[AZURE.NOTE] Raramente, è possibile che l'operazione non è possibile completare e possono essere visualizzati bloccato. In questo caso, l'utente può eseguire il comando failover forza e accettare la perdita di dati.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Avviare un failover non pianificato del database primario per il database secondario

È possibile utilizzare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario per diventare il nuovo database primario in modo non pianificato forzare la riduzione di livello di primario esistente a diventeranno secondaria quando il database principale non è più disponibile. Istruzione nel database master sul server logico Database SQL Azure in cui si trova il database replicato geografico secondario che si desidera innalzare il livello.

Questa funzionalità è progettata per il ripristino di emergenza durante il ripristino di disponibilità del database critico e perdita di dati accettabile. Quando si richiama failover forzato, il database secondario specificato immediatamente diventa il database principale e inizia ad accettare le transazioni in scrittura. Non appena il database primario originale è in grado di riconnettersi con il nuovo database principale, un backup incrementale viene accettato al database principale originale e il vecchio database principale è una chiamata effettuato in un database secondario per il nuovo database primario; Successivamente, è semplicemente una sincronizzazione replica della nuova principale.

Tuttavia, poiché punto nel tempo ripristinare non è supportata nei database secondari, se l'utente desidera recuperare i dati approvati al database primario precedente che non era stato replicato nel nuovo database principale prima che si è verificato il failover forzato, l'utente sarà necessario avviare il supporto per recuperare questa perdita di dati.

Se il database primario include più database secondari, il comando verrà automaticamente riconfigurare altri database secondari per connettersi al nuovo principale.

Utilizzare la procedura seguente per avviare un failover non pianificato.

1. Connettersi al server di Database SQL Azure logico in cui si trova un database secondario replicato geografico Management Studio.

2. Aprire la cartella di database, espandere la cartella di **Database di sistema** , fare clic su **Generale**e quindi fare clic su **Nuova Query**.

3. Utilizzare l'istruzione **ALTER DATABASE** seguente per attivare il database secondario il ruolo principale.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Fare clic su **Esegui** per eseguire la query.

>[AZURE.NOTE] Se il comando quando principale e secondarie sono online principale precedente diventerà la nuova scheda secondaria immediatamente senza la sincronizzazione dei dati. Se non è primario possono verificarsi commie transazioni quando il comando perdita di dati.



## <a name="next-steps"></a>Passaggi successivi   

- Dopo il failover, verificare che i requisiti di autenticazione per server e database configurati per il nuovo database primario. Per informazioni dettagliate, vedere [protezione del Database di SQL dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
- Per informazioni su ripristino in seguito emergenza utilizzando geografico Active-replica, tra cui pre e registrare della procedura di ripristino ed eseguire un drill di ripristino di emergenza, vedere [Ripristino di emergenza](sql-database-disaster-recovery.md)
- Per un post di blog di Sasha Nosov sulle Active geografico replica vedere [riflettore su nuove funzionalità di replica geografico](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Per informazioni sulla progettazione di applicazioni cloud per l'utilizzo della replica geografico attiva, vedere [Progettazione di applicazioni cloud per la continuità aziendale geografico replica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Per informazioni sull'utilizzo della replica geografico attivo con pool di database flessibile, vedere [strategie di ripristino di emergenza Pool flessibile](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Per una panoramica del continurity business, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
