<properties 
    pageTitle="Utilizzare i pacchetti esterni con i blocchi appunti Jupyter in Apache i cluster su HDInsight | Azure"
    description="Istruzioni dettagliate su come configurare i blocchi appunti Jupyter disponibili con i cluster HDInsight Spark per utilizzare i pacchetti di motori esterni." 
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
    ms.date="10/28/2016" 
    ms.author="nitinme"/>


# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Utilizzare i pacchetti esterni con i blocchi appunti Jupyter in Apache i cluster su HDInsight Linux

>[AZURE.NOTE] In questo articolo è applicabile ai vivacità 1.5.2 su 3.3 HDInsight ed motori 1.6.2 su 3.4 HDInsight. 

Informazioni su come configurare un blocco appunti Jupyter in cluster ad Apache sul HDInsight (Linux) usare esterni, i pacchetti contribuito community che non sono inclusi della casella del cluster. 

È possibile cercare il [repository Maven](http://search.maven.org/) per l'elenco completo dei pacchetti disponibili. È anche possibile ottenere un elenco di pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti contribuito community è disponibile in [I pacchetti](http://spark-packages.org/).

In questo articolo si imparerà a utilizzare il pacchetto di [motori csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con il blocco appunti Jupyter.

##<a name="prerequisites"></a>Prerequisiti

È necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster ad Apache su HDInsight Linux. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizzare i pacchetti esterni con i blocchi appunti Jupyter 

1. Dal [Portale di Azure](https://portal.azure.com/), da startboard, fare clic sul riquadro per il cluster motori (se è bloccato il startboard). È inoltre possibile passare al cluster in **Esplora tutto** > **Cluster HDInsight**.   

2. Fare clic su **Collegamenti rapidi**, e il cluster motori e da e il **Cluster Dashboard** , fare clic su **Blocco appunti Jupyter**. Se richiesto, immettere le credenziali di amministratore per il cluster.

    > [AZURE.NOTE] Si può anche raggiungere il blocco appunti Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __nome cluster__ con il nome del cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo blocco appunti. Fare clic su **Nuovo**e quindi fare clic su **motori**.

    ![Creare un nuovo blocco appunti Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Creare un nuovo blocco appunti Jupyter")

3. Un nuovo blocco appunti viene creato e aperto con il nome Untitled.pynb. Fare clic sul nome del blocco appunti nella parte superiore e immettere un nome descrittivo.

    ![Specifica un nome per il blocco appunti] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Specifica un nome per il blocco appunti")

4. Si userà il `%%configure` speciale per configurare il blocco appunti per l'utilizzo di un pacchetto esterno. Nei blocchi appunti che utilizzano pacchetti esterni, verificare che si chiama la `%%configure` particolare nella prima cella di codice. In questo modo che il kernel sia configurato per utilizzare il pacchetto prima viene avviata la sessione.

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    >[AZURE.IMPORTANT] Se si dimentica configurare il kernel nella prima cella, è possibile utilizzare il `%%configure` con la `-f` parametro, ma che verranno riavviare la sessione e lo stato di avanzamento tutti andranno persa.

5. Nel frammento di codice riportati sopra, `packages` prevede un elenco di coordinate maven Maven repository centrale. In questo frammento `com.databricks:spark-csv_2.10:1.4.0` è coordinata maven per pacchetto **ad csv** . Ecco come viene creata le coordinate per un pacchetto.

    un. Individuare il pacchetto nel Repository Maven. Per questa esercitazione, serve [ad csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Dal repository, raccogliere i valori per **ID gruppo**, **un ArtifactId**e **versione**.

    ![Pacchetti esterni usare con blocco appunti Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Pacchetti esterni usare con blocco appunti Jupyter")

    c. Concatena i tre valori separati da due punti (****).

        com.databricks:spark-csv_2.10:1.4.0

6. Eseguire la cella codice con la `%%configure` speciale. Questo configurerà la sessione di inserire il sottostante per utilizzare il pacchetto che è specificato. Nelle celle successive nel blocco appunti, è ora possibile usare il pacchetto, come illustrato di seguito.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. È quindi possibile eseguire i frammenti, come mostrato di seguito per visualizzare i dati da dataframe creato nel passaggio precedente.

        df.show()

        df.select("Time").count()


## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache motori su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)

* [Motori di apprendimento: usare i in HDInsight per l'analisi temperatura predefiniti utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motori di apprendimento: usare i in HDInsight per prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motori Streaming: Usare motori in HDInsight per la creazione di applicazioni di trasmissione in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log sito Web tramite motori in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma utilizza Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster di motori tramite inserire il](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Plug-in strumenti di HDInsight per IntelliJ IDEA consente di creare e inviare Scala ad applicazioni](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizzare plug-in strumenti di HDInsight per IntelliJ IDEA il debug delle applicazioni di motori in modalità remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usare i blocchi appunti Zeppelin con un cluster di motori su HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Disponibile per blocco appunti Jupyter cluster motori per HDInsight. x](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Installare Jupyter nel computer e connettersi a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire le risorse

* [Gestire le risorse per cluster ad Apache in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia e debug processi in esecuzione in un cluster di motori di Apache in HDInsight](hdinsight-apache-spark-job-debugging.md)
