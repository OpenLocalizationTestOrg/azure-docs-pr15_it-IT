<properties 
    pageTitle="Problemi di motori di Apache in HDInsight noti | Microsoft Azure" 
    description="Problemi noti di motori di Apache in HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>Problemi noti di Apache i cluster su HDInsight Linux

In questo documento tiene traccia di tutti i problemi noti per l'anteprima pubblica HDInsight Spark.  

##<a name="livy-leaks-interactive-session"></a>Inserire il perdite sessione interattiva
 
Avvio di inserire il con una sessione interattiva (da Ambari o a causa il riavvio del computer virtuale di headnode 0) ancora attiva, una sessione interattiva andrà perso. Per questi motivi, nuovi processi possono bloccato nello stato accettati e non possono essere avviati.

**Attenuazione:**

Utilizzare la procedura seguente per risolvere il problema:

1. SSH in headnode. 
2. Eseguire il seguente comando per trovare l'ID dell'applicazione dei processi interattivi iniziare a inserire il. 

        yarn application –list

    Il processo di impostazione predefinita nomi sarà inserire il se i processi avviati con una sessione interattiva di inserire il senza nomi espliciti specificato, per inserire il sessione avviata dal blocco appunti Jupyter, il nome del processo verrà avviato con remotesparkmagics_ *. 

3. Eseguire il seguente comando per eliminare i processi. 

        yarn application –kill <Application ID>

Verranno avviato nuovi processi in esecuzione. 

##<a name="spark-history-server-not-started"></a>I Server di cronologia non avviata 

I Server di cronologia non viene avviato automaticamente dopo la creazione di un cluster.  

**Attenuazione:** 

Avviare manualmente il server della cronologia da Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema di autorizzazioni nella directory di registro ad 

Quando hdiuser invia un processo con spark-submit, non è presente un java.io.FileNotFoundException di errore: /var/log/spark/sparkdriver_hdiuser.log (autorizzazione negata) e il registro driver non è scritto. 

**Attenuazione:**
 
1. Aggiungere hdiuser al gruppo Hadoop. 
2. Fornire 777 autorizzazioni su /var/log/spark dopo la creazione di cluster. 
3. Aggiornare il percorso del registro motori utilizzando Ambari da una directory con autorizzazioni di 777.  
4. Esegui inviare a motori come sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Problemi relativi ai blocchi appunti Jupyter

Ecco alcuni problemi noti relativi ai blocchi appunti Jupyter.


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocchi appunti con caratteri non ASCII nei nomi di file

I blocchi appunti Jupyter che possono essere usati nei motori HDInsight cluster non può contenere caratteri non ASCII nei nomi di file. Se si tenta di caricare un file mediante la UI Jupyter che ha un nome di file non ASCII, avrà esito negativo silenziosamente (vale a dire Jupyter non consentono di caricare il file, ma non è possibile che venga generata un errore visibile uno). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Errore durante il caricamento di blocchi appunti di grandi dimensioni

Potrebbe essere visualizzato un messaggio di errore **`Error loading notebook`** quando si caricano i blocchi appunti di grandi dimensioni.  

**Attenuazione:**

Se si riceve questo errore, non significa che i dati sono perso o danneggiato.  I blocchi appunti sono ancora sul disco in `/var/lib/jupyter`, ed è possibile SSH nel cluster di accedervi. È possibile copiare i blocchi appunti dal cluster nel computer locale (con SCP o WinSCP) come una copia di backup per evitare la perdita dei dati importanti nel blocco appunti. È quindi possibile SSH tunnel nel headnode porta 8001 per accedere a Jupyter senza passano attraverso il gateway.  A questo punto, è possibile cancellare l'output del blocco appunti e salvare nuovamente per ridurre le dimensioni del blocco appunti.

Per evitare che questo messaggio di errore in futuro, è necessario eseguire alcune procedure consigliate seguenti:

* È importante mantenere ridotte le dimensioni del blocco appunti. L'output i processi di motori che verrà inviata a Jupyter viene mantenuto nel blocco appunti.  È buona norma con Jupyter in generale per evitare esecuzione `.collect()` sui grandi RDD o dataframes; Se si desidera visualizzare i contenuti del RDD, si consiglia di esecuzione `.take()` o `.sample()` in modo che l'output non cadano troppo grande.
* Inoltre, quando si salva un blocco appunti, deselezionare tutti output celle per ridurre le dimensioni.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Avvio iniziale di blocco appunti richiede più tempo del previsto 

Prima istruzione di codice nel blocco appunti Jupyter usando speciale ad potrebbe richiedere più di un minuto.  

**Spiegazione:**
 
Infatti, quando viene eseguita la prima cella di codice. In background Avvia configurazione sessione e motori, SQL e contesti Hive sono impostati. Dopo questi contesti sono impostate, la prima istruzione viene eseguita e questo sembra che l'istruzione ha richiesto troppo tempo.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Timeout di blocco appunti Jupyter nella creazione della sessione

Quando cluster motori è compresa nel risorse, ad e Pyspark. x nel blocco appunti Jupyter verrà timeout si tenta di creare la sessione. 

**Riduzioni disponibili:** 

1. Liberare alcune risorse del cluster di motori per:

    - Interruzione della altri blocchi appunti motori passando al menu Chiudi e interrompe o facendo clic su arresto in Esplora aree di blocco appunti.
    - Interruzione della altre applicazioni di motori da filati.

2. Riavviare il blocco appunti che si desidera avviare. Risorse sufficienti dovrebbero essere disponibile per la creazione di una sessione di ora.

##<a name="see-also"></a>Vedere anche

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

* [Utilizzare i pacchetti esterni con i blocchi appunti Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter nel computer e connettersi a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire le risorse

* [Gestire le risorse per cluster ad Apache in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia e debug processi in esecuzione in un cluster di motori di Apache in HDInsight](hdinsight-apache-spark-job-debugging.md)
