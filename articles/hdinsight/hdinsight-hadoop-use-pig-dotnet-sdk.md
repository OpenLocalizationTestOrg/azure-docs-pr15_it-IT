<properties
   pageTitle="Utilizzare Hadoop maialino con .NET in HDInsight | Microsoft Azure"
   description="Informazioni su come utilizzare .NET SDK per Hadoop per inviare i processi di maialino a Hadoop in HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Eseguire i processi di maialino con .NET SDK per Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento offre un esempio dell'uso di .NET SDK per Hadoop l'invio di processi maialino a Hadoop cluster HDInsight.

HDInsight .NET SDK fornisce raccolte client .NET che rende più facile lavorare con i cluster HDInsight da .NET. Maialino consente di creare operazioni MapReduce mediante i modelli di una serie di dati. Si apprenderà come usare un'applicazione di base c# per inviare un processo maialino a un cluster di HDInsight.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue.

* Un cluster di Azure HDInsight (Hadoop in HDInsight) (di Windows o basati su Linux).
* Visual Studio 2012 o 2013 oppure 2015.

## <a name="create-the-application"></a>Creare l'applicazione

HDInsight .NET SDK fornisce raccolte client .NET, che rende più facile lavorare con i cluster HDInsight da .NET. 


1. Aprire Visual Studio 2012 o 2013
2. Dal menu **File** scegliere **Nuovo** e quindi selezionare **progetto**.
3. Per il nuovo progetto, digitare o selezionare i valori seguenti.

    <table>
    <tr>
    <th>Proprietà</th>
    <th>Valore</th>
    </tr>
    <tr>
    <th>Categoria</th>
    <th>Modelli e Visual c# / Windows</th>
    </tr>
    <tr>
    <th>Modello</th>
    <th>Applicazione console</th>
    </tr>
    <tr>
    <th>Nome</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. Fare clic su **OK** per creare il progetto.
5. Dal menu **Strumenti** selezionare **Gestione di pacchetti di una raccolta** o **Nuget Package Manager**e quindi selezionare **Console di gestione pacchetti**.
6. Eseguire il comando seguente nella console per installare i pacchetti di .NET SDK.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo. Sostituire il codice esistente con le operazioni seguenti.

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

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }


7. Premere **F5** per avviare l'applicazione.
8. Premere **INVIO** per uscire dall'applicazione.

## <a name="summary"></a>Riepilogo

Come si può notare, .NET SDK per Hadoop consente di creare applicazioni .NET che inviano processi di maialino a un cluster di HDInsight e monitorare lo stato del processo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni generali sul maialino in HDInsight.

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altri modi per lavorare con Hadoop in HDInsight.

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md) [portale preview]: https://portal.azure.com/
