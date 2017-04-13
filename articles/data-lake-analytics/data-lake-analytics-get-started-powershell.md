<properties 
   pageTitle="Guida introduttiva di Azure dati Lake Analitica tramite PowerShell Azure | Azure" 
   description="Informazioni su come usare PowerShell Azure per creare un account di archivio Lake dati, creare un processo dati Lake Analitica utilizzando U SQL e inviare il processo. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Esercitazione: Introduzione a Azure dati Lake Analitica tramite PowerShell Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come usare PowerShell Azure per creare gli account Azure dati Lake Analitica, definire processi dati Lake Analitica in [U SQL](data-lake-analytics-u-sql-get-started.md)e inviare i processi ad account dati Lake analitico. Per ulteriori informazioni sui dati Lake Analitica, vedere [Panoramica di Azure dati Lake Analitica](data-lake-analytics-overview.md).

In questa esercitazione verrà sviluppato un processo che legge i file con valori (TSV) separati da una scheda e lo converte in un file CSV con valori separati da virgola. Per passare all'interno dell'esercitazione stessa usare altri strumenti supportati, selezionare le schede nella parte superiore di questa sezione.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Un lavoro utilizzando Azure PowerShell**. Informazioni su [come installare e configurare Azure PowerShell](../powershell-install-configure.md).
    
##<a name="create-data-lake-analytics-account"></a>Creare account Analitica Lake dati

Devono avere un account Analitica Lake dati prima di poter eseguire tutti i processi. Per creare un account di dati Lake Analitica, è necessario specificare le operazioni seguenti:

- **Gruppo di risorse Azure**: account A dati Lake Analitica deve essere creato all'interno di un gruppo di risorse di Azure. [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md) consente di gestire le risorse dell'applicazione come un gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione in un'operazione su una singola e coordinata.  

    Enumerare i gruppi di risorse nell'abbonamento:
    
        Get-AzureRmResourceGroup
    
    Per creare un nuovo gruppo di risorse:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Nome dell'account Analitica Lake dati**
- **Posizione**: una delle aree di dati di Azure che supporta dati Lake Analitica.
- **Account Lake dati predefinito**: ogni account dati Lake Analitica ha un account di Lake dati predefinito.

    Per creare un nuovo account Lake dati:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] Il nome dell'account Lake dati deve contenere solo numeri e lettere minuscole.



**Per creare un account di dati Lake Analitica**

1. Aprire PowerShell ISE dalla workstation di Windows.
2. Eseguire il seguente script:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>Caricare dati Lake dati

In questa esercitazione si elaborerà alcuni log di ricerca.  Il log di ricerca può essere archiviato in archivio dati Lake o archiviazione Blob Azure. 

Un file di log di ricerca di esempio è stato copiato in un contenitore di Blob Azure pubblico. Usare il seguente script di PowerShell per scaricare il file workstation in uso e quindi caricare il file all'account di archivio di dati Lake predefinito del proprio account dati Lake Analitica.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Script di PowerShell seguente viene illustrato come ottenere il nome di archivio Lake dati predefinito per un account Analitica Lake dati:


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] Il portale di Azure offre un'interfaccia utente per copiare i file di dati di esempio per l'account di archivio Lake dati predefinito. Per ulteriori informazioni, vedere [Guida introduttiva di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Dati Lake Analitica accessibile anche archiviazione Blob Azure.  Per caricare i dati a archiviazione Blob Azure, vedere [Uso di PowerShell Azure con lo spazio di archiviazione di Azure](../storage/storage-powershell-guide-full.md).

##<a name="submit-data-lake-analytics-jobs"></a>Inviare dati Lake Analitica processi

I processi di dati Lake Analitica scritte in linguaggio U-SQL. Per ulteriori informazioni su U-SQL, vedere [Guida introduttiva a linguaggio SQL U](data-lake-analytics-u-sql-get-started.md) e [riferimento al linguaggio SQL U](http://go.microsoft.com/fwlink/?LinkId=691348).

**Per creare uno script di processo dati Lake Analitica**

- Creare un file di testo con script U SQL seguente e salvare il file di testo workstation in uso:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Questo script SQL U legge il file di dati di origine utilizzando **Extractors.Tsv()**e quindi viene creato un file csv tramite **Outputters.Csv()**. 
    
    Non modificare due percorsi, a meno che non si copia il file di origine in una posizione diversa.  Dati Lake Analitica creerà la cartella di output se non esiste.
    
    È semplice utilizzare percorsi relativi per i file memorizzati predefiniti dati account Lake. È anche possibile utilizzare percorsi assoluti.  Per esempio 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario utilizzare percorsi assoluti per accedere ai file nell'account di archiviazione collegati.  La sintassi per i file memorizzati in account di archiviazione Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contenitore di Blob Azure con BLOB pubblico o le autorizzazioni di accesso contenitori pubblico non sono attualmente supportati.    
    
    
**Per inviare il processo**

1. Aprire PowerShell ISE dalla workstation di Windows.
2. Eseguire il seguente script:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    In script, è archiviato il file di script SQL U in c:\tutorials\data-lake-analytics\copyFile.usql. Aggiornare il percorso del file di conseguenza.
 
Una volta completato il processo, è possibile utilizzare i cmdlet seguenti per visualizzare un elenco di file e scaricare il file:
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Vedere anche

- Per visualizzare l'esercitazione stessa usare altri strumenti, fare clic su selettori scheda nella parte superiore della pagina.
- Per una query più complessa, vedere [registri di sito Web analizza mediante Azure dati Lake Analitica](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a applicazioni U SQL, vedere [gli script di sviluppare U-SQL utilizzando dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni su U-SQL, vedere [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md).
- Per le attività di gestione, vedere [Gestione dei Analitica delle Lake Azure dati nel portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per ottenere una panoramica dei dati Lake Analitica, vedere [Panoramica di Azure dati Lake Analitica](data-lake-analytics-overview.md).

