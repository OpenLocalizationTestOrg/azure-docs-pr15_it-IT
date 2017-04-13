<properties
    pageTitle="Creare un cluster di motori su HDInsight Linux e usare i SQL da Jupyter per l'analisi interattiva | Microsoft Azure"
    description="Istruzioni dettagliate su come creare rapidamente un ad Apache cluster HDInsight e quindi utilizzano i SQL dai blocchi appunti Jupyter per eseguire query interattive."
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
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="get-started-create-apache-spark-cluster-on-hdinsight-linux-and-run-interactive-queries-using-spark-sql"></a>Guida introduttiva: creare cluster ad Apache su HDInsight Linux ed eseguire query interattive utilizzando i SQL

Informazioni su come creare un cluster di motori di Apache in HDInsight e quindi utilizzare blocco appunti [Jupyter](https://jupyter.org) per eseguire query interattive SQL i cluster motori.

   ![Per iniziare a usare i Apache in HDInsight] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Per iniziare a usare i Apache in esercitazione HDInsight. I passaggi illustrati: creare un account di archiviazione. creare un cluster; eseguire istruzioni SQL ad")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Prerequisiti

- **Azure un abbonamento**. Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento a Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Client A Secure Shell (SSH)**: sistemi Linux, Unix e OS X conservato un SSH client tramite il `ssh` comando. Per i sistemi Windows, è consigliabile [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Tasti di Secure Shell (SSH) (facoltativi)**: È possibile proteggere l'account SSH utilizzato per connettersi al cluster utilizzando una password o una chiave pubblica. Con una password è possibile iniziare rapidamente e utilizzare questa opzione se si desidera creare un cluster ed eseguire alcuni processi di test rapidamente. Utilizzo di una chiave è più sicura, tuttavia richiede l'esecuzione di installazione aggiuntive. È consigliabile usare questo approccio quando si crea un cluster di produzione. In questo articolo è utilizzato l'approccio password. Per istruzioni su come creare e usare i tasti SSH con HDInsight, vedere gli articoli seguenti:

    -  Da un Linux computer - [Uso SSH con basati su Linux HDInsight (Hadoop) da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  Da un computer Windows - [Uso SSH con basati su Linux HDInsight (Hadoop) da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] In questo articolo viene utilizzato un modello di gestione delle risorse Azure per creare un cluster di motori che utilizza [Lo spazio di archiviazione BLOB come lo spazio di archiviazione cluster](hdinsight-hadoop-use-blob-storage.md). È anche possibile creare un cluster di motori che utilizza [Archivio Lake dati di Azure](../data-lake-store/data-lake-store-overview.md) come un ulteriore spazio di archiviazione, oltre a archiviazione BLOB come spazio di archiviazione predefinito. Per ulteriori informazioni, vedere [creare un cluster di HDInsight con dati Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="access-control-requirements"></a>Requisiti di controllo accesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Creare motori cluster

In questa sezione, si crea un cluster di versione 3.4 HDInsight (motori versione 1.6.1) utilizzando un modello di gestione delle risorse Azure. Per informazioni sulle versioni HDInsight e i contratti di servizio, vedere [il controllo delle versioni componente HDInsight](hdinsight-component-versioning.md). Per altri metodi di creazione cluster, vedere [creare HDInsight cluster](hdinsight-hadoop-provision-linux-clusters.md).

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello si trova in un contenitore di blob pubblica, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. Da e il parametri immettere quanto segue:

    - **Nome cluster**: immettere un nome per il cluster Hadoop che verrà creato.
    - **Nome di accesso cluster e la password**: il nome di accesso predefinito è amministratore.
    - **Nome utente SSH e password**.
    
    Prendere nota questi valori.  Saranno necessari in un secondo momento nell'esercitazione.

    > [AZURE.NOTE] SSH viene utilizzato per accedere al cluster HDInsight utilizzando una riga di comando. Il nome utente e la password utilizzati in questo campo viene utilizzato quando ci si connette al cluster tramite SSH. Inoltre, il nome dell'utente SSH deve essere univoco, durante la creazione di un account utente in tutti i nodi di cluster HDInsight. Le operazioni seguenti sono riportati alcuni dei nomi di account riservati a servizi del cluster e non può essere utilizzati come nome utente SSH:
    >
    > radice, hdiuser, eccesso, hbase, ubuntu, zookeeper, hdfs, filati, mapred, hbase, hive, oozie, falcon, sqoop, amministratore, tez, hcat, zookeeper hdinsight.

    > Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere uno degli articoli seguenti:

    > * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. fare clic su **OK** per salvare i parametri.

4 e il **distribuzione personalizzata** , fare clic su casella di **gruppo di risorse** a discesa e quindi fare clic su **Nuovo** per creare un nuovo gruppo di risorse. Gruppo di risorse è un contenitore che raggruppa cluster, l'account di archiviazione dipendenti e altre risorse collegate.

5 fare clic su **note legali**e quindi fare clic su **Crea**.

6. fare clic su **Crea**. Verrà visualizzato un nuovo riquadro intitolato Submitting distribuzione per la distribuzione dei modelli. Richiede circa circa il 20 minuti per creare il cluster e il database SQL.



## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Eseguire query SQL ad mediante un blocco appunti Jupyter

In questa sezione, è possibile Jupyter blocco appunti per eseguire query SQL ad su cluster ad. HDInsight Spark cluster offrono due vengono utilizzate con il blocco appunti Jupyter. Di seguito sono:

* **PySpark** (per le applicazioni scritto in Python)
* **Motori** (per le applicazioni scritte in Scala)

In questo articolo si utilizzerà il kernel PySpark. Vedere l'articolo [disponibile nei blocchi appunti Jupyter con i cluster ad HDInsight. X](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) è possibile leggere in modo dettagliato sui vantaggi dell'utilizzo kernel PySpark. Tuttavia, alcuni dei principali vantaggi dell'utilizzo kernel PySpark sono:

* Non è necessario impostare il contesto per vivacità ed Hive. Questi vengono impostati automaticamente dell'utente.
* È possibile utilizzare magics di cella, ad esempio `%%sql`, eseguire direttamente le query SQL o Hive senza qualsiasi frammenti di codice precedenti.
* L'output della query SQL o Hive automaticamente visualizzato.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Crea blocco appunti Jupyter con kernel PySpark 

1. Dal [Portale di Azure](https://portal.azure.com/), da startboard, fare clic sul riquadro per il cluster motori (se è bloccato il startboard). È inoltre possibile passare al cluster in **Esplora tutto** > **Cluster HDInsight**.   

2. Fare clic su **Dashboard Cluster**e il cluster motori e quindi fare clic su **Blocco appunti Jupyter**. Se richiesto, immettere le credenziali di amministratore per il cluster.

    > [AZURE.NOTE] Si può anche raggiungere il blocco appunti Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __nome cluster__ con il nome del cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo blocco appunti. Fare clic su **Nuovo**e quindi fare clic su **PySpark**.

    ![Creare un nuovo blocco appunti Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Creare un nuovo blocco appunti Jupyter")

3. Un nuovo blocco appunti viene creato e aperto con il nome Untitled.pynb. Fare clic sul nome del blocco appunti nella parte superiore e immettere un nome descrittivo.

    ![Specifica un nome per il blocco appunti] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Specifica un nome per il blocco appunti")

4. Perché è stato creato un blocco appunti usando kernel PySpark, non è necessario creare in modo esplicito qualsiasi contesti. Contesti vivacità ed Hive verranno creati automaticamente automaticamente quando si esegue la prima cella di codice. È possibile iniziare importando i tipi di necessari per questo scenario. A tale scopo, incollare il frammento di codice seguente in una cella e premere **MAIUSC + INVIO**.

        from pyspark.sql.types import *
        
    Ogni volta che si esegue un processo in Jupyter, il titolo della finestra del browser web verrà visualizzato uno stato **(disponibilità)** accanto al titolo del blocco appunti. Verrà anche visualizzato un cerchio in tinta unita accanto al testo **PySpark** nell'angolo superiore destro. Una volta completato il processo, questo verrà modificato in un cerchio vuoto.

     ![Stato di un processo di blocco appunti Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Stato di un processo di blocco appunti Jupyter")

4. Caricare dati di esempio in una tabella temporanea. Quando si crea un cluster di motori in HDInsight, il file di dati di esempio **hvac.csv**, viene copiato nell'account di archiviazione associato in **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    In una cella vuota, incollare l'esempio seguente e premere **MAIUSC + INVIO**. In questo esempio Registra i dati in una tabella temporanea denominata **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Poiché si utilizza un kernel PySpark, è ora possibile direttamente eseguire una query SQL nella tabella temporanea **hvac** appena creata utilizzando il `%%sql` speciale. Per ulteriori informazioni sul `%%sql` speciale, nonché altri magics disponibile con kernel PySpark vedere [disponibile nei blocchi appunti Jupyter con i cluster ad HDInsight. X](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Al termine il processo, viene visualizzato il seguente output tabulare per impostazione predefinita.

    ![Output di tabella del risultato della query] (./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Output di tabella del risultato della query")

    È anche possibile visualizzare i risultati in anche altre visualizzazioni. Ad esempio, un grafico ad area per lo stesso output risulterebbe simile al seguente.

    ![Grafico ad aree del risultato della query] (./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Grafico ad aree del risultato della query")


6. Al termine dell'esecuzione dell'applicazione, è necessario arrestare il blocco appunti per liberare le risorse. A tale scopo, dal menu **File** nel blocco appunti, fare clic su **Chiudi e interrompere**. Questo verrà arrestato e chiudere il blocco appunti.

##<a name="delete-the-cluster"></a>Eliminare il cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="see-also"></a>Vedere anche


* [Panoramica: Apache motori su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)

* [Motori di apprendimento: usare i in HDInsight per l'analisi temperatura predefiniti utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motori di apprendimento: usare i in HDInsight per prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motori Streaming: Usare motori in HDInsight per la creazione di applicazioni di trasmissione in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log sito Web tramite motori in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Analisi dei dati telemetria comprendere l'applicazione utilizzando motori HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma utilizza Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster di motori tramite inserire il](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Plug-in strumenti di HDInsight per IntelliJ IDEA consente di creare e inviare Scala ad applicazioni](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizzare plug-in strumenti di HDInsight per IntelliJ IDEA il debug delle applicazioni di motori in modalità remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usare i blocchi appunti Zeppelin con un cluster di motori su HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Disponibile per blocco appunti Jupyter cluster motori per HDInsight. x](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilizzare i pacchetti esterni con i blocchi appunti Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter nel computer e connettersi a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire le risorse

* [Gestire le risorse per cluster ad Apache in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia e debug processi in esecuzione in un cluster di motori di Apache in HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
