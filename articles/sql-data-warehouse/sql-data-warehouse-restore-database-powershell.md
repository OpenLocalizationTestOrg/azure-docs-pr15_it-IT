<properties
   pageTitle="Ripristinare un SQL Azure Data Warehouse (PowerShell) | Microsoft Azure"
   description="Attività di PowerShell per ripristinare un Data Warehouse SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Ripristinare un SQL Azure Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Panoramica][]
- [Portale][]
- [PowerShell][]
- [RESTO][]

In questo articolo si imparerà a ripristinare un Data Warehouse SQL Azure tramite PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

**Verificare la capacità DTU.** Ogni SQL Data Warehouse è ospitato da SQL server (ad esempio myserver.database.windows.net) che ha una quota DTU predefinita.  Per poter ripristinare un Data Warehouse SQL, verificare che l'istanza di SQL server è abbastanza DTU rimanente per il database da ripristinare. Per informazioni su come calcolare DTU necessari o richiedere ulteriori DTU, vedere [richiedere una modifica di quota DTU][].

### <a name="install-powershell"></a>Installare PowerShell

Per utilizzare PowerShell Azure con SQL Data Warehouse, è necessario installare Azure PowerShell 1.0 o versione successiva.  È possibile controllare la versione eseguendo **modulo Get - ListAvailable-nome AzureRM**.  La versione più recente può essere installata da [Installazione guidata piattaforma Web Microsoft][].  Per ulteriori informazioni sull'installazione della versione più recente, vedere [come installare e configurare Azure PowerShell][].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o in pausa

Per ripristinare un database da uno snapshot utilizzare il cmdlet di PowerShell [AzureRmSqlDatabase Ripristina][] .

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure e nell'elenco di tutte le sottoscrizioni associate all'account.
3. Selezionare l'abbonamento che contiene il database da ripristinare.
4. Elencare i punti di ripristino del database.
5. Selezionare il punto di ripristino desiderate utilizzando il RestorePointCreationDate.
6. Ripristinare il database al punto di ripristino desiderate.
7. Verificare che il database ripristinato sia online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Al termine dell'operazione di ripristino, è possibile configurare il database recuperato dal seguenti [configurare il database dopo il ripristino][].


## <a name="restore-a-deleted-database"></a>Ripristinare un database eliminato

Per ripristinare un database eliminato, utilizzare il cmdlet [AzureRmSqlDatabase Ripristina][] .

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure e nell'elenco di tutte le sottoscrizioni associate all'account.
3. Selezionare l'abbonamento che contiene il database eliminato da ripristinare.
4. È possibile ottenere il database eliminato.
5. Ripristinare il database eliminato.
6. Verificare che il database ripristinato sia online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] Al termine dell'operazione di ripristino, è possibile configurare il database recuperato dal seguenti [configurare il database dopo il ripristino][].


## <a name="restore-from-an-azure-geographical-region"></a>Ripristinare da un'area geografica Azure

Per ripristinare un database, utilizzare il cmdlet [AzureRmSqlDatabase Ripristina][] .

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure e nell'elenco di tutte le sottoscrizioni associate all'account.
3. Selezionare l'abbonamento che contiene il database da ripristinare.
4. È possibile ottenere il database che si desidera ripristinare.
5. Creare la richiesta di ripristino del database.
6. Verificare lo stato del database ripristinato geografico.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] Per configurare il database dopo il ripristino è stato completato, vedere [configurare il database dopo il ripristino][]. 


Il database ripristinato sarà attivata TDE se il database di origine è abilitato TDE.


## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle funzionalità di continuità aziendale delle edizioni di Database SQL Azure, leggere la [Panoramica di continuità aziendale di Database SQL Azure][].

<!--Image references-->

<!--Article references-->
[Panoramica di continuità aziendale Database SQL Azure]: sql-database-business-continuity.md
[Richiedere una modifica di quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurare il database dopo il ripristino]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Come installare e configurare PowerShell Azure]: powershell-install-configure.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurare il database dopo il ripristino]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Ripristino AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps
