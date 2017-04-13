<properties
   pageTitle="Cenni preliminari sulla protezione di Database SQL"
   description="Informazioni sulla sicurezza di Database SQL Azure e SQL Server, incluse le differenze tra nel cloud e SQL Server locale quando si tratta di autenticazione, autorizzazione, la protezione della connessione, sulla crittografia e conformità."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>La protezione del Database SQL

## <a name="overview"></a>Panoramica

In questo articolo vengono illustrati i concetti fondamentali della protezione il livello di dati di un'applicazione utilizzando il Database di SQL Azure. In particolare, in questo articolo verrà visualizzato iniziare a usare le risorse per limitare l'accesso, la protezione dei dati, e Monitoraggio attività in un database creato in [Guida introduttiva a Database SQL di esercitazione](sql-database-get-started.md). Per una panoramica completa delle funzionalità di sicurezza disponibili in tutte le versioni di SQL, vedere il [Centro protezione per il motore di Database di SQL Server e Database di SQL Azure](https://msdn.microsoft.com/library/bb510589). Altre informazioni sono disponibili anche in [sicurezza e il Database di SQL Azure white paper tecnico](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Protezione di connessione

Protezione della connessione fa riferimento a come limitare e connessione sicura al database mediante le regole firewall e la crittografia di connessione.

Le regole firewall vengono utilizzate dal server e il database per rifiutare i tentativi di connessione da indirizzi IP non sono stati esplicitamente whitelisted. Per consentire l'applicazione o l'indirizzo IP pubblico del computer client tentare la connessione a un nuovo database, è necessario prima di tutto creare una regola del firewall a livello di server con il portale classica di Azure, API REST o PowerShell. Come ottimale, è consigliabile limitare gli intervalli di indirizzi IP consentiti attraverso il firewall server quanto più possibili. Per ulteriori informazioni, vedere [Firewall di Database SQL Azure](https://msdn.microsoft.com/library/ee621782).

Tutte le connessioni al Database SQL Azure richiedono la crittografia SSL/TLS () in qualsiasi momento mentre i dati sono "durante il transito" da e verso il database. Nella stringa di connessione dell'applicazione, è necessario specificare parametri per crittografare la connessione e *non* considerare attendibile il certificato server (potuto automaticamente se si copia la stringa di connessione dalla portale classica di Azure), in caso contrario la connessione non verrà verificata l'identità del server e sarà soggetto a "uomo-in-basati". Per il driver ADO.NET, ad esempio, i parametri di stringa di connessione sono **Crittografa = True** e **TrustServerCertificate = False**. Per ulteriori informazioni, vedere [la crittografia connessione di Database SQL Azure e la convalida dei certificati](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## <a name="authentication"></a>Autenticazione

Autenticazione fa riferimento a come si provare la propria identità quando ci si connette al database. Database SQL supporta due tipi di autenticazione:

 - **Autenticazione di SQL Server**, che utilizza un nome utente e password
 - **Autenticazione di azure Active Directory**, che utilizza identità gestite da Azure Active Directory ed è supportato per i domini gestiti e integrati

Una volta creato il server logico per il database, è specificato un account di accesso "Amministrazione server" con un nome utente e la password. Usa le credenziali, è possibile autenticare a qualsiasi database del server come proprietario del database, o "dbo." Se si desidera utilizzare l'autenticazione di Azure Active Directory, è necessario creare un altro amministratore server denominata "amministratore Azure Active Directory," che è possibile amministrare Azure AD utenti e gruppi. Questo amministrazione è inoltre possibile eseguire tutte le operazioni che possono essere un amministratore del server standard. Per una procedura dettagliata su come creare un amministratore di Azure Active Directory per abilitare l'autenticazione di Azure Active Directory, vedere [connessione a Database da con Azure Active Directory autenticazione di SQL](sql-database-aad-authentication.md) .

È buona norma l'applicazione deve utilizzare un account diverso per l'autenticazione, in questo modo è possibile limitare le autorizzazioni concesse all'applicazione e ridurre i rischi di attività dannoso nel caso in cui il codice dell'applicazione è esposto a un attacco di inserimento SQL. Si consiglia consiste nel creare un [utente del database di contenuti](https://msdn.microsoft.com/library/ff929188), che consente l'app eseguire l'autenticazione direttamente a un singolo database. È possibile creare un utente di contenuto del database che utilizza l'autenticazione di SQL eseguendo il seguente comando Transact-SQL durante la connessione al database utente come l'accesso di amministratore del server:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se è stato creato un amministratore di Azure Active Directory, è possibile creare un utente di contenuto del database che utilizza l'autenticazione di Azure Active Directory eseguendo il seguente comando Transact-SQL durante la connessione al database utente come amministratore di Azure Active Directory:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

In entrambi i casi, la stringa di connessione dell'applicazione necessario specificare le credenziali utente, anziché l'accesso di amministrazione di server, per connettersi al database.

Per ulteriori informazioni sull'autenticazione a un Database SQL, vedere [gestione dei database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md).


## <a name="authorization"></a>Autorizzazione
Autorizzazione fa riferimento a che cosa è possibile eseguire all'interno di un Database di SQL Azure e ciò dipende dalle appartenenze ai ruoli del proprio account utente e autorizzazioni. Come ottimale, è necessario concedere agli utenti i privilegi minimi necessari. Database SQL Azure consente di semplificare la gestione di ruoli in Transact-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Ci si connette con l'account dell'amministratore server fa parte di db_owner, che è autorizzato a eseguire alcuna operazione all'interno del database. Salvare l'account per la distribuzione di aggiornamenti di schema e altre operazioni di gestione. Usare l'account "ApplicationUser" con autorizzazioni limitate più connettersi dall'applicazione al database con privilegi minimi necessari per l'applicazione.

Modi per limitare ulteriormente cosa si può fare con Database di SQL Azure:

* [Ruoli di database](https://msdn.microsoft.com/library/ms189121) diversa da quella db_datareader e db_datawriter possono essere utilizzati per creare account utente dell'applicazione più potenti o meno potenti account di gestione.
* Granulare [autorizzazioni](https://msdn.microsoft.com/library/ms191291) consentono di controllare le operazioni che è possibile eseguire in singole colonne, tabelle, viste, procedure e altri oggetti nel database.
* [Rappresentazione](https://msdn.microsoft.com/library/vstudio/bb669087) e [firma del modulo](https://msdn.microsoft.com/library/bb669102) possono essere utilizzati per sicuro elevare temporaneamente le autorizzazioni.
* [Protezione a livello di riga](https://msdn.microsoft.com/library/dn765131) possono essere usate limite che le righe di un utente può accedere.
* [Dati Masking](sql-database-dynamic-data-masking-get-started.md) può essere utilizzato per limitare l'esposizione di informazioni sensibili.
* Per limitare le azioni eseguite sul database, è possibile utilizzare [le stored procedure](https://msdn.microsoft.com/library/ms190782) .

Gestione di database e server logici dal portale classica di Azure o tramite l'API di gestione risorse Azure dipende dalle assegnazioni di ruolo dell'account utente del portale. Per ulteriori informazioni su questo argomento, vedere [controllo dell'accesso basato sui ruoli nel portale di Azure](../active-directory./role-based-access-control-configure.md).


## <a name="encryption"></a>Crittografia

Database SQL Azure consente di proteggere i dati per la crittografia dei dati quando si "Trova resto" o archiviati in file di database e backup, utilizzando [La crittografia dei dati trasparente](http://go.microsoft.com/fwlink/?LinkId=526242). Per crittografare il database, connettersi come un proprietario del database ed eseguire:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Per altri modi crittografare le informazioni riservate dati, è consigliabile:

* [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche le celle di dati con i tasti di crittografia diverso.
* Se è necessario un modulo di sicurezza Hardware o gestione centrale della gerarchia di chiave di crittografia, è preferibile [Archivio chiave Azure con SQL Server in macchine Virtuali un Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Sempre crittografato](https://msdn.microsoft.com/library/mt163865.aspx) (nella versione di anteprima) rende la crittografia trasparente alle applicazioni e consente ai client crittografare dati riservati all'interno di applicazioni client senza condividere le chiavi di crittografia con Database di SQL.

## <a name="auditing"></a>Il controllo

Il controllo e tenere traccia degli eventi di database consente di gestire la conformità alle normative e identificare attività sospetto. Il controllo dei Database di SQL consente di tenere traccia di eventi del database a un controllo accedere l'account di archiviazione Azure. Il controllo dei Database di SQL si integra anche con Microsoft Power BI per facilitare l'analisi e il drill-down report. Per ulteriori informazioni, vedere [iniziare a utilizzare il controllo dei Database di SQL](sql-database-auditing-get-started.md).

Individuazione del Database SQL fornisce un livello aggiuntivo di protezione nella parte superiore di controllo. È possibile rispondere alle minacce quando si verificano fornendo gli avvisi di sicurezza sulle attività anomala. Per ulteriori informazioni, vedere [Guida introduttiva a individuazione Database SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformità

Oltre alle funzionalità e funzionalità che consentono l'applicazione di soddisfare vari requisiti di conformità di sicurezza, Database SQL Azure anche indicati partecipa controlli regolari e certificata in base a una serie di standard di conformità. Per ulteriori informazioni, vedere il [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/), in cui è possibile trovare l'elenco più recente di [certificazioni di conformità di Database SQL](https://azure.microsoft.com/support/trust-center/services/).
