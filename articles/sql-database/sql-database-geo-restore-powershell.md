<properties
    pageTitle="Ripristinare un Database di SQL Azure da un backup geografico ridondanti (PowerShell) | Microsoft Azure"
    description="Ripristinare un Database di SQL Azure in un nuovo server da un backup geografico ridondanti"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Ripristinare un Database di SQL Azure da un backup ridondanti geografico tramite PowerShell


> [AZURE.SELECTOR]
- [Panoramica](sql-database-recovery-using-backups.md)
- [Geografico Ripristina: Portale di Azure](sql-database-geo-restore-portal.md)

In questo articolo viene illustrato come ripristinare il database in un nuovo server tramite geografico Ripristina. Può essere gestito tramite PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Geografico-ripristino del database in un database autonomo

1. Eseguire il backup geografico ridondanti del database che si desidera ripristinare utilizzando il [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Avviare il ripristino da backup geografico ridondanti utilizzando il [ripristino-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Geografico-ripristino del database in un pool di database flessibile

1. Eseguire il backup geografico ridondanti del database che si desidera ripristinare utilizzando il [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Avviare il ripristino da backup geografico ridondanti utilizzando il [ripristino-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet. Specificare il nome del pool in che si desidera ripristinare il database.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md).
- Per informazioni su backup Database di SQL Azure automatica, vedere [automatizzato di Database SQL di backup](sql-database-automated-backups.md).
- Per informazioni sull'uso di backup automatico per il ripristino, vedere [ripristinare un database da un backup servizio avviato](sql-database-recovery-using-backups.md).
- Per informazioni sulle opzioni di ripristino più veloce, vedere [Replica di geografico attivo](sql-database-geo-replication-overview.md).  
- Per informazioni sull'uso di backup automatico per l'archiviazione, vedere [copia del database](sql-database-copy.md).
