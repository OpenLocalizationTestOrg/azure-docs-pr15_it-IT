<properties
    pageTitle="Tutti gli argomenti per il servizio di Database SQL | Microsoft Azure"
    description="Tabella di tutti gli argomenti per il servizio di Azure denominata Database SQL in cui sono presenti http://azure.microsoft.com/documentation/articles/, titolo e descrizione."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Tutti gli argomenti per il servizio di Database SQL Azure

In questo argomento sono elencati tutti gli argomenti che si applicano direttamente al servizio di **Database SQL** di Azure. È possibile cercare in questa pagina Web per le parole chiave mediante **Ctrl + F**per trovare gli argomenti di interesse corrente.




## <a name="new"></a>Nuovo

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 1 | [Daxko/CSI utilizzato Azure per accelerare il ciclo di sviluppo e migliorare il servizio clienti e prestazioni](sql-database-implementation-daxko.md) | Informazioni sulle modalità di utilizzo Daxko/CSI di Database SQL per accelerare il ciclo di sviluppo e migliorare il servizio clienti e prestazioni |
| 2 | [Azure offre portata globale GEP e migliorare l'efficienza](sql-database-implementation-gep.md) | Informazioni sull'utilizzo GEP Database SQL di raggiungere clienti più globali e ottenere maggiore efficienza |
| 3 | [Con Azure, SnelStart è rapidamente espanso relativi servizi business pari a 1.000 nuovi database di SQL Azure al mese](sql-database-implementation-snelstart.md) | Informazioni sull'utilizzo SnelStart Database SQL di espandere rapidamente i servizi business pari a 1.000 nuovi database di SQL Azure al mese |
| 4 | [Umbraco utilizza il Database di SQL Azure ai servizi rapidamente proporzioni e le disposizioni per migliaia di tenant nel cloud](sql-database-implementation-umbraco.md) | Informazioni sull'utilizzo Umbraco Database SQL di eseguire rapidamente il provisioning e servizi scala migliaia di tenant nel cloud |
| 5 | [Gestire il Database SQL Azure con PowerShell](sql-database-manage-powershell.md) | Gestione di Database SQL Azure con PowerShell. |
| 6 | [Supporto di SQL Server Management Studio per MFA di Active Directory Azure con SQL Data Warehouse e Database SQL](sql-database-ssms-mfa-authentication.md) | Utilizzare l'autenticazione inseriti più con SQL Server Management Studio per Database SQL e SQL Data Warehouse. |
| 7 | [Spiegare Database transazione unità (DTUs) e flessibile Database transazione (eDTUs)](sql-database-what-is-a-dtu.md) | Informazioni sulle quali un Database di SQL Azure unità transazione è. |


## <a name="updated-articles-sql-database"></a>Articoli aggiornati, Database SQL

In questa sezione sono elencati gli articoli che sono stati aggiornati di recente, nel punto in cui l'aggiornamento è stato grande o significativa. Per ogni articolo aggiornato, viene visualizzato un frammento approssimativo del testo delle vendite promozionali aggiunte. Gli articoli sono stati aggiornati nell'intervallo di date del **2016-08-22** - **2016-10-05**.

| &nbsp; | Articolo | Testo aggiornato, frammento di codice | Aggiornamento |
| --: | :-- | :-- | :-- |
| 8 | [Gestire il Database SQL Azure con PowerShell](sql-database-command-line-tools.md) | Creare un gruppo di risorse per il Database SQL e le relative risorse Azure con i cmdlet New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). *$resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" nuovo AzureRmResourceGroup-nome $resourceGroupName-percorso $resourceGroupLocation* Per ulteriori informazioni, vedere uso di PowerShell Azure Gestione risorse Azure (... / manager.md powershell-azure-delle risorse). Per uno script di esempio, vedere creare un database SQL di script di PowerShell (sql-database-get-introduttiva-powershell.md create-a-sql-database-powershell-script). **Creare un Database SQL server** Creare un server di Database SQL con i cmdlet New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Sostituire *server1* con il nome del server. I nomi dei server deve essere univoci in tutti i server di Database SQL Azure. Se il nome del server già viene accettato, viene visualizzato un errore. Questo comando può richiedere alcuni minuti. il resou | 2016-09-14 |
| 9 | [Copiare un database di SQL Azure con PowerShell](sql-database-copy-powershell.md) |   Database origine SQL (esistenti database per copiare) - $sourceDbName = "db1" $sourceDbServerName = "server1" $sourceDbResourceGroupName = "rg1" SQL database copia (da creare nuove db) - $copyDbName = "db1_copy" $copyDbServerName = "server2" $copyDbResourceGroupName = "rg2" copia un database nello stesso server, ossia AzureRmSqlDatabaseCopy New - ResourceGroupName $sourceDbResourceGroupName - nomeserver $sourceDbServerName - DatabaseName $sourceDbName - CopyDatabaseName $copyDbName copiare un database in un altro server, ossia AzureRmSqlDatabaseCopy New - ResourceGroupName $sourceDbResourceGroupName - nomeserver $sourceDbServerName - DatabaseName $sourceDbName - CopyResourceGroupName $copyDbResourceGroupName - CopyServerName $copyDbServerName - CopyDatabaseName $copyDbName copiare un database di un pool di database flessibile - $poolName = "pool1" nuovo AzureRmSqlDatabaseCopy - ResourceGroupName $ sourceDbResourceGroupName - nomeserver $sourceDbServerName - DatabaseName $sourceDbName CopyReso - | 2016-09-08 |
| 10 | [Creare un pool di database flessibile con C#](sql-database-elastic-pool-create-csharp.md) |   WriteLine ("firewall Server...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule (_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  WriteLine ("firewall Server:" + fwr. FirewallRule.Id);  WriteLine ("flessibile pool...");  Endpoint ElasticPoolCreateOrUpdateResponse = CreateOrUpdateElasticDatabasePool (_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  WriteLine ("pool flessibile:" + endpoint. ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase (_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  WriteLine ("Database:" + dbr. Database.Id);  WriteLine ("premere un tasto per continuare...");  Console.ReadKey();  } CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa ResourceGroup statico | 2016-09-14 |
| 11 | [Script di PowerShell per identificare i database adatti per un pool di database flessibile](sql-database-elastic-pool-database-assessment-powershell.md) | Candidati **per flessibile pool di database, Microsoft esclude master e i database già presenti in un pool. È possibile aggiungere più database all'elenco escluso in base alle esigenze** $ListOfDBs = AzureRmSqlDatabase Get - nomeserver $servername. Split('.') 0 - ResourceGroupName $ResourceGroupName / Where-Object {$_. DatabaseName - notin ("principale") - e $_. CurrentServiceLevelObjectiveName - notin ("ElasticPool")- e $_. CurrentServiceObjectiveName - notin ("ElasticPool")} $outputConnectionString = "origine dati = $outputServerName; sicurezza integrata = false; iniziale catalogo = $outputdatabaseName; Id utente = $outputDBUsername; Password = $outputDBpassword "$destinationTableName ="resource_stats_output" **creare una tabella nel database di output per la raccolta metriche** $sql =" se NOT EXISTS (selezionare * da Sys object_id WHERE = OBJECT_ID(N'$($destinationTableName)') e digitare (N'U ")) $($destinationTableName) iniziare a creare una tabella (nome_database varchar(128), varchar (20) OSU, end_time datetime, margine di flessibilità avg_cpu, Media_ | 2016-09-29 |
| 12 | [Esercitazione di Database SQL: creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md) |   ! Nuovo database sql di 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Fare clic su **Database SQL** per aprire e il Database di SQL. Il contenuto di questo blade varia a seconda del numero delle sottoscrizioni e gli oggetti esistenti (ad esempio server esistenti).  ! Nuovo database sql di 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. Nella casella di testo **Nome Database** specificare un nome per il primo database, ad esempio "risorse del database". Un segno di spunta verde indica che è stato specificato un nome valido.  ! Nuovo database di sql 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Se si hanno più abbonamenti, selezionare un abbonamento. 6. Nel **gruppo di risorse**, fare clic su **Crea nuovo** e immettere un nome per il primo gruppo di risorse, ad esempio "personale di gruppo risorse". Un segno di spunta verde indica che è stato specificato un nome valido.  ! Nuovo database sql di 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. In **selezionare origine* | 2016-09-08. |
| 13 | [Database SQL di provare a: Usare c# per creare un database SQL con la raccolta di Database SQL per .NET](sql-database-get-started-csharp.md) | **Creare un servizio principale per accedere alle risorse** Il seguente script di PowerShell crea l'applicazione di Active Directory (AD) e l'identità di servizio che è necessario eseguire l'autenticazione applicazione C. Lo script restituisce i valori requisiti per l'esempio C precedente. Per informazioni dettagliate, vedere usare PowerShell di Azure per creare un'entità servizio per accedere alle risorse (... / risorsa-gruppo-eseguire l'autenticazione-servizio-principal.md).  Accedere a Azure.  Aggiungere AzureRmAccount se si hanno più abbonamenti, rimuovere il commento ed è impostato per l'abbonamento a cui che si desidera utilizzare.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}" AzureRmContext Set - SubscriptionId $subscriptionId fornire questi valori per la nuova app AAD.  $appName è il nome visualizzato per l'app, devono essere univoci nella directory.  non è necessario essere un uri reale $uri.  $secret è una password creati.  $appName = "{app-nome}" $uri = "http://{app-name"} $secret = "{password per l'app}" creare un'app AAD $azureAdApplication = AzureRmADApp nuovo | 2016-09-23 |
| 14 | [Gestione dei database di SQL Azure tramite il portale di Azure](sql-database-manage-portal.md) | Per visualizzare o aggiornare le impostazioni del database, fare clic nella posizione desiderata nella e database SQL:! Impostazioni di database SQL (. / media/sql-database-manage-portal/settings.png) **come trovare il nome del server completo database SQL?** Per visualizzare il nome del server di database, fare clic su **informazioni generali** sulla e **database SQL** e notare il nome del server:! Impostazioni di database SQL (. / media/sql-database-manage-portal/server-name.png) **come gestire le regole del firewall per controllare l'accesso su SQL server e database?** Per visualizzare, creare o aggiornare le regole firewall, scegliere **Imposta firewall server** e il **database di SQL** . Per informazioni dettagliate, vedere Configurare una regola firewall a livello di server di Database SQL Azure tramite il portale di Azure (sql-database-configurazione-firewall-settings.md). ! le regole del firewall (. / media/sql-database-manage-portal/sql-database-firewall.png) **come cambia il SQL database servizio livello o prestazioni livello?** Per aggiornare il livello di livello o le prestazioni del servizio di un database SQL, fare clic su * * prezzi livello (s | 2016-09-20 |





## <a name="get-started"></a>Guida introduttiva

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 15 | [Consente di creare multi-tenant App con Database SQL Azure con isolamento e l'efficienza](sql-database-build-multi-tenant-apps.md) | Altre modalità di compilazione di applicazioni multi-tenant Database SQL |
| 16 | [Connettersi al Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md) | Informazioni su come connettersi a Database SQL Azure con SQL Server Management Studio (Express). Quindi eseguire una query di esempio con Transact-SQL (Transact-SQL). |
| 17 | [Connettersi al Database SQL utilizzando .NET (c#)](sql-database-develop-dotnet-simple.md) | Usare il codice di esempio in questa veloce iniziare a creare un'applicazione con c# moderna e supportate da un database relazionale potente nel cloud con Database di SQL Azure. |
| 18 | [Creare un nuovo pool di database flessibile con il portale di Azure](sql-database-elastic-pool-create-portal.md) | Come aggiungere un pool di scalable database flessibile per la configurazione del database SQL più facile amministrazione e condivisione tra molti database della risorsa. |
| 19 | [Esplorare le esercitazioni di Database SQL Azure](sql-database-explore-tutorials.md) | Informazioni sulle funzionalità e le funzioni di Database SQL |
| 20 | [Esercitazione di Database SQL: creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md) | Informazioni su come configurare un server di Database SQL logico, regola firewall server, database SQL e i dati di esempio. Inoltre, informazioni su come connettersi con gli strumenti client, configurare gli utenti e configurare una regola firewall di database. |
| 21 | [Database SQL Azure protegge e proteggere i](sql-database-helps-secures-and-protects.md) | Ecco come Database SQL di aiuta sicura e proteggere |
| 22 | [Database SQL Azure le apprende &amp; adatta](sql-database-learn-and-adapt.md) | Informazioni su come Database SQL le apprende e vi si adatta |
| 23 | [Scegliere un'opzione di SQL Server cloud: Database SQL Azure (PaaS) o SQL Server in macchine virtuali di Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Informazioni su quale opzione di SQL Server cloud rientri l'applicazione: Database SQL Azure (PaaS) o SQL Server nel cloud in macchine virtuali di Azure. |
| 24 | [Scale di Database SQL Azure in tempo reale](sql-database-scale-on-the-fly.md) | Informazioni su come scale di Database SQL in tempo reale |
| 25 | [Esplorare avvio rapido soluzione di Database SQL Azure](sql-database-solution-quick-starts.md) | Informazioni sulle soluzioni di Database SQL Azure |
| 26 | [Database SQL Azure funziona nel proprio ambiente](sql-database-works-in-your-environment.md) | Informazioni su come SQL Database consente di protegge, proteggere i |



## <a name="build-an-app"></a>Creare un'app

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 27 | [Risoluzione dei problemi, diagnosi ed evitare errori di connessione SQL e temporanee per Database SQL](sql-database-connectivity-issues.md) | Informazioni su come risolvere i problemi, diagnosi ed evitare un errore di connessione SQL o temporaneo nel Database di SQL Azure.  |
| 28 | [Connettersi a un Database SQL con Visual Studio](sql-database-connect-query.md) | Scrivere un programma in c# per query oltre a connettersi al database SQL. Informazioni sugli indirizzi IP, le stringhe di connessione, l'accesso protetto e gratuito Visual Studio. |
| 29 | [Porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md) | Connessioni client da ADO.NET a V12 di Database SQL Azure può succedere che ignorano il proxy e interagiscono direttamente con il database. Porte diverso da 1433 diventano importanti. |
| 30 | [Codici di errore SQL per le applicazioni client di Database SQL: errore di connessione e altri problemi di Database](sql-database-develop-error-messages.md) | Informazioni sui codici di errore SQL per le applicazioni client di Database SQL, ad esempio comuni errori di connessione di database, problemi di copia del database e di errori generali.  |
| 31 | [Connettersi al Database SQL tramite PHP su Windows](sql-database-develop-php-simple.md) | Presenta un esempio di programma PHP che si connette al Database SQL Azure da un client di Windows e vengono forniti collegamenti ai componenti software necessari richiesti dal client. |
| 32 | [Database SQL di provare a: Usare c# per creare un database SQL con la raccolta di Database SQL per .NET](sql-database-get-started-csharp.md) | Provare Database SQL per lo sviluppo di App SQL e c# e creare un Database di SQL Azure con c# usando la raccolta di Database SQL per .NET. |
| 33 | [Guida introduttiva a caratteristiche JSON nel Database di SQL Azure](sql-database-json-features.md) | Database SQL Azure consente di analisi, query e formattare i dati in notazione JavaScript Object Notation (JSON). |
| 34 | [Risoluzione dei problemi di connessione al Database SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Procedure per identificare e risolvere gli errori comuni di connessione di Database SQL Azure. |
| 35 | [Errore "Database sul server non è attualmente disponibile" quando ci si connette al database sql](sql-database-troubleshoot-connection.md) | Risoluzione dei problemi del database nel server non è attualmente disponibile errore quando un'applicazione si connette al Database SQL. |



## <a name="develop"></a>Sviluppare

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 36 | [Ottenere i valori necessari per l'autenticazione di un'applicazione di Database SQL di accedere dal codice](sql-database-client-id-keys.md) | Creare un'entità servizio per accedere al Database di SQL dal codice. |
| 37 | [Connettersi al Database SQL mediante Java JDBC in Windows](sql-database-develop-java-simple.md) | Presenta un esempio di codice Java che è possibile utilizzare per connettersi al Database SQL Azure. Nell'esempio vengono utilizzati JDBC e viene eseguita in un computer client di Windows. |
| 38 | [Connettersi al Database SQL tramite Node](sql-database-develop-nodejs-simple.md) | Presenta un esempio di codice Node che è possibile utilizzare per connettersi al Database SQL Azure. |
| 39 | [Panoramica di sviluppo di Database SQL](sql-database-develop-overview.md) | Informazioni sulle librerie di connettività disponibili e procedure consigliate per la connessione al Database SQL di applicazioni. |
| 40 | [Connettersi al Database SQL tramite Python](sql-database-develop-python-simple.md) | Presenta un esempio di codice Python che è possibile utilizzare per connettersi al Database SQL Azure. |
| 41 | [Connettersi al Database SQL con trascrizione](sql-database-develop-ruby-simple.md) | Assegnare un esempio di codice trascrizione che è possibile eseguire per connettersi al Database SQL Azure. |
| 42 | [Guida introduttiva a tabelle temporali nel Database SQL Azure](sql-database-temporal-tables.md) | Informazioni su come iniziare a utilizzare tabelle temporali nel Database di SQL Azure. |



## <a name="performance-and-scale"></a>Proporzioni e le prestazioni

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 43 | [Preparazione di Database SQL](sql-database-advisor.md) | La preparazione di Database SQL Azure consigli per il database di SQL esistenti che consentono di migliorare le prestazioni delle query corrente. |
| 44 | [Preparazione di Database SQL tramite il portale di Azure](sql-database-advisor-portal.md) | È possibile utilizzare la preparazione di Database SQL Azure nel portale di Azure per esaminare e implementare consigli per il database di SQL esistenti che consentono di migliorare le prestazioni delle query corrente. |
| 45 | [Panoramica di benchmark di Database SQL Azure](sql-database-benchmark-overview.md) | Questo argomento illustra i Benchmark di Database SQL Azure utilizzato in misurare le prestazioni del Database di SQL Azure. |
| 46 | [Quando utilizzare un pool di database flessibile](sql-database-elastic-pool-guidance.md) | Un pool di database flessibile è una raccolta di risorse disponibili condivisi da un gruppo di database flessibile. In questo documento vengono fornite informazioni utili per valutare l'idoneità dell'utilizzo di un pool di database flessibile per un gruppo di database. |
| 47 | [Guida introduttiva a strumenti Database flessibile](sql-database-elastic-scale-get-started.md) | Descrizione generale della caratteristica strumenti di database flessibile del Database di SQL Azure, tra cui facile eseguire l'applicazione di esempio. |
| 48 | [Database SQL di domande frequenti](sql-database-faq.md) | Risposte ai clienti di domande comuni domande su cloud database e Database di SQL Azure, database relazionali gestione sistema Microsoft (RDBMS) e database come servizio nel cloud. |
| 49 | [Guida introduttiva a (Preview) In memoria in Database SQL](sql-database-in-memory.md) | Le tecnologie SQL In memoria migliorano notevolmente le prestazioni delle transazioni e carichi di lavoro analitica. Informazioni su come sfruttare le tecnologie. |
| 50 | [Utilizzo di memoria OLTP (preview) per migliorare le prestazioni dell'applicazione di Database SQL](sql-database-in-memory-oltp-migration.md) | OLTP In memoria adottino per migliorare le prestazioni transazione in un database SQL esistente. |
| 51 | [Spazio di archiviazione In memoria OLTP monitor](sql-database-in-memory-oltp-monitoring.md) | In base alla valutazione e monitorare lo spazio di archiviazione in memoria XTP usare, capacità; risolvere l'errore capacità 41823 |
| 52 | [Funzionamento dell'archivio di Query in Database SQL Azure](sql-database-operate-query-store.md) | Informazioni su come utilizzare l'archivio di Query di Database SQL Azure |
| 53 | [Comprendere le prestazioni di Database SQL](sql-database-performance.md) | Il Database di SQL Azure sono disponibili gli strumenti di prestazioni per identificare più aree che è possono migliorare le prestazioni delle query corrente. |
| 54 | [Azure SQL Database e le prestazioni dei singoli database](sql-database-performance-guidance.md) | In questo articolo consentono di determinare il livello di servizio da scegliere per l'applicazione. Inoltre, si consiglia modi per ottimizzare l'applicazione per ottenere il massimo da Database SQL Azure. |
| 55 | [Comprensione delle prestazioni Query Database SQL Azure](sql-database-query-performance.md) | Monitoraggio delle prestazioni di query identifica maggior parte delle query di utilizzo della CPU per un Database di SQL Azure. |
| 56 | [Modificare il livello e prestazioni livello di servizio (prezzi livello) di un database SQL](sql-database-scale-up.md) | Modificare il livello di servizio e livello di prestazioni di un database di SQL Azure viene illustrato come ridimensionare il database SQL verso l'alto o verso il basso. Modificare il livello di prezzo di un database di SQL Azure. |
| 57 | [Monitoraggio delle prestazioni di database nel Database di SQL Azure](sql-database-single-database-monitor.md) | Informazioni sulle opzioni disponibili per il database con strumenti Azure e viste a gestione dinamica di monitoraggio. |
| 58 | [Suggerimenti su come ottimizzare le prestazioni Database SQL](sql-database-troubleshoot-performance.md) | Suggerimenti per l'ottimizzazione delle prestazioni nel Database di SQL Azure la valutazione e analisi utilizzo software. |
| 59 | [Come utilizzare il batch per migliorare le prestazioni dell'applicazione di Database SQL](sql-database-use-batching-to-improve-performance.md) | L'argomento fornisce una prova tale batch operazioni di database imroves notevolmente la velocità e scalabilità delle applicazioni di Database SQL Azure. Sebbene queste tecniche batch funzionano per un database SQL Server, dell'articolo è incentrato Azure. |



## <a name="tools-for-scaling-out"></a>Strumenti per la scalabilità

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 60 | [Progettare modelli per le applicazioni SaaS multi-tenant e Database di SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | In questo articolo vengono illustrati i requisiti e modelli di architettura dati comuni di database multi-tenant applicazioni in esecuzione in un ambiente basato su cloud necessario prendere in considerazione e compromessi vari associati a questi modelli. Viene inoltre come Database di SQL Azure, le relative flessibile pool e flessibile strumenti risolvere questi requisiti in modo senza compromessi. |
| 61 | [Eseguire la migrazione di database esistenti a scalabilità orizzontale](sql-database-elastic-convert-to-use-elastic-tools.md) | Convertire database sharded per utilizzare gli strumenti di database flessibile creando un condiviso manager mappa |
| 62 | [Creazione di database scalable cloud](sql-database-elastic-database-client-library.md) | Creazione di applicazioni di database .NET scalable con la raccolta di client database flessibile |
| 63 | [Contatori delle prestazioni per il gestore di mappa condiviso](sql-database-elastic-database-perf-counters.md) | ShardMapManager classe e dati dipendenti routing contatori delle prestazioni |
| 64 | [Aggiunta di un condiviso con strumenti Database flessibile](sql-database-elastic-scale-add-a-shard.md) | Imposta come usare flessibile scala API per aggiungere nuovi shards a un condiviso. |
| 65 | [Distribuire un servizio di stampa unione di divisione](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Divisione e unione con Strumenti database flessibile |
| 66 | [Il routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) | Come usare la classe ShardMapManager nelle applicazioni .NET per il routing, una caratteristica di database flessibile per il Database di SQL Azure dipendenti dai dati |
| 67 | [Strumenti di database flessibile domande frequenti](sql-database-elastic-scale-faq.md) | Domande frequenti su scalabilità flessibile Database SQL Azure. |
| 68 | [Glossario di strumenti di Database flessibile](sql-database-elastic-scale-glossary.md) | Spiegazione dei termini utilizzati per gli strumenti di database flessibile |
| 69 | [Scalabilità orizzontale con il Database di SQL Azure](sql-database-elastic-scale-introduction.md) | Software come gli sviluppatori di servizio poter creare facilmente database flessibile, scalable nel cloud tramite questi strumenti |
| 70 | [Credenziali usate per accedere alla raccolta di client Database flessibile](sql-database-elastic-scale-manage-credentials.md) | Come impostare il livello di destro delle credenziali, amministrazione in sola lettura, per le applicazioni di database flessibile |
| 71 | [Query su più condiviso](sql-database-elastic-scale-multishard-querying.md) | Eseguire query su shards usando la raccolta di client database flessibile. |
| 72 | [Spostare dati tra database cloud scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md) | In questo articolo viene spiegato come modificare shards e spostare i dati tramite un servizio indipendente usando flessibile API di database. |
| 73 | [Scalabilità database con il responsabile di mappa condiviso](sql-database-elastic-scale-shard-map-management.md) | Come usare ShardMapManager, libreria client database flessibile |
| 74 | [Configurazione della protezione di stampa unione di divisione](sql-database-elastic-scale-split-merge-security-configuration.md) | Configurare x409 i certificati di crittografia |
| 75 | [Aggiornare un'app per utilizzare la libreria di client più recente di database flessibile](sql-database-elastic-scale-upgrade-client-library.md) | Aggiornamento di applicazioni e raccolte con Nuget |
| 76 | [Raccolta di client Database flessibile con entità Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Utilizzare la raccolta di client Database flessibile che entità Framework per la codifica di database |
| 77 | [Utilizzo della libreria client di database flessibile con Dapper](sql-database-elastic-scale-working-with-dapper.md) | Utilizzo di una raccolta di client database flessibile con Dapper. |
| 78 | [Applicazioni multi-tenant con Strumenti database flessibile e protezione a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Informazioni su come usare gli strumenti database flessibile insieme protezione a livello di riga per creare un'applicazione con un livello di scalabilità dati nel Database di SQL Azure che supporta shards multi-tenant. |



## <a name="elastic-jobs"></a>Processi flessibile

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 79 | [Creare e gestire in scala i database di SQL Azure con processi flessibile (preview)](sql-database-elastic-jobs-create-and-manage.md) | Scorrere la creazione e gestione di un processo di database flessibile. |
| 80 | [Guida introduttiva di processi di Database flessibile](sql-database-elastic-jobs-getting-started.md) | sull'utilizzo dei processi di database flessibile |
| 81 | [Gestione dei database fuori architetture cloud](sql-database-elastic-jobs-overview.md) | Viene illustrato il servizio di processo database flessibile |
| 82 | [Creare e gestire i processi di database flessibile un Database SQL tramite PowerShell (preview)](sql-database-elastic-jobs-powershell.md) | PowerShell usato per gestire i pool di Database SQL Azure |
| 83 | [Panoramica di processi di installazione Database flessibile](sql-database-elastic-jobs-service-installation.md) | Scorrere l'installazione della caratteristica flessibile processo. |
| 84 | [Disinstallare componenti del Database flessibile processi](sql-database-elastic-jobs-uninstall.md) | Come disinstallare lo strumento di processi database flessibile |



## <a name="elastic-pools"></a>Pool flessibile

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 85 | [Che cos'è un pool flessibile Azure?](sql-database-elastic-pool.md) | Gestire centinaia o migliaia di database utilizzando un pool. Un prezzo di un insieme di unità di performance può essere distribuito in pool. Spostare verranno database avanti o indietro. |
| 86 | [Creare un pool di database flessibile con C#](sql-database-elastic-pool-create-csharp.md) | Usare tecniche di sviluppo database c# per creare un pool di database flessibile scalable nel Database di SQL Azure in modo che è possibile condividere risorse tra molti database. |
| 87 | [Creare un nuovo pool di database flessibile con PowerShell](sql-database-elastic-pool-create-powershell.md) | Informazioni su come usare PowerShell per il Database di SQL Azure scalabilità risorse mediante la creazione di un pool di scalable database flessibile per la gestione di più database. |
| 88 | [Script di PowerShell per identificare i database adatti per un pool di database flessibile](sql-database-elastic-pool-database-assessment-powershell.md) | Un pool di database flessibile è una raccolta di risorse disponibili condivisi da un gruppo di database flessibile. Questo documento offre uno script di Powershell che consente di valutare l'idoneità dell'utilizzo di un pool di database flessibile per un gruppo di database. |
| 89 | [Monitorare e gestire un pool di database flessibile con c#](sql-database-elastic-pool-manage-csharp.md) | Usare tecniche di sviluppo database c# per gestire un pool di database flessibile Database SQL Azure. |
| 90 | [Monitorare e gestire un pool di database flessibile con il portale di Azure](sql-database-elastic-pool-manage-portal.md) | Informazioni su come usare il portale di Azure e intelligenti del Database di SQL per gestire, monitorare e un pool di database flessibile scalable per ottimizzare le prestazioni del database e gestire i costi di dimensioni a destra. |
| 91 | [Monitorare e gestire un pool di database flessibile con PowerShell](sql-database-elastic-pool-manage-powershell.md) | Informazioni su come usare PowerShell per gestire un pool di database flessibile. |
| 92 | [Monitorare e gestire un pool di database flessibile con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Consente di creare un database SQL Azure in un pool flessibile T-SQL. Oppure utilizzare Transact-SQL per spostare la datbase e disconnettersi pool. |
| 93 | [Database flessibile pool fatturazione e informazioni sui prezzi](sql-database-elastic-pool-price.md) | Informazioni sui prezzi specifiche per i pool di database flessibile. |



## <a name="elastic-query"></a>Query flessibile

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 94 | [Rapporto tra il database fuori architetture cloud (preview)](sql-database-elastic-query-getting-started.md) | come usare cross query di database database |
| 95 | [Guida introduttiva a query tra database (partizione verticale) (versione di anteprima)](sql-database-elastic-query-getting-started-vertical.md) | come usare una query di database flessibile con verticalmente suddiviso database |
| 96 | [Creazione di report in database fuori architetture cloud (preview)](sql-database-elastic-query-horizontal-partitioning.md) | come configurare la query flessibile su partizioni orizzontali |
| 97 | [Panoramica di query di Azure Database SQL database flessibile (preview)](sql-database-elastic-query-overview.md) | Panoramica della funzione query flessibile |
| 98 | [Query su database cloud con schemi diversi (preview)](sql-database-elastic-query-vertical-partitioning.md) | come configurare la query tra database su partizioni verticali |



## <a name="elastic-transaction"></a>Transazione flessibile

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 99 | [Transazioni distribuite tra database cloud](sql-database-elastic-transactions-overview.md) | Panoramica flessibile le transazioni del Database con Database SQL Azure |



## <a name="backup-and-recovery"></a>Eseguire il backup e ripristino

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 100 | [Backup del Database SQL](sql-database-automated-backups.md) | Informazioni sui Database SQL incorporata backup del database che consentono di ripristinare lo stato di nuovo un Database di SQL Azure in un punto precedente nel tempo o copiare un database in un nuovo database in un'area geografica (fino a 35 giorni). |
| 101 | [Panoramica di continuità aziendale con Database di SQL Azure](sql-database-business-continuity.md) | Informazioni su come Database SQL Azure supporta la continuità aziendale cloud e il ripristino del database e di mantenere le applicazioni cruciali cloud in esecuzione. |
| 102 | [Come ripristinare un'unica tabella da un backup del Database di SQL Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Informazioni su come ripristinare un'unica tabella di backup del Database di SQL Azure. |
| 103 | [Progettare un'applicazione di emergenza cloud usando la replica di geografico Active in Database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Informazioni su come progettare soluzioni di ripristino di emergenza cloud per pianificazione continuità aziendale usando replica geografico per il backup dei dati app con Database di SQL Azure. |
| 104 | [Ripristinare un Database SQL Azure o failover secondario](sql-database-disaster-recovery.md) | Informazioni su come ripristinare un database da un'interruzione del data center locale con Azure SQL attiva geografico-replica di Database e funzionalità di ripristino geografico. |
| 105 | [Esecuzione di drill-down ripristino di emergenza](sql-database-disaster-recovery-drills.md) | Informazioni su Guida e procedure consigliate per l'uso di Database SQL Azure per eseguire esercitazioni di ripristino di emergenza che aiuta a mantengono la missione critiche alle applicazioni aziendali flessibili di errori e interruzioni. |
| 106 | [Strategie di ripristino di emergenza per applicazioni che utilizzano Pool flessibile Database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Informazioni su come progettare una soluzione di cloud di emergenza scegliendo il criterio di failover destra. |
| 107 | [Utilizzo della classe RecoveryManager per risolvere i problemi di mappa condiviso](sql-database-elastic-database-recovery-manager.md) | Utilizzare la classe RecoveryManager per risolvere i problemi di mapping condiviso |
| 108 | [Importare un file BACPAC per creare un database SQL Azure](sql-database-import.md) | Creare un database SQL Azure importando un file BACPAC esistente. |
| 109 | [Ripristinare un Database di SQL Azure in un punto precedente nel tempo con il portale di Azure](sql-database-point-in-time-restore-portal.md) | Ripristinare un Database di SQL Azure in un punto precedente nel tempo. |
| 110 | [Ripristinare un Database di SQL Azure in un punto precedente nel tempo con PowerShell](sql-database-point-in-time-restore-powershell.md) | Ripristinare un Database di SQL Azure in un punto precedente nel tempo |
| 112 | [Ripristinare un database di SQL Azure con backup automatici](sql-database-recovery-using-backups.md) | Le informazioni in un momento ripristino, che consente di ripristinare un Database di SQL Azure un punto precedente nel tempo (fino a 35 giorni). |
| 113 | [Ripristinare un database SQL Azure eliminato tramite il portale di Azure](sql-database-restore-deleted-database-portal.md) | Ripristinare un database SQL Azure eliminato (Azure Portal). |
| 114 | [Ripristinare un Database di SQL Azure eliminati tramite PowerShell](sql-database-restore-deleted-database-powershell.md) | Ripristinare un Database di SQL Azure eliminato (PowerShell). |
| 115 | [Ripristinare un database in un punto precedente nel tempo, ripristinare un database eliminato o recuperare da un'interruzione del centro di dati](sql-database-troubleshoot-backup-and-restore.md) | Informazioni su come ripristinare un database cloud da errori e interruzioni con backup e repliche nel Database di SQL Azure. |



## <a name="migrate"></a>Eseguire la migrazione

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 116 | [Esportare un database di SQL Server in un file BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Database SQL di Microsoft Azure, la migrazione di database, esportare database, Esporta file BACPAC, sqlpackage |
| 117 | [Esportare un database di SQL Server in un file BACPAC tramite SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Database SQL di Microsoft Azure, la migrazione di database, esportare database, Esporta file BACPAC, creazione guidata applicazione esportare dati |
| 118 | [Importare al Database SQL da un file BACPAC utilizzando SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Database SQL di Microsoft Azure, la migrazione di database, importare database, importare file di BACPAC, sqlpackage |
| 119 | [Importare da BACPAC al Database SQL con SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Database SQL di Microsoft Azure, database distribuire, la migrazione di database, database di importazione, esportazione database, la migrazione guidata |
| 120 | [Eseguire la migrazione di database SQL Server al Database SQL usando distribuire Database alla creazione guidata Database di Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Database SQL di Microsoft Azure, la migrazione di database, creazione guidata Database di Microsoft Azure |
| 121 | [Eseguire la migrazione di database di SQL Server di Database di SQL Azure con replica transazione](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Database SQL di Microsoft Azure, la migrazione di database, importare database, replica transazione |
| 122 | [Determinare la compatibilità di Database SQL con SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Database SQL di Azure, la migrazione del database, la compatibilità del Database di SQL, SqlPackage |
| 123 | [Usare SQL Server Management Studio per la compatibilità di Database SQL di determinare prima della migrazione a Database SQL Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Database SQL di Azure, la migrazione del database, la compatibilità del Database di SQL, esportazione guidata di applicazione livello dati |
| 124 | [Utilizzare SQL Azure migrazione guidata per problemi di compatibilità risolvere SQL Server database prima della migrazione a Database SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Database SQL di Azure, la migrazione del database, compatibilità, migrazione guidata di SQL Azure |
| 125 | [Eseguire la migrazione di un Database SQL Server di Database SQL Azure con SQL Server Data Tools per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Database SQL di Azure, la migrazione del database, compatibilità, migrazione guidata di SQL Azure, SSDT |
| 126 | [Risolvere i problemi di compatibilità database SQL Server con SQL Server Management Studio prima della migrazione al Database SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Database SQL di Azure, la migrazione del database, compatibilità, migrazione guidata di SQL Azure |
| 127 | [Archiviare un database di SQL Azure in un file BACPAC tramite PowerShell](sql-database-export-powershell.md) | Archiviare un database di SQL Azure in un file BACPAC tramite PowerShell |
| 128 | [Importare un file BACPAC per creare un database SQL Azure tramite PowerShell](sql-database-import-powershell.md) | Importare un file BACPAC per creare un database SQL Azure tramite PowerShell |
| 129 | [Caricare i dati da file CSV in SQL Azure Data Warehouse (file flat)](sql-database-load-from-csv-with-bcp.md) | Per una dimensione di dati di piccole dimensioni, Usa bcp per importare dati nel Database di SQL Azure. |
| 130 | [Spostare i database tra server, tra le sottoscrizioni ed ed estrarre Azure](sql-database-troubleshoot-moving-data.md) | Azioni rapide per copiare, spostare, quindi eseguire la migrazione dei dati e database nel Database di SQL Azure. |



## <a name="move-data-in-and-out"></a>Spostare i dati avanti e indietro

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 131 | [Migrazione del database di SQL Server di Database SQL nel cloud](sql-database-cloud-migrate.md) | Informazioni su come circa locale SQL Server la migrazione di database al Database di SQL Azure nel cloud. Utilizzare gli strumenti di migrazione di database per verificare la compatibilità prima di migrazione del database. |
| 132 | [Copiare un Database SQL Azure](sql-database-copy.md) | Creare una copia di un database di SQL Azure |
| 133 | [Archiviare un database di SQL Azure in un file BACPAC tramite il portale di Azure](sql-database-export.md) | Archiviare un database di SQL Azure in un file BACPAC tramite il portale di Azure |



## <a name="security"></a>Sicurezza

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 134 | [Connessione al Database SQL o SQL Data Warehouse utilizzando l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) | Informazioni su come connettersi al Database SQL tramite autenticazione di Azure Active Directory. |
| 135 | [Sempre crittografati: Proteggere i dati sensibili nel Database di SQL e archiviare le chiavi di crittografia nell'archivio certificati di Windows](sql-database-always-encrypted.md) | Proteggere i dati sensibili nel database di SQL in minuti. |
| 136 | [Sempre crittografati: Proteggere i dati sensibili nel Database di SQL e archiviare le chiavi di crittografia in Azure chiave archivio](sql-database-always-encrypted-azure-key-vault.md) | Proteggere i dati sensibili nel database di SQL in minuti. |
| 137 | [Database SQL - supporto per i client e sulle modifiche apportate all'endpoint IP per il controllo](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Informazioni sui Database SQL client supporto e IP modifiche endpoint per il controllo. |
| 138 | [Configurare le regole firewall a livello di server di Database SQL Azure tramite PowerShell](sql-database-configure-firewall-settings-powershell.md) | Informazioni su come configurare il firewall per gli indirizzi IP di accedere ai database SQL Azure. |
| 139 | [Configurare le regole firewall a livello di server di Database SQL Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md) | Informazioni su come configurare il firewall per gli indirizzi IP di accedere ai database SQL Azure. |
| 140 | [Configurare le regole firewall livello di server e database di Database SQL Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md) | Informazioni su come configurare il firewall per gli indirizzi IP di accedere ai database SQL Azure. |
| 141 | [Guida introduttiva a SQL Database dinamico dati Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md) | Come iniziare a utilizzare SQL Database dinamico dati Masking nel portale di Azure |
| 142 | [Guida introduttiva a SQL Database dinamico dati Masking (Azure Portal classica)](sql-database-dynamic-data-masking-get-started-portal.md) | Come iniziare a utilizzare SQL Database dinamico dati Masking nel portale classica di Azure |
| 143 | [Configurare le regole firewall di Database SQL Azure \- Panoramica](sql-database-firewall-configure.md) | Informazioni su come configurare un firewall di database SQL con le regole del firewall di livello di server e di database per gestire l'accesso. |
| 144 | [Esercitazione di Database SQL: creare database SQL di account utente di accedere e gestire un database](sql-database-get-started-security.md) | Informazioni su come creare account utente per accedere e gestire un database. |
| 145 | [Autenticazione di Database SQL e l'autorizzazione: concedere l'accesso](sql-database-manage-logins.md) | Informazioni sulla gestione di protezione dei Database SQL, in modo specifico per la gestione di protezione del database di access e accedere tramite l'account principale a livello di server. |
| 146 | [Limitazioni e indicazioni sulla sicurezza di Database SQL azure](sql-database-security-guidelines.md) | Informazioni sulle istruzioni di Database SQL di Microsoft Azure e limitazioni relative alla sicurezza. |
| 147 | [Guida introduttiva a individuazione Database SQL](sql-database-threat-detection-get-started.md) | Come iniziare a utilizzare SQL Database individuazione nel portale di Azure |
| 148 | [Come eseguire attività amministrative comuni come la reimpostazione della password di amministratore nel Database di SQL Azure](sql-database-troubleshoot-permissions.md) | Viene descritto come eseguire attività amministrative più comuni nel Database di SQL. Ad esempio, la reimpostazione password di amministratore, concessione e rimozione di access. |



## <a name="manage-your-database"></a>Gestire il database

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 149 | [Iniziare a utilizzare il controllo dei database SQL](sql-database-auditing-get-started.md) | Iniziare a utilizzare il controllo dei database SQL |
| 150 | [Configurare una regola firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md) | Informazioni su come configurare il firewall per gli indirizzi IP che accedono a server SQL Azure. |
| 151 | [Copiare un Database di SQL Azure tramite il portale di Azure](sql-database-copy-portal.md) | Creare una copia di un database di SQL Azure |
| 152 | [Gestione dei database di SQL Azure utilizzando l'automazione di Azure](sql-database-manage-automation.md) | Informazioni sull'utilizzo del servizio di automazione di Azure per gestire i database di SQL Azure in scala. |
| 153 | [Panoramica: strumenti di Database SQL](sql-database-manage-overview.md) | Confronta strumenti e opzioni per la gestione di Database SQL Azure |
| 154 | [Database di SQL Azure monitoraggio usando le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md) | Informazioni su come rilevare e diagnosticare i problemi di prestazioni utilizzando le visualizzazioni di gestione dinamica per monitorare Database SQL di Microsoft Azure. |
| 155 | [La protezione del Database SQL](sql-database-security.md) | Informazioni sulla sicurezza di Database SQL Azure e SQL Server, incluse le differenze tra nel cloud e SQL Server locale quando si tratta di autenticazione, autorizzazione, la protezione della connessione, sulla crittografia e conformità. |



## <a name="extended-events"></a>Eventi "Extended"

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 156 | [Codice di destinazione File eventi per gli eventi "Extended" nel Database di SQL](sql-database-xevent-code-event-file.md) | Consente di PowerShell e Transact-SQL per un esempio di codice in due fasi che illustra la destinazione del File di evento in un evento "Extended" nel Database di SQL Azure. Spazio di archiviazione Azure è un requisito di questo scenario. |
| 157 | [Chiama il codice di destinazione di Buffer per eventi "Extended" nel Database di SQL](sql-database-xevent-code-ring-buffer.md) | Fornisce un esempio di codice Transact-SQL produttrice semplice e veloce tramite la destinazione di Buffer circolare, nel Database di SQL Azure. |
| 158 | [Eventi estesi in Database SQL](sql-database-xevent-db-diff-from-svr.md) | Vengono illustrati gli eventi "Extended" (XEvents) nel Database di SQL Azure e come le sessioni di evento sono leggermente diverse da sessioni di evento in Microsoft SQL Server. |



## <a name="geo-replication"></a>Replica geografico

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 159 | [Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con il portale di Azure](sql-database-geo-replication-failover-portal.md) | Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure tramite il portale di Azure |
| 160 | [Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) | Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con PowerShell |
| 161 | [Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con Transact-SQL |
| 162 | [Panoramica: SQL attiva geografico-replica di Database](sql-database-geo-replication-overview.md) | La replica di geografico Active consente di installare 4 repliche del database in uno dei Data Center di Azure. |
| 163 | [Configurare la replica geografico per Database di SQL Azure con il portale di Azure](sql-database-geo-replication-portal.md) | Configurare la replica geografico per Database di SQL Azure tramite il portale di Azure |
| 164 | [Configurare la replica geografico per Database SQL Azure con PowerShell](sql-database-geo-replication-powershell.md) | Configurare la replica di geografico Active per Database di SQL Azure con PowerShell |
| 165 | [Come gestire la protezione del Database di SQL Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) | In questo argomento viene considerazioni sulla protezione per la gestione della sicurezza dopo il ripristino del database o caso di errore. |
| 166 | [Configurare la replica geografico per Database SQL Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurare la replica geografico per Database di SQL Azure con Transact-SQL |
| 167 | [Geografico-Ripristina un Database di SQL Azure da una copia di backup ridondanti geografico tramite il portale di Azure](sql-database-geo-restore-portal.md) | Geografico-Ripristina un Database di SQL Azure da una copia di backup ridondanti geografico (Azure Portal). |
| 168 | [Ripristinare un Database di SQL Azure da un backup ridondanti geografico tramite PowerShell](sql-database-geo-restore-powershell.md) | Ripristinare un Database di SQL Azure in un nuovo server da un backup geografico ridondanti |



## <a name="upgrade"></a>Eseguire l'aggiornamento

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 169 | [Miglioramento delle prestazioni di query con compatibilità 130 livello nel Database di SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Passaggi e strumenti per determinare il livello di compatibilità più adatto per il database nel Database di SQL Azure o Microsoft SQL Server |
| 170 | [Gestire gli aggiornamenti in sequenza cloud delle applicazioni che utilizzano SQL attiva geografico-replica di Database](sql-database-manage-application-rolling-upgrade.md) | Informazioni su come utilizzare replica di Database SQL Azure geografico per supportare aggiornamenti dell'applicazione cloud. |
| 171 | [Eseguire l'aggiornamento a V12 di Database SQL Azure tramite il portale di Azure](sql-database-upgrade-server-portal.md) | Spiega come eseguire l'aggiornamento a V12 di Database SQL Azure compresa la procedura aggiornare i database Web e Business e come eseguire l'aggiornamento di un server V11 la migrazione dei database direttamente in un pool di database flessibile tramite il portale di Azure. |
| 172 | [Eseguire l'aggiornamento a V12 di Database SQL Azure con PowerShell](sql-database-upgrade-server-powershell.md) | Spiega come eseguire l'aggiornamento a V12 di Database SQL Azure compresa la procedura aggiornare i database Web e Business e come eseguire l'aggiornamento di un server V11 la migrazione dei database direttamente in un pool di database flessibile tramite PowerShell. |
| 173 | [Pianificare e preparare l'aggiornamento a SQL Database V12](sql-database-v12-plan-prepare-upgrade.md) | Descrive le operazioni preparatorie e limitazioni per l'aggiornamento alla versione V12 del Database di SQL Azure. |
| 174 | [Quali sono le novità in SQL Database V12](sql-database-v12-whats-new.md) | Descrive il motivo per cui usufruire sistemi aziendali che utilizzano il Database di SQL Azure nel cloud, eseguire l'aggiornamento alla versione V12. |
| 175 | [Domande frequenti su tramonto Web e Business Edition](sql-database-web-business-sunset-faq.md) | Scoprire quando i database Web di SQL Azure e Business ritiro e informazioni sulle caratteristiche e funzionalità dei livelli di servizio nuovo. |



## <a name="tools"></a>Strumenti

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 176 | [Gestire il Database SQL Azure con PowerShell](sql-database-command-line-tools.md) | Gestione di Database SQL Azure con PowerShell. |
| 177 | [Esercitazione di Database SQL: connettere Excel a un database SQL Azure e creare un report](sql-database-connect-excel.md) | Informazioni su come connettersi database di SQL Azure nel cloud di Microsoft Excel. Importare dati in Excel per reporting ed esplorazione dei dati. |
| 178 | [Creare un database SQL ed eseguire comuni attività di configurazione di database con i cmdlet di PowerShell](sql-database-get-started-powershell.md) | Vedere creare un database SQL con PowerShell. Attività comuni di configurazione del database può essere gestita tramite i cmdlet di PowerShell. |
| 179 | [Guida introduttiva di sincronizzazione di dati SQL Azure (Preview)](sql-database-get-started-sql-data-sync.md) | In questa esercitazione consente di iniziare a utilizzare la sincronizzazione di dati di SQL Azure (Preview). |
| 180 | [Gestione di Database di SQL Azure con SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Informazioni su come usare SQL Server Management Studio per gestire i Database di SQL Server e database. |
| 181 | [Gestione dei database di SQL Azure tramite il portale di Azure](sql-database-manage-portal.md) | Informazioni su come utilizzare il portale di Azure per gestire un database relazionale nel cloud tramite il portale di Azure. |
| 182 | [Modificare il livello e prestazioni livello di servizio (prezzi livello) di un database SQL con PowerShell](sql-database-scale-up-powershell.md) | Modificare il livello di servizio e livello di prestazioni di un database di SQL Azure viene illustrato come ridimensionare il database SQL verso l'alto o verso il basso con PowerShell. Modificare il livello di prezzo di un database di SQL Azure con PowerShell. |
| 183 | [In SQL Server Management Studio RemoteApp di Azure consente di connettersi al Database SQL](sql-database-ssms-remoteapp.md) | Utilizzare questa esercitazione per informazioni su come usare SQL Server Management Studio in Azure RemoteApp per protezione e le prestazioni quando ci si connette al Database SQL |



## <a name="reference"></a>Guida di riferimento

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 184 | [Raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md) | Elenca il numero di versione minima per ogni driver programmi client consente di connettersi al Database SQL Azure o a Microsoft SQL Server. Per informazioni sulle versioni sui driver rilasciati alla community anziché Microsoft viene fornito un collegamento. |
| 185 | [Limiti delle risorse di Database SQL Azure](sql-database-resource-limits.md) | Questa pagina illustra alcuni limiti delle risorse comuni per il Database di SQL Azure. |
| 186 | [Differenze Transact-SQL Database SQL Azure](sql-database-transact-sql-information.md) | Istruzioni Transact-SQL minore completamente supportate in Database SQL Azure |



## <a name="miscellaneous"></a>Varie

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 187 | [Copiare un database di SQL Azure con PowerShell](sql-database-copy-powershell.md) | Creare una copia di un database di SQL Azure con PowerShell |
| 188 | [Copiare un database di SQL Azure con Transact-SQL](sql-database-copy-transact-sql.md) | Creare una copia di un database di SQL Azure con Transact-SQL |
| 189 | [Linee guida e le limitazioni generale di Database SQL Azure](sql-database-general-limitations.md) | Questa pagina illustra alcune limitazioni generali per il Database di SQL Azure, nonché le aree di supporto e interoperabilità. |
| 190 | [Consigli livello prezzi Database SQL](sql-database-service-tier-advisor.md) | Quando si modificano i prezzi livelli nel portale di Azure, prezzi consigli livello vengono fornito consigliare il livello quello più adatto per l'esecuzione di carico di lavoro di esistente Azure Database SQL. Livelli prezzi descrivano il livello di livello e le prestazioni del servizio di un database SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Funzionalità aggiuntive

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Articoli correlati da altri servizi di Azure

- [Eseguire il backup l'applicazione Servizi di App di Windows Azure in Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Strumenti della documentazione

- [Aggiornamenti annunciati per Database di SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Ricerca di tutti i tipi di documentazione di Microsoft Azure, con i filtri](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Elementi grafici percorso formativo

- [Elementi grafici di percorso di apprendimento per tutti i servizi di Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Percorso formativo: Informazioni su Database SQL Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Percorso di apprendimento: Scalabilità flessibile Database SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


