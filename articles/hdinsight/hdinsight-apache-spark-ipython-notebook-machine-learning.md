<properties 
    pageTitle="Applicazioni di apprendimento computer su HDInsight mediante ad Apache | Microsoft Azure" 
    description="Istruzioni dettagliate su come utilizzare i blocchi appunti con i Apache risorse computer delle applicazioni" 
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


# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight-linux"></a>Creare applicazioni di apprendimento per l'esecuzione su cluster ad Apache su HDInsight Linux

Informazioni su come creare un'applicazione usando un cluster di motori di Apache in HDInsight di apprendimento. In questo articolo viene illustrato come utilizzare il blocco appunti Jupyter disponibile con i cluster per creare e testare l'applicazione. L'applicazione utilizza HVAC.csv i dati di esempio sono disponibili in tutti i cluster per impostazione predefinita.

**Prerequisiti:**

È necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster ad Apache su HDInsight Linux. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Mostra i dati

Prima di iniziare la creazione dell'applicazione, possiamo comprendere la struttura dei dati e il tipo di analisi che facciamo sui dati. 

In questo articolo è usare il file di dati di esempio **HVAC.csv** disponibile nella finestra account di archiviazione Azure associato cluster HDInsight. All'interno di account di archiviazione, il file è **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Scaricare e aprire il file CSV per ottenere uno snapshot dei dati.  

![Snapshot dati HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Snapshot dei dati HVAC")

I dati vengono visualizzate la temperatura di destinazione e la temperatura effettiva di un edificio contenente sistemi HVAC installati. Si supponga la colonna di **sistema** rappresenta l'ID di sistema e la colonna **SystemAge** rappresenta il numero di anni che il sistema HVAC è stato in vigore a edificio.

Questi dati vengono utilizzati per prevedere se un edificio sarà hotter o colder basata sulla temperatura destinazione, data un ID di sistema e l'età di sistema.

##<a name="app"></a>Scrivere un'applicazione di apprendimento computer utilizzando i MLlib

In questa applicazione è utilizzare una pipeline ML motori per eseguire una classificazione di documento. Nella pipeline di è dividere il documento in parole, convertire le parole in un vettore caratteristica numerico e infine generare un modello di previsione utilizzando i vettori funzionalità e le etichette. Eseguire la procedura seguente per creare l'applicazione.

1. Dal [Portale di Azure](https://portal.azure.com/), da startboard, fare clic sul riquadro per il cluster motori (se è bloccato il startboard). È inoltre possibile passare al cluster in **Esplora tutto** > **Cluster HDInsight**.   

2. Fare clic su **Dashboard Cluster**e il cluster motori e quindi fare clic su **Blocco appunti Jupyter**. Se richiesto, immettere le credenziali di amministratore per il cluster.

    > [AZURE.NOTE] Si può anche raggiungere il blocco appunti Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __nome cluster__ con il nome del cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo blocco appunti. Fare clic su **Nuovo**e quindi fare clic su **PySpark**.

    ![Creare un nuovo blocco appunti Jupyter] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Creare un nuovo blocco appunti Jupyter")

3. Un nuovo blocco appunti viene creato e aperto con il nome Untitled.pynb. Fare clic sul nome del blocco appunti nella parte superiore e immettere un nome descrittivo.

    ![Specifica un nome per il blocco appunti] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Specifica un nome per il blocco appunti")

3. Perché è stato creato un blocco appunti usando kernel PySpark, non è necessario creare in modo esplicito qualsiasi contesti. Contesti vivacità ed Hive verranno creati automaticamente automaticamente quando si esegue la prima cella di codice. È possibile iniziare importando i tipi di necessari per questo scenario. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**. 

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        
        import os
        import sys
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        
     
4. È ora necessario caricare i dati (hvac.csv), analisi essere e usarlo per formare il modello. A tale scopo, definire una funzione che verifica se la temperatura effettiva dell'edificio è maggiore della temperatura di destinazione. Se la temperatura effettiva è maggiore, edificio è attivo, contrassegnati con il valore **1.0**. Se la temperatura effettiva è minore, edificio è fredda, contrassegnati con il valore **0,0**. 

    Incollare il frammento di codice seguente in una cella vuota e premere **MAIUSC + INVIO**.

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. Configurare la pipeline di apprendimento computer ad costituita da tre fasi: tokenizer, hashingTF e lr. Per ulteriori informazioni sulle novità di una pipeline e sul suo funzionamento vedere <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">machine motori pipeline di apprendimento</a>.

    Incollare il frammento di codice seguente in una cella vuota e premere **MAIUSC + INVIO**.

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Adattare la pipeline al documento di formazione. Incollare il frammento di codice seguente in una cella vuota e premere **MAIUSC + INVIO**.

        model = pipeline.fit(training)

7. Verificare che il documento di formazione per verifica dello stato di avanzamento con l'applicazione. Incollare il frammento di codice seguente in una cella vuota e premere **MAIUSC + INVIO**.

        training.show()

    Questa operazione deve fornire l'output simile al seguente:

        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+


    Tornare indietro e verificare l'output nel file CSV non elaborati. Ad esempio, la prima riga del file CSV contiene questi dati:

    ![Snapshot dati HVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Snapshot dei dati HVAC")

    Si noti come la temperatura effettiva è minore di edificio è fredda suggerimento di temperatura destinazione. In output di formazione, pertanto il valore di **etichetta** nella prima riga è **0,0**, ovvero che la compilazione non è attiva.

8.  Preparare un set di dati per l'esecuzione del modello esperto rispetto a. A tale scopo, passiamo un ID di sistema e l'età di sistema (contrassegnato come **SystemInfo** nell'output di formazione) e il modello da prevedere se la compilazione con l'ID di sistema e l'età di sistema sarebbe hotter (contrassegnato da 1.0) o funzioni (contrassegnato da 0,0).

    Incollare il frammento di codice seguente in una cella vuota e premere **MAIUSC + INVIO**.
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. Infine, verificare le stime sui dati di test. Incollare il frammento di codice seguente in una cella vuota e premere **MAIUSC + INVIO**.

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. Verrà visualizzato un output simile al seguente:

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    Della prima riga nella previsione, è possibile vedere che per un sistema HVAC con ID 20 e l'età di sistema di 25 anni, edificio sarà attivo (**stima = 1.0**). Il primo valore per DenseVector (0.49999) corrisponde alla stima 0,0 e al secondo valore (0.5001) corrisponde alla stima 1.0. Nell'output, anche se il secondo valore è leggermente più alto, viene visualizzato il modello **stima = 1.0**.

11. Al termine dell'esecuzione dell'applicazione, è necessario arrestare il blocco appunti per liberare le risorse. A tale scopo, dal menu **File** nel blocco appunti, fare clic su **Chiudi e interrompere**. Questo verrà arrestato e chiudere il blocco appunti.
           

##<a name="anaconda"></a>Utilizzare Anaconda scikit-informazioni su una raccolta per l'apprendimento

Apache i cluster su HDInsight includono librerie Anaconda. Questo include anche il **scikit-informazioni** raccolta per l'apprendimento. La raccolta include inoltre diversi insiemi di dati che è possibile utilizzare la creazione di applicazioni di esempio direttamente da un blocco appunti Jupyter. Per esempi sull'utilizzo di scikit-raccolta di informazioni, vedere [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Vedere anche

* [Panoramica: Apache motori su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)

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

* [Utilizzare i pacchetti esterni con i blocchi appunti Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter nel computer e connettersi a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire le risorse

* [Gestire le risorse per cluster ad Apache in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia e debug processi in esecuzione in un cluster di motori di Apache in HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
