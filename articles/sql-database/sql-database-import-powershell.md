<properties
    pageTitle="Importare un file BACPAC per creare un database SQL Azure tramite PowerShell | Microsoft Azure"
    description="Importare un file BACPAC per creare un database SQL Azure tramite PowerShell"
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
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importare un file BACPAC per creare un database SQL Azure tramite PowerShell

**Singolo database**

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SQL SERVER MANAGEMENT STUDIO](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

In questo articolo vengono fornite istruzioni per la creazione di un database SQL Azure importando un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) con PowerShell.

Il database viene creato da un file BACPAC (.bacpac) importato da un contenitore di blob Azure lo spazio di archiviazione. Se non si dispone di un file BACPAC in Azure lo spazio di archiviazione, vedere [archiviare un database di SQL Azure in un file BACPAC tramite PowerShell](sql-database-export-powershell.md). Se si dispone già di un file BACPAC non presente nel sistema di archiviazione Azure, [utilizzare AzCopy per caricarlo facilmente al proprio account di archiviazione Azure](../storage/storage-use-azcopy.md#blob-upload).

> [AZURE.NOTE] Database SQL Azure creati e gestiti automaticamente per ogni database di utente che è possibile ripristinare l'esecuzione di backup. Per informazioni dettagliate, vedere [automatizzato di Database SQL di backup](sql-database-automated-backups.md).


Per importare un database SQL, è necessario quanto segue:

- Un abbonamento Azure. Se è necessario un abbonamento a Azure, è sufficiente fare clic su **Versione di valutazione gratuita** nella parte superiore della pagina e quindi tornare alla fine di questo articolo.
- Un file BACPAC del database a cui si desidera importare. Il BACPAC deve essere in un contenitore di [account Azure archiviazione](../storage/storage-create-storage-account.md) blob.



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>Configurare le variabili per l'ambiente

Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e dell'account di archiviazione.

Il nome del server deve essere un server presente nella sottoscrizione selezionata nel passaggio precedente. Dovrebbe essere il server desiderato per essere creati nel database. Importazione di un database direttamente in un pool flessibile non è supportata. Ma è possibile importare in un solo database e quindi spostare il database in un pool.

Il nome del database è il nome desiderato per il nuovo database.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Le seguenti variabili sono dall'account di archiviazione in cui si trova il BACPAC. Nel [portale di Azure](https://portal.azure.com), passare al proprio account di archiviazione per ottenere questi valori. È possibile trovare la chiave primaria access, fare clic su **tutte le impostazioni** e quindi **chiavi** blade del proprio account di archiviazione.

Il nome blob è il nome di un file BACPAC esistente che si desidera creare il database da. È necessario includere l'estensione .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Esecuzione di [Get-Credential] (https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) cmdlet Visualizza una finestra in cui viene richiesto il nome utente e la password. Immettere l'account di amministratore e la password per il server di Database SQL ($ServerName dall'alto) e non il nome utente e password per l'account Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importare il database

Questo comando invia una richiesta di database di importazione al servizio. A seconda delle dimensioni del database, l'operazione di importazione può richiedere del tempo per completare.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Monitorare l'avanzamento dell'operazione

Dopo aver eseguito [nuovo AzureRmSqlDatabaseImport] (https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx), è possibile verificare lo stato della richiesta eseguendo la [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Importa script di PowerShell di Database SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come connettersi e un Database SQL importato della query, vedere [Connetti a Database SQL di SQL Server Management Studio ed eseguire una query di esempio T-SQL](sql-database-connect-query-ssms.md)
