<properties
   pageTitle="Utilizzare MapReduce e PowerShell con Hadoop | Microsoft Azure"
   description="Informazioni su come usare PowerShell per eseguire in remoto i processi di MapReduce con Hadoop su HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Eseguire i processi di MapReduce con Hadoop su HDInsight tramite PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Questo documento offre un esempio dell'uso di Azure PowerShell per eseguire un processo MapReduce in un Hadoop cluster HDInsight.

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

- **Un cluster di Azure HDInsight (Hadoop in HDInsight) (basato su Windows o basati su Linux)**

- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Eseguire un processo MapReduce tramite PowerShell Azure

Azure PowerShell fornisce *i cmdlet* che consentono di eseguire in remoto i processi di MapReduce su HDInsight. Internamente, questa operazione viene eseguita mediante le chiamate resto ai [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (denominata in precedenza Templeton) in esecuzione su cluster HDInsight.

I cmdlet seguenti vengono utilizzati durante l'esecuzione di processi MapReduce in un cluster di HDInsight remoto.

* **Accesso AzureRmAccount**: autentica Azure PowerShell all'abbonamento Azure

* **Nuovo AzureRmHDInsightMapReduceJobDefinition**: crea una nuova *definizione del processo* in base alle informazioni MapReduce specificate

* **Inizio AzureRmHDInsightJob**: invia la definizione del processo a HDInsight, verrà avviato il processo e restituisce un oggetto di *processo* che può essere utilizzato per controllare lo stato del processo

* **Attendi AzureRmHDInsightJob**: utilizza l'oggetto processo per controllare lo stato del processo. È in attesa fino al termine del processo o si supera il tempo di attesa.

* **Get-AzureRmHDInsightJobOutput**: utilizzato per recuperare l'output del processo

La procedura seguente viene illustrato come utilizzare i cmdlet per eseguire un processo del cluster HDInsight.

1. Utilizzando un editor, salvare il codice seguente come **mapreducejob.ps1**. **Nome cluster** deve essere sostituito con il nome del cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Aprire un nuovo prompt dei comandi di **PowerShell Azure** . Passare al percorso del file **mapreducejob.ps1** , quindi usare il seguente comando per eseguire lo script:

        .\mapreducejob.ps1
    
    Quando si esegue lo script, potrebbe essere richiesto per eseguire l'autenticazione all'abbonamento Azure. Verrà inoltre chiesto di specificare il nome dell'account HTTPS/amministratore e la password per il cluster HDInsight.

3. Al termine del processo, verrà visualizzato output simile al seguente:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    L'output indica che il processo è stato completato correttamente.

    > [AZURE.NOTE] Se il **codice di uscita** non è un valore diverso da 0, vedere [risoluzione dei problemi](#troubleshooting).

    In questo esempio verrà archiviata anche i file scaricati in un file di **output. txt** nella directory che si esegue lo script da.

###<a name="view-output"></a>Visualizzare l'output

Aprire il file di **output. txt** in un editor di testo per visualizzare le parole e i conteggi dei prodotti dal processo.

> [AZURE.NOTE] I file di output di un processo MapReduce non sono modificabili. Se si esegue nuovamente in questo esempio, pertanto è necessario modificare il nome del file di output.

##<a id="troubleshooting"></a>Risoluzione dei problemi

Se nessuna informazione viene restituita al completamento del processo, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni sugli errori per questo processo, aggiungere il comando seguente alla fine del file **mapreducejob.ps1** , salvarlo e quindi eseguire nuovamente.

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Restituisce le informazioni che è stato scritto STDERR nel server quando è stato eseguito il processo e può essere utile determinare perché il processo.

##<a id="summary"></a>Riepilogo

Come si può notare, Azure PowerShell fornisce un modo semplice per eseguire processi MapReduce in un cluster di HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Utilizzare MapReduce su HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)
