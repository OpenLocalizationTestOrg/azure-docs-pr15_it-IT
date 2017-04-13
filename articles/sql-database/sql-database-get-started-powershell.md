<properties
    pageTitle="Nuova impostazione di Database SQL con PowerShell | Microsoft Azure"
    description="Vedere creare un database SQL con PowerShell. Attività comuni di configurazione del database può essere gestita tramite i cmdlet di PowerShell."
    keywords="Crea nuovo database di sql, il programma di installazione di database"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>Creare un database SQL ed eseguire attività comuni di configurazione del database con i cmdlet di PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Informazioni su come creare un database SQL utilizzando i cmdlet di PowerShell. (Per la creazione di database flessibile, vedere [creare un nuovo pool di database flessibile con PowerShell](sql-database-elastic-pool-create-powershell.md)).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>Impostazione del database: creare un gruppo di risorse, server e regole firewall

Dopo avere accesso per eseguire i cmdlet per la sottoscrizione di Azure selezionata, il passaggio successivo è stabilire il gruppo di risorse che contiene il server in cui verrà creato il database. È possibile modificare il comando successivo da utilizzare qualsiasi percorso valido che scelto. Eseguire **(Get-AzureRmLocation | Where-Object {$_. Provider - eq "Microsoft.Sql"}). Percorso** per ottenere un elenco di percorsi validi.

Eseguire il seguente comando per creare un gruppo di risorse:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>Creare un server

Database SQL vengono creati all'interno di server di Database SQL Azure. Eseguire il [New-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) per creare un server. Il nome del server deve essere univoco in tutti i server di Database SQL Azure. Se il nome del server già viene accettato, viene visualizzato un errore. Anche importante notare che questo comando può richiedere alcuni minuti. È possibile modificare il comando da utilizzare un punto qualsiasi valido che scelto, ma è consigliabile utilizzare nella stessa posizione che è stato usato per il gruppo di risorse creato nel passaggio precedente.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Quando si esegue questo comando, viene richiesto il nome utente e la password. Non immettere le credenziali Azure. In alternativa, immettere il nome utente e la password per creare l'amministratore del server. Lo script nella parte inferiore di questo articolo viene illustrato come impostare le credenziali del server nel codice.

Dettagli sul server vengono visualizzate dopo il server è stato creato correttamente.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>Configurare una regola del firewall server per consentire l'accesso al server

Per accedere al server, è necessario stabilire una regola del firewall. Eseguire il [New-AzureRmSqlServerFirewallRule] (comando https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx), sostituendo gli indirizzi IP di inizio e fine con valori validi per il computer.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Dopo la regola viene creata, vengono visualizzati i dettagli della regola firewall.

Per consentire altri servizi di Azure ad accedere al server, aggiungere una regola del firewall e impostare StartIpAddress sia EndIpAddress su 0.0.0.0. Questa regola consente il traffico Azure da tutti gli abbonamenti Azure per accedere al server.

Per ulteriori informazioni, vedere [Firewall di Database SQL Azure](sql-database-firewall-configure.md).


## <a name="create-a-sql-database"></a>Creare un database SQL

A questo punto si dispone di un gruppo di risorse, un server e una regola del firewall configurata in modo che è possibile accedere al server.

Le operazioni seguenti [nuovo AzureRmSqlDatabase] (comando https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) consente di creare un database SQL (vuoto) il livello di servizio Standard, con un livello di prestazioni S1:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Dopo il database viene creato, vengono visualizzati i dettagli di database.

## <a name="create-a-sql-database-powershell-script"></a>Creare un database SQL di script di PowerShell

Il seguente script di PowerShell crea un database SQL e tutte le risorse dipendenti. Sostituisci tutto `{variables}` con valori specifici per la sottoscrizione e le risorse (quando si impostano i valori e rimuovere **{}** ).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un database SQL di eseguire attività di configurazione di base del database, si è pronti per le operazioni seguenti:

- [Gestire il Database SQL con PowerShell](sql-database-manage-powershell.md)
- [Connettersi al Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md)


## <a name="additional-resources"></a>Risorse aggiuntive

- [Cmdlet di Database SQL azure] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Database SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)
