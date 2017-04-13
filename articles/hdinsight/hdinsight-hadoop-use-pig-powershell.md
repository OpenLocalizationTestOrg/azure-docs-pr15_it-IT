<properties
   pageTitle="Utilizzare Hadoop maialino con PowerShell in HDInsight | Microsoft Azure"
   description="Informazioni su come inviare i processi di maialino in un cluster di Hadoop tramite PowerShell Azure HDInsight."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>Eseguire i processi di maialino con PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento offre un esempio di uso di Azure PowerShell per inviare i processi di maialino a Hadoop cluster HDInsight. Maialino consente di scrivere MapReduce processi utilizzando una lingua (alfabeto latino maialino), che i modelli trasformazioni dei dati, invece di eseguire il mapping e ridurre funzioni.

> [AZURE.NOTE] In questo documento non forniscono una descrizione dettagliata delle istruzioni sull'alfabeto latino maialino utilizzate negli esempi. Per informazioni su latino maialino utilizzati in questo esempio, vedere [Usare maialino con Hadoop in HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue.

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Eseguire i processi di maialino con PowerShell

Azure PowerShell fornisce *i cmdlet* che consentono di eseguire in remoto i processi di maialino su HDInsight. Internamente, questa operazione viene eseguita mediante le chiamate resto ai [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (denominata in precedenza Templeton) in esecuzione su cluster HDInsight.

Durante l'esecuzione di processi maialino in un cluster di HDInsight remoto, vengono utilizzati i cmdlet seguenti:

* **Accesso AzureRmAccount**: autentica Azure PowerShell per l'abbonamento Azure

* **Nuovo AzureRmHDInsightPigJobDefinition**: crea una nuova *definizione del processo* utilizzando le istruzioni sull'alfabeto latino maialino specificate

* **Inizio AzureRmHDInsightJob**: invia la definizione del processo a HDInsight, verrà avviato il processo e restituisce un oggetto di *processo* che può essere utilizzato per controllare lo stato del processo

* **Attendi AzureRmHDInsightJob**: utilizza l'oggetto processo per controllare lo stato del processo. Dovrà attendere completato il processo o è stato superato il tempo di attesa.

* **Get-AzureRmHDInsightJobOutput**: utilizzato per recuperare l'output del processo

La procedura seguente viene illustrato come utilizzare i cmdlet per eseguire un processo il cluster HDInsight.

1. Utilizzando un editor, salvare il codice seguente come **pigjob.ps1**. **Nome cluster** deve essere sostituito con il nome del cluster HDInsight.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Aprire un nuovo prompt dei comandi di Windows PowerShell. Passare al percorso del file **pigjob.ps1** , quindi usare il seguente comando per eseguire lo script:

        .\pigjob.ps1
        
    Verrà innanzitutto richiesto di accedere al proprio abbonamento Azure. Quindi, verrà richiesto il nome dell'account HTTPs/amministratore e la password per il cluster HDInsight.

7. Al termine del processo, il risultato è informazioni simile al seguente:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Risoluzione dei problemi

Se nessuna informazione viene restituita al completamento del processo, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni sugli errori per questo processo, aggiungere il comando seguente alla fine del file **pigjob.ps1** , salvarlo e quindi eseguire nuovamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Restituirà le informazioni che è stato scritto STDERR nel server quando è stato eseguito il processo e può essere utile determinare perché il processo.

##<a id="summary"></a>Riepilogo

Come si può notare, Azure PowerShell fornisce un modo semplice per eseguire processi maialino in un cluster di HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su maialino in HDInsight:

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
