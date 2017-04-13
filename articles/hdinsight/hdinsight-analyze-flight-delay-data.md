<properties
    pageTitle="Analizzare i dati dei ritardi volo con Hadoop in HDInsight | Microsoft Azure"
    description="Informazioni su come usare uno script di Windows PowerShell per creare un cluster di HDInsight, eseguire un processo Hive, eseguire un processo Sqoop ed eliminare il cluster."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analizzare i dati dei ritardi volo usando Hive in HDInsight

Hive consente di processi Hadoop MapReduce tramite un linguaggio di script simile a SQL denominato * [HiveQL][hadoop-hiveql]*, che possono essere applicati verso i riepiloghi, l'esecuzione di query e l'analisi di volumi elevati di dati.

> [AZURE.NOTE] La procedura descritta in questo documento richiede un cluster basato su Windows HDInsight. Per istruzioni che funzionano con un cluster basato su Linux, vedere [dati dei ritardi volo analizza tramite Hive in HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

Uno dei vantaggi principali di Azure HDInsight è la separazione di archiviazione dei dati e calcolo. HDInsight utilizza archiviazione Blob Azure per l'archiviazione di dati. Un tipico processo prevede tre elementi:

1. **Archiviare dati in archiviazione Blob Azure.**  Ad esempio meteorologiche dati, sensore dati, i registri web e in questo caso, i dati dei ritardi volo vengono salvati in archiviazione Blob Azure.
2. **Eseguire i processi.** All'orario stabilito per elaborare i dati, eseguire uno script di Windows PowerShell oppure un'applicazione client per creare un cluster di HDInsight, eseguire i processi ed eliminare il cluster. I processi di salvano i dati di output a archiviazione Blob Azure. I dati di output viene mantenuti anche dopo il raggruppamento viene eliminato. In questo modo pagare solo cosa hanno utilizzato.
3. **Recuperare l'output dallo spazio di archiviazione Blob Azure**, o in questa esercitazione, esportare i dati in un database SQL Azure.

Il diagramma seguente illustra lo scenario e la struttura di questa esercitazione:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

**Nota**: I numeri nel diagramma corrispondono ai titoli delle sezioni. **M** il processo principale. **Un** acronimo per il contenuto in appendice.

La parte principale dell'esercitazione viene illustrato come utilizzare uno script di Windows PowerShell per eseguire le operazioni seguenti:

- Creare un cluster di HDInsight.
- Eseguire un processo Hive cluster per calcolare ritardi medi negli aeroporti. I dati di volo ritardo vengono archiviati in un account di archiviazione Blob Azure.
- Eseguire un processo Sqoop per esportare l'output di processo Hive in un database SQL Azure.
- Eliminare il cluster HDInsight.

In appendici, è possibile trovare le istruzioni per il caricamento di dati di volo ritardo, creazione o caricamento di una stringa di query Hive e preparare il database di SQL Azure per il processo di Sqoop.

> [AZURE.NOTE] La procedura descritta in questo documento è specifica per cluster HDInsight basato su Windows. Per istruzioni che funzionano con un cluster basato su Linux, vedere [dati dei ritardi volo analizza utilizzando Hive in HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre gli elementi seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**File utilizzati in questa esercitazione**

In questa esercitazione utilizza le prestazioni in fase di dati di volo delle compagnie aeree di [ricerca e amministrazione tecnologia innovativa, Bureau delle statistiche relative a trasporti o RITA] [rita-website]. È stata caricata una copia dei dati a un contenitore di spazio di archiviazione Blob Azure con l'autorizzazione di accesso Blob pubblico. Una parte dello script di PowerShell di copiare i dati dal contenitore blob pubblica il contenitore di blob predefinito del cluster. Lo script HiveQL viene copiato anche allo stesso contenitore di Blob. Per informazioni su come ottenere/carica i dati al proprio account di archiviazione e come creare/caricare il file di script HiveQL, vedere [Appendice](#appendix-a) e [Appendice B](#appendix-b).

Nella tabella seguente elenca i file utilizzati in questa esercitazione:

<table border="1">
<tr><th>File</th><th>Descrizione</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>File di script HiveQL utilizzato dal processo Hive. Questo script è stato caricato a un account di archiviazione Blob Azure con l'accesso del pubblico. <a href="#appendix-b">Appendice B</a> sono istruzioni su come preparare e caricare il file per il proprio account di archiviazione Blob Azure.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Dati di input per il processo di Hive. I dati sono stati caricati in un account di archiviazione Blob Azure con l'accesso del pubblico. <a href="#appendix-a">Appendice</a> include istruzioni su come ottenere i dati e caricare i dati al proprio account di archiviazione Blob Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Il percorso di output per il processo di Hive. Il contenitore predefinito viene utilizzato per archiviare i dati di output.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>La cartella di stato processo Hive sul contenitore predefinito.</td></tr>
</table>


##<a name="create-cluster-and-run-hivesqoop-jobs"></a>Creare cluster ed eseguire i processi di Hive/Sqoop

Hadoop MapReduce è elaborazione batch. Il modo più conveniente per eseguire un processo Hive consiste nel creare un cluster per il processo ed eliminare il processo dopo il completamento del processo. Lo script seguente illustra l'intero processo. Per ulteriori informazioni sulla creazione di un cluster di HDInsight e Hive processi in esecuzione, vedere [creare Hadoop cluster in HDInsight] [ hdinsight-provision] e [Usare Hive con HDInsight][hdinsight-use-hive].

**Per eseguire la query Hive tramite PowerShell Azure**

1. Creare un database SQL Azure e la tabella per l'output di processo Sqoop, seguendo le istruzioni in [Appendice C](#appendix-c).
3. Aprire Windows PowerShell ISE ed eseguire il seguente script:

        $subscriptionID = "<Azure Subscription ID>"
        $nameToken = "<Enter an Alias>" 
        
        ###########################################
        # You must configure the follwing variables
        # for an existing Azure SQL Database
        ###########################################
        $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
        $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
        $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
        $existingSqlDatabaseName = "<Azure SQL Database name>"
        
        $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files. 
        $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
        
        ###########################################
        # (Optional) configure the following variables
        ###########################################
        
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $location = "EAST US 2"
        
        $HDInsightClusterName = $namePrefix + "hdi"
        $httpUserName = "admin"
        $httpPassword = "<Enter the Password>"
        
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $HDInsightClusterName # use the cluster name
        
        $existingSqlDatabaseTableName = "AvgDelays"
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
        
        $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
        
        $jobStatusFolder = "/tutorials/flightdelays/jobstatus"
        
        ###########################################
        # Login
        ###########################################
        try{
            $acct = Get-AzureRmSubscription
        }
        catch{
            Login-AzureRmAccount
        }
        Select-AzureRmSubscription -SubscriptionID $subscriptionID
        
        ###########################################
        # Create a new HDInsight cluster
        ###########################################
        
        # Create ARM group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
        # Create the default storage account
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
        
        # Create the default Blob container
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
        New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
        
        # Create the HDInsight cluster
        $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
        $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
        
        New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -Location $location `
            -ClusterType Hadoop `
            -OSType Windows `
            -ClusterSizeInNodes 2 `
            -HttpCredential $httpCredential `
            -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultStorageContainer $existingDefaultBlobContainerName 
        
        
        ###########################################
        # Prepare the HiveQL script and source data
        ###########################################
        
        # Create the temp location  
        New-Item -Path $localFolder -ItemType Directory -Force 
        
        # Download the sample file from Azure Blob storage
        $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
        $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
        #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
        
        # Upload data to default container
        
        $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
        
        Invoke-Expression -Command:$azcopycmd
        
        ###########################################
        # Submit the Hive job
        ###########################################
        Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
        $response = Invoke-AzureRmHDInsightHiveJob `
                        -Files $hqlScriptFile `
                        -DefaultContainer $defaultBlobContainerName `
                        -DefaultStorageAccountName $defaultStorageAccountName `
                        -DefaultStorageAccountKey $defaultStorageAccountKey `
                        -StatusFolder $jobStatusFolder 
        
        write-Host $response
        
        
        ###########################################
        # Submit the Sqoop job
        ###########################################
        $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
        
        $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                        -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
        $sqoopJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $hdinsightClusterName `
                        -HttpCredential $httpCredential `
                        -JobDefinition $sqoopDef #-Debug -Verbose
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -HttpCredential $httpCredential `
            -WaitTimeoutInSeconds 3600 `
            -Job $sqoopJob.JobId
        
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -HttpCredential $httpCredential `
            -DefaultContainer $existingDefaultBlobContainerName `
            -DefaultStorageAccountName $defaultStorageAccountName `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -JobId $sqoopJob.JobId `
            -DisplayOutputType StandardError
        
        ###########################################
        # Delete the cluster
        ###########################################
        Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Connettersi al database di SQL e vedere ritardi volo Media in base alla città nella tabella AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Appendice a-Carica dati dei ritardi volo a archiviazione Blob Azure
Caricamento di file di dati e i file di script HiveQL (vedere [Appendice B](#appendix-b)) richiede una pianificazione. L'idea è per archiviare i file di dati e il file HiveQL prima di creare un cluster di HDInsight ed eseguire il processo di Hive. Sono disponibili due opzioni:

- **Usare lo stesso account Azure lo spazio di archiviazione che verrà utilizzato dal cluster HDInsight come file system predefinito.** Poiché il cluster HDInsight dispone tasto di scelta rapida account lo spazio di archiviazione, non è necessario apportare eventuali altre modifiche.
- **Usare un altro account di archiviazione Azure dal file system HDInsight cluster predefinito.** In questo caso, è necessario modificare la parte di creazione dello script di Windows PowerShell disponibili in [creare HDInsight cluster e processi Hive/Sqoop Esegui](#runjob) per collegare l'account di archiviazione come un altro account di archiviazione. Per ulteriori informazioni, vedere [creare Hadoop cluster in HDInsight][hdinsight-provision]. Cluster HDInsight SA quindi il tasto di scelta per l'account di archiviazione.

>[AZURE.NOTE] Il percorso di archiviazione Blob per il file di dati è hard-coded nel file di script HiveQL. È necessario aggiornarla di conseguenza.

**Per scaricare i dati di volo**

1. Passare alla [ricerca e tecnologia innovativa amministrazione Bureau delle statistiche relative a trasporti][rita-website].
2. Nella pagina, selezionare i valori seguenti:

    <table border="1">
    <tr><th>Nome</th><th>Valore</th></tr>
    <tr><td>Filtro anno</td><td>2013 </td></tr>
    <tr><td>Filtro periodo</td><td>Gennaio</td></tr>
    <tr><td>Campi</td><td>*Anno*, *FlightDate*, *UniqueCarrier*, *vettore*, *FlightNum*, *OriginAirportID*, *origine*, *OriginCityName*, *OriginState*, *DestAirportID*, *destinazione*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (deselezionare tutti gli altri campi)</td></tr>
    </table>

3. Fare clic su **Scarica**.
4. Decomprimere il file nella cartella **C:\Tutorials\FlightDelay\2013Data** . Ogni file è un file CSV e dimensione circa 60 GB.
5.  Rinominare il file con il nome del mese che contiene dati per. Ad esempio, il file contenente i dati di gennaio sarebbe denominato *January.csv*.
6. Ripetere i passaggi 2 e 5 per scaricare un file per ogni 12 mesi in 2013. È necessario un minimo di un file per eseguire l'esercitazione.  

**Per caricare i dati di volo ritardo in archiviazione Blob Azure**

1. Preparare i parametri:

    <table border="1">
    <tr><th>Nome della variabile</th><th>Note</th></tr>
    <tr><td>$storageAccountName</td><td>L'account di archiviazione Azure in cui si desidera caricare i dati.</td></tr>
    <tr><td>$blobContainerName</td><td>Contenitore di Blob nel punto in cui si desidera caricare i dati.</td></tr>
    </table>
2. Aprire Azure PowerShell ISE.
3. Incollare lo script seguente nel riquadro dello script:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
        $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
        #EndRegion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"
        
                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
        
                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }
        
        # List the uploaded files on HDInsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion




4. Premere **F5** per eseguire lo script.

Se si sceglie di usare un altro metodo per caricare i file, assicurarsi che il percorso del file sia esercitazioni/flightdelay/dati. La sintassi per accedere ai file è:

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Esercitazioni/flightdelay/dati del percorso è la cartella virtuale creata durante il caricamento di file. Verificare che i 12 file, uno per ogni mese.

>[AZURE.NOTE] È necessario aggiornare la query Hive di leggere nella nuova posizione.

> È necessario configurare l'autorizzazione di accesso contenitore per essere pubblico o per il cluster HDInsight associare l'account di archiviazione. In caso contrario, la stringa di query Hive non saranno in grado di accedere ai file di dati.

---
##<a id="appendix-b"></a>Appendice B: creare e caricare uno script HiveQL

Utilizzo di PowerShell di Azure, è possibile eseguire più istruzioni HiveQL uno alla volta o comprimere l'istruzione HiveQL in un file di script. In questa sezione viene illustrato come creare uno script HiveQL e caricare lo script archiviazione Blob Azure tramite PowerShell Azure. Hive richiede gli script HiveQL per essere archiviati in archiviazione Blob Azure.

Lo script HiveQL verrà eseguite le operazioni seguenti:

1. **Eliminare la tabella delays_raw**, nel caso la tabella già esistente.
2. **Creare la tabella Hive esterna delays_raw** che punta alla posizione di archiviazione Blob contenente i file di ritardo volo. Questa query specifica che i campi sono racchiusi tra "," e che le righe vengono terminate da "\n". Questo costituisce un problema quando i valori dei campi contengano virgole perché Hive non è possibile distinguere tra un punto e virgola presente delimitatore di campo e uno che fa parte di un valore di campo (nel caso dei valori di campo ORIGIN\_Città\_nome e la destinazione\_Città\_nome). Per risolvere questo problema, la query create colonne TEMP per i dati in modo non corretto dividere in colonne.  
3. **Eliminare la tabella ritardi**, nel caso la tabella già esistente.
4. **Creare la tabella ritardi**. Può essere utile pulire i dati prima di eseguire ulteriori elaborazioni. Questa query crea una nuova tabella, *ritardi*, dalla tabella delays_raw. Si noti che le colonne TEMP (come detto in precedenza) non vengono copiate e la funzione **sottostringa** viene utilizzata per rimuovere i dati tra virgolette.
5. **Calcolare il ritardo medio meteo e gruppi i risultati in base al nome di città.** Fornirà anche i risultati a archiviazione Blob. Si noti che la query verrà rimosso apostrofi dai dati e verrà esclusi righe in cui il valore di **weather_delay** è null. Questo è necessario perché Sqoop utilizzato più avanti in questa esercitazione, non gestire correttamente i valori per impostazione predefinita.

Per un elenco completo dei comandi HiveQL, vedere [Hive Data Definition Language][hadoop-hiveql]. Ogni comando HiveQL deve terminare con un punto e virgola.

**Per creare un file di script HiveQL**

1. Preparare i parametri:

    <table border="1">
    <tr><th>Nome della variabile</th><th>Note</th></tr>
    <tr><td>$storageAccountName</td><td>L'account di archiviazione Azure in cui si desidera caricare lo script HiveQL.</td></tr>
    <tr><td>$blobContainerName</td><td>Contenitore di Blob nel punto in cui si desidera caricare lo script HiveQL.</td></tr>
    </table>
2. Aprire Azure PowerShell ISE.

3. Copiare e incollare lo script seguente nel riquadro dello script:

        [CmdletBinding()]
        Param(

            # Azure Blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #region - Define variables
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        # The HiveQL script file is exported as this file before it's uploaded to Blob storage
        $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
        
        # The HiveQL script file will be uploaded to Blob storage as this blob name
        $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
        
        # These two constants are used by the HiveQL script file
        #$srcDataFolder = "tutorials/flightdelay/data"
        $dstDataFolder = "/tutorials/flightdelay/output"
        #endregion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #region - Validate the file and file path
        
        # Check if a file with the same file name already exists on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){
        
            $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
        
            if ($isDelete.ToLower() -ne "y")
            {
                Exit
            }
        }
        
        # Create the folder if it doesn't exist
        $folder = split-path $hqlLocalFileName
        if (-not (test-path $folder))
        {
            Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
        
            new-item $folder -ItemType directory  
        }
        #end region
        
        #region - Write the Hive script into a local file
        Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green
        
        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
        
        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
        
        $hqlDropDelays = "DROP TABLE delays;"
        
        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;"
        
        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"
        
        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
        
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
        #endregion
        
        #region - Upload the Hive script to the default Blob container
        Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
        
        # Create a storage context object
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        # Upload the file from local workstation to Blob storage
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
        #endregion
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Ecco le variabili utilizzate nello script:

    - **$hqlLocalFileName** - lo script consente di salvare il file di script HiveQL localmente prima di caricare in archiviazione Blob. Questo è il nome del file. Il valore predefinito è <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
    - **$hqlBlobName** - questo è il nome del blob HiveQL script file utilizzato in archiviazione Blob Azure. Il valore predefinito è tutorials/flightdelay/flightdelays.hql. Perché il file verrà scritto direttamente a archiviazione Blob Azure, non c'è un "/" all'inizio del nome del blob. Se si vuole accedere al file dallo spazio di archiviazione Blob, sarà necessario aggiungere un "/" all'inizio del nome del file.
    - **$srcDataFolder** e **$dstDataFolder** - = "esercitazioni/flightdelay/dati" = "esercitazioni/flightdelay/output"


---
##<a id="appendix-c"></a>Appendice C - preparare un database SQL Azure per l'output di processo Sqoop
**Per preparare il database SQL (unire questo script Sqoop)**

1. Preparare i parametri:

    <table border="1">
    <tr><th>Nome della variabile</th><th>Note</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Il nome del server di database SQL Azure. Immettere alcuna operazione per creare un nuovo server.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Nome di accesso per il server di database SQL Azure. Se $sqlDatabaseServerName è un server esistente, l'accesso e la password di accesso vengono utilizzati per eseguire l'autenticazione con il server. In caso contrario vengono utilizzati per creare un nuovo server.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Password di accesso per il server di database SQL Azure.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Questo valore viene utilizzato solo quando si crea un nuovo server di database Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Database SQL utilizzato per creare la tabella AvgDelays per il processo di Sqoop. Lasciare vuoto creerà un database denominato HDISqoop. Il nome della tabella per l'output di processo Sqoop è AvgDelays. </td></tr>
    </table>
2. Aprire Azure PowerShell ISE.
3. Copiare e incollare lo script seguente nel riquadro dello script:

        [CmdletBinding()]
        Param(
        
            # Azure Resource group variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
            [String]$resourceGroupName,
        
            # SQL database server variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseServer, 
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseLogin,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.
        
            # SQL database variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
        )
        
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        #region - Constants and variables
        
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"
        
        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10
        
        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                    [origin_city_name] ASC
                )
                )"
        #endregion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #region - Create and validate Azure resouce group
        try{
            Get-AzureRmResourceGroup -Name $resourceGroupName
        }
        catch{
            New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
        }
        
        #EndRegion
        
        #region - Create and validate Azure SQL database server
        try{
            Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
        catch{
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
        
            $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
            $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
        
            $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
        
            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
        
            #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
        }
        
        #endregion
        
        #region - Create and validate Azure SQL database
        
        try {
            Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
        }
        catch {
            Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
            New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
        }
        
        #endregion
        
        #region -  Execute an SQL command to create the AvgDelays table
        
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()
        
        $conn.close()
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    >[AZURE.NOTE] Lo script utilizza un servizio di trasferimento (REST) stato representational, http://bot.whatismyipaddress.com, per recuperare l'indirizzo IP esterno. L'indirizzo IP viene utilizzato per la creazione di una regola del firewall per il server di database SQL.  

    Ecco alcune variabili utilizzate nello script:

    - **$ipAddressRestService** - il valore predefinito è http://bot.whatismyipaddress.com. È un indirizzo IP pubblico servizio REST per ottenere l'indirizzo IP esterno. Se si desidera, è possibile usare altri servizi. L'indirizzo IP esterno recuperato tramite il servizio da utilizzare per creare una regola per il server di database SQL Azure, in modo che è possibile accedere al database dalla workstation (utilizzando uno script di Windows PowerShell).
    - **$fireWallRuleName** - questo è il nome della regola del firewall per il server di database SQL Azure. Il nome predefinito è <u>FlightDelay</u>. Se si desidera, è possibile rinominarla con.
    - **$sqlDatabaseMaxSizeGB** - questo valore viene utilizzato solo quando si crea un nuovo server di database SQL Azure. Il valore predefinito è 10GB. 10GB è sufficiente per questa esercitazione.
    - **$sqlDatabaseName** - questo valore viene utilizzato solo quando si crea un nuovo database SQL Azure. Il valore predefinito è HDISqoop. Se si rinomina, è necessario aggiornare lo script Sqoop Windows PowerShell di conseguenza.

4. Premere **F5** per eseguire lo script.
5. Convalidare l'output di script. Verificare che lo script è stata eseguita correttamente.

##<a id="nextsteps"></a>Passaggi successivi
A questo punto si informazioni su come caricare un file in archiviazione Blob Azure, come popolare una tabella Hive utilizzando i dati dallo spazio di archiviazione Blob Azure, come eseguire query Hive e come usare Sqoop per esportare dati da HDFS in un database SQL Azure. Per ulteriori informazioni, vedere gli articoli seguenti:

* [Introduzione a HDInsight][hdinsight-get-started]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Utilizzare Oozie con HDInsight][hdinsight-use-oozie]
* [Utilizzare Sqoop con HDInsight][hdinsight-use-sqoop]
* [Utilizzare maialino con HDInsight][hdinsight-use-pig]
* [Sviluppare programmi Java MapReduce per HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
