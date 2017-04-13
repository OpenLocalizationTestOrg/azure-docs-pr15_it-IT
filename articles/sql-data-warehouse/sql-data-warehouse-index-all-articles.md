<properties
    pageTitle="Tutti gli argomenti per il servizio SQL Data Warehouse | Microsoft Azure"
    description="Tabella di tutti gli argomenti per il servizio di Azure denominata SQL Data Warehouse presenti in http://azure.microsoft.com/documentation/articles/, titolo e descrizione."
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Tutti gli argomenti per servizio Warehouse dati di SQL Azure

In questo argomento sono elencati tutti gli argomenti che si applicano direttamente al servizio **SQL Data Warehouse** di Azure. È possibile cercare in questa pagina Web per le parole chiave mediante **Ctrl + F**per trovare gli argomenti di interesse corrente.




## <a name="new"></a>Nuovo

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 1 | [Backup SQL Data Warehouse](sql-data-warehouse-backups.md) | Informazioni sui backup di database incorporati SQL Data Warehouse che consentono di ripristinare un Data Warehouse SQL Azure in un punto di ripristino o un'area geografica diversa. |


## <a name="updated-articles-sql-data-warehouse"></a>Articoli aggiornati, SQL Data Warehouse

In questa sezione sono elencati gli articoli che sono stati aggiornati di recente, nel punto in cui l'aggiornamento è stato grande o significativa. Per ogni articolo aggiornato, viene visualizzato un frammento approssimativo del testo delle vendite promozionali aggiunte. Gli articoli sono stati aggiornati nell'intervallo di date del **2016-08-22** - **2016-10-05**.

| &nbsp; | Articolo | Testo aggiornato, frammento di codice | Aggiornamento |
| --: | :-- | :-- | :-- |
| 2 | [Caricare dati da archiviazione blob Azure in SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-Per tenere traccia dei file e byte r.command selezionare, s.request_id, r.status, conteggio (distinct input_name) come nbr_files, somma (s.bytes_processed) / 1024/1024 come gb_processed da sys.dm_pdw_exec_requests r inner join sys.dm_pdw_dms_external_work s su r.request_id = s.request_id dove r. etichetta = ' CTAS: carico compagnia. DimProduct ' OR r. etichetta = ' CTAS: carico compagnia. GROUP BY r.command degli FactOnlineSales, s.request_id, r.status ORDER BY nbr_files desc, desc gb_processed;  | 2016-09-07 |
| 3 | [Ripristino di SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) | **È possibile ripristinare un in pausa data warehouse?** Per ripristinare un data warehouse in pausa, è necessario innanzitutto riportare in linea. Una volta torna online data warehouse, si dispone di sette giorni dei punti di ripristino tra cui scegliere. **Ripristinare un'area geografico ridondanti** Se si usa lo spazio di archiviazione geografico ridondanti, è possibile ripristinare data warehouse al centro dati accoppiate in un'area geografica diversa. Data warehouse verrà ripristinati dall'ultimo giorno backup. **Ripristinare un indicatore cronologico** È possibile ripristinare un database in un punto qualsiasi ripristino negli ultimi sette giorni. Snapshot avviare ogni quattro a 8 ore e sono disponibili per sette giorni. Quando uno snapshot è più di sette giorni prima della scadenza e il punto di ripristino non è più disponibile. **Ripristinare i costi** Le spese di spazio di archiviazione per l'archivio dati ripristinato sono fatturata alla tariffa lo spazio di archiviazione di Azure Premium. Se si sospende un ripristinato data warehouse, addebitate per lo spazio di archiviazione alla tariffa lo spazio di archiviazione di Azure Premium. Il vantaggio di sospensione non è che si carica | 2016-09-29 |





## <a name="get-started"></a>Guida introduttiva

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 4 | [Autenticazione di SQL Azure Data warehouse](sql-data-warehouse-authentication.md) | Azure Active Directory (AAD) e SQL Server l'autenticazione nell'archivio di dati di SQL Azure. |
| 5 | [Procedure consigliate per la Data Warehouse di SQL Azure](sql-data-warehouse-best-practices.md) | Suggerimenti e procedure consigliate, che è necessario conoscere come si sviluppano soluzioni per archivio di dati di SQL Azure. Queste considerazioni consentono di esito positivo. |
| 6 | [Driver di SQL Azure Data Warehouse](sql-data-warehouse-connection-strings.md) | Stringhe di connessione e driver per SQL Data Warehouse |
| 7 | [Connettersi a SQL Azure Data Warehouse](sql-data-warehouse-connect-overview.md) | Come trovare il server stringa di connessione e nome per l'archivio di dati di SQL Azure |
| 8 | [Analizzare i dati con apprendimento Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Utilizzare Azure Machine formazione per creare un modello in base a dati archiviati in Data Warehouse di SQL Azure previsione di apprendimento. |
| 9 | [Query SQL Azure Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | Invio di query Warehouse di dati di SQL Azure con sqlcmd utilità della riga di comando. |
| 10 | [Creare un database SQL Data Warehouse utilizzando Transact-SQL (t)](sql-data-warehouse-get-started-create-database-tsql.md) | Informazioni su come creare un Data Warehouse SQL Azure con TSQL |
| 11 | [Come creare un ticket di supporto per SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) | Come creare un ticket di supporto in Warehouse di dati di SQL Azure. |
| 12 | [Caricare i dati con dati di Azure Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Informazioni su come caricare i dati con Azure Data Factory |
| 13 | [Caricare i dati con PolyBase SQL Data warehouse](sql-data-warehouse-get-started-load-with-polybase.md) | Informazioni su PolyBase e come usarlo per data warehouse scenari. |
| 14 | [Creare un Data Warehouse SQL Azure](sql-data-warehouse-get-started-provision.md) | Informazioni su come creare un Data Warehouse SQL Azure nel portale di Azure |
| 15 | [Creare SQL Data Warehouse tramite PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Creare SQL Data Warehouse tramite PowerShell |
| 16 | [Visualizzare i dati con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualizzare i dati di SQL Data Warehouse con Power BI |
| 17 | [Query SQL Azure Data Warehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Query SQL Data Warehouse con Visual Studio. |



## <a name="develop"></a>Sviluppare

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 18 | [Ottimizzare le transazioni per SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) | Procedure consigliate su come scrivere aggiornamenti transazioni efficienti warehouse dati di SQL Azure |
| 19 | [Gestione della concorrenza e il carico di lavoro in SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) | Comprendere la gestione della concorrenza e carico di lavoro in Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 20 | [Creare una tabella come selezionare (CTAS) SQL Data warehouse](sql-data-warehouse-develop-ctas.md) | Suggerimenti per la codifica con la tabella crea come dell'istruzione select (CTAS) Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 21 | [SQL dinamico SQL Data warehouse](sql-data-warehouse-develop-dynamic-sql.md) | Suggerimenti per l'utilizzo dinamico SQL Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 22 | [Raggruppare le opzioni in SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) | Suggerimenti per l'implementazione gruppo dalle opzioni di Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 23 | [Utilizzare le etichette per le query strumento SQL Data warehouse](sql-data-warehouse-develop-label.md) | Suggerimenti per l'uso di etichette alle query strumento Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 24 | [Cicli SQL Data warehouse](sql-data-warehouse-develop-loops.md) | Suggerimenti per la sostituzione cursori Azure SQL Data warehouse per lo sviluppo di soluzioni e cicli Transact-SQL. |
| 25 | [Stored procedure SQL Data warehouse](sql-data-warehouse-develop-stored-procedures.md) | Suggerimenti per l'implementazione stored procedure di Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 26 | [Transazioni SQL Data warehouse](sql-data-warehouse-develop-transactions.md) | Suggerimenti per l'implementazione delle transazioni Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 27 | [Schemi definiti dall'utente in SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) | Suggerimenti per l'uso di schemi Transact-SQL Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 28 | [Assegnare le variabili di SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) | Suggerimenti per l'assegnazione di variabili Transact-SQL Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 29 | [Visualizzazioni SQL Data warehouse](sql-data-warehouse-develop-views.md) | Suggerimenti per l'utilizzo di visualizzazioni Transact-SQL Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 30 | [Scelte relative alla progettazione e tecniche per SQL Data Warehouse](sql-data-warehouse-overview-develop.md) | Concetti di sviluppo, decisioni relative alla progettazione, suggerimenti e tecniche per SQL Data Warehouse. |



## <a name="manage"></a>Gestire

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 31 | [Gestire le capacità di calcolo in SQL Azure Data Warehouse (panoramica)](sql-data-warehouse-manage-compute-overview.md) | Scala di prestazioni le funzionalità di Warehouse di dati di SQL Azure. Scalabilità regolando DWUs o interrompere e riprendere risorse informatiche per risparmiare sui costi. |
| 32 | [Gestire le capacità di calcolo in SQL Azure Data Warehouse (portal Azure)](sql-data-warehouse-manage-compute-portal.md) | Azure attività portale per la gestione calcolare power. Risorse di elaborazione scala regolando DWUs. In alternativa, sospendere e riprendere calcolare le risorse per risparmiare sui costi. |
| 33 | [Gestire le capacità di calcolo in SQL Azure Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | Attività di PowerShell per gestire calcolare power. Risorse di elaborazione scala regolando DWUs. In alternativa, sospendere e riprendere calcolare le risorse per risparmiare sui costi. |
| 34 | [Gestire le capacità di calcolo in Azure SQL dati Warehouse (REST)](sql-data-warehouse-manage-compute-rest-api.md) | Attività di PowerShell per gestire calcolare power. Risorse di elaborazione scala regolando DWUs. In alternativa, sospendere e riprendere calcolare le risorse per risparmiare sui costi. |
| 35 | [Gestire le capacità di calcolo in SQL Azure Data Warehouse (Transact-SQL)](sql-data-warehouse-manage-compute-tsql.md) | Attività di Transact-SQL (Transact-SQL) per le prestazioni di scalabilità regolando DWUs. Salvare i costi ridimensionando durante non periodi. |
| 36 | [Monitorare il carico di lavoro utilizzando viste](sql-data-warehouse-manage-monitor.md) | Informazioni su come controllare il carico di lavoro utilizzando viste. |
| 37 | [Gestire i database in Data Warehouse di SQL Azure](sql-data-warehouse-overview-manage.md) | Panoramica della gestione dei database di SQL Data Warehouse. Include gli strumenti di gestione DWUs e prestazioni ottimali scalabilità, risoluzione dei problemi relativi alle prestazioni delle query, la definizione di criteri di sicurezza efficace e si ripristina un database da danneggiamento dei dati o da un'interruzione internazionali. |
| 38 | [Monitorare le query degli utenti in Warehouse di dati di SQL Azure](sql-data-warehouse-overview-manage-user-queries.md) | Panoramica delle considerazioni, procedure consigliate e attività per il monitoraggio delle query degli utenti in Warehouse di dati di SQL Azure |
| 39 | [Ripristino di SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md) | Panoramica delle opzioni di ripristino del database per ripristinare un database SQL di Azure Data warehouse. |
| 40 | [Ripristinare un SQL Azure Data Warehouse (Portal)](sql-data-warehouse-restore-database-portal.md) | Attività portale Azure per ripristinare un Data Warehouse SQL Azure. |
| 41 | [Ripristinare un SQL Azure Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | Attività di PowerShell per ripristinare un Data Warehouse SQL Azure. |
| 42 | [Ripristinare un SQL Azure Data Warehouse (API REST)](sql-data-warehouse-restore-database-rest-api.md) | Attività di API REST per ripristinare un Data Warehouse SQL Azure. |



## <a name="tables-and-indexes"></a>Tabelle e indici

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 43 | [Tipi di dati per le tabelle di SQL Data Warehouse](sql-data-warehouse-tables-data-types.md) | Guida introduttiva di tipi di dati per tabelle Warehouse dati di SQL Azure. |
| 44 | [Distribuzione di tabelle in SQL Data Warehouse](sql-data-warehouse-tables-distribute.md) | Guida introduttiva di distribuzione tabelle Azure SQL Data Warehouse. |
| 45 | [L'indicizzazione tabelle SQL Data Warehouse](sql-data-warehouse-tables-index.md) | Guida introduttiva a tabella l'indicizzazione nella Warehouse di dati di SQL Azure. |
| 46 | [Panoramica delle tabelle di SQL Data Warehouse](sql-data-warehouse-tables-overview.md) | Guida introduttiva a tabelle Warehouse dati di SQL Azure. |
| 47 | [Partizione tabelle SQL Data Warehouse](sql-data-warehouse-tables-partition.md) | Guida introduttiva partizione della tabella Data warehouse di SQL Azure. |
| 48 | [Gestione delle statistiche sulle tabelle di SQL Data Warehouse](sql-data-warehouse-tables-statistics.md) | Guida introduttiva a statistiche sulle tabelle Data Warehouse di SQL Azure. |
| 49 | [Tabelle temporanee SQL Data warehouse](sql-data-warehouse-tables-temporary.md) | Guida introduttiva a tabelle temporanee in Warehouse di dati di SQL Azure. |



## <a name="integrate"></a>Integrare

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 50 | [Utilizzare Factory dati Azure con SQL Data Warehouse](sql-data-warehouse-integrate-azure-data-factory.md) | Suggerimenti per l'uso di Azure dati Factory (alimentatore automatico) con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 51 | [Usare l'apprendimento Azure con SQL Data Warehouse](sql-data-warehouse-integrate-azure-machine-learning.md) | Esercitazione per l'uso di apprendimento Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 52 | [Utilizzare Analitica flusso Azure con SQL Data Warehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) | Suggerimenti per l'uso di Azure flusso Analitica con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 53 | [Usare Power BI con SQL Data Warehouse](sql-data-warehouse-integrate-power-bi.md) | Suggerimenti per l'uso di Power BI con Azure SQL Data Warehouse per lo sviluppo di soluzioni. |
| 54 | [Sfruttare altri servizi con SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) | Strumenti e partner con soluzioni che si integrano con SQL Data Warehouse.  |



## <a name="load"></a>Caricamento

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 55 | [Caricare dati dallo spazio di archiviazione blob Azure nell'archivio di dati di SQL Azure, Azure Data Factory,](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Informazioni su come caricare i dati con Azure Data Factory |
| 56 | [Caricare dati da archiviazione blob Azure in SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Informazioni su come utilizzare PolyBase per caricare dati dallo spazio di archiviazione blob Azure nell'archivio di dati SQL. Caricare lo schema di Contoso Retail Data Warehouse alcune tabelle di dati pubblici. |
| 57 | [Caricare dati da SQL Server in SQL Azure Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Utilizza bcp per esportare i dati da SQL Server in file flat, AZCopy per importare i dati a archiviazione blob Azure e PolyBase per acquisire i dati nell'archivio di dati di SQL Azure. |
| 58 | [Caricare dati da SQL Server in SQL Azure Data Warehouse (file flat)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Per una dimensione di dati di piccole dimensioni, utilizza bcp per esportare i dati da SQL Server in file flat e importare i dati direttamente nell'archivio di dati di SQL Azure. |
| 59 | [Caricare i dati da SQL Server in Azure SQL dati Warehouse (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Viene illustrato come creare un pacchetto di SQL Server Integration Services (SSIS) per spostare dati da una vasta gamma di origini dati di SQL Data Warehouse. |
| 60 | [Caricare i dati con PolyBase SQL Data warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Utilizza bcp per esportare i dati da SQL Server in file flat, AZCopy per importare i dati a archiviazione blob Azure e PolyBase per acquisire i dati nell'archivio di dati di SQL Azure. |
| 61 | [Guida per l'uso di PolyBase SQL Data warehouse](sql-data-warehouse-load-polybase-guide.md) | Suggerimenti per l'uso di PolyBase in scenari SQL Data Warehouse e linee guida. |
| 62 | [Caricare dati di esempio nell'archivio di dati SQL](sql-data-warehouse-load-sample-databases.md) | Caricare dati di esempio nell'archivio di dati SQL |
| 63 | [Caricare i dati con bcp](sql-data-warehouse-load-with-bcp.md) | Informazioni su quali bcp e come usarlo per data warehouse scenari. |
| 64 | [Caricare i dati nell'archivio di dati di SQL Azure](sql-data-warehouse-overview-load.md) | Informazioni su scenari comuni per il caricamento in SQL Data Warehouse dati. Le quali le PolyBase, archiviazione blob Azure, file flat e spedizione disco. È anche possibile utilizzare gli strumenti di terze parti. |



## <a name="migrate"></a>Eseguire la migrazione

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 65 | [Eseguire la migrazione del codice SQL in SQL Data Warehouse](sql-data-warehouse-migrate-code.md) | Suggerimenti per la migrazione di codice SQL Azure SQL Data warehouse per lo sviluppo di soluzioni. |
| 66 | [Eseguire la migrazione dei dati](sql-data-warehouse-migrate-data.md) | Suggerimenti per la migrazione dei dati nell'archivio di dati di SQL Azure per lo sviluppo di soluzioni. |
| 67 | [Utilità di migrazione Warehouse dati (Preview)](sql-data-warehouse-migrate-migration-utility.md) | Eseguire la migrazione nell'archivio di dati SQL. |
| 68 | [Eseguire la migrazione dello schema in SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) | Suggerimenti per la migrazione dello schema nell'archivio di dati di SQL Azure per lo sviluppo di soluzioni. |
| 69 | [Eseguire la migrazione della soluzione nell'archivio di dati SQL](sql-data-warehouse-overview-migrate.md) | Guida alla migrazione per trasportando la soluzione piattaforma Warehouse di dati di SQL Azure. |



## <a name="partners"></a>Partner

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 70 | [SQL Data Warehouse business intelligence partner](sql-data-warehouse-partner-business-intelligence.md) | Elenchi di partner di business intelligence aziendali di terze parti con le soluzioni che supportano SQL Data Warehouse. |
| 71 | [Partner di integrazione di dati SQL Data Warehouse](sql-data-warehouse-partner-data-integration.md) | Elenchi dei partner di terze parti con soluzioni di integrazione di dati che supportano Warehouse di dati di SQL Azure. |
| 72 | [Partner di gestione dei dati di SQL Data Warehouse](sql-data-warehouse-partner-data-management.md) | Elenchi di partner di gestione dei dati di terze parti con soluzioni che supportano SQL Data Warehouse. |



## <a name="reference"></a>Guida di riferimento

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 73 | [Argomenti della Guida di riferimento per SQL Data Warehouse](sql-data-warehouse-overview-reference.md) | Collegamenti al contenuto di riferimento per SQL Data Warehouse. |
| 74 | [Cmdlet di PowerShell e le API REST per SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) | Trovare i cmdlet di PowerShell superiore per archivio di dati di SQL Azure ad esempio come interrompere e riprendere un database. |
| 75 | [Elementi del linguaggio](sql-data-warehouse-reference-tsql-language-elements.md) | Elenco di collegamenti a contenuto di riferimento per gli elementi del linguaggio Transact-SQL utilizzata per SQL Data Warehouse. |
| 76 | [Argomenti in Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Collegamenti a contenuto di riferimento per gli argomenti Transact-SQL utilizzata da SQL Data Warehouse. |
| 77 | [Visualizzazioni di sistema](sql-data-warehouse-reference-tsql-system-views.md) | Collegamenti al sistema visualizzazioni contenuto per SQL Data Warehouse. |



## <a name="security"></a>Sicurezza

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 78 | [SQL Data Warehouse - supporta client di livello inferiore per dati dinamici e controllo Masking](sql-data-warehouse-auditing-downlevel-clients.md) | Informazioni sul supporto di client di livello inferiore SQL Data Warehouse per il controllo dei dati |
| 79 | [Il controllo in SQL Azure Data Warehouse](sql-data-warehouse-auditing-overview.md) | Iniziare a utilizzare il controllo in Warehouse di dati di SQL Azure |
| 80 | [Iniziare con la crittografia di dati trasparente (TDE) SQL Data warehouse](sql-data-warehouse-encryption-tde.md) | Crittografia dati trasparente (TDE) SQL Data warehouse |
| 81 | [Iniziare con la crittografia dati trasparente (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Crittografia dati trasparente (TDE) SQL Data warehouse (Transact-SQL) |
| 82 | [Proteggere un database SQL Data warehouse](sql-data-warehouse-overview-manage-security.md) | Suggerimenti per proteggere un database SQL di Azure Data warehouse per lo sviluppo di soluzioni. |



## <a name="miscellaneous"></a>Varie

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 83 | [Installare Visual Studio 2015 e SSDT per SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md) | Installare Visual Studio e strumenti di sviluppo di SQL Server (SSDT) per SQL Azure Data Warehouse |
| 84 | [Migrazione a dettagli di archiviazione Premium](sql-data-warehouse-migrate-to-premium-storage.md) | Istruzioni per la migrazione di un Data Warehouse SQL esistente allo spazio di archiviazione premium |
| 85 | [Guida introduttiva a individuazione](sql-data-warehouse-security-threat-detection.md) | Come iniziare con individuazione |
| 86 | [Limiti di capacità SQL Data Warehouse](sql-data-warehouse-service-capacity-limits.md) | Valori massimi per le connessioni, database, tabelle e query SQL Data warehouse. |
| 87 | [Risoluzione dei problemi di SQL Azure Data Warehouse](sql-data-warehouse-troubleshoot.md) | Risoluzione dei problemi di SQL Azure Data Warehouse. |

