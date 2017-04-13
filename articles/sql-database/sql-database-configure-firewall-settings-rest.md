<properties
    pageTitle="Regole del firewall a livello di server Database SQL Azure tramite l'API REST | Microsoft Azure"
    description="Informazioni su come configurare il firewall per gli indirizzi IP di accedere ai database SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/09/2016"
    ms.author="sstein"/>


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurare le regole firewall a livello di server di Database SQL Azure tramite l'API REST


> [AZURE.SELECTOR]
- [Panoramica](sql-database-firewall-configure.md)
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni al server e database. È possibile definire impostazioni del livello di server e database firewall per lo schema o in un database utente del server di Database SQL Azure in modo selettivo consentire l'accesso al database.

> [AZURE.IMPORTANT] Per consentire alle applicazioni comuni di connettersi al server di database, è necessario attivare connessioni Azure. Per ulteriori informazioni sulle regole del firewall e consentire le connessioni da Azure, vedere [Firewall di Database SQL Azure](sql-database-firewall-configure.md). Se invece si creano connessioni all'interno del limite di Azure cloud, è necessario aprire alcune porte TCP aggiuntive. Per ulteriori informazioni, vedere il **V12 di Database SQL: esterne vs all'interno di** sezione delle [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Gestire le regole firewall a livello di server tramite l'API REST
1. Gestione delle regole firewall tramite API REST devono essere autenticati. Per informazioni, vedere [la Guida per gli sviluppatori di autorizzazione con l'API di gestione risorse Azure](../resource-manager-api-authentication.md).
2. Possibile creare regole di livello di server, aggiornata o eliminata utilizzando API REST

    Per creare o aggiornare una regola del firewall a livello di server, eseguire il metodo di caricamento mediante le operazioni seguenti:
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    Corpo della richiesta

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    Per rimuovere una regola firewall a livello di server esistente, eseguire il metodo di eliminazione mediante le operazioni seguenti:
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Gestire le regole firewall tramite l'API REST

* [Creazione o aggiornamento di regole Firewall](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Eliminare una regola del Firewall](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Ottenere regola Firewall](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Elenco di tutte le regole Firewall](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>Passaggi successivi

Per istruzioni sulla articolo su come utilizzare Transact-SQL per creare regole del firewall a livello di server e di database, vedere [regole del firewall di livello di server e database di configurare il Database SQL Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Per come articoli sulla creazione di regole del firewall a livello di server con altri metodi, vedere: 

- [Configurare le regole firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
- [Configurare le regole firewall a livello di server Database SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)

Per un'esercitazione sulla creazione di un database, vedere [creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md).
Per informazioni su come connettersi a un database SQL Azure da Apri origine o applicazioni di terze parti, vedere [Client introduttiva esempi di codice al Database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per capire come passare al database, vedere [gestire la sicurezza di access e login database](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Risorse aggiuntive

- [La protezione del database](sql-database-security.md)
- [Centro protezione per il motore di Database SQL Server e Database SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 
