<properties
    pageTitle="Gestire il Database SQL Azure con PowerShell | Microsoft Azure"
    description="Gestione di Database SQL Azure con PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="sstein"/>

# <a name="manage-azure-sql-database-with-powershell"></a>Gestire il Database SQL Azure con PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-manage-portal.md)
- [In Transact-SQL (SQL Server Management Studio)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Questo argomento illustra i cmdlet di PowerShell che consentono di eseguire molte attività di Database SQL Azure. Per un elenco completo, vedere [Cmdlet per Database di SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx).


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse per il Database SQL e le relative risorse Azure con i cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx) .

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Per ulteriori informazioni, vedere [Uso Azure PowerShell Gestione risorse Azure](../powershell-azure-resource-manager.md).
Per un esempio di script, vedere [creare un database SQL di script di PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="create-a-sql-database-server"></a>Creare un Database SQL server

Creare un server di Database SQL con i cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) . Sostituire *server1* con il nome del server. I nomi dei server deve essere univoci in tutti i server di Database SQL Azure. Se il nome del server già viene accettato, viene visualizzato un errore. Questo comando può richiedere alcuni minuti. Gruppo risorse contiene già l'abbonamento.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Per ulteriori informazioni, vedere [che cos'è il Database di SQL](sql-database-technical-overview.md). Per un esempio di script, vedere [creare un database SQL di script di PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-server-firewall-rule"></a>Creare una regola firewall di Database SQL server

Creare una regola del firewall per accedere al server con i cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) . Eseguire il comando seguente, sostituendo gli indirizzi IP di inizio e fine con valori validi per il client. Il gruppo di risorse e il server contiene già l'abbonamento.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Per consentire l'accesso altri servizi Azure al server, creare una regola e si impostano entrambe le `-StartIpAddress` e `-EndIpAddress` su **0.0.0.0**. Questa regola firewall speciali consente tutto il traffico Azure accedere al server.

Per ulteriori informazioni, vedere [Firewall di Database SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Per un esempio di script, vedere [creare un database SQL di script di PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-blank"></a>Creare un database SQL (vuoto)

Creare un database con i cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) . Il gruppo di risorse e il server contiene già l'abbonamento. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Per ulteriori informazioni, vedere [che cos'è il Database di SQL](sql-database-technical-overview.md). Per un esempio di script, vedere [creare un database SQL di script di PowerShell](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="change-the-performance-level-of-a-sql-database"></a>Modificare il livello di prestazioni di un database SQL

Ridimensionare il database verso l'alto o verso il basso con il cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) . Raggruppamento delle risorse, server e database deve esistere già nell'abbonamento. Impostare il `-RequestedServiceObjectiveName` in uno spazio singolo (ad esempio il frammento di codice seguente) per livello di base. Impostarlo su *S0*, *S1*, *P1*, *P6*e così via, come illustrato nell'esempio precedente per altri livelli.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Per ulteriori informazioni, vedere [prestazioni e le opzioni del Database di SQL: informazioni sulle funzionalità disponibili in ogni livello di servizio](sql-database-service-tiers.md). Per uno script di esempio, vedere [script di PowerShell di esempio per modificare il livello del servizio di livello e le prestazioni del database SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server

Copiare un database SQL nello stesso server con i cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx) . Impostare il `-CopyServerName` e `-CopyResourceGroupName` per gli stessi valori per il gruppo di risorse e server di database origine.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Per ulteriori informazioni, vedere [copiare un Database di SQL Azure](sql-database-copy.md). Per un esempio di script, vedere [copiare un database SQL di script di PowerShell](sql-database-copy-powershell.md#example-powershell-script).


## <a name="delete-a-sql-database"></a>Eliminare un database SQL

Eliminare un database SQL con il cmdlet [AzureRmSqlDatabase Rimuovi](https://msdn.microsoft.com/library/azure/mt619368.aspx) . Raggruppamento delle risorse, server e database deve esistere già nell'abbonamento.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="delete-a-sql-database-server"></a>Eliminare un Database SQL server

Eliminare un server con il cmdlet [AzureRmSqlServer Rimuovi](https://msdn.microsoft.com/library/azure/mt603488.aspx) .

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="create-and-manage-elastic-database-pools-using-powershell"></a>Creare e gestire i pool di database flessibile tramite PowerShell

Per informazioni dettagliate sulla creazione di database flessibile pool tramite PowerShell, vedere [creare un nuovo pool di database flessibile con PowerShell](sql-database-elastic-pool-create-powershell.md).

Per informazioni dettagliate sulla gestione di database flessibile pool tramite PowerShell, vedere [monitorare e gestire un pool di database flessibile con PowerShell](sql-database-elastic-pool-manage-powershell.md).



## <a name="related-information"></a>Informazioni correlate

- [Cmdlet di Database SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Riferimento sui Cmdlet di Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx)
