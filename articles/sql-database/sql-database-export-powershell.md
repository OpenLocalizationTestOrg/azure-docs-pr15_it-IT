<properties
    pageTitle="Archiviare un database di SQL Azure in un file BACPAC tramite PowerShell"
    description="Archiviare un database di SQL Azure in un file BACPAC tramite PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>Archiviare un database di SQL Azure in un file BACPAC tramite PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


In questo articolo vengono fornite istruzioni per l'archiviazione del database di SQL Azure in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) (archiviato in archiviazione Blob Azure) tramite PowerShell.

Quando è necessario creare un archivio di un database di SQL Azure, è possibile esportare lo schema di database e i dati in un file BACPAC. Un file BACPAC è semplicemente un file ZIP con estensione .bacpac. Un file BACPAC può essere creato in un secondo momento nell'archiviazione Blob Azure o in un archivio locale in una posizione locale. Può inoltre essere importato nel Database di SQL Azure o in un'installazione di SQL Server locale.

**Considerazioni**

- Per un archivio per coerenti, è non necessario assicurarsi che nessuna scrittura di attività viene eseguita durante l'esportazione o che desidera esportare i dati di una [copia coerente a livello](sql-database-copy.md) del database SQL Azure.
- La dimensione massima di un file BACPAC archiviato in archiviazione Blob Azure è 200 GB. Per archiviare un file BACPAC più grande nell'archivio locale, l'utilità [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) prompt dei comandi. Questa utilità viene fornito con Visual Studio e SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) l'ultima versione di SQL Server Data Tools per ottenere questa utilità.
- Archiviazione allo spazio di archiviazione di Azure premium tramite un file BACPAC non è supportata.
- Se l'operazione di esportazione supera 20 ore, potrebbero essere annullata. Per migliorare le prestazioni durante l'esportazione, è possibile:
 - Aumentare temporaneamente il livello di servizio.
 - Cessazione tutti leggere e scrivere attività durante l'esportazione.
 - Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, un'esportazione potrebbe non riuscire se sono necessari più di 6-12 ore. Ciò avviene perché il servizio di esportazione deve completare un'analisi della tabella per tentare di esportare l'intera tabella. Un buon modo per stabilire se le tabelle sono ottimizzate per l'esportazione è eseguire **DBCC SHOW_STATISTICS** e assicurarsi che *RANGE_HI_KEY* non null e il relativo valore ha distribuzione efficace. Per informazioni dettagliate, vedere [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [AZURE.NOTE] BACPACs non devono essere utilizzato per il backup e ripristino. Database SQL Azure viene creato automaticamente backup per ogni database utente. Per informazioni dettagliate, vedere [automatizzato di Database SQL di backup](sql-database-automated-backups.md).

Per completare in questo articolo, è necessario quanto segue:

- Un abbonamento Azure.
- Un database di SQL Azure.
- Un [account di archiviazione Standard di Azure](../storage/storage-create-storage-account.md), con un contenitore di blob per archiviare la BACPAC nello spazio di memorizzazione standard.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]




## <a name="export-your-database"></a>Esportare il database

[Nuovo-AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx) cmdlet invia una richiesta di database di esportazione al servizio. A seconda delle dimensioni del database, l'operazione di esportazione può richiedere del tempo per completare.

> [AZURE.IMPORTANT] Per garantire un file BACPAC coerente, è necessario prima [creare una copia del database](sql-database-copy-powershell.md)e quindi esportare la copia del database.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorare l'avanzamento dell'operazione di esportazione

Dopo aver eseguito [nuovo AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx), è possibile verificare lo stato della richiesta eseguendo [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx). Esecuzione di questa immediatamente dopo la richiesta in genere restituisce **stato: in corso**. Quando viene visualizzato il **stato: ha avuto esito positivo** l'esportazione è stata completata.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Esempio di database SQL di esportazione

Nell'esempio seguente consente di esportare un database SQL di un BACPAC e quindi viene illustrato come controllare lo stato dell'operazione di esportazione.

Per eseguire l'esempio, esistono alcune variabili che è necessario sostituire con i valori specifici per il proprio account di database e lo spazio di archiviazione. Nel [portale di Azure](https://portal.azure.com), passare al proprio account di archiviazione per ottenere il nome dell'account di archiviazione, nome del contenitore di blob e valore chiave. È possibile trovare la chiave facendo clic sui **tasti di scelta** nella blade account di archiviazione.

Sostituire le operazioni seguenti `VARIABLE-VALUES` con i valori per le risorse di Azure specifiche. Il nome del database è il database esistente che si desidera esportare.



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come importare un database SQL Azure tramite Powershell, vedere [importare un BACPAC tramite PowerShell](sql-database-import-powershell.md).


## <a name="additional-resources"></a>Risorse aggiuntive

- [Nuova AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)