<properties
   pageTitle="Non supportate in T-SQL Database SQL Azure | Microsoft Azure"
   description="Istruzioni Transact-SQL minore completamente supportate in Database SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Differenze Transact-SQL Database SQL Azure


La maggior parte delle caratteristiche Transact-SQL che dipendono da applicazioni supportata in Microsoft SQL Server e Database di SQL Azure. Un elenco parziale delle funzionalità supportate per le applicazioni seguenti:

- Tipi di dati.
- Operatori.
- Funzioni di stringa, aritmetici, logici e cursore.

Tuttavia, il Database di SQL Azure consente di isolare caratteristiche da qualsiasi dipendenza nel database **master** . Di conseguenza molte attività di livello di server sono appropriate per Database SQL e non sono supportate. Caratteristiche che sono obsoleti in SQL Server non sono in genere supportate nel Database di SQL.

> [AZURE.NOTE]
> In questo argomento vengono illustrate le funzionalità disponibili con Database di SQL durante l'aggiornamento alla versione corrente; V12 di Database SQL. Per ulteriori informazioni su V12, vedere [del SQL Database V12 cosa nuovo](sql-database-v12-whats-new.md).

Nelle sezioni seguenti sono elencati caratteristiche che sono parzialmente supportati e le caratteristiche che non sono completamente supportate.


## <a name="features-partially-supported-in-sql-database-v12"></a>Funzionalità supportate parzialmente nel V12 di Database SQL

SQL Database V12 supporta alcuni ma non tutti gli argomenti presenti nelle istruzioni SQL Server 2016 Transact-SQL corrispondente. Ad esempio, l'istruzione CREATE PROCEDURE è disponibile, tuttavia non sono disponibili tutte le opzioni dell'istruzione CREATE PROCEDURE. Vedere gli argomenti sintassi collegate per informazioni dettagliate sulle aree di ogni istruzione supportate.

- Database: [Crea](https://msdn.microsoft.com/library/dn268335.aspx )/[modificare](https://msdn.microsoft.com/library/ms174269.aspx)
- Viste sono in genere disponibili per le caratteristiche disponibili.
- Le funzioni: [Crea](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER funzione](https://msdn.microsoft.com/library/ms186967.aspx)
- [INTERRUZIONE](https://msdn.microsoft.com/library/ms173730.aspx) 
- Account di accesso: [Crea](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Stored procedure: [creare](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabelle: [Crea](https://msdn.microsoft.com/library/dn305849.aspx)/[modificare](https://msdn.microsoft.com/library/ms190273.aspx)
- Tipi di (personalizzato): [Crea tipo](https://msdn.microsoft.com/library/ms175007.aspx)
- Gli utenti: [Crea](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER utente](https://msdn.microsoft.com/library/ms176060.aspx)
- Visualizzazioni: [Crea](https://msdn.microsoft.com/library/ms187956.aspx)/[modificare visualizzazione](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>Caratteristiche non supportate nel Database SQL

- Confronto degli oggetti di sistema
- Connessione correlati: istruzioni Endpoint, ORIGINAL_DB_NAME. Database SQL non supporta l'autenticazione di Windows, ma supporta l'autenticazione di Azure Active Directory simile. Alcuni tipi di autenticazione richiedono l'ultima versione di SQL Server Management Studio. Per ulteriori informazioni, vedere [connessione al Database SQL o dati Warehouse da con Azure Active Directory autenticazione di SQL](sql-database-aad-authentication.md).
- Cross query di database con tre o quattro nomi di parte. (Sola lettura query tra database sono supportate utilizzando [una query di database flessibile](sql-database-elastic-query-overview.md))
- Database tra la proprietà concatenazione, l'impostazione attendibile
- Raccolta dati
- Diagrammi di database
- Posta elettronica database
- DATABASEPROPERTY (utilizzare DATABASEPROPERTYEX)
- Account di accesso EXECUTE AS
- Crittografia: gestione delle chiavi extensible
- Gestione degli eventi: eventi, le notifiche di eventi, le notifiche di query
- Funzionalità relative alla posizione di file di database, le dimensioni e i file di database automaticamente gestiti da Microsoft Azure.
- Funzionalità relative alla disponibilità, viene gestita tramite il proprio account di Microsoft Azure: eseguire il backup, ripristino AlwaysOn, che rispecchiano database, distribuzione dei log, modalità di ripristino. Per ulteriori informazioni, vedere Backup del Database SQL Azure e ripristino.
- Caratteristiche che si basano su lettura log in esecuzione nel Database di SQL: replica Push, acquisizione dei dati di modifica.
- Caratteristiche che si basano su Agente SQL Server o il database MSDB: processi, avvisi e operatori, in base a criteri di gestione, posta elettronica database, server di gestione centrale.
- FILESTREAM
- Le funzioni: fn_get_sql fn_virtualfilestats, la funzione fn_virtualservernodes
- Tabelle temporanee globali
- Impostazioni del server componenti hardware: memoria, thread di lavoro, affinità della CPU, flag di traccia e così via. Utilizzare i livelli di servizio.
- HAS_DBACCESS
- ELIMINARE STAT PROCESSO
- Server collegati, OPENQUERY, OPENROWSET, OPENDATASOURCE, inserimento di massa e nomi composti
- Server master/target
- .NET framework [integrazione di CLR con SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Carichi
- Ricerca semantic
- Credenziali del server. Usa database ambito invece le credenziali.
- Gli elementi di livello server: Server ruoli, la funzione IS_SRVROLEMEMBER, sys.login_token. Le autorizzazioni a livello di server non sono disponibili anche se alcuni vengono sostituiti da autorizzazioni a livello di database. Alcune viste a livello di server non sono disponibili anche se alcuni vengono sostituiti da viste a livello di database.
- Senza server express: localdb, istanze utente
- Gestore del servizio
- SET REMOTE_PROC_TRANSACTIONS
- CHIUSURA
- sp_addmessage
- opzioni di sp_configure e RICONFIGURARE. Alcune delle opzioni disponibili tramite [ALTER configurazione nell'ambito del DATABASE](https://msdn.microsoft.com/library/mt629158.aspx).
- sp_helpuser
- sp_migrate_user_to_contained
- Controllo di SQL Server. Utilizzare invece il controllo di Database SQL.
- SQL Server Profiler
- Traccia di SQL Server
- Tenere traccia dei contrassegni. Alcuni elementi di contrassegno traccia sono stati spostati in modalità di compatibilità.
- Il debug Transact-SQL
- Trigger: Nell'ambito Server o i trigger di accesso
- Istruzione USE: per modificare il contesto di database a un altro database, è necessario effettuare una nuova connessione per il nuovo database.


## <a name="full-transact-sql-reference"></a>Riferimento Transact-SQL completo

Per ulteriori informazioni su grammatica Transact-SQL, l'uso ed esempi, vedere informazioni di [Riferimento Transact-SQL (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) nella documentazione Online di SQL Server. 

### <a name="about-the-applies-to-tags"></a>Informazioni sui tag "Si applica a"

Il riferimento Transact-SQL include gli argomenti relativi a versioni di SQL Server 2008 a quella odierna. Sotto il titolo dell'argomento sono è un'icona barra elencando le quattro piattaforme di SQL Server e che indica applicabilità. Gruppi di disponibilità, ad esempio, sono stati introdotti in SQL Server 2012. L'argomento [Crea gruppo AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) indica che l'istruzione si applica a * * SQL Server (a partire dalla 2012). L'istruzione non è valida per SQL Server 2008, SQL Server 2008 R2, Database SQL Azure, Warehouse di dati di SQL Azure o Parallel Data Warehouse.

In alcuni casi, l'argomento generale di un argomento può essere utilizzato in un prodotto, ma vi sono differenze tra i prodotti. Le differenze sono indicate in punti medi nell'argomento in base alle esigenze.

