<properties
    pageTitle="Gestire i cluster Hadoop in HDInsight con .NET SDK | Microsoft Azure"
    description="Informazioni su come eseguire attività amministrative per i cluster Hadoop in HDInsight mediante HDInsight .NET SDK."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gestire i cluster Hadoop in HDInsight utilizzando .NET SDK

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come gestire i cluster HDInsight utilizzando [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).


**Prerequisiti**

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##<a name="connect-to-azure-hdinsight"></a>Connettersi a Azure HDInsight

È necessario pacchetti Nuget seguenti:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight

Nell'esempio seguente viene illustrato come connettersi a Azure prima di poter amministrare cluster HDInsight in abbonamento Azure.

    using System;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    namespace HDInsightManagement
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            static void Main(string[] args)
            {
                // Authenticate and get a token
                var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // insert code here

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            /// <summary>
            /// Authenticate to an Azure subscription and retrieve an authentication token
            /// </summary>
            /// <param name="TenantId">The AAD tenant ID</param>
            /// <param name="ClientId">The AAD client ID</param>
            /// <param name="SubscriptionId">The Azure subscription ID</param>
            /// <returns></returns>
            static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
            {
                var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
                var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                    ClientId, 
                    new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                    PromptBehavior.Always, 
                    UserIdentifier.AnyUser);
                return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
            }
            /// <summary>
            /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
            /// </summary>
            /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
            /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
            /// <param name="authToken">An authentication token for your Azure subscription</param>
            static void EnableHDInsight(TokenCloudCredentials authToken)
            {
                // Create a client for the Resource manager and set the subscription ID
                var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
                resourceManagementClient.SubscriptionId = SubscriptionId;
                // Register the HDInsight provider
                var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
            }
        }
    }

Si è visualizzato un messaggio quando si esegue il programma.  Se non si desidera venga visualizzato il messaggio, vedere [creare autenticazione non interattiva .NET HDInsight applicazioni](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

##<a name="create-clusters"></a>Creare cluster

Vedere [basati su Linux creare cluster in HDInsight utilizzando .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##<a name="list-clusters"></a>Elenco cluster

Frammento di codice seguenti sono elencati i cluster e alcune proprietà:

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##<a name="delete-clusters"></a>Eliminare cluster

Per eliminare un cluster sincrono o asincrono, utilizzare il frammento di codice seguenti: 

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##<a name="scale-clusters"></a>Cluster di scala
Cluster proporzioni dei caratteri caratteristica consente di modificare il numero dei nodi di lavoro utilizzato da un cluster che è in esecuzione in Azure HDInsight senza dover ricreare il cluster.

>[AZURE.NOTE] Solo cluster con HDInsight versione 3.1.3 o superiore sono supportati. Se non si conosce la versione del cluster, è possibile controllare la pagina delle proprietà.  Vedere [cluster di elenco e la presentazione](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

Effetti della modifica del numero dei nodi di dati per ogni tipo di grafico supportato da HDInsight:

- Hadoop

    Diretta, è possibile aumentare il numero dei nodi di lavoro in un cluster di Hadoop che è in esecuzione senza impatto processi in sospeso o è in esecuzione. Nuovi processi possono essere inviati anche durante l'esecuzione dell'operazione. Gli errori in un'operazione di ridimensionamento normalmente vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster di Hadoop viene ridimensionato verso il basso, riducendo il numero dei nodi di dati, alcuni dei servizi del cluster, è necessario riavviare. In questo modo tutti in esecuzione e processi in sospeso in fase di completamento dell'operazione di ridimensionamento. Al termine dell'operazione, è possibile, tuttavia, inviare nuovamente i processi.

- HBase

    Diretta, è possibile aggiungere o rimuovere nodi al cluster HBase mentre è in esecuzione. Server regionali automaticamente Bilanciamento all'interno di pochi minuti per completare l'operazione di ridimensionamento. Tuttavia, è possibile saldare manualmente server regionali accedendo headnode di cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Eccesso

    Diretta, è possibile aggiungere o rimuovere dati nodi al cluster eccesso mentre è in esecuzione. Ma dopo il completamento dell'operazione di ridimensionamento, sarà necessario ribilanciare la topologia.

    Riequilibrare può essere eseguita in due modi:

    * Interfaccia utente web eccesso
    * Strumento dell'interfaccia della riga di comando (CLI)

    Fare riferimento alla [documentazione per eccesso Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) per altri dettagli.

    L'interfaccia web eccesso è disponibile in cluster HDInsight:

    ![hdinsight eccesso scala ribilanciamento](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Di seguito viene illustrato come utilizzare il comando CLI per ribilanciare la topologia eccesso:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Frammento di codice riportato di seguito viene illustrato come ridimensionare un cluster sincrono o asincrono:

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    

##<a name="grantrevoke-access"></a>Concessione/revoca accesso

HDInsight cluster sono i seguenti servizi web HTTP (tutti questi servizi presentano endpoint REST):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Per impostazione predefinita, questi servizi vengono concessi per l'accesso. È possibile revocare/concedere l'accesso. Per revocare:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = false,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

Per concedere:

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = enable,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] Per concedere o revocare l'accesso, si verrà reimpostare il nome utente cluster e la password.

Questa operazione può essere eseguita tramite il portale. Vedere [Amministrare HDInsight tramite il portale di Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Aggiornare le credenziali utente per HTTP

È la stessa procedura [accesso Grant/revoke HTTP](#grant/revoke-access). Se il cluster è stato concesso l'accesso tramite HTTP, è necessario revocare il.  E quindi concedere l'accesso con le credenziali utente per nuovi HTTP.


##<a name="find-the-default-storage-account"></a>Trovare l'account di archiviazione predefinito

Frammento di codice riportato di seguito viene illustrato come ottenere il nome dell'account predefinito dello spazio di archiviazione e la chiave account di archiviazione predefinito per un cluster.

    var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
    foreach (var key in results.Configuration.Keys)
    {
        Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
    }


##<a name="submit-jobs"></a>Inviare i processi

**Per inviare i processi di MapReduce**

Vedere [esempi di esecuzione Hadoop MapReduce in HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Per inviare i processi di Hive** 

Vedere [esecuzione Hive query .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Per inviare i processi di maialino**

Vedere [processi eseguire maialino con .NET SDK](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Per inviare i processi di Sqoop**

Vedere [usare Sqoop con HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Per inviare i processi di Oozie**

Vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in HDInsight](hdinsight-use-oozie-linux-mac.md).

##<a name="upload-data-to-azure-blob-storage"></a>Caricare dati in archiviazione Blob Azure
Vedere [caricare dati da HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Vedere anche
* [Documentazione di riferimento HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Amministrare HDInsight tramite il portale di Azure][hdinsight-admin-portal]
* [Amministrare HDInsight mediante un'interfaccia della riga di comando][hdinsight-admin-cli]
* [Creare cluster HDInsight][hdinsight-provision]
* [Caricare dati HDInsight][hdinsight-upload-data]
* [Guida introduttiva di Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


