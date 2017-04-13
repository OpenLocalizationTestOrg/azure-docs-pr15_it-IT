<properties
   pageTitle="Usare Hive Hadoop con PowerShell in HDInsight | Microsoft Azure"
   description="Usare PowerShell per eseguire query Hive in Hadoop sui HDInsight."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-using-powershell"></a>Eseguire query Hive tramite PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo documento viene fornito un esempio dell'utilizzo di PowerShell Azure in modalità gruppo di risorse Azure per eseguire query Hive in un Hadoop cluster HDInsight.

> [AZURE.NOTE] In questo documento non forniscono una descrizione dettagliata delle affermazioni HiveQL utilizzati negli esempi. Per informazioni su HiveQL utilizzati in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).


**Prerequisiti**

Per completare la procedura descritta in questo articolo, è necessario quanto segue.

- **Un cluster di Azure HDInsight (Hadoop in HDInsight) (basato su Windows o basati su Linux)**
- **Un lavoro utilizzando Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>Eseguire query Hive tramite PowerShell Azure

Azure PowerShell fornisce *i cmdlet* che consentono di eseguire in modalità remota Hive query su HDInsight. Internamente, questa operazione viene eseguita mediante le chiamate resto ai [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (denominata in precedenza Templeton) in esecuzione su cluster HDInsight.

Quando si eseguono ricerche Hive in un cluster di HDInsight remoto, vengono utilizzati i cmdlet seguenti:

* **Aggiungi AzureRmAccount**: autentica Azure PowerShell all'abbonamento Azure

* **Nuovo AzureRmHDInsightHiveJobDefinition**: crea una nuova *definizione del processo* utilizzando le istruzioni HiveQL specificate

* **Inizio AzureRmHDInsightJob**: invia la definizione del processo a HDInsight, verrà avviato il processo e restituisce un oggetto di *processo* che può essere utilizzato per controllare lo stato del processo

* **Attendi AzureRmHDInsightJob**: utilizza l'oggetto processo per controllare lo stato del processo. Verrà attendere fino al termine del processo o si supera il tempo di attesa.

* **Get-AzureRmHDInsightJobOutput**: utilizzato per recuperare l'output del processo

* **Richiama AzureRmHDInsightHiveJob**: utilizzato per eseguire istruzioni HiveQL. Ciò blocca le query completa e quindi restituisce i risultati

* **Utilizzare AzureRmHDInsightCluster**: imposta cluster corrente da utilizzare per il comando **Richiama AzureRmHDInsightHiveJob**

La procedura seguente viene illustrato come utilizzare i cmdlet per eseguire un processo del cluster HDInsight:

1. Utilizzando un editor, salvare il codice seguente come **hivejob.ps1**. **Nome cluster** deve essere sostituito con il nome del cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Aprire un nuovo prompt dei comandi di **PowerShell Azure** . Passare al percorso del file **hivejob.ps1** , quindi usare il seguente comando per eseguire lo script:

        .\hivejob.ps1

    Quando viene eseguito lo script, verrà richiesto di immettere le credenziali dell'account HTTPS/amministratore per il cluster. Potrebbe essere richiesto di accedere al proprio abbonamento Azure.
    
7. Al termine del processo, il risultato è informazioni simile al seguente:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Come detto in precedenza, è possibile utilizzare **Hive richiama** per eseguire una query e attendere la risposta. Usare i comandi seguenti e sostituire **nome cluster** con il nome del cluster:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    L'output un aspetto simile al seguente:

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] Per le query HiveQL più tempo, è possibile utilizzare il cmdlet di PowerShell Azure **Stringhe** o i file di script HiveQL. Il frammento di codice seguente viene illustrato come utilizzare il cmdlet **Hive richiama** per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in wasbs: / /.
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > Per ulteriori informazioni sulle **Stringhe**, vedere <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">-stringhe usando Windows PowerShell</a>.

##<a name="troubleshooting"></a>Risoluzione dei problemi

Se nessuna informazione viene restituita al completamento del processo, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni sugli errori per questo processo, aggiungere quanto segue alla fine del file **hivejob.ps1** , salvarlo e quindi eseguire nuovamente.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Restituisce le informazioni che sono scritto STDERR nel server quando è stato eseguito il processo e può essere utile determinare perché il processo.

##<a name="summary"></a>Riepilogo

Come si può notare, Azure PowerShell fornisce un modo semplice per eseguire query Hive in un cluster di HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a name="next-steps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
