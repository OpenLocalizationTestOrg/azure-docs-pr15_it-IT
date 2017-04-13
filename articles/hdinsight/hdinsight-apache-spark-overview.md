<properties 
    pageTitle="Cenni preliminari ad Apache in HDInsight | Microsoft Azure" 
    description="Introduzione ad Apache in HDInsight e scenari in cui si desidera utilizzare motori nella HDInsight nelle applicazioni." 
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
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="overview-apache-spark-on-hdinsight-linux"></a>Panoramica: Apache motori su HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Motori Apache</a> è un parallelo Apri origine framework che supporta l'elaborazione in memoria per migliorare le prestazioni di grande dati analitiche applicazioni di elaborazione. Motore di elaborazione ad integrato per velocità, facilità di utilizzo e sofisticate analitica. Funzionalità di calcolo in memoria del motori renderlo un'ottima scelta per algoritmi iterativi nei calcoli di apprendimento e graph computer. Motori sono compatibile con lo spazio di archiviazione Blob Azure (WASB) in modo che i dati esistenti archiviati in Azure possono essere elaborati rapidamente tramite motori.

Quando si crea un cluster di motori in HDInsight, si crea risorse di elaborazione Azure con motori installato e configurato. È sufficiente circa dieci minuti per creare un cluster di motori in HDInsight. I dati per l'elaborazione vengono archiviati in archiviazione Blob Azure. Vedere [usare archiviazione Blob Azure con HDInsight][hdinsight-storage].

![Motori di Apache su Azure HDInsight] (./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Motori di Apache su Azure HDInsight")


**Se si vuole iniziare a utilizzare i Apache su Azure HDInsight?** Vedere [Guida introduttiva: creare un cluster di motori su HDInsight Linux ed eseguire le applicazioni di esempio con Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Per un elenco di problemi noti e limitazioni con la versione corrente, vedere [problemi di motori di Apache in Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="why-use-spark-on-azure-hdinsight"></a>Perché utilizzare motori su Azure HDInsight? 

Azure HDInsight offre un servizio ad completamente gestito. Vantaggi dell'utilizzo di motori sulla HDInsight sono:

| Caratteristica                             | Descrizione       |
|-------------------------------------|-------------------|
| Facilità di creazione di cluster            | È possibile creare un nuovo cluster di motori in HDInsight in minuti tramite il portale di gestione di Azure, PowerShell Azure o HDInsight .NET SDK. Vedere [Guida introduttiva a cluster di motori in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilità d'uso                     | Motori HDInsight cluster includono i blocchi appunti Jupyter preconfigurati. È possibile utilizzare per l'elaborazione dei dati interattive e di visualizzazione. L'URL per il è https://CLUSTERNAME.azurehdinsight.net/jupyter. Sostituire __nome cluster__ con il nome del cluster ad HDInsight.|
| API REST                       | Motori di HDInsight includono [inserire il](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), in base a una API REST server dei processi motori per inviare e monitorare i processi in esecuzione in modalità remota. |
| Supporto per archivio Lake dati di Azure | Motori nella HDInsight possano configurato per l'utilizzo di archivio Lake dati di Azure come un ulteriore spazio di archiviazione. Per ulteriori informazioni sui dati Lake Store, vedere [Panoramica di Azure Lake archivio](../data-lake-store/data-lake-store-overview.md).
| Integrazione con servizi Azure | Motori in HDInsight viene fornito con un connettore a un hub evento Azure. Gli utenti possono creare applicazioni di flusso con gli hub di evento, oltre a [Kafka](http://kafka.apache.org/), che è già disponibile come parte di motori. |
| Supporto per il Server R  | È possibile impostare un Server R cluster HDInsight Spark per eseguire calcoli R distribuiti con velocità impegna con un cluster di motori. Per ulteriori informazioni, vedere [Introduzione all'utilizzo di Server R HDInsight](hdinsight-hadoop-r-server-get-started.md).   |
| Integrazione con IntelliJ IDEA  | È possibile utilizzare il HDInsight Plugin per IntelliJ per creare e inviare le applicazioni a un cluster HDInsight Spark. Per ulteriori informazioni vedere [Plug-in strumenti di HDInsight di utilizzo per IDEA IntelliJ creare ad applicazioni per cluster Linux ad HDInsight](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Query simultanee              | Motori di HDInsight supportano le query simultanee. In questo modo più query da un utente o più query da diversi utenti e alle applicazioni di condividere le stesse risorse cluster. |
| La cache su SSDs                 | È possibile scegliere di dati nella cache in memoria o in SSDs associati a nodi del cluster. La memorizzazione nella cache in memoria fornisce le massime prestazioni di query ma può essere costosa; la memorizzazione nella cache in SSDs fornisce un'ottima soluzione per migliorare le prestazioni di query senza la necessità di creare un cluster di dimensioni necessarie per adattare l'intero set di dati in memoria.|
| Integrazione con strumenti di Business Intelligence       | Motori per HDInsight offre connettori per strumenti di Business Intelligence come [Power BI](http://www.powerbi.com/) e [Tableau](http://www.tableau.com/products/desktop) per analitica di dati.|
| Raccolte Anaconda precaricate        | Motori cluster su HDInsight accompagnato da raccolte Anaconda preinstallate. [Anaconda](http://docs.continuum.io/anaconda/) fornisce vicino 200 raccolte per l'apprendimento, analisi dei dati, visualizzazione e così via.|
| Scalabilità                     | Sebbene sia possibile specificare il numero dei nodi del cluster durante la creazione, è consigliabile aumentare o ridurre il cluster in modo che corrisponda carico di lavoro. Tutti i cluster HDInsight consentono di modificare il numero dei nodi del cluster. Inoltre, è possibile eliminarlo cluster motori con senza perdita di dati dal momento che tutti i dati vengono archiviati in archiviazione Blob Azure. |
| Supporto 24/7                    | Motori in HDInsight viene fornito con il supporto di 24/7 a livello di organizzazione e un contratto di servizio di 99,9% i tempi.|



## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quali sono i casi di utilizzo per motori nella HDInsight?

Motori Apache in HDInsight attiva gli scenari principali seguenti.

### <a name="interactive-data-analysis-and-bi"></a>Analisi dei dati interattive e BI

[Osservare un'esercitazione](hdinsight-apache-spark-use-bi-tools.md)

Motori di Apache in HDInsight archivia i dati nei blob Azure. Esperti aziendali e responsabili decisionali possano analizzare e compilare report su tali dati e consente di creare report interattivi da dati analizzati Microsoft Power BI. Analisti possono avviare dai dati non strutturati/semitrasparente strutturati archiviazione Azure, definire uno schema per i dati utilizzando i blocchi appunti e quindi creare modelli di dati mediante Microsoft Power BI. Motori di HDInsight supporta anche una serie di strumenti di Business Intelligence di terze parti, ad esempio Tableau, Qlikview e Lumira SAP rendendola una piattaforma ideale per gli analisti dei dati, esperti aziendali e responsabili decisionali.

### <a name="iterative-machine-learning"></a>Apprendimento iterativo

[Osservare un'esercitazione: prevedere creazione temperature abbonamento HVAC dati](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Osservare un'esercitazione: prevedere i risultati del controllo alimentari](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Motori Apache viene fornito con [MLlib](http://spark.apache.org/mllib/), una libreria basata su motori di apprendimento. Inoltre, motori in HDInsight includono anche Anaconda, una distribuzione Python con una serie di pacchetti per l'apprendimento. Usare un supporto incorporato per i blocchi appunti Jupyter e si dispone di un ambiente di inizio della riga per la creazione di applicazioni di apprendimento computer.  

### <a name="streaming-and-real-time-data-analysis"></a>Analisi dei dati di flusso e in tempo reale

[Osservare un'esercitazione](hdinsight-apache-spark-eventhub-streaming.md)

Analisi dei dati in tempo reale viene utilizzato per gli scenari che vanno dal ridurre i tempi di analisi di dati per l'elaborazione dei dati come recapitata, alla creazione di un vero e proprio soluzione di flusso. Motori di HDInsight offre un supporto completo per la creazione di soluzioni in tempo reale analitica. Mentre motori ha già connettori per acquisire dati da molte origini come sockets Kafka, Flume, Twitter, ZeroMQ o TCP, motori in HDInsight aggiunge un eccellente supporto per il caricamento di dati di Azure evento hub. Hub di evento sono il servizio di Accodamento maggiormente utilizzato in Azure. Per avere un supporto della casella per evento hub rende è riguardo in HDInsight una piattaforma ideale per la creazione della pipeline analitica in tempo reale.

##<a name="next-steps"></a>Quali componenti sono inclusi come parte di un cluster di motori?

Motori di HDInsight includono i componenti seguenti sono disponibili nei cluster per impostazione predefinita.

- [Motori Core](https://spark.apache.org/docs/1.5.1/). Include i principali, motori SQL, motori streaming API, GraphX e MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Inserire il](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Blocco appunti Jupyter](https://jupyter.org)

Motori di HDInsight vengono forniti anche un [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) per la connettività a cluster di motori in HDInsight da strumenti di Business Intelligence, ad esempio Microsoft Power BI e Tableau.

## <a name="where-do-i-start"></a>Dove iniziare?

Iniziare con la creazione di un cluster di motori su HDInsight Linux. Vedere [Guida introduttiva: creare un cluster di motori su HDInsight Linux ed eseguire le applicazioni di esempio con Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Passaggi successivi

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


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
