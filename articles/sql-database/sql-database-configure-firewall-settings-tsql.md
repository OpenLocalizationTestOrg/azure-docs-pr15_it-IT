<properties
    pageTitle="Regole del firewall a livello di database e a livello di server Database SQL Azure mediante T-SQL | Microsoft Azure"
    description="Informazioni su come configurare il firewall per gli indirizzi IP di accedere ai database SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurare le regole firewall livello di server e database di Database SQL Azure mediante T-SQL


> [AZURE.SELECTOR]
- [Panoramica](sql-database-firewall-configure.md)
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni al server e database. È possibile definire impostazioni del livello di server e database firewall per lo schema o in un database utente del server di Database SQL Azure in modo selettivo consentire l'accesso al database.

> [AZURE.IMPORTANT] Per consentire alle applicazioni comuni di connettersi al server di database, è necessario attivare connessioni Azure. Per ulteriori informazioni sulle regole del firewall e consentire le connessioni da Azure, vedere [Firewall di Database SQL Azure](sql-database-firewall-configure.md). Se invece si creano connessioni all'interno del limite di Azure cloud, è necessario aprire alcune porte TCP aggiuntive. Per ulteriori informazioni, vedere il **V12 di Database SQL: esterne vs all'interno di** sezione delle [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="server-level-firewall-rules"></a>Regole del firewall a livello di server

Solo il server a livello accesso principale o l'amministratore di Azure Active Directory è possibile creare una regola del firewall a livello di server tramite Transact-SQL.

1. Avviare una finestra di query e connettersi al database master virtuale tramite SQL Server Management Studio.
2. Possano selezionate, create, aggiornate o eliminate dalla finestra della query regole del firewall a livello di server.
3. Per creare o aggiornare le regole del firewall a livello di server, eseguire la `sp_set_firewall_rule` stored procedure di. Nell'esempio seguente consente a un intervallo di indirizzi IP nel server di Contoso.<br/>Innanzitutto le regole già esistono.

        SELECT * FROM sys.firewall_rules ORDER BY name;

    Aggiungere una regola del firewall.

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    Per eliminare una regola del firewall a livello di server, eseguire la sp_delete_firewall_rule stored procedure. Nell'esempio seguente elimina la regola denominata ContosoFirewallRule.
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Per ulteriori informazioni su queste stored procedure, vedere [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Regole del firewall a livello di database

Solo utente del database con autorizzazione **controllo** sul database (ad esempio, il proprietario del database) è possibile creare una regola del firewall a livello di database.

1. Dopo la creazione di un firewall a livello di server per l'indirizzo IP, avviare una finestra query tramite il portale classica o SQL Server Management Studio.
2. Connetti a database per il quale si desidera creare una regola del firewall a livello di database.

    Per creare un nuovo o aggiornare una regola firewall a livello di database esistente, eseguire la `sp_set_database_firewall_rule` stored procedure di. Nell'esempio seguente viene creata una nuova regola firewall denominata ContosoFirewallRule.
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    Per eliminare una regola firewall a livello di database esistente, eseguire la `sp_delete_database_firewall_rule` stored procedure di. Nell'esempio seguente elimina la regola denominata ContosoFirewallRule.
`
   EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Per ulteriori informazioni su queste stored procedure, vedere [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Passaggi successivi

Per come articoli sulla creazione di regole del firewall a livello di server con altri metodi, vedere: 

- [Configurare le regole firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
- [Configurare le regole firewall a livello di server Database SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurare le regole firewall a livello di server di Database SQL Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md)

Per un'esercitazione sulla creazione di un database, vedere [creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md).
Per informazioni su come connettersi a un database SQL Azure da Apri origine o applicazioni di terze parti, vedere [Client introduttiva esempi di codice al Database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per capire come passare al database, vedere [gestire la sicurezza di access e login database](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Risorse aggiuntive

- [La protezione del database](sql-database-security.md)
- [Centro protezione per il motore di Database SQL Server e Database SQL Azure](https://msdn.microsoft.com/library/bb510589)
