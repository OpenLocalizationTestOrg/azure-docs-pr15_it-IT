<properties
    pageTitle="Azione di Script per installare motori in cluster Hadoop | Microsoft Azure"
    description="Informazioni su come personalizzare un cluster di HDInsight con motori tramite Script azione."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-spark-on-hdinsight-hadoop-clusters-using-script-action"></a>Installare e usare motori nei cluster HDInsight Hadoop tramite Script azione

> [AZURE.IMPORTANT] In questo articolo è obsoleta. HDInsight sono ora disponibili ad come tipo di oggetto cluster per cluster basato su Windows, ovvero è ora possibile direttamente creare un cluster di motori senza modificare un cluster di Hadoop tramite l'azione Script. Usa il tipo di grafico ad, viene visualizzato un cluster di versione 3.2 HDInsight con motori versione 1.3.1.  Per installare versioni diverse di motori, è possibile usare Script azione. HDInsight fornisce uno script azione Script di esempio.

Informazioni su come installare motori in Windows in base a HDInsight tramite Script azione e cluster di esecuzione di motori query in HDInsight.


**Articoli correlati**

- [Creare Hadoop cluster in HDInsight](hdinsight-provision-clusters.md): informazioni generali sulla creazione di cluster HDInsight.

- [Guida introduttiva ad Apache in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): creare un cluster HDInsight Spark.

- [Personalizzare cluster HDInsight tramite Script azione][hdinsight-cluster-customize]: informazioni generali su come personalizzare cluster HDInsight tramite Script azione.

- [Script di sviluppare Script azione per HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-spark"></a>Che cos'è motori?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Motori Apache</a> è un parallelo Apri origine framework che supporta l'elaborazione in memoria per migliorare le prestazioni di grande dati analitiche applicazioni di elaborazione. Funzionalità di calcolo in memoria del motori renderlo un'ottima scelta per algoritmi iterativi nei calcoli di apprendimento e graph computer.

Motori possono anche essere utilizzato per eseguire convenzionale elaborazione dei dati basate su disco. Motori migliora il framework MapReduce tradizionale evitando scrive su disco in fasi intermedie. Inoltre, motori sono compatibile con lo spazio di archiviazione Blob Azure e Hadoop Distributed File System (HDFS) in modo che i dati esistenti possono essere elaborati rapidamente tramite motori.

In questo argomento vengono fornite istruzioni su come personalizzare un cluster di HDInsight per installare motori.

## <a name="install-spark-using-the-azure-portal"></a>Installare motori tramite il portale di Azure

Script di esempio per installare motori in un cluster di HDInsight è disponibile un blob di sola lettura Azure lo spazio di archiviazione in [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Questo script è possibile installare motori 1.2.0 o ad 1.0.2 a seconda della versione del cluster HDInsight che si crea.

- Se si usa lo script durante la creazione di un cluster di **3,2 HDInsight** , installa **motori 1.2.0**.
- Se si usa lo script durante la creazione di un cluster **3.1 HDInsight** , installa **motori 1.0.2**.

È possibile modificare lo script o creare uno script personalizzato per installare altre versioni di motori.

> [AZURE.NOTE] Script di esempio funziona solo con HDInsight 3.1 e 3.2 cluster. Per ulteriori informazioni sulle versioni di cluster HDInsight, vedere [HDInsight cluster versioni](hdinsight-component-versioning.md).

1. Iniziare a creare un cluster utilizzando l'opzione **Crea personalizzato** , come descritto nel [cluster di creare Hadoop in HDInsight](hdinsight-provision-clusters.md#portal). Scegliere la versione di raggruppamento in base alle seguenti:

    - Se si vuole installare **motori 1.2.0**, creare un cluster di 3,2 HDInsight.
    - Se si vuole installare **motori 1.0.2**, creare un cluster 3.1 HDInsight.


2. Nella pagina di **Azioni di Script** della procedura guidata, fare clic su **Aggiungi azione script** per fornire dettagli sull'azione script come illustrato di seguito:

    ![Azione di usare Script per personalizzare un cluster] (./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Azione di usare Script per personalizzare un cluster")

    <table border='1'>
        <tr><th>Proprietà</th><th>Valore</th></tr>
        <tr><td>Nome</td>
            <td>Specificare un nome per l'azione script. Ad esempio, <b>Installare motori</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Specificare la risorsa identificatore URI (Uniform) per lo script richiamato per personalizzare il grafico. Ad esempio <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>Tipo di nodo</td>
            <td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>tutti i nodi</b>, <b>solo i nodi di testa</b>o <b>solo i nodi di lavoro</b>.
        <tr><td>Parametri</td>
            <td>Specificare i parametri, se richiesto dallo script. Script per installare motori non richiede i parametri in modo che può essere lasciato vuoto.</td></tr>
    </table>

    È possibile aggiungere più di un'azione di script per installare componenti multipli nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare la creazione del cluster.

È anche possibile utilizzare lo script per installare motori in HDInsight tramite PowerShell Azure o HDInsight .NET SDK. Istruzioni per queste procedure sono vedere più avanti in questo argomento.

## <a name="use-spark-in-hdinsight"></a>Utilizzare motori in HDInsight
Motori fornisce API in Scala, Python e linguaggio. Utilizzare la shell motori interattiva per eseguire query ad. In questa sezione fornisce istruzioni su come utilizzare i diversi metodi per l'uso con motori:

- [Utilizzare la shell motori per eseguire query interattive](#sparkshell)
- [Utilizzare shell motori per eseguire query SQL ad](#sparksql)
- [Utilizzare un programma autonomo di Scala](#standalone)

###<a name="sparkshell"></a>Utilizzare la shell motori per eseguire query interattive
Per eseguire query di motori da una shell motori interattiva, procedere come segue. In questa sezione è eseguire una query ad un file di dati di esempio (/ example/data/gutenberg/davinci.txt) che è disponibile nei cluster HDInsight per impostazione predefinita.

1. Dal portale di Azure, attivare Desktop remoto per il cluster che è stato creato con motori installato e quindi remota nel cluster. Per ulteriori informazioni, vedere [connettersi a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Nella sessione remota protocollo RDP (Desktop), dal desktop, aprire la riga di comando Hadoop (da un collegamento sul desktop) e passare al percorso in cui sono installato motori; ad esempio **C:\apps\dist\spark-1.2.0**.


3. Eseguire il seguente comando per avviare la shell motori:

         .\bin\spark-shell --master yarn

    Al termine dell'esecuzione del comando, è necessario ottenere una Scala Chiedi conferma:

         scala>

5. Quando viene richiesto di Scala, immettere la query ad illustrata di seguito. Questa query conta l'occorrenza di ogni parola nel file davinci.txt che è disponibile nel/esempio/dati/gutenberg/punto lo spazio di archiviazione Blob Azure associato al cluster.

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)

6. L'output sarà simile al seguente:

    ![Output dell'esecuzione Scala shell interattiva in un cluster di HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Immettere: q per uscire dal prompt di Scala.

        :q

###<a name="sparksql"></a>Utilizzare shell motori per eseguire query SQL ad

Motori SQL consente di utilizzare motori per eseguire query relazionale espressa in linguaggio SQL (Structured Query), HiveQL o Scala. In questa sezione, si osserva utilizzando motori per eseguire una query Hive in una tabella Hive di esempio. La tabella Hive utilizzata in questa sezione (denominata **hivesampletable**) è disponibile per impostazione predefinita, quando si crea un cluster.

>[AZURE.NOTE] Nell'esempio seguente è stato creato con **riguardo 1.2.0**, che viene installato se si esegue l'azione script durante la creazione di cluster 3,2 HDInsight.

1. Dal portale di Azure, attivare Desktop remoto per il cluster che è stato creato con motori installato e quindi remota nel cluster. Per ulteriori informazioni, vedere [connettersi a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

2. In sessione RDP, dal desktop, aprire la riga di comando Hadoop (da un collegamento sul desktop) e passare al percorso in cui sono installato motori; ad esempio **C:\apps\dist\spark-1.2.0**.


3. Eseguire il seguente comando per avviare la shell motori:

         .\bin\spark-shell --master yarn

    Al termine dell'esecuzione del comando, è necessario ottenere una Scala Chiedi conferma:

         scala>

4. Quando viene richiesto di Scala, impostare il contesto Hive. Questo è necessario per lavorare con le query Hive utilizzando motori.

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    Si noti che **sc** contesto motori predefinito impostato quando si avvia shell motori.

5. Eseguire una query Hive utilizzando il contesto Hive e stampare l'output alla console. La query recupera i dati su dispositivi di una specifica apportare e limita il numero di record recuperati a 20.

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Verrà visualizzato un output simile al seguente:

    ![Motori SQL in esecuzione in un cluster di HDInsight l'output](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Immettere: q per uscire dal prompt di Scala.

        :q

### <a name="standalone"></a>Utilizzare un programma autonomo di Scala

In questa sezione è scrivere un'applicazione di Scala che conta il numero di righe che contengono le lettere ' a ' e 'b' in un file di dati di esempio (/ example/data/gutenberg/davinci.txt) che è disponibile nei cluster HDInsight per impostazione predefinita. Per scrivere e utilizzare un programma autonomo di Scala con un cluster personalizzato con l'installazione di motori, è necessario eseguire la procedura seguente:

- Scrivere un programma di Scala
- Generare il programma di Scala per ottenere il file JAR
- Eseguire il processo sul cluster

#### <a name="write-a-scala-program"></a>Scrivere un programma di Scala
In questa sezione, si scrive un programma di Scala che conta il numero di righe che contengono "a" e "b" nel file di dati di esempio.

1. Aprire un editor di testo e incollare il codice seguente:


        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"         //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

2. Salvare il file con il nome **SimpleApp.scala**.

#### <a name="build-the-scala-program"></a>Generare il programma di Scala
In questa sezione, utilizzare lo <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Strumento di creare semplici</a> (o sbt) per generare il programma di Scala. SBT richiede linguaggio 1.6 o versioni successiva, dunque, verificare di che avere la versione corretta di linguaggio installato prima di procedere con questa sezione.

1. Installare sbt da http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Creare una cartella denominata **SimpleScalaApp**e inclusi in questa cartella creare un file denominato **simple.sbt**. Si tratta di un file di configurazione che contiene informazioni sulla versione Scala dipendenze libreria, e così via. Incollare le operazioni seguenti nel file simple.sbt e salvarlo:


        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



    >[AZURE.NOTE] Assicurarsi che mantenere le righe vuote nel file.


3. Nella cartella **SimpleScalaApp** creare una struttura di directory **\src\main\scala** e incollare il programma di Scala (**SimpleApp.scala**) creata in precedenza all'interno della cartella \src\main\scala.
4. Aprire un prompt dei comandi, passare alla directory SimpleScalaApp e immettere il comando seguente:


        sbt package


    Dopo l'applicazione compilata, verrà visualizzato un file di **simpleapp_2.10 1.0.jar** creato nella directory **\target\scala-2.10** all'interno della cartella SimpleScalaApp radice.


#### <a name="run-the-job-on-the-cluster"></a>Eseguire il processo sul cluster
In questa sezione è remote in cluster che contiene ad installato e quindi copiare la cartella di destinazione del progetto SimpleScalaApp. È quindi usare il comando **Invia motori** per inviare il processo nel cluster.

1. Remote in cluster che ha installato motori. Dal computer in cui è stato scritto e compilato il programma SimpleApp.scala, copiare la cartella **SimpleScalaApp\target** e incollarlo in un percorso sul cluster.
2. Nella sessione RDP, dal desktop, aprire la riga di comando Hadoop e passare al percorso in cui è incollato la cartella di **destinazione** .
3. Immettere il seguente comando per eseguire il programma SimpleApp.scala:


        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Al termine dell'esecuzione il programma, l'output viene visualizzato nella console.


        Lines with a: 21374, Lines with b: 11430

## <a name="install-spark-using-azure-powershell"></a>Installare motori tramite PowerShell Azure

In questa sezione è utilizzare il cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Aggiungi AzureHDInsightScriptAction</a>** per richiamare gli script tramite Script azione per personalizzare un cluster. Prima di procedere, verificare che è stato installato e configurato Azure PowerShell. Per informazioni su come configurare una workstation per eseguire i cmdlet di PowerShell Azure per HDInsight, vedere [installare e configurare Azure PowerShell](../powershell-install-configure.md).

Eseguire i passaggi seguenti:

1. Aprire una finestra di PowerShell Azure e dichiarare le variabili seguenti:

        # Provide values for these variables
        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
        $clusterName = "<HDInsightClusterName>"         # HDInsight cluster name
        $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
        $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
        $containerName = $clusterName
        $location = "<MicrosoftDataCenter>"             # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $clusterNodes = <ClusterSizeInNumbers>          # Number of nodes in the HDInsight cluster
        $version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Specificare i valori di configurazione, ad esempio nodi il cluster e lo spazio di archiviazione predefinito da utilizzare.

        # Specify the configuration options
        Select-AzureSubscription $subscriptionName
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
        $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
        $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
        $config.DefaultStorageAccount.StorageContainerName=$containerName

3. Utilizzare il cmdlet **AzureHDInsightScriptAction Aggiungi** per aggiungere un'azione script alla configurazione cluster. In un secondo momento, quando viene creato il cluster, viene eseguita l'azione script.

        # Add a script action to the cluster configuration
        $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

    **Aggiungi AzureHDInsightScriptAction** cmdlet accetta i parametri seguenti:

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parametro</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definizione</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configurazione</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Oggetto di configurazione a quale script viene aggiunto le informazioni sull'azione.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome dell'azione script.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifica i nodi in cui viene eseguito lo script di personalizzazione. I valori validi sono HeadNode (per installare sul nodo principale) o DataNode (per installare in tutti i nodi di dati). È possibile usare uno o entrambi i valori.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifica l'URI da script che viene eseguito.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri richiesti per lo script. Script di esempio utilizzati in questo argomento non richiede parametri e pertanto non viene visualizzato questo parametro nel frammento riportato sopra.
    </td></tr>
    </table>

4. Infine, iniziare a creare un grafico personalizzato con motori installato.  

        # Start creating a cluster with Spark installed
        New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Quando richiesto, immettere le credenziali per il cluster. È possibile richiedere alcuni minuti prima che venga creato il grafico.

## <a name="install-spark-using-powershell"></a>Installare motori tramite PowerShell

Vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## <a name="install-spark-using-net-sdk"></a>Installare motori utilizzando .NET SDK

Vedere [personalizzare HDInsight cluster tramite Script azione](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## <a name="see-also"></a>Vedere anche

- [Creare Hadoop cluster in HDInsight](hdinsight-provision-clusters.md): creare cluster HDInsight.
- [Guida introduttiva ad Apache in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): Guida introduttiva ad in HDInsight.
- [Personalizzare cluster HDInsight tramite Script azione][hdinsight-cluster-customize]: personalizzare cluster HDInsight tramite Script azione.
- [Script di sviluppare Script azione per HDInsight](hdinsight-hadoop-script-actions.md): sviluppare script Script azione.
- [Installare R nei cluster HDInsight] [ hdinsight-install-r] fornisce istruzioni su come utilizzare la personalizzazione di cluster per installare e usare R nei cluster HDInsight Hadoop. R è un ambiente per il calcolo statistiche e lingua Apri origine. Fornisce centinaia di funzioni statistiche e il proprio linguaggio di programmazione che combina aspetti della programmazione funziona e orientato agli oggetti. E offre funzionalità di grafica estese.
- [Installare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install.md). Personalizzazione di cluster utilizzata per installare Giraph nei cluster HDInsight Hadoop. Giraph consente di eseguire grafico elaborazione tramite Hadoop e può essere utilizzata con Azure HDInsight.
- [Installare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install.md). Personalizzazione di cluster utilizzata per installare Solr nei cluster HDInsight Hadoop. Solr consente di eseguire operazioni di ricerca potenti sui dati archiviati.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: powershell-install-configure.md
