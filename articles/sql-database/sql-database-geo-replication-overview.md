<properties
    pageTitle="Replica geografico attiva per Database SQL Azure"
    description="La replica di geografico Active consente di installare 4 repliche del database in uno dei Data Center di Azure."
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>Panoramica: SQL attiva geografico-replica di Database

La replica di geografico Active consente di configurare fino a quattro database secondari leggibili in posizioni di centro dati uguali o diversi (aree). Sono disponibili per la ricerca e failover nel caso di un'interruzione dell'area dati o impossibilità di connettersi al database primario per database secondari.

>[AZURE.NOTE] Replica geografico attiva (leggibile dei secondari) è ora disponibile per tutti i database in tutti i livelli di servizio. In aprile 2017, il tipo secondario non leggibile ritiro e database non leggibile esistenti verranno aggiornati automaticamente a database secondari leggibili.

 È possibile configurare Active replica geografico tramite il [portale di Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md), o [API REST - creazione o aggiornamento Database](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Configurare: Portale di Azure](sql-database-geo-replication-portal.md)
- [Configurare: PowerShell](sql-database-geo-replication-powershell.md)
- [Configurare: T-SQL](sql-database-geo-replication-transact-sql.md)

Se per qualsiasi motivo il database principale non riuscita o semplicemente necessario impostare la modalità offline, è possibile *failover* a un database secondari. Quando è attivato failover a uno dei database secondari, tutti gli altri database secondari vengono collegati automaticamente nel nuovo database primario.

È possibile il controllo a secondario tramite il [portale Azure](sql-database-geo-replication-failover-portal.md), [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), [API REST - pianificato Failover](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)o [API REST - Failover non pianificato](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Failover: Portale di Azure](sql-database-geo-replication-failover-portal.md)
- [Failover: PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Failover: T-SQL](sql-database-geo-replication-failover-transact-sql.md)

Dopo il failover, verificare che i requisiti di autenticazione per server e database configurati per il nuovo database primario. Per informazioni dettagliate, vedere [protezione del Database di SQL dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).


La caratteristica di replica di geografico Active implementato un meccanismo per garantire la ridondanza database all'interno dell'area di Microsoft Azure stesso o in diverse aree geografiche (geografico ridondanza). La replica di geografico Active replica asincrono transazioni da un database a un massimo di quattro copie del database nel server diversi, utilizzando leggere approvata Off (combinazione) per l'isolamento. Quando si configura la replica di geografico Active, viene creato un database secondario nel server specificato. Il database originale diventa il database principale. Il database primario replica asincrono transazioni salvate in ciascuno dei database secondari. Vengono replicate solo le transazioni complete. In qualsiasi punto, il database secondario potrebbe essere leggermente dietro il database primario, dati secondaria è mai necessario transazioni parziale. Sono disponibili gli specifici dati rilasciato alla [Panoramica di continuità aziendale](sql-database-business-continuity.md).

Uno dei vantaggi principali di Active geografico replica è che fornisce una soluzione di ripristino di emergenza a livello di database con il tempo di recupero bassa. Quando si posiziona il database secondario in un server in un'area diversa, aggiungere la massima flessibilità per l'applicazione. Area tra ridondanza consente alle applicazioni recuperare una perdita permanente di un intero Data Center o parti di un Data Center dovuti emergenza naturale, gli errori di risorse umani irreversibili o atti dannosi. La figura seguente mostra che un esempio di Active geografico replica configurati con primaria nell'area North Central US e secondario nell'area Sud Central US.

![Relazione geografico replica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Un altro vantaggio chiave è i database secondari vengono leggibili che possono essere utilizzati per trasferire i carichi di lavoro di sola lettura, ad esempio processi di report. Se si intende solo utilizzare il database secondario per il bilanciamento del carico, è possibile creare nella stessa area principale. Creazione secondario nella stessa regione, non aumenta adattabilità dell'applicazione a irreversibili errori.  

Altri scenari in cui è possibile utilizzare la replica di geografico Active includono:

- **La migrazione del database**: È possibile utilizzare la replica di geografico Active per eseguire la migrazione di un database da un server a altro in linea con il tempo di inattività minimo.
- **Aggiornamenti dell'applicazione**: È possibile creare un secondario aggiuntivo come copia posteriore errori durante l'aggiornamento dell'applicazione.

Per garantire la continuità aziendale reale, l'aggiunta di ridondanza del database tra i Data Center è solo una parte della soluzione. Ripristino di un'applicazione (servizio)-to-end dopo un errore irreversibile richiede il ripristino di tutti i componenti che costituiscono il servizio e tutti i servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio un browser con un JavaScript personalizzato), web front-end, lo spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano flessibili per gli stessi errori e diventano disponibili all'interno dell'obiettivo di tempo di ripristino (RTO) dell'applicazione. Di conseguenza, è necessario identificare tutti i servizi dipendenti e si conoscono le garanzie e le funzionalità che forniscono. Quindi, attenersi alla procedura adeguata per garantire che le funzioni di servizio durante il failover dei servizi da cui dipende. Per ulteriori informazioni sulle soluzioni di progettazione per il ripristino di emergenza, vedere [Creazione di soluzioni basate su Cloud per emergenza ripristino con Active geografico-replica](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Funzionalità di replica geografico attiva
La caratteristica di replica di geografico Active fornisce le seguenti funzionalità essenziali:

- **La replica asincrono automatica**: È possibile creare un database secondario solo mediante l'aggiunta a un database esistente. Secondario può essere creato solo in un altro server di Database SQL Azure. Una volta creato, il database secondario viene popolato con i dati copiati dal database principale. Questo processo è noto come seeding. Dopo aver creato e definiti database secondario, gli aggiornamenti al database primario vengono asincrono replicati al database secondario automaticamente. Replica asincrona indica che le transazioni vengono eseguito il commit sul database principale prima che vengano replicate al database secondario. 

- **Più database secondari**: due o più database secondari aumentano la ridondanza e livello di protezione per il database primario e l'applicazione. Se sono presenti più database secondari, l'applicazione rimane protetto, anche se uno dei database secondari non riesce. Se esiste un solo database secondario e non riesce, l'applicazione viene esposta a un rischio maggiore finché non viene creato un nuovo database secondario.

- **Database secondari leggibili**: un'applicazione può accedere a un database secondario per le operazioni di sola lettura utilizzando l'identità di protezione stesse o in altre usata per accedere al database principale. I database secondari funzionano in modalità di isolamento snapshot per assicurare la replica degli aggiornamenti di principale (la riproduzione dei log) non è ritardata dalle query eseguita nel database secondario.

>[AZURE.NOTE] La riproduzione del registro subisce un ritardo nel database secondario se sono presenti gli aggiornamenti dello schema che sta ricevendo dall'oggetto primario che richiedono un blocco di schema del database secondario.

- **Replica di geografico Active dei database pool flessibile**: replica di geografico Active può essere configurata per un database di qualsiasi pool di database flessibile. Il database secondario può essere un altro pool di database flessibile. Per regolari database, secondario può essere un versa pool e viceversa database flessibile, purché i livelli di servizio sono identici. 

- **Livello di prestazioni configurabili del database secondario**: è possibile creare un database secondario con più primario basso prestazioni. Database primario e secondario devono avere lo stessa livello di servizio. Questa opzione non è consigliata per applicazioni con attività di scrittura database elevato perché il ritardo di replica aumenta il rischio di perdere dati sostanziali dopo caso di errore. Inoltre, dopo il failover le prestazioni dell'applicazione sono interessata finché non viene aggiornato nel nuovo database primario a un livello superiore di prestazioni. Il grafico di percentuale log IO nel portale Azure offre un'ottima soluzione per stimare il livello di prestazioni minimo di secondario necessari per supportare il carico di replica. Ad esempio, se il database principale è P6 (1000 DTU) e il relativo log percentuale IO è 50% secondario deve avere almeno P4 (500 DTU). È inoltre possibile recuperare i dati di IO log usando le visualizzazioni di database [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) o [sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx) .  Per ulteriori informazioni sui livelli di prestazioni di Database SQL, vedere [prestazioni e le opzioni del Database di SQL](sql-database-service-tiers.md). 

- **Failback e failover controllata dall'utente**: un database secondario può in modo esplicito passare al ruolo principale in qualsiasi momento dall'applicazione o l'utente. Durante un periodo di inattività reale l'opzione "non pianificato" da utilizzare, che promuove immediatamente secondario per primario. Quando principale viene ripristinata ed è disponibile, il sistema automaticamente contrassegna principale recuperato come secondario e aggiornarlo con il nuovo database primario. A causa della natura asincrona della replica, una piccola quantità di dati possono essere perse durante failover non previsti se primaria ha esito negativo prima che venga replicato le modifiche più recenti sul server secondario. In caso di errore primaria con più database secondari sopra, il sistema automaticamente consente di riconfigurare le relazioni di replica e collegamenti database secondario rimanente a appena alzati di livello principale senza intervento dell'utente. Dopo l'interruzione che generano il failover è ridotto, potrebbe essere utile per restituire l'applicazione all'area principale. A tale scopo, il comando failover deve essere richiamato con l'opzione "pianificato". 

- **Mantenere le credenziali e le regole firewall aggiornati**: È consigliabile utilizzando [le regole firewall del database](sql-database-firewall-configure.md) di replica geografico i database in tal caso le regole può essere replicata con il database per assicurarsi che tutti i database secondari con le stesse regole firewall come primario. Questo approccio elimina la necessità di clienti di manualmente configurare e gestire le regole del firewall nel server che ospitano i database primario e secondario. Analogamente, utilizzare [contenuto gli utenti del database](sql-database-manage-logins.md) per l'accesso ai dati garantisce database primario e secondario avere sempre lo stesso nome utente credenziali riuscito durante caso di errore, è possibile senza interruzioni a causa di mancata corrispondenza con gli account di accesso e la password. Con l'aggiunta di [Azure Active Directory](../active-directory/active-directory-whatis.md), i clienti possono gestire l'accesso degli utenti per database primario e secondario e la necessità di gestione delle credenziali in completamente database.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Aggiornamento o downgrade database primario
È possibile eseguire l'aggiornamento o eseguire il downgrade database primario a un livello di prestazioni diversi (nello stesso livello di servizio) senza disconnettere tutti i database secondari. Durante l'aggiornamento, è consigliabile aggiornare innanzitutto il database secondario e quindi aggiornare il principale. Quando il downgrade, inversione dell'ordine: eseguire il downgrade primario prima di tutto e quindi eseguire il downgrade secondario. 

Il database secondario deve essere in stesso livello di servizio principale, in modo che la migrazione del database principale a un livello di servizi diversa è necessario interrompere il collegamento di replica geografico e rinominare i database secondario o semplicemente rilasciarlo. Quindi eseguire la migrazione primario per il nuovo livello di servizio e riconfigurare la replica geografico. Il nuovo database secondario verrà creato automaticamente con lo stesso livello di prestazioni primario per impostazione predefinita.

## <a name="preventing-the-loss-of-critical-data"></a>Prevenzione della perdita dei dati critici
A causa di latenza elevata di reti, copia continuo utilizza un meccanismo di replica asincrono. Replica asincrona rende inevitabile perdita di dati in caso di errore. Tuttavia, alcune applicazioni non possono richiedere perdita di dati. Per proteggere gli aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) subito dopo il commit della transazione. Chiamata **sp_wait_for_database_copy_sync** blocchi thread chiamante fino all'ultima transazione approvata replicato al database secondario. La procedura di attesa finché tutte le transazioni in coda riconosciute dal database secondario. l'ambito **sp_wait_for_database_copy_sync** è impostato su un collegamento Copia continuo specifica. Tutti gli utenti con i diritti di connessione al database primario possono chiamare questa procedura.

>[AZURE.NOTE] Ritardi dovuti a una chiamata di procedura **sp_wait_for_database_copy_sync** possono essere significativo. Il ritardo dipende dalle dimensioni della lunghezza log transazione al momento e la chiamata viene restituito finché non viene replicato l'intero log. Evitare di chiamare questa procedura, a meno che non indispensabili.

## <a name="programmatically-managing-active-geo-replication"></a>Gestione a livello di programmazione della replica di geografico attivo

Come descritto in precedenza, anche la replica di geografico Active può essere gestita a livello di programmazione tramite PowerShell Azure e le API REST. Le tabelle seguenti descrivono l'insieme di comandi disponibili.

- **API di Azure Manager delle risorse e la sicurezza basata sui ruoli**: replica di geografico Active include un insieme di [API di Azure Manager delle risorse]( https://msdn.microsoft.com/library/azure/mt163571.aspx) per la gestione, inclusi [i cmdlet di PowerShell basate su Gestione risorse di Azure](sql-database-geo-replication-powershell.md). Queste API richiedono l'utilizzo di gruppi di risorse e supporto tecnico di sicurezza basata sui ruoli (RBAC). Per ulteriori informazioni su come implementare l'accesso ruoli vedere [Controllo dell'accesso Azure Role-Based](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] Molte nuove funzionalità di Active geografico replica sono supportate solo tramite [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md) in base [All'API REST di SQL Azure](https://msdn.microsoft.com/library/azure/mt163571.aspx) e [i cmdlet di PowerShell di Database SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx). API REST (classica)] (https://msdn.microsoft.com/library/azure/dn505719.aspx) e [i cmdlet (classici) di Database SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) sono supportati per la compatibilità in modo che si consiglia di utilizzare API basate su Gestione risorse di Azure. 


### <a name="transact-sql"></a>In Transact-SQL

|Comando|Descrizione|
|-------|-----------|
|[La modifica del DATABASE (Database di SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Utilizzare aggiungere SERVER secondario via argomento per creare un database secondario per un database esistente e avvii dati replica|
|[La modifica del DATABASE (Database di SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Utilizzare FAILOVER o FORCE_FAILOVER_ALLOW_DATA_LOSS per convertire un database secondario per essere principale per avviare failover
|[La modifica del DATABASE (Database di SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Utilizzare rimuovere SERVER secondario via per terminare una replica di dati tra un Database SQL e il database secondario specificato.|
|[Sys.geo_replication_links (Database di SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx)|Restituisce tutti i collegamenti di replica esistenti per ogni database di informazioni sul server logico Database SQL Azure.|
|[Sys.dm_geo_replication_link_status (Database di SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)|Ottiene l'ora dell'ultima replica, ultimo ritardo di replica e altre informazioni relative al collegamento di replica per un database SQL specificato.|
|[Sys.dm_operation_status (Database di SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx)|Mostra lo stato per tutte le operazioni di database, tra cui lo stato dei collegamenti di replica.|
|[sp_wait_for_database_copy_sync (Database di SQL Azure)](https://msdn.microsoft.com/library/dn467644.aspx)|causa dell'applicazione in attesa finché tutte le transazioni completate siano replicate e riconosciute dal database secondario attivo.|
||||

### <a name="powershell"></a>PowerShell

|Cmdlet|Descrizione|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Ottiene uno o più database.|
|[Nuovo AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Crea un database secondario per un database esistente e avvia la replica dei dati.|
|[Set AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|Passa a un database secondario per essere principale per avviare failover.|
|[Rimuovi AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|Termina la replica dei dati tra un Database SQL e il database secondario specificato.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Recupera i collegamenti di replica geografico tra un Database di SQL Azure e un gruppo di risorse o SQL Server.|
||||

### <a name="rest-api"></a>API REST

|API|Descrizione|
|---|-----------|
|[Creazione o aggiornamento Database (createMode = ripristino)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Consente di creare, aggiornare o ripristina un principale o un database secondario.|
|[Ottenere creare o aggiornare lo stato del Database](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Restituisce lo stato di un'operazione di creazione.|
|[Impostare Database secondario come principale (Failover pianificato)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Alzare di livello un database secondario in una relazione di replica geografico per diventare il nuovo database primario.|
|[Impostare Database secondario come principale (Failover non pianificato)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Per forzare caso di errore al database secondario e impostare secondario come primario.|
|[Recuperare i collegamenti di replica](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Ottiene tutti i collegamenti di replica per un determinato database SQL in una relazione di replica geografico. Recupera informazioni visibili nella visualizzazione sys.geo_replication_links catalogo.|
|[Ottieni collegamento di replica](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Ottiene un collegamento di replica specifici per un determinato database SQL in una relazione di replica geografico. Recupera informazioni visibili nella visualizzazione sys.geo_replication_links catalogo.|
||||



## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- Per informazioni su backup Database di SQL Azure automatica, vedere [automatizzato di Database SQL di backup](sql-database-automated-backups.md).
- Per informazioni sull'uso di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md).
- Per informazioni sull'uso di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md).
- Per informazioni sui requisiti di autenticazione per un nuovo server primario e un database, vedere [protezione del Database di SQL dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
