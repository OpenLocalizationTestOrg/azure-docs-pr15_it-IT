<properties
   pageTitle="Proteggere un database SQL Data warehouse | Microsoft Azure"
   description="Suggerimenti per proteggere un database SQL di Azure Data warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteggere un database SQL Data warehouse

> [AZURE.SELECTOR]
- [Cenni preliminari sulla sicurezza](sql-data-warehouse-overview-manage-security.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Crittografia (Portal)](sql-data-warehouse-encryption-tde.md)
- [Crittografia (Transact-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

In questo articolo vengono illustrati i concetti fondamentali della protezione del database Warehouse di dati di SQL Azure. In particolare, in questo articolo verrà iniziare a usare le risorse per limitare l'accesso, la protezione dei dati e il monitoraggio attività in un database.

## <a name="connection-security"></a>Protezione di connessione

Protezione della connessione fa riferimento a come limitare e connessione sicura al database mediante le regole firewall e la crittografia di connessione.

Le regole firewall vengono utilizzate dal server e il database per rifiutare i tentativi di connessione da indirizzi IP non sono stati esplicitamente whitelisted. Per consentire le connessioni da un'applicazione o l'indirizzo IP pubblico del computer client, è necessario prima di tutto creare una regola del firewall a livello di server con il portale di Azure, API REST o PowerShell. Come ottimale, è consigliabile limitare gli intervalli di indirizzi IP consentiti attraverso il firewall server quanto più possibili.  Per accedere a dati di SQL Azure Warehouse dal computer locale, assicurarsi che il firewall nella rete e computer locale consente la comunicazione in uscita sulla porta TCP 1433.  Per ulteriori informazioni, vedere [firewall di Database SQL Azure][], [sp_set_firewall_rule][]e [sp_set_database_firewall_rule][].

Connessioni a SQL Data Warehouse vengono crittografate per impostazione predefinita.  Modifica impostazioni di connessione per disabilitare la crittografia vengono ignorate.

## <a name="authentication"></a>Autenticazione

Autenticazione fa riferimento a come si provare la propria identità quando ci si connette al database. SQL Data Warehouse supporta attualmente l'autenticazione di SQL Server con un nome utente e password, nonché un Azure Active Directory. 

Una volta creato il server logico per il database, è specificato un account di accesso "Amministrazione server" con un nome utente e la password. Usa le credenziali, è possibile autenticare a qualsiasi database del server come proprietario del database, o "dbo" tramite autenticazione di SQL Server.

Tuttavia, come ottimale, gli utenti dell'organizzazione devono utilizzare un account diverso per l'autenticazione. In questo modo è possibile limitare le autorizzazioni concesse all'applicazione e ridurre i rischi di attività dannoso nel caso in cui il codice dell'applicazione è esposto a un attacco di inserimento SQL. 

Per creare un utente autenticato di SQL Server, connettersi al database **master** con l'account di accesso di amministrazione server sul server e creare un nuovo account di accesso di server.  Inoltre, è consigliabile creare un utente nel database master per gli utenti di Warehouse di dati di SQL Azure. Creazione di un utente in schema consente all'utente di accesso con strumenti come SQL Server Management Studio senza specificare un nome di database.  Inoltre, consente loro di utilizzare Esplora aree di oggetto per visualizzare tutti i database su un server SQL.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Quindi connettersi al **database SQL Data Warehouse** con l'account di accesso di amministratore di server e creare un utente del database in base all'account di accesso server che appena creato.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Se un utente verrà eseguendo le operazioni aggiuntive, ad esempio la creazione di account di accesso o la creazione di nuovi database, sarà anche necessario assegnare al `Loginmanager` e `dbmanager` ruoli nel database master. Per ulteriori informazioni su questi ruoli aggiuntivi e l'autenticazione a un Database SQL, vedere [gestione dei database e gli account di accesso di Database SQL Azure][].  Per ulteriori informazioni su Azure Active Directory per SQL Data Warehouse, vedere [connessione a dati Warehouse da con Azure Active Directory autenticazione di SQL][].


## <a name="authorization"></a>Autorizzazione

Autorizzazione fa riferimento a che cosa è possibile eseguire all'interno di un database SQL di Azure Data Warehouse e ciò dipende dalle appartenenze ai ruoli del proprio account utente e autorizzazioni. Come ottimale, è necessario concedere agli utenti i privilegi minimi necessari. Warehouse di dati di SQL Azure consente di semplificare la gestione di ruoli in Transact-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Ci si connette con l'account dell'amministratore server fa parte di db_owner, che è autorizzato a eseguire alcuna operazione all'interno del database. Salvare l'account per la distribuzione di aggiornamenti di schema e altre operazioni di gestione. Usare l'account "ApplicationUser" con autorizzazioni limitate più connettersi dall'applicazione al database con privilegi minimi necessari per l'applicazione.

Modi per limitare ulteriormente cosa si può fare con Database di SQL Azure:

- Granulare [autorizzazioni][] consentono di controllare le operazioni che è possibile eseguire in singole colonne, tabelle, viste, procedure e altri oggetti nel database. Utilizzare le autorizzazioni granulare per avere il massimo controllo e concedere le autorizzazioni minime necessarie. Il sistema granulare delle autorizzazioni è un po' complicato e richiederà alcuni study da utilizzare in modo efficace.
- [Ruoli di database][] diversa da quella db_datareader e db_datawriter possono essere utilizzati per creare account utente dell'applicazione più potenti o meno potenti account di gestione. Ruoli predefiniti del database consentono di concedere autorizzazioni, ma possono comportare la concessione di autorizzazioni più quelli necessari.
- Per limitare le azioni eseguite sul database, è possibile utilizzare [le stored procedure][] .

Gestione di database e server logici dal portale classica di Azure o tramite l'API di gestione risorse Azure dipende dalle assegnazioni di ruolo dell'account utente del portale. Per ulteriori informazioni su questo argomento, vedere [controllo dell'accesso basato sui ruoli nel portale di Azure][].

## <a name="encryption"></a>Crittografia

Azure SQL dati Warehouse trasparente dati crittografia (TDE) consente di evitare il rischio di attività dannoso eseguendo in tempo reale crittografia e decrittografia dei dati inattivi.  Quando si crittografa il database, backup associati e i file di registro delle transazioni vengono crittografati senza eseguire alcuna modifica alle applicazioni. TDE Crittografa lo spazio di archiviazione di un intero database utilizzando una chiave simmetrica denominata la chiave di crittografia del database. Nel Database di SQL della chiave di crittografia di database è protetto da un certificato server incorporato. Il certificato server incorporato sia univoco per ogni Database di SQL server. Microsoft Ruota automaticamente i certificati almeno ogni 90 giorni. Algoritmo di crittografia utilizzato da SQL Data Warehouse è AES 256. Per una descrizione generale del TDE, vedere [Trasparente la crittografia dei dati][].

È possibile crittografare il database tramite il [Portale di Azure] [ Encryption with Portal] o [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate ed esempi sulla connessione a SQL Data Warehouse con protocolli diversi, vedere [connettersi a SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Connettersi a SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connessione a SQL Data Warehouse tramite l'autenticazione di Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Firewall di Database SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Ruoli del database]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestione di database e gli account di accesso di Database SQL Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Autorizzazioni]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedure]: https://msdn.microsoft.com/library/ms190782.aspx
[Crittografia dati trasparente]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Controllo dell'accesso basato sui ruoli nel portale di Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
