<properties
    pageTitle="Inviare i processi di MapReduce mediante HDInsight .NET SDK | Microsoft Azure"
    description="Informazioni su come inviare i processi di MapReduce a Azure HDInsight Hadoop mediante HDInsight .NET SDK."
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
   ms.date="10/28/2016"
    ms.author="jgao"/>

# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Eseguire i processi di MapReduce mediante HDInsight .NET SDK

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Informazioni su come inviare i processi di MapReduce mediante HDInsight .NET SDK. HDInsight cluster accompagnato da un file di vaso con alcuni esempi di MapReduce. Il file vaso è */example/jars/hadoop-mapreduce-examples.jar*.  Uno degli esempi è *wordcount*. Si sviluppa un'applicazione console c# per inviare un processo wordcount.  Il processo legge il file */example/data/gutenberg/davinci.txt* e visualizza i risultati in */example/data/davinciwordcount*.  Se si desidera eseguire nuovamente l'applicazione, è necessario pulire la cartella di output.

> [AZURE.NOTE] La procedura descritta in questo articolo deve essere eseguita da un client di Windows. Per informazioni sull'uso di un Linux, OS X o client Unix per l'uso con Hive, usare il selettore di tabulazione illustrato all'inizio dell'articolo.

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Cluster A Hadoop in HDInsight**. Vedere [Introduzione all'utilizzo basati su Linux Hadoop in HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012/2013/2015**.

##<a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Inviare i processi di MapReduce mediante HDInsight .NET SDK

HDInsight .NET SDK fornisce raccolte client .NET, che rende più facile lavorare con i cluster HDInsight da .NET. 

**Per inviare i processi**

1. Creare un'applicazione console c# in Visual Studio.
2. Dalla Console di gestione di pacchetti Nuget, eseguire il comando seguente.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

2. Utilizzare il codice seguente:

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitMRJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                    var paras = new MapReduceJobSubmissionParameters
                    {
                        //Content = "wordcount  ",
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);

                    System.Console.WriteLine("Waiting for the job completion ...");

                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. Premere **F5** per eseguire l'applicazione.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite diversi modi per creare un cluster di HDInsight. Per ulteriori informazioni, vedere gli articoli seguenti:

- Per creare un cluster e l'invio di un processo Hive, vedere [Guida introduttiva di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
- Per la creazione di cluster HDInsight, vedere [cluster basati su Linux creare Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
- Per la gestione dei cluster HDInsight, vedere [gestire Hadoop cluster in HDInsight](hdinsight-administer-use-management-portal.md).
- Per l'apprendimento HDInsight .NET SDK, vedere [informazioni di riferimento HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).
- Per non interattiva autenticare Azure, vedere [creazione di applicazioni .NET HDInsight autenticazione non interattiva](hdinsight-create-non-interactive-authentication-dotnet-applications.md).




