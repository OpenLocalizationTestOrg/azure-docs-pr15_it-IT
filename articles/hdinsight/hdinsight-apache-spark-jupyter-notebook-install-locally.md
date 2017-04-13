<properties 
    pageTitle="Installare Jupyter blocco appunti nel computer e connettersi a un cluster di HDInsight Spark | Microsoft Azure" 
    description="Informazioni su come installare Jupyter blocco appunti in locale nel computer e connettersi a un cluster di motori di Apache su Azure HDInsight." 
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
    ms.date="09/26/2016" 
    ms.author="nitinme"/>


# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Installare Jupyter blocco appunti nel computer e connettersi a cluster ad Apache su HDInsight Linux

In questo articolo si imparerà installare Jupyter blocco appunti, con il PySpark personalizzato (per Python) e vengono motori (per Scala) con chiave motori e connettere il blocco appunti a un cluster di HDInsight. Può essere un numero dei motivi per installare Jupyter nel computer locale e possono essere presenti anche alcuni problemi. Per un elenco dei motivi e problemi, vedere la sezione [perché è consigliabile installare Jupyter nel computer](#why-should-i-install-jupyter-on-my-computer) alla fine di questo articolo.

Esistono tre passaggi principali per l'installazione Jupyter e speciale motori nel computer in uso.

* Installare App blocco appunti Jupyter
* Installare il PySpark e motori x con speciale motori
* Configurare speciale motori per accedere ad cluster su HDInsight

Per ulteriori informazioni su vengono personalizzati e speciale motori disponibili per i blocchi appunti Jupyter con cluster HDInsight, vedere [disponibili per i blocchi appunti Jupyter con i cluster Apache motori Linux su HDInsight. X](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##<a name="prerequisites"></a>Prerequisiti

Prerequisiti elencati di seguito non sono per l'installazione di Jupyter. Si tratta per la connessione del blocco appunti Jupyter a un cluster di HDInsight dopo aver installato il blocco appunti.

- Un abbonamento Azure. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster ad Apache su HDInsight Linux. Per ulteriori informazioni, vedere [creare Apache i cluster in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installare Jupyter blocco appunti nel computer in uso

È possibile installare Jupyter blocchi appunti, è necessario installare Python. Python e Jupyter sono disponibili come parte della [distribuzione Ananconda](https://www.continuum.io/downloads). Quando si installa Anaconda, una distribuzione di Python installazione effettiva. Dopo aver installato Anaconda, per aggiungere l'installazione di Jupyter, eseguire un comando. In questa sezione fornisce le istruzioni che è necessario seguire.

1. Scaricare il [programma di installazione di Anaconda](https://www.continuum.io/downloads) per la piattaforma ed eseguire l'installazione. Durante l'esecuzione della configurazione guidata, assicurarsi di selezionare l'opzione per aggiungere Anaconda alla variabile del percorso.

2. Eseguire il comando seguente per installare Jupyter.

        conda install jupyter

    Per ulteriori informazioni sulla installting Jupyter, vedere [Installazione Jupyter utilizzando Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Installare il Toolkit vengono and speciale motori

Per istruzioni su come installare speciale motori, PySpark e ad. x, vedere la [documentazione sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) su GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Configurare speciale motori per accedere al cluster HDInsight Spark

In questa sezione viene configurata la chiave di motori che è stato installato in precedenza per connettersi a un cluster di motori di Apache che è necessario avere già creato in Azure HDInsight.

1. Le informazioni di configurazione Jupyter in genere è archiviate nella cartella utenti privati. Per individuare la home directory su qualsiasi piattaforma del sistema operativo, digitare i comandi seguenti.

    Avviare la shell Python. Nella finestra di comando, digitare quanto segue:

        python

    Nella shell Python immettere il seguente comando per scoprire la home directory.

        import os
        print(os.path.expanduser('~'))

2. Passare alla home directory e creare una cartella denominata **.sparkmagic** se non esiste già.

3. All'interno della cartella, creare un file denominato **config.json** e aggiungere il seguente frammento JSON all'interno.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Sostituire **{USERNAME}**, **{CLUSTERDNSNAME}**e **{BASE64ENCODEDPASSWORD}** con i valori appropriati. È possibile utilizzare diverse utilità del linguaggio di programmazione preferito o in linea per generare una password con codificata base 64 per la password actualy. Dovrebbero essere un semplice frammento Python per l'esecuzione dal prompt dei comandi:

        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Avviare Jupyter. Usare il comando seguente dal prompt dei comandi.

        jupyter notebook

6. Verificare che sia possibile connettersi al cluster tramite il blocco appunti Jupyter e che è possibile utilizzare la chiave di motori disponibili con la x. Eseguire la procedura seguente.

    1. Creare un nuovo blocco appunti. L'angolo in alto a destra fare clic su **Nuovo**. Verrà visualizzato il kernel predefinito **Python2** e le due nuove vengono installati, **PySpark** e **ad**.

        ![Creare un nuovo blocco appunti Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Creare un nuovo blocco appunti Jupyter")

    
        Fare clic su **PySpark**.


    2. Eseguire il frammento di codice seguenti.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Se non è possibile recuperare l'output, la connessione al cluster HDInsight testata

    >[AZURE.TIP] Se si desidera aggiornare la configurazione del blocco appunti per la connessione a un altro cluster, aggiornare la config.json con un nuovo set di valori, come illustrato nel passaggio 3 sopra. 

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Perché è necessario installare Jupyter nel computer?

Può essere un numero dei motivi perché è consigliabile installare Jupyter nel computer e quindi connettersi a un cluster di motori in HDInsight.

* Anche se i blocchi appunti Jupyter già disponibili nel cluster di motori di Azure HDInsight, l'installazione di Jupyter nel computer in uso disponibile è l'opzione per creare i blocchi appunti in locale, testare l'applicazione su un cluster in esecuzione e quindi caricare i blocchi appunti cluster. Per caricare i blocchi appunti al cluster, è possibile caricarli usando il blocco appunti Jupyter che è in esecuzione o il cluster oppure salvarli nella cartella /HdiNotebooks nell'account di archiviazione associato al cluster. Per ulteriori informazioni sull'archiviazione sul cluster blocchi appunti, vedere [in cui sono archiviati i blocchi appunti Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Con i blocchi appunti disponibili in locale, è possibile connettersi a cluster motori diversi in base alle esigenze dell'applicazione.
* È possibile utilizzare GitHub per implementare un sistema di controllo origine avere il controllo delle versioni per i blocchi appunti. È anche un ambiente collaborativo nel punto in cui più utenti possono utilizzare con lo stesso blocco appunti.
* È possibile collaborare con i blocchi appunti in locale senza disporre di un cluster. È sufficiente un cluster per testare i blocchi appunti, non gestire manualmente i blocchi appunti o un ambiente di sviluppo.
* Potrebbe essere più semplice configurare l'ambiente di sviluppo locale rispetto a configurare l'installazione di Jupyter nel cluster.  È possibile trarre vantaggio del software installato localmente senza configurare uno o più cluster remoto.

>[AZURE.WARNING] Con Jupyter installato nel computer locale, più utenti possono eseguire lo stesso blocco appunti nel cluster ad stesso nello stesso momento. In questo caso, vengono create più sessioni di inserire il. Se si desidera eseguire il debug che verificarsi un problema, sarà un'attività complessa per tenere traccia delle quali sessione di inserire il appartiene a cui l'utente.




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

* [Utilizzare i pacchetti esterni con i blocchi appunti Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gestire le risorse

* [Gestire le risorse per cluster ad Apache in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia e debug processi in esecuzione in un cluster di motori di Apache in HDInsight](hdinsight-apache-spark-job-debugging.md)
