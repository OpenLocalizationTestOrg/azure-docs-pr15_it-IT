<properties
    pageTitle="Creare un nuovo pool di database flessibile con PowerShell | Microsoft Azure"
    description="Informazioni su come usare PowerShell per il Database di SQL Azure scalabilità risorse mediante la creazione di un pool di scalable database flessibile per la gestione di più database."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Creare un nuovo pool di database flessibile con PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Informazioni su come creare un [pool di database flessibile](sql-database-elastic-pool.md) uso dei cmdlet di PowerShell. 

I codici di errore comuni, vedere [codici di errore SQL per le applicazioni client di Database SQL: errore di connessione e altri problemi di Database](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Pool flessibile sono in genere disponibile (GA) in tutte le aree Azure ad eccezione di North Central US e Ovest India nel punto in cui è in anteprima.  GA flessibile pool in queste aree di sarà fornito più presto possibile. Inoltre, pool flessibile attualmente non supporta database che utilizzano [OLTP in memoria o in memoria analitica](sql-database-in-memory.md).


È necessario essere in esecuzione Azure PowerShell 1.0 o superiore. Per informazioni dettagliate, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Creare un nuovo pool

Il cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) crea un nuovo pool. I valori per eDTU al pool, min e max Dtus sono limitati dal valore di livello di servizio (basic, standard o premium). Vedere [limiti di eDTU e lo spazio di archiviazione per i pool flessibile e database flessibile](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Creare un nuovo database flessibile in un pool

Utilizzare il cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e impostare il parametro **ElasticPoolName** al pool di destinazione. Per spostare un database esistente in un pool, vedere [spostare un database in un pool flessibile](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Creare un pool e inserirvi più nuovi database 

Creazione di un numero elevato di database in un pool di tempo dopo completato tramite il portale e i cmdlet di PowerShell che creano un solo database alla volta. Per automatizzare la creazione in un pool di nuovo, vedere [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Esempio: creare un pool di utilizzo di PowerShell 

Questo script crea un nuovo gruppo di risorse Azure e un nuovo server. Quando richiesto, fornire un nome utente amministratore e una password per il nuovo server (non le credenziali di Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Passaggi successivi

- [Gestire il pool](sql-database-elastic-pool-manage-powershell.md)
- [Creare processi flessibile](sql-database-elastic-jobs-overview.md) Processi flessibile consentono di eseguire qualsiasi numero di database nel pool di script T-SQL.
- [Scalabilità con Database di SQL Azure](sql-database-elastic-scale-introduction.md): utilizzare gli strumenti di database flessibile a scalabilità.

