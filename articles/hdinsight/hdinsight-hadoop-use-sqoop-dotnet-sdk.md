<properties
    pageTitle="Usare Hadoop Sqoop in HDInsight | Microsoft Azure"
    description="Informazioni su come usare HDInsight .NET SDK per eseguire Sqoop importare ed esportare tra un cluster di Hadoop e un database SQL Azure."
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
   ms.date="09/14/2016"
    ms.author="jgao"/>

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Eseguire i processi di Sqoop utilizzando .NET SDK per Hadoop in HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare HDInsight .NET SDK per eseguire processi Sqoop in HDInsight per importare ed esportare tra cluster HDInsight e il database SQL Azure o database di SQL Server.

> [AZURE.NOTE] La procedura descritta in questo articolo può essere utilizzata con uno dei due un cluster HDInsight Windows o Linux. Tuttavia, questa procedura funziona solo da un client di Windows. Usare il selettore di tabulazione all'inizio di questo articolo per scegliere altri metodi.

###<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Cluster A Hadoop in HDInsight**. Vedere [Crea cluster e database SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Eseguire Sqoop mediante .NET SDK

HDInsight .NET SDK fornisce raccolte client .NET, che rende più facile lavorare con i cluster HDInsight da .NET. In questa sezione, si creerà un'applicazione console c# per esportare i hivesampletable nella tabella di Database SQL creata precedentemente in questa esercitazioni.

**Per inviare un processo Sqoop**

1. Creare un'applicazione console c# in Visual Studio.
2. Dalla Console di gestione di pacchetti di Visual Studio, eseguire il seguente comando Nuget per importare il pacchetto.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Utilizzare il codice seguente nel file Program.cs:

        using System.Collections.Generic;
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
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. Premere **F5** per eseguire il programma. 

##<a name="limitations"></a>Limitazioni

* Esporta in blocco - HDInsight basati su con Linux, il connettore Sqoop utilizzato per esportare i dati a Microsoft SQL Server o Database SQL Azure attualmente non supporta inserimenti di massa.

* Divisione in batch - con basati su Linux HDInsight quando si usa il `-batch` passa durante l'esecuzione di inserimenti, Sqoop eseguirà più inserimenti anziché in batch le operazioni di inserimento.

##<a name="next-steps"></a>Passaggi successivi

A questo punto sono appreso come utilizzare Sqoop. Per ulteriori informazioni, vedere:

- [Utilizzare Oozie con HDInsight](hdinsight-use-oozie.md): usare Sqoop azione in un flusso di lavoro Oozie.
- [Dati dei ritardi volo analizza utilizzando HDInsight](hdinsight-analyze-flight-delay-data.md): utilizzare Hive analizzare volo ritardare dati e quindi utilizzare Sqoop per esportare i dati in un database SQL Azure.
- [Caricare dati da HDInsight](hdinsight-upload-data.md): trovare altri metodi per il caricamento di dati in archiviazione Blob/Azure HDInsight.


