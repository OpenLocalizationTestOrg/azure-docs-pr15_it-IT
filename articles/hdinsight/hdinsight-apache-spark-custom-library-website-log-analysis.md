<properties 
    pageTitle="Utilizzare raccolte personalizzate con un cluster HDInsight Spark per analizzare i registri di sito Web | Microsoft Azure" 
    description="Utilizzare raccolte personalizzate con un cluster HDInsight Spark per analizzare i registri di sito Web" 
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

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>Analizzare i registri di sito Web usando una libreria personalizzata con cluster ad Apache su HDInsight Linux

Il blocco appunti viene illustrato come analizzare i dati di registro con una libreria personalizzata ad in HDInsight. La raccolta personalizzata che serve è una raccolta di Python denominata **iislogparser.py**.

> [AZURE.TIP] In questa esercitazione è disponibile anche come un blocco appunti Jupyter in un cluster di motori (Linux) creati in HDInsight. L'esperienza di blocco appunti consente di eseguire i frammenti di Python dal blocco appunti. Per eseguire l'esercitazione all'interno di un blocco appunti, creare un cluster di motori, avviare un blocco appunti Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), quindi eseguire il blocco appunti **Analizza registra con motori utilizzando un library.ipynb personalizzato** nella cartella **PySpark** .

**Prerequisiti:**

È necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster ad Apache su HDInsight Linux. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Salvare i dati non elaborati come un RDD

In questa sezione vengono utilizzati il blocco appunti [Jupyter](https://jupyter.org) associato a un cluster di motori di Apache in HDInsight per eseguire i processi che elaborano i dati di esempio non elaborati e salvarlo come una tabella Hive. I dati di esempio sono un file CSV (hvac.csv) disponibile in tutti i cluster per impostazione predefinita.

Dopo aver salvati i dati come tabella Hive, nella sezione seguente si connetterà alla tabella Hive utilizzando strumenti di Business Intelligence come Power BI e Tableau.

1. Dal [Portale di Azure](https://portal.azure.com/), da startboard, fare clic sul riquadro per il cluster motori (se è bloccato il startboard). È inoltre possibile passare al cluster in **Esplora tutto** > **Cluster HDInsight**.   

2. Fare clic su **Dashboard Cluster**e il cluster motori e quindi fare clic su **Blocco appunti Jupyter**. Se richiesto, immettere le credenziali di amministratore per il cluster.

    > [AZURE.NOTE] Si può anche raggiungere il blocco appunti Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __nome cluster__ con il nome del cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo blocco appunti. Fare clic su **Nuovo**e quindi fare clic su **PySpark**.

    ![Creare un nuovo blocco appunti Jupyter] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Creare un nuovo blocco appunti Jupyter")

3. Un nuovo blocco appunti viene creato e aperto con il nome Untitled.pynb. Fare clic sul nome del blocco appunti nella parte superiore e immettere un nome descrittivo.

    ![Specifica un nome per il blocco appunti] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Specifica un nome per il blocco appunti")

4. Perché è stato creato un blocco appunti usando kernel PySpark, non è necessario creare in modo esplicito qualsiasi contesti. Contesti vivacità ed Hive verranno creati automaticamente automaticamente quando si esegue la prima cella di codice. È possibile iniziare importando i tipi di necessari per questo scenario. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**.


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. Creare un RDD utilizzando i dati del Registro di esempio già disponibili nel cluster. È possibile accedere ai dati nell'account di archiviazione predefinito associato al **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**cluster.


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Recuperare un file di log di esempio impostare per verificare che il passaggio precedente è stato completato correttamente.

        logs.take(5)

    Verrà visualizzato un output simile al seguente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analizzare i dati di registro utilizzando una libreria Python personalizzata

7. Nell'output di ogni riga rimanente corrispondenti allo schema descritto in tale intestazione le prime righe paio includono le informazioni di intestazione. Analisi tali registri possono essere complicato. Abbiamo, utilizzare una libreria Python personalizzata (**iislogparser.py**) che rende analisi tali registri molto più semplice. Per impostazione predefinita, questa raccolta è inclusa con i cluster di motori su HDInsight in **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Tuttavia, non è in questa raccolta di `PYTHONPATH` in modo che non è possibile utilizzare utilizzando un'istruzione di importazione come `import iislogparser`. Per utilizzare questa raccolta, è necessario distribuirlo a tutti i nodi di lavoro. Eseguire il frammento di codice seguente.


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`fornisce una funzione `parse_log_line` che restituisce `None` se una linea di log è una riga di intestazione e restituisce un'istanza del `LogLine` classe se rileva una linea di log. Utilizzare la `LogLine` classe per estrarre solo le righe di log dal RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Recuperare un paio di righe log estratte per verificare che il passaggio è stato completato correttamente.

        logLines.take(2)

    L'output dovrebbe essere simile al seguente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. Il `LogLine` classe, a sua volta, include alcuni metodi utili, ad esempio `is_error()`, che restituisce se una voce di log ha un codice di errore. Utilizzare questa opzione per calcolare il numero di errori nelle righe di log estratti e quindi accedere tutti gli errori in un file diverso.

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    Verrà visualizzato un output simile al seguente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. È anche possibile utilizzare **Matplotlib** per creare una visualizzazione dei dati. Ad esempio, se si desidera individuare la causa delle richieste che eseguono una lunga esperienza, è consigliabile trovare i file che richiedono più tempo per servire in Media.
Il frammento di codice seguente recupera superiore 25 risorse che ha ora la maggior parte per servire una richiesta.

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    Verrà visualizzato un output simile al seguente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. È anche possibile presentare queste informazioni nel modulo del tracciato. Come primo passaggio per creare un grafico, possiamo prima di tutto creare una tabella temporanea **AverageTime**. La tabella gruppi i log al momento per verificare se si sono verificati picchi latenza insolito in un determinato momento.

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. È quindi possibile eseguire la query SQL seguente per visualizzare tutti i record nella tabella **AverageTime** .

        %%sql -o averagetime
        SELECT * FROM AverageTime

    Il `%%sql` speciale seguita da `-o averagetime` assicura che l'output della query viene mantenuto in locale nel server Jupyter (in genere l'headnode del cluster). L'output viene mantenuto come un dataframe [Pandas](http://pandas.pydata.org/) con il nome specificato **averagetime**.

    Verrà visualizzato un output simile al seguente:

    ![Output della query SQL] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Output della query SQL")

    Per ulteriori informazioni sul `%%sql` speciale, nonché altri magics disponibile con kernel PySpark vedere [disponibile nei blocchi appunti Jupyter con i cluster ad HDInsight. X](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. È ora possibile utilizzare Matplotlib, una raccolta utilizzata per creare la visualizzazione dei dati, per creare un grafico. Poiché il tracciato deve essere creato da dataframe localmente persistente **averagetime** , il frammento di codice deve iniziare con la `%%local` speciale. In questo modo che il codice viene eseguito in locale nel server Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    Verrà visualizzato un output simile al seguente:

    ![Matplotlib output] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Matplotlib output")

16. Al termine dell'esecuzione dell'applicazione, è necessario arrestare il blocco appunti per liberare le risorse. A tale scopo, dal menu **File** nel blocco appunti, fare clic su **Chiudi e interrompere**. Questo verrà arrestato e chiudere il blocco appunti.
    

## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache motori su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Motori con BI: eseguire l'analisi dei dati interattive tramite motori in HDInsight con strumenti di Business Intelligence](hdinsight-apache-spark-use-bi-tools.md)

* [Motori di apprendimento: usare i in HDInsight per l'analisi temperatura predefiniti utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motori di apprendimento: usare i in HDInsight per prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Motori Streaming: Usare motori in HDInsight per la creazione di applicazioni di trasmissione in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

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
