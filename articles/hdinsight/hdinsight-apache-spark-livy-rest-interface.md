<properties
    pageTitle="Inviare i processi di motori in modalità remota utilizzando inserire il | Microsoft Azure"
    description="Informazioni su come consente di inserire il con i cluster HDInsight per inviare i processi di motori in remoto."
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


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Inviare i processi di motori in modalità remota a un cluster ad Apache su HDInsight Linux con inserire il

Cluster ad Apache su Azure HDInsight include inserire il, un'interfaccia REST per l'invio di processi in modalità remota in un cluster di motori. Per informazioni dettagliate, vedere [inserire il](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

È possibile utilizzare inserire il eseguire motori interattivi shell o inoltrare processi batch deve essere eseguito in motori. In questo articolo parla sull'uso di inserire il inoltrare processi batch. La sintassi seguente usa curvatura per effettuare chiamate l'endpoint di inserire il resto.

**Prerequisiti:**

È necessario disporre le operazioni seguenti:

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster ad Apache su HDInsight Linux. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job-the-cluster"></a>Inviare un processo batch cluster

Prima di inviare un processo batch, è necessario caricare vaso applicazione archivio cluster associato al cluster. È possibile utilizzare [**AzCopy**](../storage/storage-use-azcopy.md), un'utilità della riga di comando per farlo. Sono disponibili molti altri client, che è possibile utilizzare per caricare i dati. È possibile trovare ulteriori informazioni sulle loro al [caricamento dei dati per i processi di Hadoop in HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Esempi**:

* Se il file vaso nell'archivio cluster (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se si desidera passare il nome del file vaso e classe come parte di un file di input (in questo esempio, txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Informazioni su batch eseguite nel cluster

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Esempi**:

* Se si vuole recuperare tutti i batch eseguite nel cluster:

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se si vuole recuperare uno specifico batch con un determinato batchId

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>Eliminare un processo batch

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Esempio**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Inserire il e disponibilità

Inserire il fornisce disponibilità per motori processi in esecuzione nel cluster. Ecco un paio di esempi.

* Se il servizio di inserire il è raggiungibile dopo l'invio di un processo in modalità remota in un cluster di motori, il processo continua per l'esecuzione in background. Una volta inserire il backup, Ripristina lo stato di processo e dei report nuovamente.

* I blocchi appunti Jupyter per HDInsight sono gestiti da inserire il in back-end. Se un blocco appunti è in esecuzione un processo di motori, ottiene riavviare il servizio di inserire il blocco appunti continuerà a eseguire le celle di codice. 

## <a name="show-me-an-example"></a>Mostra un esempio

In questa sezione, esaminati esempi su come utilizzare inserire il per inviare una richiesta di motori, monitorare l'avanzamento dell'applicazione e quindi eliminare il processo. L'applicazione che serve in questo esempio è quella sviluppati nell'articolo [creare una Scala applicazione autonoma e da eseguire in cluster ad HDInsight](hdinsight-apache-spark-create-standalone-application.md). I passaggi seguenti presuppongono le operazioni seguenti:

* È già stato copiato su vaso applicazione nell'account di archiviazione associato al cluster.
* È necessario curvatura installato nel computer in cui si siano tentando la procedura seguente.

Eseguire la procedura seguente.

1. Automatica verificare innanzitutto che inserire il sia in esecuzione nel cluster. È possibile farlo creando un elenco di esecuzione batch. Se si tratta la prima volta che si esegue un processo utilizzando inserire il, questo deve restituire zero.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Dovrebbe ottenere un risultato simile al seguente:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Si noti come l'ultima riga nell'output viene visualizzato **totale: 0**, che non suggerisce batch in esecuzione.

2. Possiamo inviare un processo batch. Il frammento di codice seguente viene utilizzato un file di input (txt) per passare il nome vaso e il nome della classe come parametri. Si consiglia di utilizzare questa opzione se si siano eseguendo la procedura seguente da un computer Windows.

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    I parametri nel file **txt** sono i seguenti:

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    Verrà visualizzato un output simile al seguente:

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Si noti come viene visualizzato l'ultima riga di output **stato: avvio di**. Verrà anche visualizzato il numero, **id: 0**. Questo è l'ID di batch.

3. È ora possibile recuperare il lo stato di questo specifico batch con l'ID batch.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Verrà visualizzato un output simile al seguente:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    A questo punto, l'output mostra **stato: esito positivo**, che indica che il processo è stato completato.

4. Se si desidera, è ora possibile eliminare il batch.

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Verrà visualizzato un output simile al seguente:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    L'ultima riga l'output mostra che il batch è stato eliminato. Se si elimina un processo mentre è in esecuzione, esso verrà essenzialmente a eliminare il processo. Se si elimina un processo che è stata completata, esito positivo o negativo, Elimina le informazioni sul processo completamente.

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
