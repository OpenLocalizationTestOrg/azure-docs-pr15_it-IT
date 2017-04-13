<properties 
    pageTitle="Copiare un database di SQL Azure con PowerShell | Microsoft Azure" 
    description="Creare una copia di un database di SQL Azure con PowerShell" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>Copiare un database di SQL Azure con PowerShell


> [AZURE.SELECTOR]
- [Panoramica](sql-database-copy.md)
- [Portale di Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

In questo articolo viene illustrato come copiare un database SQL con PowerShell nello stesso server, a un altro server o copiare un database in un [pool di database flessibile](sql-database-elastic-pool.md). Copia del database viene utilizzato il cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) . 


Per completare in questo articolo, è necessario quanto segue:

- Un database di SQL Azure (un database da copiare). Se non si dispone di un database SQL, creare una seguendo i passaggi descritti in questo articolo: [creare il primo Database di SQL Azure](sql-database-get-started.md).
- La versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).


Molte nuove funzionalità di Database SQL sono supportate solo se si usa il [modello di distribuzione di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md), in modo esempi utilizzano i [cmdlet di PowerShell di Database SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) per Manager delle risorse. Il modello di distribuzione classica esistente [cmdlet (classico) di Database SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) sono supportati per la compatibilità, ma è consigliabile che utilizzare i cmdlet Gestione risorse.


>[AZURE.NOTE] A seconda delle dimensioni del database, l'operazione di copia può richiedere del tempo per completare.


## <a name="copy-a-sql-database-to-the-same-server"></a>Copiare un database SQL nello stesso server

Per creare la copia nello stesso server, omettere il `-CopyServerName` parametro (o impostarlo nello stesso server).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copiare un database SQL in un server diverso

Per creare la copia in un server diverso, includere il `-CopyServerName` parametro e impostarla su un server diverso. Il server *Copia* esista già. Se è in un altro gruppo di risorse, quindi è necessario includere anche la `-CopyResourceGroupName` parametro.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Copiare un database SQL di un pool di database flessibile

Per creare una copia di un database SQL in un pool, impostare il `-ElasticPoolName` parametro a un pool esistente.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Risolvere gli account di accesso

Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [risolvere gli account di accesso](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>Script di PowerShell di esempio

Il seguente script prende in considerazione tutti i gruppi di risorse, server, e il pool esiste già (sostituire i valori delle variabili con le risorse esistenti). Tutti gli elementi deve essere presente, fatta eccezione per la copia del database.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica su come copiare un Database di SQL Azure, vedere [copiare un database SQL Azure](sql-database-copy.md) .
- Vedere [copiare un database di SQL Azure tramite il portale di Azure](sql-database-copy-portal.md) per copiare un database tramite il portale di Azure.
- Vedere [Copia un database SQL Azure mediante T-SQL](sql-database-copy-transact-sql.md) per copiare un database utilizzando Transact-SQL.
- Informazioni su [come gestire la protezione del database di SQL Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e gli account di accesso durante la copia di un database in un altro server logico.


## <a name="additional-resources"></a>Risorse aggiuntive

- [Nuovo AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Gestire gli account di accesso](sql-database-manage-logins.md)
- [Connettersi al Database SQL di SQL Server Management Studio ed eseguire una query di Transact-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)
- [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- [Documentazione di Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Riferimento sui Cmdlet di PowerShell Database SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx)
