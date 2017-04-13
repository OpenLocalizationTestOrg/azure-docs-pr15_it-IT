<properties
   pageTitle="Configurare una panoramica SQL server firewall | Microsoft Azure"
   description="Informazioni su come configurare un firewall di database SQL con le regole del firewall di livello di server e di database per gestire l'accesso."
   keywords="firewall di database"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>Configurare le regole firewall di Database SQL Azure \- Panoramica


> [AZURE.SELECTOR]
- [Panoramica](sql-database-firewall-configure.md)
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Database SQL di Microsoft Azure offre un servizio di database relazionali di Azure e altre applicazioni basate su Internet. Per informazioni su come proteggere i dati, firewall impediscono l'accesso tutti al server di database finché non si specifica quali computer dispone delle autorizzazioni necessarie. Il firewall concede l'accesso al database in base a indirizzo IP di origine di ogni richiesta.

Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. Livello di server e database, è possibile creare regole del firewall.

- **Regole del firewall a livello di server:** Queste regole consentono ai client di accedere al server di SQL Azure intero, vale a dire tutti i database nello stesso server logici. Queste regole sono archiviate nel database **master** . Le regole del firewall a livello di server possono essere configurate tramite il portale o utilizzando istruzioni Transact-SQL.
- **Regole del firewall a livello di database:** Queste regole consentono ai client per accedere ai singoli database all'interno del server di Database SQL Azure. È possibile creare le regole per ogni database e vengono archiviati nei singoli database. (È possibile creare regole del firewall a livello di database per il database **master** ). Le regole possono essere utili per limitare l'accesso a determinate database (sicura) nello stesso server logici. Le regole del firewall a livello di database possono essere configurate solo tramite istruzioni Transact-SQL.

**Recommendation:** Si consiglia di utilizzare le regole firewall a livello di database ogni volta che è possibile migliorare la protezione e alla portabilità del database. Usare le regole firewall a livello di server per gli amministratori e dopo avere molti database con gli stessi requisiti di accesso e non si desidera dedicare troppo tempo a configurare singolarmente ogni database.


## <a name="firewall-overview"></a>Cenni preliminari sul firewall

Inizialmente, tutti gli accessi Transact-SQL al server SQL Azure sono bloccato dal firewall. Per iniziare a utilizzare il server di SQL Azure, è necessario accedere al portale di Azure e specificare una o più regole firewall a livello di server che consentono di accedere al server SQL Azure. Utilizzare le regole del firewall per specificare quali intervalli di indirizzi IP da Internet consentiti e se le applicazioni di Azure provare a connettersi al server di SQL Azure.

Per concedere l'accesso a solo uno dei database del server di SQL Azure in modo selettivo, è necessario creare una regola a livello di database per il database necessario. Specificare un intervallo di indirizzi IP per la regola firewall database oltre l'intervallo di indirizzi IP specificato nella regola firewall a livello di server e assicurarsi che l'indirizzo IP del client rientri nell'intervallo specificato nella regola a livello di database.

Tentativi di connessione da Internet e Azure devono prima passare attraverso il firewall di raggiungere il server SQL Azure o un Database SQL, come illustrato nella figura seguente.

   ![Diagramma che descrivono la configurazione del firewall.][1]

## <a name="connecting-from-the-internet"></a>La connessione da Internet

Quando un computer tenta di connettersi al server di database da Internet, il firewall verifica prima di tutto l'indirizzo IP di origine della richiesta rispetto al set completo di regole del firewall.

- Se l'indirizzo IP della richiesta in uno degli intervalli specificati regole del firewall a livello di server, è abbia concesso o meno la connessione al server di Database SQL Azure.

- Se l'indirizzo IP della richiesta non è in uno degli intervalli specificati nella regola firewall a livello di server, vengono controllate le regole firewall a livello di database. Se l'indirizzo IP della richiesta in uno degli intervalli specificati regole del firewall a livello di database, la connessione viene concesso solo al database che dispone di una regola corrispondente a livello di database.

- Se l'indirizzo IP della richiesta non è all'interno degli intervalli specificati in una delle regole firewall server a livello o di database, la richiesta di connessione ha esito negativo.

> [AZURE.NOTE] Per accedere a Database SQL Azure dal computer locale, assicurarsi che il firewall nella rete e computer locale consente la comunicazione in uscita sulla porta TCP 1433.


## <a name="connecting-from-azure"></a>La connessione da Azure

Quando un'applicazione da Azure tenta di connettersi al server di database, il firewall consente di verificare che le connessioni Azure sono consentite. Un firewall impostazione con inizio e fine indirizzo uguale a 0.0.0.0 indica che le connessioni sono consentite. Se il tentativo di connessione non è consentito, la richiesta non raggiungere il server di Database SQL Azure.

> [AZURE.IMPORTANT] Questa opzione consente di configurare il firewall per consentire a tutte le connessioni da Azure connessioni incluse dalle sottoscrizioni di altri utenti. Quando si seleziona questa opzione, verificare che l'account di accesso e autorizzazioni utente limitare l'accesso a solo gli utenti autorizzati.

È possibile attivare le connessioni da Azure in due modi:

- Nel [portale di Microsoft Azure](https://portal.azure.com/), selezionare la casella di controllo **Consenti servizi azure al server di accesso** durante la creazione di un nuovo server.

- Nel [portale classica](http://go.microsoft.com/fwlink/p/?LinkID=161793), nella scheda **Configura** un server, nella sezione **Servizi consentito** fare clic su **Sì** per **I servizi Microsoft Azure**.

## <a name="creating-the-first-server-level-firewall-rule"></a>Creare la prima regola firewall a livello di server

L'impostazione del livello di server firewall prima può essere creata tramite il [portale di Azure](https://portal.azure.com/) o a livello di programmazione tramite l'API REST o Azure PowerShell. Le regole successive firewall a livello di server possono essere create e gestite utilizzo di questi metodi come tramite Transact-SQL. Per migliorare le prestazioni, regole del firewall a livello di server vengono memorizzate temporaneamente il livello di database. Per aggiornare la cache, vedere [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Per ulteriori informazioni sulle regole firewall a livello di server, vedere [How to: configurare un firewall di server SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Creazione di regole del firewall a livello di database

Dopo aver configurato il primo firewall a livello di server, è consigliabile limitare l'accesso a determinate database. Se si specifica un indirizzo IP della regola firewall a livello di database che non è compreso nell'intervallo specificato nella regola del firewall a livello di server, solo i clienti che dispongono di indirizzi IP nell'intervallo a livello di database possono accedere al database. Si può avere un massimo 128 a livello di database regole del firewall per un database. Le regole firewall a livello di database per database master e utente possono essere create e gestite tramite Transact-SQL. Per ulteriori informazioni sulla configurazione di regole del firewall a livello di database, vedere [sp_set_database_firewall_rule (database di SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Livello di codice la gestione delle regole firewall

Oltre al portale di Azure, è possono gestire le regole firewall a livello di programmazione utilizzando Transact-SQL, API REST e Azure PowerShell. Le tabelle seguenti descrivono l'insieme di comandi disponibili per ogni metodo.


### <a name="transact-sql"></a>In Transact-SQL

| Stored Procedure o visualizzazione catalogo                                                           | Livello     | Descrizione                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [Sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | Server    | Vengono visualizzate le regole firewall server a livello corrente     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | Server    | Crea o aggiorna le regole del firewall a livello di server       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | Server    | Rimuove le regole firewall a livello di server                  |
| [Sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | Database  | Vengono visualizzate le regole firewall a livello di database corrente   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | Database  | Crea o aggiorna le regole del firewall a livello di database |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Database | Rimuove le regole del firewall a livello di database                |

### <a name="rest-api"></a>API REST

| API                  | Livello  | Descrizione                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [Elenco regole del Firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | Server | Vengono visualizzate le regole firewall server a livello corrente                 |
| [Creare regole Firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Server | Crea o aggiorna le regole del firewall a livello di server                   |
| [Impostare regole Firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | Server | Aggiorna le proprietà di una regola firewall a livello di server esistente |
| [Eliminare una regola del Firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Server | Rimuove le regole firewall a livello di server                              |


### <a name="azure-powershell"></a>PowerShell Azure

| Cmdlet                                                                                              | Livello  | Descrizione                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | Server | Restituisce le regole firewall server a livello corrente                  |
| [Nuovo AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | Server | Crea una nuova regola firewall a livello di server                         |
| [Set AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | Server | Aggiorna le proprietà di una regola firewall a livello di server esistente |
| [Rimuovi AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Server | Rimuove le regole firewall a livello di server                              |

> [AZURE.NOTE] Possono essere presenti backup come ritardo di cinque minuti per le impostazioni del firewall per rendere effettive le modifiche.

## <a name="troubleshooting-the-database-firewall"></a>Risoluzione dei problemi del firewall di database

Accesso al servizio di Database SQL di Microsoft Azure non funziona come previsto, considerare i seguenti punti:

- **La configurazione del firewall locale:** Prima che il computer poter accedere a Database SQL Azure, potrebbe essere necessario creare un'eccezione del firewall nel computer in uso per la porta TCP 1433. Se invece si creano connessioni all'interno del limite di Azure cloud, è necessario aprire porte aggiuntive. Per ulteriori informazioni, vedere il **V12 di Database SQL: esterne vs all'interno di** sezione delle [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Rete NAT (NAT):** A causa di NAT, l'indirizzo IP utilizzato dal computer per connettersi al Database SQL Azure potrebbe essere diverso da indirizzo IP indicato nelle impostazioni di configurazione IP del computer. Per visualizzare l'indirizzo IP che nel computer a cui connettersi Azure, accedere al portale e passare alla scheda **Configura** nel server che ospita i database. Nella sezione **Indirizzi IP consentiti** viene visualizzato l' **Indirizzo IP Client corrente** . Fare clic su **Aggiungi** a **Indirizzi IP consentiti** per consentire il computer per accedere al server.

- **Modifiche all'elenco Consenti non sono state applicate ancora:** Può essere molto simile a quella cinque minuti di ritardo per la configurazione del firewall Database SQL Azure per rendere effettive le modifiche.

- **L'account di accesso non è autorizzato o è stata usata una password errata:** Se un account di accesso non dispone delle autorizzazioni nel server di Database SQL Azure o la password utilizzata non è corretta, di effettuare la connessione al server di Database SQL Azure. Creazione di un'impostazione firewall fornisce solo i clienti a un'opportunità di provare la connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie. Per ulteriori informazioni sulla preparazione gli account di accesso, vedere Gestione dei database, gli account di accesso e gli utenti di Database SQL Azure.

- **Indirizzo IP dinamico:** Se si dispone di una connessione Internet con gli indirizzi IP dinamici e si verificano problemi con la attraverso il firewall, è possibile provare una delle seguenti soluzioni:

 - Chiedere il Provider di servizi Internet (ISP) per l'intervallo di indirizzi IP assegnato ai computer client che accede al server di Database SQL Azure e quindi aggiungere l'indirizzo IP come una regola del firewall.

 - Ottenere indirizzi IP statici invece per i computer client e quindi aggiungere gli indirizzi IP come le regole del firewall.

## <a name="next-steps"></a>Passaggi successivi

Per la procedura per trovare articoli sulla creazione di regole del firewall a livello di server e di database, vedere:

- [Configurare le regole firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
- [Configurare le regole firewall livello di server e database di Database SQL Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md)
- [Configurare le regole firewall a livello di server Database SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurare le regole firewall a livello di server di Database SQL Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md)

Per un'esercitazione sulla creazione di un database, vedere [creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md).
Per informazioni su come connettersi a un database SQL Azure da Apri origine o applicazioni di terze parti, vedere [Client introduttiva esempi di codice al Database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per capire come passare al database, vedere [gestire la sicurezza di access e login database](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## <a name="additional-resources"></a>Risorse aggiuntive

- [La protezione del database](sql-database-security.md)
- [Centro protezione per il motore di Database SQL Server e Database SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
