<properties
   pageTitle="Esplorare le esercitazioni di Database SQL Azure | Microsoft Azure"
   description="Informazioni sulle funzionalità e le funzioni di Database SQL"
   keywords=""
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
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>Esplorare le esercitazioni di Database SQL Azure

I collegamenti seguenti consentono di per una panoramica di ogni area di funzionalità nell'elenco e semplice esercitazione passaggio dall'inizio per ogni area. Per ambito di soluzioni rapide che illustrano l'utilizzo del Database di SQL in una soluzione completa basata su scenari reali, vedere [Azure SQL Database soluzioni rapide](sql-database-solution-quick-starts.md).

## <a name="using-sql-server-management-studio"></a>Con SQL Server Management Studio

Nelle esercitazioni seguenti verranno fornite informazioni sull'uso di SQL Server Management Studio per amministrare ed eseguire query di Database SQL Azure.


> [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Esercitazione  | Descrizione  |
|---|---|---|
| [Connettersi al Database di SQL Azure usando un account di accesso principale a livello di server](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| In questa esercitazione si imparerà a connettersi al Database di SQL Azure con un account di accesso a livello di server principale.|
| [Connettersi al Database SQL Azure come un utente](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con un account utente a livello di database.|
||||

## <a name="elastic-pools"></a>Pool flessibile

Nelle esercitazioni seguenti verranno fornite informazioni sull'uso [pool flessibile](sql-database-elastic-pool.md) per gestire gli obiettivi delle prestazioni per più database contenenti ampiamente imprevisti e diversi modelli di utilizzo.

| Esercitazione  | Descrizione  |
|---|---|---|
| [Creare un pool flessibile](sql-database-elastic-pool-create-portal.md) | In questa esercitazione si imparerà a creare un pool di scalable dei database di SQL Azure. |
| [Monitor di un database di flessibile](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | In questa esercitazione si imparerà a monitorare un singolo database flessibile per potenziali problemi. |
| [Aggiungere un avviso per una risorsa di pool](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | In questa esercitazione si imparerà aggiungere regole per le risorse che inviano la posta elettronica a utenti o stringhe degli avvisi per i punti finali URL quando la risorsa raggiunge una soglia di utilizzo che ha configurato. |
| [Spostare un database in un pool di flessibile](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | In questa esercitazione si imparerà a spostare un database in un pool flessibile. |
| [Spostare un database da un pool flessibile](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | In questa esercitazione si imparerà a spostare un database da un pool flessibile. |
| [Modificare le impostazioni di prestazioni di un pool](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | In questa esercitazione si imparerà a modificare i limiti delle prestazioni e lo spazio di archiviazione per un pool. |
||||

## <a name="elastic-database-jobs"></a>Processi di database flessibile

Nelle esercitazioni seguenti verranno fornite informazioni sull'uso di [processi di database flessibile](sql-database-elastic-jobs-overview.md).

| Esercitazione  | Descrizione  |
|---|---|---|
| [Guida introduttiva a strumenti Database flessibile](sql-database-elastic-scale-get-started.md) | In questa esercitazione si imparerà a utilizzare le funzionalità di strumenti di database flessibile usando una semplice applicazione sharded. |
| [Guida introduttiva a processi flessibile Database SQL Azure](sql-database-elastic-jobs-getting-started.md)  | In questa esercitazione si apprende come a come creare e gestire processi gestire un gruppo di database correlati.  | 
||||

## <a name="elastic-queries"></a>Query flessibile

Nelle esercitazioni seguenti verranno fornite informazioni sull'esecuzione di [query flessibile](sql-database-elastic-query-overview.md). 

| Esercitazione  | Descrizione  |
|---|---|---|
| [Invio di query in un database (sharded) partizionato orizzontali)](sql-database-elastic-query-getting-started.md) | In questa esercitazione si imparerà a creare report da tutti i database in un database (sharded) partizionato orizzontali tramite [query flessibile](sql-database-elastic-query-overview.md) |
| [Invio di query in un database partizionato verticalmente)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | In questa esercitazione si imparerà a creare report da tutti i database in un verticalmente database usando [una query flessibile](sql-database-elastic-query-overview.md) |
| [Eseguire la migrazione di un database esistente da fuori scala](sql-database-elastic-convert-to-use-elastic-tools.md)| In questa esercitazione si apprende orizzontalmente le dimensioni del database di SQL Azure (condiviso). |
||||

## <a name="performance-optimization"></a>Ottimizzazione delle prestazioni

Nelle esercitazioni seguenti verranno fornite informazioni sull'ottimizzazione delle [prestazioni dei singoli database](sql-database-performance-guidance.md). Per ottimizzare le prestazioni di più database, vedere [pool flessibile](#elastic-pools).

| Esercitazione  | Descrizione  |
|---|---|---|
| [Modificare il livello di livello e le prestazioni di servizio del database](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | In questa esercitazione si imparerà a scalare o modificare la scala verso il basso le prestazioni di un database di SQL Azure con livelli di servizio. |
| [Comprendere le prestazioni di SQL Database Advisor Query](sql-database-performance.md#performance-overview) | In questa esercitazione si imparerà aprire e utilizzare SQL Database Advisor Query prestazioni comprensione.|
| [Suggerimenti relativi alle prestazioni di preparazione di Database SQL](sql-database-advisor.md#viewing-recommendations) | In questa esercitazione si imparerà a visualizzare e applicare suggerimenti relativi alle prestazioni di preparazione di Database SQL. |
| [Esaminare CPU superiore utilizzo di query](sql-database-query-performance.md#review-top-cpu-consuming-queries)| In questa esercitazione si imparerà usare SQL Database Advisor Query prestazioni analisi per esaminare CPU superiore utilizzo di query.|
| [Visualizzazione dei dettagli singola query](sql-database-query-performance.md#viewing-individual-query-details)| In questa esercitazione si imparerà usare SQL Database Advisor Query prestazioni informazioni per visualizzare i dettagli delle prestazioni singola query.|
||||

## <a name="sql-database-migration-and-archive"></a>Archiviazione e la migrazione di Database SQL 

Nelle esercitazioni seguenti verranno fornite informazioni sulla [migrazione di un database di SQL Server esistente da Database SQL Azure](sql-database-cloud-migrate.md).

| Esercitazione  | Descrizione  |
|---|---|---|
| [Rilevare i problemi di compatibilità con SQL Server Data Tools per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | In questa esercitazione si imparerà usare SQL Server Data Tools per Visual Studio per determinare la compatibilità di Database SQL Azure. |
| [Risoluzione dei problemi di compatibilità con SQL Server Data Tools per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | In questa esercitazione si imparerà a utilizzare SQL Server Data Tools per Visual Studio per risolvere i problemi di compatibilità di Database SQL Azure. |
| [Determinare la compatibilità di Database SQL con SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | In questa esercitazione si imparerà a utilizzare l'utilità della riga di comando SQLPackage.exe per determinare la compatibilità di Database SQL Azure.|
| [Determinare la compatibilità di Database SQL con SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |In questa esercitazione si imparerà a utilizzare SQL Server Management Studio per determinare la compatibilità di Database SQL Azure.|
| [Eseguire la migrazione di database SQL Server Database SQL di usare distribuire Database alla creazione guidata Database di Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | In questa esercitazione si imparerà a eseguire la migrazione di un database di SQL Server compatibile al Database di SQL Azure usando la distribuzione per il Database di creazione guidata Database di Microsoft Azure in SQL Server Management Studio.
| [Esportare un database SQL Server in un file BACPAC con SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | In questa esercitazione si imparerà esportare un database di SQL Server compatibile in un file BACPAC usando l'esportazione guidata applicazione di livello dati in SQL Server Management Studio.|
| [Esportare un database di SQL Server in un file BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | In questa esercitazione si imparerà esportare un database di SQL Server compatibile in un file BACPAC utilizzare l'utilità SQLPackage.exe.|
| [Importare un file BACPAC nel Database di SQL Azure con SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | In questa esercitazione si imparerà a importare un database nel Database di SQL Azure da un file BACPAC tramite esportazione guidata applicazione di livello dati di SQL Server Management Studio. |
| [Importare un file BACPAC nel Database di SQL Azure con SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | In questa esercitazione si imparerà a importare un database nel Database di SQL Azure da un file BACPAC usando l'utilità di SQLPackage. |
| [Importare un file BACPAC nel Database di SQL Azure tramite il portale di Azure](sql-database-import.md) | In questa esercitazione si imparerà importare un database nel Database di SQL Azure da un file BACPAC memorizzati in un archivio blob Azure tramite il portale di Azure.|
| [Importare un file BACPAC nel Database di SQL Azure con PowerShell](sql-database-import-powershell.md) | In questa esercitazione si imparerà importare un database nel Database di SQL Azure da un file BACPAC tramite PowerShell.|
| [Archiviare un database di SQL Azure tramite il portale di Azure](sql-database-export.md#export-your-database) | In questa esercitazione si imparerà archiviare un database di SQL Azure in un file BACPAC tramite il portale di Azure. |
| [Archiviare un database di SQL Azure con PowerShell](sql-database-export-powershell.md) | In questa esercitazione si imparerà archiviare un database di SQL Azure in un file BACPAC tramite PowerShell. |
| [Copiare un database di SQL Azure tramite il portale di Azure](sql-database-copy.md#copy-your-sql-database) | In questa esercitazione si imparerà a copiare un database di SQL Azure tramite il portale di Azure. |
| [Copiare un database di SQL Azure con PowerShell](sql-database-copy-powershell#copy-your-sql-database) | In questa esercitazione si imparerà a copiare un database di SQL Azure con PowerShell. |
| [Copiare un database di SQL Azure con Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | In questa esercitazione si imparerà a copiare un database di SQL Azure con Transact-SQL. |
||||

##<a name="develop"></a>Sviluppo

Nelle esercitazioni seguenti verranno fornite informazioni sulla [Sviluppo di Database SQL](sql-database-develop-overview.md) e l'utilizzo di [librerie di connettività](sql-database-libraries.md).

| Esercitazione  | Descrizione  |
|---|---|---|
| [Connettersi al Database SQL utilizzando .NET (c#)](sql-database-develop-dotnet-simple.md) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con c#. |
| [Connettersi al Database SQL utilizzando Java](sql-database-develop-java-simple.md) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con linguaggio. |
| [Connettersi al Database SQL tramite Node. js](sql-database-develop-nodejs-simple.md) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con Node. |
| [Connettersi al Database SQL tramite PHP](sql-database-develop-php-simple.md) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con PHP. |
| [Connettersi al Database SQL tramite Python](sql-database-develop-python-simple.md) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con Python. |
| [Connettersi al Database SQL tramite trascrizione](sql-database-develop-ruby-simple.md) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con trascrizione. |
||||
 
## <a name="database-access"></a>Accesso al database

Nelle esercitazioni seguenti verranno fornite informazioni sulla [creazione e gestione di utenti e gli account di accesso](sql-database-manage-logins.md).

| Esercitazione  | Descrizione  |
|---|---|---|
| [Creare una regola firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)  | In questa esercitazione si imparerà a configurare un firewall di livello di server di Database SQL tramite il portale di Azure.  |
| [Creare una regola firewall a livello di database in Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | In questa esercitazione si imparerà creare una regola a livello di database con Transact-SQL.|
| [Gestire le regole firewall di livello di server tramite Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | In questa esercitazione si imparerà a gestire un firewall di livello di server di Database SQL Azure con Transact-SQL.|
| [Gestire le regole firewall a livello di server con PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | In questa esercitazione si imparerà a gestire un firewall di livello di server di Database SQL Azure tramite PowerShell.|
| [Gestire le regole firewall a livello di server tramite l'API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | In questa esercitazione si imparerà a gestire un firewall di livello di server Database SQL Azure tramite l'API REIMPOSTA.|
| [Connettersi al Database di SQL Azure usando un account di accesso principale a livello di server](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| In questa esercitazione si imparerà a connettersi al Database di SQL Azure con un account di accesso a livello di server principale.|
| [Concessione dell'accesso al database a un account di accesso] (sql-database-manage-logins.md#granting-database-access-to-a-login() | In questa esercitazione si imparerà a concedere l'accesso al database a un account di accesso a livello di server.|
| [Crea nuovo utente di database con SQL Server Management Studio](sql-database-get-started-security.md#create-new-database-user-using-ssms) | In questa esercitazione si imparerà a creare un nuovo utente di database in un database esistente con SQL Server Management Studio.|
| [Nuovo database db_owner concedere autorizzazioni agli utenti](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | In questa esercitazione si imparerà a un database esistente db_owner concedere autorizzazioni agli utenti.|
| [Connettersi al Database SQL Azure come un utente](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | In questa esercitazione si imparerà a connettersi a un database di SQL Azure con un account utente a livello di database.|
||||


## <a name="data-security"></a>Protezione dei dati

Nelle esercitazioni seguenti verranno fornite informazioni sulla [protezione dei dati di Database SQL Azure](sql-database-security.md). 

| Esercitazione  | Descrizione  |
|---|---|---|
| [Attivare il rilevamento di rischio per il database tramite il portale di Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | In questa esercitazione si imparerà a configurare individuazione nel portale di Azure per il database.|
| [Proteggere i dati sensibili abbonamento sempre crittografato](sql-database-always-encrypted-azure-key-vault.md) | In questa esercitazione si utilizzerà la procedura guidata sempre crittografati per proteggere i dati riservati in un database SQL Azure.|
| [Proteggere i dati senstive Usa la crittografia dati trasparente](https://msdn.microsoft.com/library/dn948096.aspx)| In questa esercitazione si imparerà a utilizzare la crittografia trasparente per proteggere i dati senstive.|
| [Crittografare una colonna di dati](https://msdn.microsoft.com/library/ms179331.aspx)| In questa esercitazione si imparerà a crittografare una colonna di dati mediante Transact-SQL.|
| [Configurare la definizione della maschera dati dinamici](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | In questa esercitazione si imparerà a configurare la definizione della maschera dati dinamici per il database di SQL Azure. |
||||

## <a name="business-continuity-and-query-scale-out"></a>Continuità aziendale e scalabilità Query

Nelle esercitazioni seguenti verranno informazioni sull'uso [geografico Ripristina e replica di geografico Active](sql-database-business-continuity.md) a reccover da errori, per la continuità aziendale e per la query scalabilità.

| Esercitazione  | Descrizione  |
|---|---|---|
| [Ripristinare un Database di SQL Azure in un punto precedente nel tempo con il portale di Azure](sql-database-point-in-time-restore-portal.md)| In questa esercitazione si imparerà a ripristinare il database in un momento precedente nel tempo tramite il portale di Azure.|
| [Ripristinare un Database di SQL Azure in un punto precedente nel tempo con PowerShell](sql-database-point-in-time-restore-powershell.md) | In questa esercitazione si imparerà a ripristinare il database in un momento precedente nel tempo tramite PowerShell|
| [Ripristinare un database SQL Azure eliminato tramite il portale di Azure](sql-database-restore-deleted-database-portal.md) | In questa esercitazione si imparerà a ripristinare un database eliminato tramite il portale di Azure.|
| [Ripristinare un database SQL Azure eliminato utilizzando di PowerShell](sql-database-restore-deleted-database-powershell.md) | In questa esercitazione si imparerà a ripristinare un database eliminato tramite PowerShell.|
| [Configurare la replica geografico per Database di SQL Azure tramite il portale di Azure](sql-database-geo-replication-portal.md)| In questa esercitazione si imparerà a configurare Active replica geografico tramite il portale di Azure.|
| [Configurare la replica geografico per Database di SQL Azure con PowerShell](sql-database-geo-replication-powershell.md)| In questa esercitazione si imparerà a configurare la replica di geografico Active tramite PowerShell.|
| [Configurare la replica geografico per Database di SQL Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md)| In questa esercitazione si imparerà a configurare la replica di geografico attivo con Transact-SQL.|
| [Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure tramite il portale di Azure](sql-database-geo-replication-failover-portal.md) | In questa esercitazione viene illustrato come il controllo a una replica secondario replicato geografico tramite il portale di Azure.|
| [Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) | In questa esercitazione viene illustrato come il controllo a una replica secondario replicato geografico tramite PowerShell.|
| [Avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | In questa esercitazione viene illustrato come il controllo a una replica di secondario replicato geografico tramite Transact-SQL.|
||||

## <a name="data-sync"></a>Sincronizzazione dei dati

In questa esercitazione verranno fornite informazioni sulla [Sincronizzazione dei dati](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Esercitazione  | Descrizione  |
|---|---|---| 
| [Guida introduttiva di sincronizzazione di dati SQL Azure (Preview)](sql-database-get-started-sql-data-sync.md)  | In questa esercitazione si concetti di sincronizzazione di dati SQL Azure tramite il portale di classica Azure. |
||||

## <a name="next-steps"></a>Passaggi successivi

[Esplorare avvio rapido soluzione di Database SQL Azure](sql-database-solution-quick-starts.md)
