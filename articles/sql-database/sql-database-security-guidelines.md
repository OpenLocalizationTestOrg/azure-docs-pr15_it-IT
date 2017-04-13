<properties
   pageTitle="Linee guida di sicurezza di Database SQL Azure e limitazioni | Microsoft Azure"
   description="Informazioni sulle istruzioni di Database SQL di Microsoft Azure e limitazioni relative alla sicurezza."
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
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Limitazioni e indicazioni sulla sicurezza di Database SQL azure

In questo argomento descrive le linee guida per il Database SQL di Microsoft Azure e limitazioni relative alla sicurezza. Tenere presente quanto segue quando si gestisce la sicurezza dei database SQL Azure.

## <a name="access-to-the-virtual-master-database"></a>Accesso al database master virtuale

In genere, solo gli amministratori necessario accedere al database master. Routine accesso al database ogni utente deve essere tra gli utenti non amministratori database indipendente creati in ogni database. Quando si usano gli utenti del database contenuti, non è necessario creare gli account di accesso nel database master. Per ulteriori informazioni, vedere [Contenuto gli utenti del Database - effettua il Database portatili](https://msdn.microsoft.com/library/ff929188.aspx).


## <a name="firewall"></a>Firewall

Il firewall di SQL Server, l'ambito è impostato per l'intero Server SQL Azure in genere viene configurato tramite il portale e deve ammettere solo gli indirizzi IP utilizzati dagli amministratori. Per creare una regola firewall nell'ambito del database che si apre il necessarie intervallo di indirizzi IP per ogni database, connettersi a un database utente e quindi utilizzare [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) istruzione Transact-SQL.

Il servizio di Database SQL Azure è disponibile solo tramite la porta TCP 1433. Per accedere a un Database SQL dal proprio computer, assicurarsi che il firewall di computer client consente la comunicazione TCP in uscita sulla porta TCP 1433. Se non è necessario per le altre applicazioni, bloccare le connessioni in ingresso sulla porta TCP 1433. 

Come parte del processo di connessione, le connessioni da macchine virtuali di Azure vengono reindirizzate a un altro indirizzo IP e porte, univoca per ogni ruolo di lavoro. Il numero di porta è nell'intervallo compreso tra 11000 a 11999. Per ulteriori informazioni sulle porte TCP, vedere [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="authentication"></a>Autenticazione

Usa autenticazione di Active Directory (protezione integrata) laddove possibile. Per informazioni su come configurare l'autenticazione di Active Directory, vedere [connessione al Database da con Azure Active Directory autenticazione di SQL](sql-database-aad-authentication.md)e [scegliere una modalità di autenticazione](https://msdn.microsoft.com/library/ms144284.aspx) nella documentazione Online di SQL Server. 

Utilizzare gli utenti del database contenuti per migliorare la scalabilità. Per ulteriori informazioni, vedere [Contenuto gli utenti del Database - effettua il Database portatili](https://msdn.microsoft.com/library/ff929188.aspx), [Crea utente (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)e [I database contenuti](https://technet.microsoft.com/library/ff929071.aspx).

Motore di Database di chiusura delle connessioni inattive possono essere entro 30 minuti. La connessione è necessario accedere nuovamente può essere utilizzato.

Continuamente le connessioni attive al Database SQL di richiedono la nuova autorizzazione (operazione eseguita dal motore di Database) almeno ogni 10 ore. Il motore di Database tenta la nuova autorizzazione tramite password inviata ed non è necessario alcun input dell'utente. Per motivi di prestazioni quando si reimposta la password di Database SQL, la connessione non è possibile nuovamente autenticato, anche se la connessione viene reimpostata a causa di un pool di connessioni. Questa è la differenza tra il comportamento di SQL Server locale. Se la password è stata modificata dopo la connessione è stata inizialmente autorizzata, deve terminare la connessione e una nuova connessione apportate mediante la nuova password. Un utente con autorizzazioni interrompere la connessione al DATABASE può terminare una connessione al Database di SQL in modo esplicito tramite il comando di [interruzione](https://msdn.microsoft.com/library/ms173730.aspx) .

## <a name="logins-and-users"></a>Account di accesso e gli utenti

Quando si gestiscono gli account di accesso e i relativi utenti nel Database di SQL, esistono restrizioni.


- È necessario essere connessi al database **master** durante l'esecuzione di ``CREATE/ALTER/DROP DATABASE`` istruzioni. -L'utente del database nel database master corrispondente all'account di accesso principale a livello di server non può essere modificato o eliminato. 
- Inglese è la lingua predefinita dell'account di accesso principale del livello di server.
- Solo gli amministratori (server a livello accesso principale o amministratore di Azure Active Directory) e i membri del ruolo del database **dbmanager** nel database **master** dispongono dell'autorizzazione per eseguire il ``CREATE DATABASE`` e ``DROP DATABASE`` istruzioni.
- È necessario essere connessi al database master durante l'esecuzione di ``CREATE/ALTER/DROP LOGIN`` istruzioni. Tuttavia si sconsiglia utilizzando account di accesso. Utilizzare gli utenti del database contenuti.
- Per connettersi a un database utente, è necessario specificare il nome del database nella stringa di connessione.
- Solo l'account di accesso a livello di server principale e i membri del ruolo del database **loginmanager** nel database **master** dispongono dell'autorizzazione per eseguire il ``CREATE LOGIN``, ``ALTER LOGIN``, e ``DROP LOGIN`` istruzioni.
- Quando si esegue il ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE`` istruzioni in un'applicazione ADO.NET, usando i comandi di parametri non è consentita. Per ulteriori informazioni, vedere [comandi e parametri](https://msdn.microsoft.com/library/ms254953.aspx).
- Quando si esegue il ``CREATE/ALTER/DROP DATABASE`` e ``CREATE/ALTER/DROP LOGIN`` istruzioni, ognuna di queste istruzioni devono essere la sola istruzione in un batch Transact-SQL. In caso contrario, si verifica un errore. Ad esempio Transact-SQL seguente verifica l'esistenza di database. Se è presente, un ``DROP DATABASE`` istruzione è chiamato per rimuovere il database. Poiché il ``DROP DATABASE`` istruzione non è l'unica istruzione nel batch, eseguire le operazioni seguenti istruzione Transact-SQL genera un errore.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Quando si esegue il ``CREATE USER`` conto con la ``FOR/FROM LOGIN`` opzione, deve essere la sola istruzione in un batch Transact-SQL.
- Quando si esegue il ``ALTER USER`` conto con la ``WITH LOGIN`` opzione, deve essere la sola istruzione in un batch Transact-SQL.
- Per ``CREATE/ALTER/DROP`` un utente richiede il ``ALTER ANY USER`` autorizzazione sul database.
- Quando il proprietario di un ruolo di database tenta di aggiungere o rimuovere un utente del database in o da tale ruolo di database, potrebbe verificarsi l'errore seguente: **utente o il ruolo "Nome" non esiste nel database.** Questo errore è dovuto al fatto che l'utente non è visibile al proprietario. Per risolvere il problema, concedere al proprietario del ruolo di ``VIEW DEFINITION`` autorizzazione all'utente. 

Per ulteriori informazioni sull'account di accesso e gli utenti, vedere [gestione dei database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md).


## <a name="see-also"></a>Vedere anche

[Firewall di Database SQL Azure](sql-database-firewall-configure.md)

[Procedura: configurare le impostazioni del Firewall (Database di SQL Azure)](sql-database-configure-firewall-settings.md)

[Gestione di database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md)

[Centro protezione per il motore di Database SQL Server e Database SQL Azure](https://msdn.microsoft.com/library/bb510589)
