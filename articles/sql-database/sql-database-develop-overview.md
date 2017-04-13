<properties
    pageTitle="Panoramica di sviluppo di Database SQL | Microsoft Azure"
    description="Informazioni sulle librerie di connettività disponibili e procedure consigliate per la connessione al Database SQL di applicazioni."
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>Panoramica di sviluppo di Database SQL
In questo articolo vengono illustrati considerazioni di base che uno sviluppatore deve essere a conoscenza quando si scrive codice per connettersi al Database SQL Azure.

## <a name="language-and-platform"></a>Lingua e la piattaforma
Sono disponibili esempi di codice per diverse lingue e piattaforme di programmazione. È possibile trovare i collegamenti per gli esempi di codice in: 

* Ulteriori informazioni: [le raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limiti delle risorse
Database SQL Azure gestisce le risorse disponibili per un database utilizzando due diversi meccanismi: risorse Governance e imposizione di limiti.

* Per ulteriori informazioni: [Limiti delle risorse di Database SQL Azure](sql-database-resource-limits.md)

## <a name="security"></a>Sicurezza
Database SQL Azure fornisce le risorse per limitare l'accesso, la protezione dei dati e il monitoraggio attività in un Database SQL.

* Ulteriori informazioni: [la protezione del Database SQL](sql-database-security.md)

## <a name="authentication"></a>Autenticazione
* Database SQL Azure supporta sia agli utenti l'autenticazione di SQL Server e gli account di accesso, nonché [l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) utenti e gli account di accesso.
* È necessario specificare un determinato database, invece che verrà utilizzato il database *master* .
* È possibile usare l'istruzione Transact-SQL **utilizzare NomeDatabase;** nel Database di SQL per passare a un altro database.
* Ulteriori informazioni: [protezione del Database di SQL: gestisce la sicurezza di access e login database](sql-database-manage-logins.md)

## <a name="resiliency"></a>Sulla resilienza
Quando si verifica un errore temporaneo durante la connessione al Database SQL, il codice deve riprendere la chiamata.  È consigliabile che Riprova logica backoff usare logica, in modo che esso non sovraccaricare il Database di SQL con più client riprovare contemporaneamente.

* Esempi di codice: per gli esempi di codice che illustrano riprovare logica, vedere esempi per la lingua desiderata in: [raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md)
* Ulteriori informazioni: [messaggi di errore per i programmi di Database SQL client](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Gestire le connessioni
* Logica di connessione del client, ignorare il timeout predefinito per essere 30 secondi.  Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da internet.
* Se si utilizza un [pool di connessioni](http://msdn.microsoft.com/library/8xx3tyca.aspx), assicurarsi di chiudere la connessione immediata del programma non utilizza attivamente e non la preparazione per poterlo riutilizzare.

## <a name="network-considerations"></a>Considerazioni sulla rete
* Nel computer che ospita il programma client, assicurarsi che il firewall consente la comunicazione TCP in uscita sulla porta 1433.  Ulteriori informazioni: [configurare un firewall di Database SQL Azure](sql-database-configure-firewall-settings.md)
* Se il programma client si connette a SQL Database V12 mentre il client viene eseguito su una Azure macchine (), è necessario aprire alcuni intervalli di porta nella macchina virtuale. Ulteriori informazioni: [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
* Connessioni client al V12 di Database SQL Azure può succedere che ignorano il proxy e interagiscono direttamente con il database. Porte diverso da 1433 diventano importante. Ulteriori informazioni: [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Dati Sharding con scalabilità flessibile
Scalabilità flessibile semplifica il processo di ridimensionamento ritirare (e). 

* [Modelli di progettazione per applicazioni multi-tenant SaaS con Database SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Il routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md)
* [Guida introduttiva a anteprima scalabilità flessibile di Database SQL Azure](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Passaggi successivi

Esplorare tutte le [funzionalità di Database SQL](https://azure.microsoft.com/services/sql-database/).
