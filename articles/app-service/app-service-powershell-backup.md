<properties
    pageTitle="Usare PowerShell per eseguire il backup e ripristinare le applicazioni di servizio di App"
    description="Informazioni su come usare PowerShell per eseguire il backup e ripristinare un'applicazione di servizio App Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Usare PowerShell per eseguire il backup e ripristinare le applicazioni di servizio di App

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API REST](../app-service-web/websites-csm-backup.md)

Informazioni su come usare Azure PowerShell per eseguire il backup e ripristinare [le applicazioni di servizio di App](https://azure.microsoft.com/services/app-service/web/). Per ulteriori informazioni sui backup di app web, inclusi i requisiti e limitazioni, vedere [eseguire il backup un'app web nel servizio App Azure](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Prerequisiti
Per usare PowerShell per gestire i backup app, è necessario quanto segue:

- **Risposte SA URL** che consente l'accesso in lettura e scrittura a un contenitore di archiviazione Azure. Per visualizzare una spiegazione di URL SA, vedere [informazioni sul modello di SA](../storage/storage-dotnet-shared-access-signature-part-1.md) . Per esempi di gestione di archiviazione Azure con PowerShell, vedere [Uso di PowerShell Azure con lo spazio di archiviazione di Azure](../storage/storage-powershell-guide-full.md) .
- **Stringa di connessione di un database** se si desidera eseguire il backup di un database insieme a un'applicazione web.

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Come generare un URL sa da utilizzare con i cmdlet di backup app web
È possibile generare un URL sa con PowerShell. Ecco un esempio di come generare uno che può essere utilizzata con i cmdlet descritti in questo articolo.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Installare PowerShell Azure 1.3.2 o versione successiva

Per istruzioni sull'installazione e utilizzo di PowerShell di Azure, vedere [Uso di PowerShell Azure Gestione risorse Azure](../powershell-install-configure.md) .

## <a name="create-a-backup"></a>Creare una copia di backup

Utilizzare il cmdlet New-AzureRmWebAppBackup per creare una copia di backup di un'app web.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Verrà creata una copia di backup con un nome generato automaticamente. Se si desidera specificare un nome per il backup, utilizzare il parametro facoltativo BackupName.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Per includere un database di backup, creare un'impostazione di backup del database mediante il cmdlet New-AzureRmWebAppDatabaseBackupSetting, quindi specificare tale impostazione nel parametro database del cmdlet New-AzureRmWebAppBackup. Il parametro database accetta una matrice di impostazioni del database, che consente di eseguire il backup più di un database.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Ottenere l'esecuzione di backup

Il cmdlet Get-AzureRmWebAppBackupList restituisce una matrice di tutti i backup per un'app web. Specificare il nome del web app e il relativo gruppo di risorse.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Per ottenere una copia di backup specifico, utilizzare il cmdlet Get-AzureRmWebAppBackup.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

È anche possibile inviare un oggetto di app web in alcuno dei cmdlet di gestione del backup per semplicità di utilizzo.

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>Pianificare backup automatici

È possibile pianificare l'esecuzione di backup da eseguire automaticamente un intervallo specificato. Per configurare una pianificazione di backup, utilizzare il cmdlet AzureRmWebAppBackupConfiguration di modifica. Questo cmdlet accetta parametri diversi:

- **Nome** - il nome dell'applicazione web.
- **ResourceGroupName** - il nome del gruppo di risorse contenente l'applicazione web.
- **Intervallo aperto** - facoltativo. Nome dell'intervallo aperto di app web.
- **StorageAccountUrl** - SA URL per il contenitore di archiviazione Azure utilizzato per archiviare i backup.
- **FrequencyInterval** - valore numerico per con quale frequenza occorre i backup. Deve essere un numero intero positivo.
- **FrequencyUnit** - unità di tempo per con quale frequenza occorre i backup. Opzioni sono ora e giorno.
- **RetentionPeriodInDays** - il numero di giorni backup automatici deve essere salvato prima di essere automaticamente eliminati.
- **Ora di inizio** - facoltativo. Ora quando deve iniziare il backup automatici. Backup iniziano immediatamente se è null. Deve essere un valore DateTime.
- **Database** - facoltativo. Matrice di DatabaseBackupSettings per i database di backup.
- **KeepAtLeastOneBackup** - facoltativo cambiato parametro. Fornire questo se un backup deve sempre essere mantenuto nell'account di archiviazione, indipendentemente dall'età è.

Di seguito è illustrato un esempio di come utilizzare questo cmdlet.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Per ottenere la pianificazione corrente, utilizzare il cmdlet Get-AzureRmWebAppBackupConfiguration. Può essere utile per la modifica di un impiego è già stato configurato.

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>Ripristinare un'app web da un backup

Per ripristinare un'app web da un backup, utilizzare il cmdlet AzureRmWebAppBackup Ripristina. Il modo più semplice per utilizzare questo cmdlet consiste nel barra verticale in un oggetto backup recuperati il cmdlet Get-AzureRmWebAppBackup o il cmdlet Get-AzureRmWebAppBackupList.

Dopo avere inserito un oggetto di backup, è possibile inviare nel cmdlet AzureRmWebAppBackup Ripristina. Specificare il parametro di opzione Sovrascrivi per indicare che si intende sovrascrivere il contenuto di un'applicazione web con il contenuto del backup. Se il backup contiene database, vengono ripristinati anche i database.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

Di seguito è illustrato un esempio di come utilizzare il ripristino AzureRmWebAppBackup specificando tutti i parametri.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Eliminare una copia di backup

Per eliminare una copia di backup, utilizzare il cmdlet AzureRmWebAppBackup Rimuovi. Consente di rimuovere il backup dal proprio account di archiviazione. Specificare il nome dell'app, il gruppo di risorse e l'ID di backup che si desidera eliminare.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

È anche possibile inviare un oggetto di backup nel cmdlet AzureRmWebAppBackup Rimuovi per eliminarlo.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite
