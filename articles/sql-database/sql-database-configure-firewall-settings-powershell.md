<properties
    pageTitle="Configurare le regole firewall a livello di server di Database SQL Azure tramite PowerShell | Microsoft Azure"
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


# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurare le regole firewall a livello di server di Database SQL Azure tramite PowerShell


> [AZURE.SELECTOR]
- [Panoramica](sql-database-firewall-configure.md)
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Database SQL Azure utilizza le regole del firewall per consentire le connessioni al server e database. È possibile definire impostazioni del livello di server e database firewall per il database master o un utente nel server di Database di SQL in modo selettivo consentire l'accesso al database.

> [AZURE.IMPORTANT] Per consentire alle applicazioni comuni di connettersi al server di database, è necessario attivare connessioni Azure. Per ulteriori informazioni sulle regole del firewall e consentire le connessioni da Azure, vedere [Firewall di Database SQL Azure](sql-database-firewall-configure.md). Se invece si creano connessioni all'interno del limite di Azure cloud, è necessario aprire alcune porte TCP aggiuntive. Per ulteriori informazioni, vedere la "V12 del Database SQL: esterne vs all'interno di" sezione di [porte 1433 per ADO.NET 4.5 e V12 di Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Creare regole del firewall server

Firewall a livello di server è possibile creare regole, aggiornate ed eliminate tramite PowerShell Azure.

Per creare una nuova regola firewall a livello di server, eseguire la [New-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) cmdlet. Nell'esempio seguente consente a un intervallo di indirizzi IP nel server di Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'       

Per modificare una regola firewall a livello di server esistente, eseguire la [Set-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx) cmdlet. Nell'esempio seguente viene modificato l'intervallo di indirizzi IP accettabili per la regola denominata ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Per eliminare una regola firewall a livello di server esistente, eseguire la [Rimuovi-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx) cmdlet. Nell'esempio seguente elimina la regola denominata ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Gestire le regole firewall tramite PowerShell

È anche possibile usare PowerShell per gestire le regole firewall. Per ulteriori informazioni, vedere gli argomenti seguenti:

* [Nuova AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)
* [Rimuovi AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603586(v=azure.300\).aspx)


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come utilizzare Transact-SQL per creare regole del firewall di livello di server e di database, vedere [le regole firewall livello di server e database di configurare il Database SQL Azure mediante T-SQL](sql-database-configure-firewall-settings-tsql.md).

Per informazioni su come creare regole del firewall a livello di server con altri metodi, vedere:

- [Configurare le regole firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
- [Configurare le regole firewall a livello di server di Database SQL Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md)

Per un'esercitazione sulla creazione di un database, vedere [creare un database SQL in minuti tramite il portale di Azure](sql-database-get-started.md).
Per informazioni sulla connessione a un database SQL Azure da Apri origine o applicazioni di terze parti, vedere [Client introduttiva esempi di codice al Database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per capire come passare al database, vedere [gestire la sicurezza di access e login database](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Risorse aggiuntive

- [La protezione del database](sql-database-security.md)
- [Centro protezione per il motore di Database SQL Server e Database SQL Azure](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->
