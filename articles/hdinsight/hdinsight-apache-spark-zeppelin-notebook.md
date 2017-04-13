<properties 
    pageTitle="Utilizzare i blocchi appunti Zeppelin con motori cluster su HDInsight Linux | Microsoft Azure" 
    description="Istruzioni dettagliate su come utilizzare i blocchi appunti Zeppelin con i cluster di motori su HDInsight Linux." 
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
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-hdinsight-linux"></a>Utilizzare i blocchi appunti Zeppelin con cluster ad Apache su HDInsight Linux

HDInsight Spark cluster includono i blocchi appunti Zeppelin che è possibile utilizzare per l'esecuzione ad processi. In questo articolo si imparerà a utilizzare il blocco appunti Zeppelin in un cluster di HDInsight.


**Prerequisiti:**

* Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster di motori di Apache. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Avviare un blocco appunti Zeppelin

1. Fare clic su **Dashboard Cluster**e il cluster motori e quindi fare clic su **Blocco appunti Zeppelin**. Se richiesto, immettere le credenziali di amministratore per il cluster.

    > [AZURE.NOTE] Si può anche raggiungere il blocco appunti Zeppelin per il cluster aprendo l'URL seguente nel browser. Sostituire __nome cluster__ con il nome del cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Creare un nuovo blocco appunti. Dal riquadro di intestazione, fare clic su **blocco appunti**e quindi fare clic su **Crea nuova nota**.

    ![Creare un nuovo blocco appunti Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "Creare un nuovo blocco appunti Zeppelin")

    Immettere un nome per il blocco appunti e quindi fare clic su **Crea nota**.

3. Inoltre, assicurarsi che l'intestazione del blocco appunti indichi allo stato di connessione. È indicato da un punto verde nell'angolo superiore destro.

    ![Stato di blocco appunti Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "Stato di blocco appunti Zeppelin")

4. Caricare dati di esempio in una tabella temporanea. Quando si crea un cluster di motori in HDInsight, il file di dati di esempio **hvac.csv**, viene copiato nell'account di archiviazione associato in **\HdiSamples\SensorSampleData\hvac**.

    Paragrafo vuoto creati per impostazione predefinita nel nuovo blocco appunti, incollare il frammento di codice seguente.

        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Premere **MAIUSC + INVIO** o fare clic sul pulsante **Riproduci** per il paragrafo eseguire il frammento di codice. Lo stato nella parte destra del paragrafo deve svolgersi da pronto in sospeso in esecuzione in completato. L'output viene visualizzata nella parte inferiore dello stesso paragrafo. La schermata risulta simile alla seguente:

    ![Creare una tabella temporanea da dati non elaborati] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Creare una tabella temporanea da dati non elaborati")

    È anche possibile specificare un titolo a ogni paragrafo. Da angolo superiore destro fare clic sull'icona **Impostazioni** e quindi fare clic su **Mostra titolo**.

5. È ora possibile eseguire istruzioni SQL ad sulla tabella **hvac** . Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID di creazione e la differenza tra le temperature effettive per ogni la di creazione di una data specificata. Premere **MAIUSC + INVIO**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 

    L'istruzione **sql %** all'inizio indica il blocco appunti da usare interprete inserire il Scala.

    La schermata seguente mostra l'output.

    ![Eseguire un'istruzione SQL ad utilizzando il blocco appunti] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Eseguire un'istruzione SQL ad utilizzando il blocco appunti")

     Fare clic su Opzioni di visualizzazione (evidenziate in rettangolo) per passare tra diverse rappresentazioni per lo stesso risultato. Fare clic su **Impostazioni** per indicare che costituiscono la chiave e i valori nell'output. L'acquisizione di schermata sopra utilizza **buildingID** come chiave e la media di **temp_diff** come valore.

    
6. È anche possibile eseguire istruzioni SQL ad tramite variabili nella query. Il frammento di codice successivo viene illustrato come definire una variabile **Temp**, in query con i valori possibili che si desidera eseguire una query con. Quando si esegue prima di tutto la query, un elenco a discesa popolata automaticamente con i valori specificati per la variabile.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 

    Incollare il frammento in un nuovo paragrafo e premere **MAIUSC + INVIO**. La schermata seguente mostra l'output.

    ![Eseguire un'istruzione SQL ad utilizzando il blocco appunti] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Eseguire un'istruzione SQL ad utilizzando il blocco appunti")

    Per le query successive, è possibile selezionare un nuovo valore dall'elenco a discesa ed eseguire nuovamente la query. Fare clic su **Impostazioni** per indicare che costituiscono la chiave e i valori nell'output. Nella schermata precedente utilizza **buildingID** come chiave, la media di **temp_diff** come valore e **targettemp** come gruppo.

7. Riavviare l'interprete di inserire il per uscire dall'applicazione. A tale scopo, aprire Impostazioni interprete facendo clic su connesso in nome utente nell'angolo superiore destro e quindi fare clic su **interprete**.

    ![Barra di avvio interprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive output")

2. Scorrere fino a inserire il interprete impostazioni e quindi scegliere **Riavvia**.

    ![Riavviare intepreter inserire il] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Riavviare intepreter Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Utilizzo di pacchetti esterni con il blocco appunti

È possibile configurare il blocco appunti Zeppelin in cluster ad Apache sul HDInsight (Linux) per l'utilizzo di pacchetti esterni, ha contribuito community che non sono inclusi in uscita della-all'uso del cluster. È possibile cercare il [repository Maven](http://search.maven.org/) per l'elenco completo dei pacchetti disponibili. È anche possibile ottenere un elenco di pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti contribuito community è disponibile in [I pacchetti](http://spark-packages.org/).

In questo articolo, verrà visualizzato come utilizzare il pacchetto [ad csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con il blocco appunti Jupyter.

1. Aprire Impostazioni interprete. Nell'angolo superiore destro fare clic su registrato nella casella nome utente e quindi fare clic su **interprete**.

    ![Barra di avvio interprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive output")

2. Scorrere fino a inserire il interprete impostazioni e quindi fare clic su **Modifica**.

    ![Modificare le impostazioni di interprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Modificare le impostazioni di interprete")

3. Aggiungere una nuova chiave denominata **livy.spark.jars.packages** e impostarne il valore nel formato `group:id:version`. Pertanto, se si desidera utilizzare il pacchetto di [motori csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , è necessario impostare il valore della chiave da `com.databricks:spark-csv_2.10:1.4.0`.

    ![Modificare le impostazioni di interprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Modificare le impostazioni di interprete")

    Fare clic su **Salva** e quindi riavviare l'interprete di inserire il.

4. **Suggerimento**: se si vuole sapere come ottenere il valore della chiave immesso sopra, ecco come.

    un. Individuare il pacchetto nel Repository Maven. Per questa esercitazione, è stato usato [motori csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Dal repository, raccogliere i valori per **ID gruppo**, **un ArtifactId**e **versione**.

    ![Pacchetti esterni usare con blocco appunti Jupyter] (./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Pacchetti esterni usare con blocco appunti Jupyter")

    c. Concatena i tre valori separati da due punti (****).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>In cui vengono salvati i blocchi appunti Zeppelin?

I blocchi appunti Zeppelin vengono salvati headnodes cluster. In modo che, se si elimina il cluster, verranno eliminati anche i blocchi appunti. Se si desidera mantenere i blocchi appunti per successivi utilizzi in altri cluster, è necessario esportarli dopo aver completato i processi. Per esportare un blocco appunti, fare clic sull'icona **Esporta** come illustrato nell'immagine seguente.

![Scaricare blocco appunti] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Scaricare il blocco appunti")

Consente di salvare il blocco appunti come file JSON nel percorso di download.

## <a name="livy-session-management"></a>Gestione delle sessioni di inserire il

Quando si esegue il primo paragrafo di codice del blocco appunti Zeppelin, viene creata una nuova sessione di inserire il cluster HDInsight Spark. Questa sessione viene condiviso tra tutti i blocchi appunti Zeppelin che verranno create. Se per qualche motivo inserire il sessione è stata interrotta (riavviare il computer cluster e così via) non sarà possibile eseguire processi dal blocco Zeppelin.

In questo caso, è necessario eseguire la procedura seguente prima di iniziare l'esecuzione di processi da un blocco appunti Zeppelin. 

1. Riavviare l'interprete di inserire il dal blocco Zeppelin. A tale scopo, aprire Impostazioni interprete facendo clic su connesso in nome utente nell'angolo superiore destro e quindi fare clic su **interprete**.

    ![Barra di avvio interprete] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive output")

2. Scorrere fino a inserire il interprete impostazioni e quindi scegliere **Riavvia**.

    ![Riavviare intepreter inserire il] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Riavviare intepreter Zeppelin")

3. Eseguire una cella di codice da un blocco appunti Zeppelin esistente. In questo modo si crea una nuova sessione di inserire il cluster HDInsight.

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







