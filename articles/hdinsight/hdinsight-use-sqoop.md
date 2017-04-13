<properties
    pageTitle="Usare Hadoop Sqoop in HDInsight | Microsoft Azure"
    description="Informazioni su come usare PowerShell Azure da una workstation per eseguire Sqoop importare ed esportare tra un cluster di Hadoop e un database SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight"></a>Utilizzare Sqoop con Hadoop in HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come utilizzare Sqoop in HDInsight per importare ed esportare tra cluster HDInsight e il database SQL Azure o database di SQL Server.

Anche se Hadoop è ideale per l'elaborazione dei dati non strutturati e semistructured, ad esempio i registri e file, è anche possibile necessario elaborare dati strutturati archiviata nei database relazionali.

[Sqoop] [ sqoop-user-guide-1.4.4] è uno strumento progettato per trasferire dati tra cluster Hadoop e database relazionali. È possibile utilizzare per importare dati da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL Server, MySQL o Oracle nel Hadoop distributed file system (HDFS), la trasformazione dei dati in Hadoop con MapReduce o Hive e quindi esportare i dati in un RDBMS. In questa esercitazione si utilizza un database SQL Server per il database relazionale.

Per le versioni Sqoop sono supportate nei cluster HDInsight, vedere [quali sono le novità in versioni cluster fornite da HDInsight?] [hdinsight-versions].

##<a name="understand-the-scenario"></a>Conoscere lo scenario

HDInsight cluster viene fornito con alcuni dati di esempio. Utilizzare i due esempi seguenti:

- Un file di log log4j, che si trova in */example/data/sample.log*. I registri seguenti sono estratti dal file:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

- Una tabella Hive denominata *hivesampletable*, che fa riferimento il file di dati che si trova in */hive/warehouse/hivesampletable*. La tabella contiene alcuni dati di un dispositivo mobile. 

  	| Campo | Tipo di dati |
  	| ----- | --------- |
  	| ClientID | stringa |
  	| querytime | stringa |
  	| mercato | stringa |
  	| deviceplatform | stringa |
  	| devicemake | stringa |
  	| devicemodel | stringa |
  	| stato | stringa |
  	| paese | stringa |
  	| querydwelltime | doppia |
  	| ID sessione | bigint |
  	| sessionpagevieworder | bigint |

Si verrà prima esportare *sample.log* e *hivesampletable* al database SQL Azure o SQL Server e quindi come importare la tabella che contiene i dati di un dispositivo mobile alla HDInsight utilizzando il percorso seguente:

    /tutorials/usesqoop/importeddata

## <a name="create-cluster-and-sql-database"></a>Creare cluster e database SQL

In questa sezione viene illustrato come creare un cluster e degli schemi di database SQL per eseguire l'esercitazione tramite il portale di Azure e un modello di gestione di risorse Azure.  Se si preferisce utilizzare Azure PowerShell, vedere [Appendice](#appendix-a---a-powershell-sample).

1. Fare clic sull'immagine seguente per aprire un modello di gestione delle risorse nel portale di Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di gestione delle risorse si trova in un contenitore di blob pubblica, *https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*. 
    
    Il modello di manager delle risorse chiama un pacchetto di bacpac per distribuire gli schemi di tabella di database SQL.  Il pacchetto bacpac disponibile anche in un contenitore di blob pubblica, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se si desidera utilizzare un contenitore privato per i file bacpac, utilizzare i valori seguenti nel modello:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. Da e il parametri immettere quanto segue:

    - **Nome cluster**: immettere un nome per il cluster Hadoop che verrà creato.
    - **Nome di accesso cluster e la password**: il nome di accesso predefinito è amministratore.
    - **Nome utente SSH e password**.
    - **Nome di accesso server di database SQL e password**.

    Nella sezione variabili hardcoded sono i seguenti valori:
    
  	|Nome dell'account di archiviazione predefinito|<CluterName>archivio|
  	|----------------------------|-----------------|
  	|Nome del server di database SQL Azure|<ClusterName>dbserver|
  	|Nome del database SQL Azure|<ClusterName>DB|
    
    Prendere nota questi valori.  Saranno necessari in un secondo momento nell'esercitazione.
    
3. fare clic su **OK** per salvare i parametri.

4 e il **distribuzione personalizzata** , fare clic su casella di **gruppo di risorse** a discesa e quindi fare clic su **Nuovo** per creare un nuovo gruppo di risorse. Gruppo di risorse è un contenitore che raggruppa cluster, l'account di archiviazione dipendenti e altre risorse collegate.

5 fare clic su **note legali**e quindi fare clic su **Crea**.

6. fare clic su **Crea**. Verrà visualizzato un nuovo riquadro intitolato Submitting distribuzione per la distribuzione dei modelli. Richiede circa circa il 20 minuti per creare il cluster e il database SQL.

Se si sceglie di usare i database di SQL Azure esistente o Microsoft SQL Server

- **Database SQL Azure**: È necessario configurare una regola del firewall per il server di database SQL Azure consentire l'accesso dalla workstation. Per istruzioni sulla creazione di SQL Azure del database e configurare il firewall, vedere [Introduzione all'utilizzo di database SQL Azure][sqldatabase-get-started]. 

    > [AZURE.NOTE] Per impostazione predefinita un database SQL Azure consente connessioni da servizi di Azure, ad esempio Azure HDInsight. Se questa impostazione firewall è disabilitata, è necessario attivato dal portale di Azure. Per istruzioni sulla creazione di un database SQL Azure e configurare le regole firewall, vedere [creare e configurare Database SQL][sqldatabase-create-configue].

- **SQL Server**: se il cluster HDInsight nella stessa rete virtuale in Azure di SQL Server, è possibile utilizzare la procedura descritta in questo articolo per importare ed esportare i dati a un database di SQL Server.

    > [AZURE.NOTE] HDInsight supporta solo le reti virtuali basata sulla posizione e non è attualmente funziona con reti virtuale gruppo affinità.

    * Per creare e configurare una rete virtuale, vedere [creazione di una rete virtuale tramite il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

        * Quando si utilizza SQL Server nel centro dati, è necessario configurare la rete virtuale come *da sito* o un *punto al sito*.

            > [AZURE.NOTE] Per le reti virtuali **punto al sito** , SQL Server deve eseguire il client VPN applicazione di configurazione, è disponibile nel **Dashboard** della configurazione di rete virtuale Azure.

        * Quando si utilizza SQL Server in un computer virtuale Azure, qualsiasi configurazione della rete virtuale può essere utilizzato se il computer virtuale che ospita SQL Server fa parte della stessa rete virtuale HDInsight.

    * Per creare un cluster di HDInsight in una rete virtuale, vedere [creare Hadoop cluster in HDInsight usando le opzioni personalizzate](hdinsight-provision-clusters.md)

    > [AZURE.NOTE] SQL Server è inoltre necessario consentire l'autenticazione. È necessario utilizzare un account di accesso di SQL Server per completare la procedura descritta in questo articolo.


## <a name="run-sqoop-jobs"></a>Eseguire i processi Sqoop

HDInsight eseguire Sqoop processi, con una vasta gamma di metodi. Utilizzare la tabella seguente per decidere quale metodo appropriato, quindi fare clic sul collegamento per una procedura dettagliata.

| **Utilizzare questa opzione** se si desidera che...                                   | .... is shell **interattivi** | ... elaborazione **batch** | via nuovo **sistema operativo del cluster** | via Soido questo **sistema operativo client** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-use-sqoop-mac-linux.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [.NET SDK per Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux o Windows                          | Windows (per ora)                        |
| [PowerShell Azure](hdinsight-hadoop-use-sqoop-powershell.md)  |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |

##<a name="limitations"></a>Limitazioni

* Esporta in blocco - HDInsight basati su con Linux, il connettore Sqoop utilizzato per esportare i dati a Microsoft SQL Server o Database SQL Azure attualmente non supporta inserimenti di massa.

* Divisione in batch - con basati su Linux HDInsight quando si usa il `-batch` passa durante l'esecuzione di inserimenti, Sqoop eseguirà più inserimenti anziché in batch le operazioni di inserimento.

##<a name="next-steps"></a>Passaggi successivi

A questo punto sono appreso come utilizzare Sqoop. Per ulteriori informazioni, vedere:

- [Usare Hive con HDInsight](hdinsight-use-hive.md)
- [Utilizzare maialino con HDInsight](hdinsight-use-pig.md)
- [Utilizzare Oozie con HDInsight][hdinsight-use-oozie]: usare Sqoop azione in un flusso di lavoro Oozie.
- [Analizzare i dati dei ritardi volo utilizzando HDInsight][hdinsight-analyze-flight-data]: utilizzare Hive analizzare volo ritardare dati e quindi utilizzare Sqoop per esportare i dati in un database SQL Azure.
- [Caricare dati HDInsight][hdinsight-upload-data]: trovare altri metodi per il caricamento di dati in archiviazione Blob/Azure HDInsight.


## <a name="appendix-a---a-powershell-sample"></a>Appendice a-un campione di PowerShell

Nell'esempio di PowerShell esegue le operazioni seguenti:

1. Connettersi a Azure.
2. Creare un gruppo di risorse Azure. Per ulteriori informazioni, vedere [Uso di PowerShell Azure Gestione risorse Azure](../powershell-azure-resource-manager.md)
3. Creare un server di Database SQL Azure, un database SQL Azure e due tabelle. 

    Se si utilizza SQL Server, utilizzare le istruzioni seguenti per creare le tabelle:
    
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))

        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])

    Per esaminare il database e le tabelle in modo semplice consiste nell'utilizzare Visual Studio. Il server di database e il database può essere esaminati tramite il portale di Azure.

4. Creare un cluster di HDInsight.

    Per esaminare il cluster, è possibile usare il portale di Azure e Azure PowerShell.

5. Pre-elaborazione il file di dati di origine.

    In questa esercitazione verranno esportati un file di log log4j (file con valori delimitati da) e una tabella Hive a un database SQL Azure. Il file con valori delimitati da è denominato */example/data/sample.log*. Precedenza nell'esercitazione, è stato alcuni esempi dei registri log4j. Nel file di log, esistono alcune righe vuote e alcune righe simili ai seguenti:
    
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
    
    Si tratta di fine per altri esempi che utilizzano questi dati, ma è necessario rimuovere queste eccezioni prima sarà possibile importare nel database di SQL Azure o SQL Server. Esportazione Sqoop avrà esito negativo se c'è una stringa vuota o una riga con una meno numero di elementi rispetto al numero di campi definiti nella tabella di database SQL Azure. La tabella log4jlogs include 7 campi di tipo stringa.

    Questa procedura consente di creare un nuovo file sul cluster: tutorials/usesqoop/data/sample.log. Per esaminare il file di dati modificati, è possibile usare il portale di Azure, uno strumento di esplorazione di archiviazione Azure o Azure PowerShell. [Guida introduttiva a HDInsight] [ hdinsight-get-started] è un esempio di codice per l'uso di Azure PowerShell per scaricare un file e visualizzare il contenuto del file.

6. Esportare un file di dati nel database di SQL Azure.

    Il file di origine è tutorials/usesqoop/data/sample.log. La tabella in cui i dati vengono esportati in è denominata log4jlogs.
    
    > [AZURE.NOTE] Diverso da informazioni della stringa di connessione, la procedura descritta in questa sezione funzionerà per un database SQL Azure o SQL Server. Questa procedura sono stata testata utilizzando le seguenti operazioni:
    >
    > * **Configurazione di Azure virtuali punto al sito**: una rete virtuale connessi cluster HDInsight a SQL Server in un Data Center privato. Per ulteriori informazioni, vedere [configurare una connessione VPN punto al sito nel portale di gestione](../vpn-gateway/vpn-gateway-point-to-site-create.md) .
    > * **Azure HDInsight 3.1**: vedere [cluster creare Hadoop in HDInsight usando le opzioni personalizzate](hdinsight-provision-clusters.md) per informazioni sulla creazione di un cluster di una rete virtuale.
    > * **SQL Server 2014**: è configurato per consentire l'autenticazione e l'esecuzione del client VPN pacchetto di configurazione di connettersi alla rete virtuale.

7. Esportare una tabella Hive al database SQL Azure.

8. Importare la tabella mobiledata cluster HDInsight.

    Per esaminare il file di dati modificati, è possibile usare il portale di Azure, uno strumento di esplorazione di archiviazione Azure o Azure PowerShell.  [Guida introduttiva a HDInsight] [ hdinsight-get-started] è un esempio sull'utilizzo di Azure PowerShell per scaricare un file e visualizzare il contenuto del file.


### <a name="the-powershell-sample"></a>Nell'esempio di PowerShell

    # Prepare an Azure SQL database to be used by the Sqoop tutorial
    
    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure Subscription ID>"
    
    $sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
    $sqlDatabasePassword = "<Enter a Password>"
    
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    
    # used for creating Azure service names
    $nameToken = "<Enter an alias>" 
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion
    
    #region - variables
    
    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial
    
    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10
    
    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"
    
    # Used for creating tables and clustered indexes
    $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
        [t1] [nvarchar](50),
        [t2] [nvarchar](50),
        [t3] [nvarchar](50),
        [t4] [nvarchar](50),
        [t5] [nvarchar](50),
        [t6] [nvarchar](50),
        [t7] [nvarchar](50))"
    
    $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
    
    $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder][bigint])"
    
    $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
    
    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion
    
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"
    
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
    
    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion
    
    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
    
        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $credential `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
    
        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress
    
        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    
    #endregion
    
    #region - Create and validate Azure SQL database
    Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    
    #endregion
    
    #region - Create tables
    Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jTable
    $ret = $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateLog4jClusteredIndex
    $cmd.ExecuteNonQuery()
    
    # Create the mobiledata table and index
    $cmd.CommandText = $cmdCreateMobileTable
    $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
    $cmd.ExecuteNonQuery()
    
    $conn.close()
    
    #endregion
    
    
    #region - Create HDInsight cluster
    
    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
    
    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    
    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 
    
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
        -DefaultStorageContainer $defaultBlobContainerName 
    
    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion
    
    #region - pre-process the source file
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
    
    # Define the connection string
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
    
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    
    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)
    
    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    
    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")
    
        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)
    
            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }
    
        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }
    
    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)
    
    #endregion
    
    #region - export a log file from the cluster to the SQL database
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    $tableName_log4j = "log4jlogs"
    
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $exportDir_log4j = "/tutorials/usesqoop/data"
    
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - export a Hive table
    
    $tableName_mobile = "mobiledata"
    $exportDir_mobile = "/hive/warehouse/hivesampletable"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - import a database
    
    $targetDir_mobile = "/tutorials/usesqoop/importeddata/"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
    
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
