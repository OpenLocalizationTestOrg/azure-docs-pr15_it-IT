<properties
    pageTitle="Personalizzare cluster HDInsight mediante le azioni script | Microsoft Azure"
    description="Informazioni su come personalizzare cluster HDInsight tramite Script azione."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Personalizzare i cluster HDInsight basato su Windows tramite Script azione

**Azione di script** può essere utilizzata per richiamare [script personalizzati](hdinsight-hadoop-script-actions.md) durante il processo di creazione cluster per l'installazione di software aggiuntivo in un cluster.

Le informazioni in questo articolo sono specifiche di cluster HDInsight basato su Windows. Per cluster basati su Linux, vedere [cluster basati su Linux personalizzare HDInsight tramite Script azione](hdinsight-hadoop-customize-cluster-linux.md). 

HDInsight cluster può essere personalizzato in vari altri modi, ad esempio tra altri account di archiviazione Azure, modificando il Hadoop file di configurazione (core site.xml, site.xml hive ecc.) o librerie (ad esempio Hive, Oozie) aggiunta condivise nelle posizioni comuni del cluster. Queste personalizzazioni possono essere eseguite tramite PowerShell di Azure, Azure HDInsight .NET SDK o il portale di Azure. Per ulteriori informazioni, vedere [creare Hadoop cluster in HDInsight][hdinsight-provision-cluster].

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Azione di script nel processo di creazione cluster

Azione di script viene utilizzato solo mentre un cluster è in corso la creazione. Il diagramma seguente illustra durante l'esecuzione Script azione durante il processo di creazione:

![Personalizzazione di cluster HDInsight e fasi durante la creazione di cluster][img-hdi-cluster-states]

Durante l'esecuzione di script cluster immette la fase di **ClusterCustomization** . In questa fase, lo script viene eseguito con l'account di amministratore di sistema, in parallelo su tutti i nodi specificati del cluster e fornisce privilegi di amministratore completi nei nodi.

> [AZURE.NOTE] Poiché si dispone di privilegi di amministratore nei nodi del cluster durante la fase di **ClusterCustomization** , è possibile utilizzare lo script per eseguire operazioni come arrestare e avviare servizi, inclusi quelli relativi Hadoop. Così come parte dello script, è necessario assicurarsi che i servizi di Ambari e altri servizi di Hadoop sono in esecuzione prima che lo script al termine dell'esecuzione. Questi servizi devono correttamente verificare l'integrità e lo stato del cluster durante la creazione. Se si modifica una configurazione cluster che viene applicata a questi servizi, è necessario utilizzare le funzioni di supporto forniti. Per ulteriori informazioni sulle funzioni di supporto, vedere [script sviluppare Script azione per HDInsight][hdinsight-write-script].

L'output e i log degli errori per lo script sono archiviate in account di archiviazione predefinito specificato per il cluster. I log sono archiviati in una tabella con il nome **u < \cluster-name-fragment >< \time-stamp > setuplog**. Questi sono registri aggregati da script eseguiti in tutti i nodi (nodo principale e nodi di lavoro) del cluster.

Ogni cluster può accettare più azioni di script richiamati nell'ordine in cui vengono specificati. Uno script può essere eseguito su nodo principale, i nodi di lavoro o entrambi.

HDInsight offre diversi script per installare i componenti seguenti nei cluster HDInsight:

Nome | Script
----- | -----
**Installare motori** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-Installer-v03.ps1. Vedere [installare e utilizzare riguardo nei cluster HDInsight][hdinsight-install-spark].
**Installare R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Vedere [installazione e uso R su cluster HDInsight][hdinsight-install-r].
**Installare Solr** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Vedere [installare e utilizzare cluster di Solr su HDInsight](hdinsight-hadoop-solr-install.md).
- **Installare Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Vedere [installare e utilizzare cluster di Giraph su HDInsight](hdinsight-hadoop-giraph-install.md).
| **Pre-caricare Hive raccolte** | https://hdiconfigactions.BLOB.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-v01.ps1. Vedere [aggiungere Hive raccolte in cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>Script di chiamata tramite il portale di Azure

**Dal portale di Azure**

1. Iniziare a creare un cluster come descritto nel [cluster di creare Hadoop in HDInsight](hdinsight-provision-clusters.md#portal).
2. Nella sezione configurazione facoltativo per e il **Script azioni** , fare clic su **Aggiungi azione script** per fornire dettagli sull'azione script come illustrato di seguito:

    ![Azione di usare Script per personalizzare un cluster] (./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Azione di usare Script per personalizzare un cluster")

    <table border='1'>
        <tr><th>Proprietà</th><th>Valore</th></tr>
        <tr><td>Nome</td>
            <td>Specificare un nome per l'azione script.</td></tr>
        <tr><td>Script URI</td>
            <td>Specificare l'URI per lo script richiamato per personalizzare il grafico. s</td></tr>
        <tr><td>Testa/lavoro</td>
            <td>Specificare i nodi (**testa** o **lavoro**) in cui viene eseguito lo script di personalizzazione. </b>.
        <tr><td>Parametri</td>
            <td>Specificare i parametri, se richiesto dallo script.</td></tr>
    </table>

    Premere INVIO per aggiungere più di un'azione di script per installare componenti multipli nel cluster.

3. Fare clic su **Seleziona** per salvare la configurazione di azione script e continuare con la creazione di cluster.

## <a name="call-scripts-using-azure-powershell"></a>Script di chiamata tramite PowerShell Azure

Questo script di PowerShell seguente viene illustrato come installare motori in cluster HDInsight basato su Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Per installare il software, è necessario sostituire il file di script dello script:


Quando richiesto, immettere le credenziali per il cluster. È possibile richiedere alcuni minuti prima che venga creato il grafico.

## <a name="call-scripts-using-net-sdk"></a>Script di chiamata utilizzando .NET SDK 

Nell'esempio seguente viene illustrato come installare motori in cluster HDInsight basato su Windows. Per installare il software, sarà necessario sostituire il file di script nel codice.

**Per creare un cluster di HDInsight con motori** 

1. Creare un'applicazione console c# in Visual Studio.
2. Dalla Console di gestione di pacchetti Nuget, eseguire il comando seguente.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Utilizzare le seguenti istruzioni using nel file Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. Inserire il codice della classe con le operazioni seguenti:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
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


4. Premere **F5** per eseguire l'applicazione.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Supporto per il software open source utilizzato nei cluster HDInsight
Il servizio di Microsoft Azure HDInsight è una piattaforma flessibile che consente di creare applicazioni di grande dati nel cloud tramite un insieme di tecnologie Apri origine formato intorno Hadoop offre. Microsoft Azure offre un livello generale di supporto per la tecnologia Apri origine, come illustrato nella sezione **Ambito supporto** del <a href="http://azure.microsoft.com/support/faq/" target="_blank">sito Web di Azure supportano domande frequenti su</a>. Il servizio HDInsight fornisce un ulteriore livello di supporto per alcuni dei componenti, come descritto di seguito.

Esistono due tipi di componenti Apri origine che sono disponibili nel servizio HDInsight:

- **Componenti incorporati** : questi componenti nel cluster HDInsight preinstallati e fornire funzionalità di base del cluster. Ad esempio, ResourceManager filati, il linguaggio di query Hive (HiveQL) e la libreria Mahout appartengono a questa categoria. È disponibile in un elenco completo dei componenti cluster [quali sono le novità in versioni cluster Hadoop fornite da HDInsight?](hdinsight-component-versioning.md) </a>.
- **Componenti personalizzati** : l'utente, come utente del cluster, è possibile installare o utilizzare il carico di lavoro qualsiasi componente disponibile nella community di o creati dall'utente.

Componenti incorporati sono completamente supportati e supporto Microsoft aiuta a isolare e risolvere i problemi relativi a questi componenti.

> [AZURE.WARNING] Componenti forniti con il cluster HDInsight sono completamente supportati e supporto Microsoft aiuta a isolare e risolvere i problemi relativi a questi componenti.
>
> Componenti personalizzati riceveranno supporto per le misure che consentono di risolvere il problema. Questo può comportare la risoluzione del problema o in cui viene richiesto di effettuare canali disponibili per le tecnologie Apri origine in cui si trova esperienza completa per tale tecnologia. Ad esempio, sono disponibili numerosi siti della community che possono essere usati, ad esempio: [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Progetti Apache dovranno siti di progetto in [http://apache.org](http://apache.org), ad esempio: [Hadoop](http://hadoop.apache.org/), [motori](http://spark.apache.org/).

Il servizio HDInsight offre diversi modi per usare i componenti personalizzati. Indipendentemente da come un componente utilizzato o installato sul cluster, viene applicato lo stesso livello di supporto. Di seguito è riportato un elenco dei modi più comuni che è possono utilizzare componenti personalizzati nei cluster HDInsight:

1. Invio di processi - Hadoop o altri tipi di processi eseguire o che utilizzano componenti personalizzati può essere inviato al cluster.
2. Personalizzazione di cluster - durante la creazione di cluster, è possibile specificare impostazioni aggiuntive e i componenti personalizzati installata nei nodi del cluster.
3. Esempi di-per popolari componenti personalizzati, Microsoft e gli altri utenti possono offrire esempi di come questi componenti possono essere usati su cluster HDInsight. In questi esempi vengono forniti senza supporto.

## <a name="develop-script-action-scripts"></a>Sviluppare script azione Script

Vedere [sviluppare Script azione script per HDInsight][hdinsight-write-script].


## <a name="see-also"></a>Vedere anche

- [Creare cluster Hadoop in HDInsight] [ hdinsight-provision-cluster] fornisce istruzioni su come creare un cluster di HDInsight con altre opzioni personalizzate.
- [Sviluppare script azione Script per HDInsight][hdinsight-write-script]
- [Installare e usare motori nei cluster HDInsight][hdinsight-install-spark]
- [Installare e usare R nei cluster HDInsight][hdinsight-install-r]
- [Installare e utilizzare i cluster Solr su HDInsight](hdinsight-hadoop-solr-install.md).
- [Installare e utilizzare i cluster Giraph su HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasi durante la creazione di cluster"
