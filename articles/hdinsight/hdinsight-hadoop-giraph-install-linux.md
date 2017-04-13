<properties
    pageTitle="Installare e usare Giraph nello HDInsight basati su Linux (Hadoop) | Microsoft Azure"
    description="Informazioni su come installare Giraph nei cluster basati su Linux HDInsight utilizzo di azioni di Script. Azioni di script consentono di personalizzare i cluster durante la creazione, modifica configurazione cluster o l'installazione di servizi e utilità."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="larryfr"/>

# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installare Giraph in cluster HDInsight Hadoop e utilizzare Giraph per l'elaborazione di grafici di grandi dimensioni

È possibile installare Giraph qualsiasi tipo di cluster in Hadoop su Azure HDInsight tramite **Script azione** per personalizzare un cluster.

In questo argomento si imparerà a installare Giraph tramite Script azione. Dopo aver installato Giraph, si apprenderà inoltre a utilizzare Giraph per le applicazioni più comuni, ovvero per l'elaborazione di grafici di grandi dimensioni.

> [AZURE.NOTE] Le informazioni in questo articolo sono specifiche di cluster basati su Linux HDInsight. Per informazioni sull'uso con i cluster basato su Windows, vedere [Installare Giraph sui HDInsight Hadoop cluster (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>Che cos'è Giraph?

[Apache Giraph](http://giraph.apache.org/) consente di eseguire grafico elaborazione tramite Hadoop e può essere utilizzata con Azure HDInsight. Grafici del modello di relazioni tra gli oggetti, ad esempio le connessioni tra router in una rete di grandi dimensioni come Internet o relazioni tra le persone presenti social network (detta anche un grafico di social networking). Grafico elaborazione consente a motivo sulle relazioni tra gli oggetti in un grafico, ad esempio:

- Identificazione potenziali amici in base alle relazioni corrente.
- Identificare l'itinerario tra due computer in una rete.
- Calcolare il rango di pagina delle pagine Web.

> [AZURE.WARNING] Componenti forniti con il cluster HDInsight sono completamente supportati e supporto Microsoft aiuta a isolare e risolvere i problemi relativi a questi componenti.
>
> Componenti personalizzati, ad esempio Giraph, riceveranno supporto per le misure che consentono di risolvere il problema. Questo può comportare la risoluzione del problema o in cui viene richiesto di effettuare canali disponibili per le tecnologie Apri origine in cui si trova esperienza completa per tale tecnologia. Ad esempio, sono disponibili numerosi siti della community che possono essere usati, ad esempio: [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Progetti Apache dovranno siti di progetto in [http://apache.org](http://apache.org), ad esempio: [Hadoop](http://hadoop.apache.org/).

##<a name="what-the-script-does"></a>Che cosa significa lo script

Questo script esegue le operazioni seguenti:

* Installa Giraph a`/usr/hdp/current/giraph`
* Copia il `giraph-examples.jar` file allo spazio di archiviazione predefinito (WASB) per il cluster:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Installare Giraph utilizzo di azioni di Script

Script di esempio per installare Giraph in un cluster di HDInsight è disponibile nel percorso seguente.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

In questa sezione fornisce istruzioni su come utilizzare lo script di esempio durante la creazione del cluster tramite il portale di Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, HDInsight .NET SDK o Gestione risorse Azure modelli utilizzabili per applicare le azioni script. È inoltre possibile applicare le azioni script per già in esecuzione cluster. Per ulteriori informazioni, vedere [personalizzare HDInsight cluster con le azioni Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Iniziare a creare un cluster utilizzando la procedura descritta in [cluster basati su Linux creare HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md), ma non viene completata la creazione.

2. Nella e **Facoltativi** , selezionare **Le azioni Script**e fornire le informazioni seguenti:

    * __Nome__: immettere un nome descrittivo per l'azione script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    * __Testa__: selezionare questa opzione
    * __Lavoro__: lasciare questo deselezionata
    * __ZOOKEEPER__: lasciare questo deselezionata
    * __Parametri__: lasciare questo campo vuoto

3. Nella parte inferiore delle **Azioni di Script**, utilizzare il pulsante **selezione** per salvare la configurazione. Infine, utilizzare il pulsante **Selezionare** nella parte inferiore della stessa e **Facoltativi** per salvare le informazioni di configurazione facoltativo.

4. Continuare a creare il cluster come descritto nei [cluster basati su Linux creare HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Utilizzo di Giraph in HDInsight

Al termine del creazione cluster, utilizzare la procedura seguente per eseguire l'esempio SimpleShortestPathsComputation incluso in Giraph. Questo implementato l'implementazione di <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> base per trovare il percorso più breve tra gli oggetti in un grafico.

1. Connettersi a cluster HDInsight utilizzando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere le operazioni seguenti:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Per creare un nuovo file denominato __tiny_graph.txt__, utilizzare le operazioni seguenti:

        nano tiny_graph.txt

    Utilizzare la seguente come il contenuto del file:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Questi dati vengono illustrati una relazione tra gli oggetti in un grafico diretto, utilizzando il formato [origine\_id e origine\_valore, [[destinazione\_id], [bordo\_valore];...]]. Ogni riga rappresenta una relazione tra un **origine\_id** oggetto e uno o più **destinazione\_id** oggetti. Il **bordo\_valore** (o lo spessore) possono essere considerati come l'intensità o la distanza della connessione tra **source_id** e **destinazione\_id**.

    Disegnato, e utilizzando il valore (o lo spessore) come la distanza tra gli oggetti, i dati indicati potrebbe essere simile alla:

    ![tiny_graph.txt disegnato come cerchi con righe di variabile distanza tra](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Per salvare il file, usare __Ctrl + X__, quindi __Y__e infine __INVIO__ per accettare il nome del file.

3. Consente di archiviare i dati in archiviazione principale per il cluster HDInsight le operazioni seguenti:

        hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt

4. Eseguire l'esempio SimpleShortestPathsComputation utilizzando il comando seguente.

         yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

    I parametri utilizzati con questo comando sono descritti nella tabella seguente.

  	| Parametro | Risultato |
  	| --------- | ------------ |
  	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | File di vaso contenente gli esempi. |
  	| `org.apache.giraph.GiraphRunner` | Classe utilizzata per avviare gli esempi. |
  	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | Nell'esempio che verrà eseguito. In questo caso, calcola il percorso più breve tra ID 1 e tutti gli altri ID nel grafico. |
  	| `-ca mapred.job.tracker=headnodehost:9010` | Headnode per il cluster. |
  	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Il formato di input da utilizzare per i dati di input. |
  	| `-vip /example/data/tiny_graph.txt` | Il file di dati di input. |
  	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Il formato di output. In questo caso, ID e dei valori come testo normale. |
  	| `-op /example/output/shortestpaths` | Il percorso di output. |
  	| `-w 2` | Il numero di collaboratori da usare. In questo caso, 2. |

    Per ulteriori informazioni su questi e altri parametri utilizzati con Giraph esempi, vedere [Guida introduttiva Giraph](http://giraph.apache.org/quick_start.html).

5. Al termine di processo, i risultati verranno archiviati nel __wasbs: / / / esempio/esterna/shotestpaths__ directory. I file creati inizierà con __parte-m -__ e terminare con un numero che indica il primo, secondo, file e così via. Per visualizzare l'output, utilizzare il seguente:

        hdfs dfs -text /example/output/shortestpaths/*

    L'output dovrebbe essere simile al seguente:

        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    SimpleShortestPathComputation esempio è hard-coded per iniziare con oggetto ID 1 e individuare il percorso più breve su altri oggetti. Per consentire l'output deve essere letto come `destination_id distance`, in cui è distanza di valore oppure lo spessore dei bordi è stati trasferiti tra oggetto ID 1 e l'ID di destinazione.

    La visualizzazione seguente, è possibile verificare i risultati viaggiando percorsi più brevi tra ID 1 e tutti gli altri oggetti. Si noti che il percorso più breve compreso tra 1 ID e ID 4 5. Questa è la distanza totale tra <span style="color:orange">ID 1 e 3</span>, quindi <span style="color:red">ID 3 e 4</span>.

    ![Disegno di oggetti come cerchi con percorsi più brevi tracciati tra](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## <a name="next-steps"></a>Passaggi successivi

- [Installare e utilizzare cluster di tonalità in HDInsight](hdinsight-hadoop-hue-linux.md). Tonalità è un'interfaccia utente che semplifica la creazione, esecuzione e Salva processi maialino e Hive, oltre al tipo Sfoglia spazio di archiviazione predefinito per il HDInsight cluster web.

- [Installare R nei cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md): istruzioni su come utilizzare cluster personalizzazione per installare e utilizzare R nei cluster HDInsight Hadoop. R è un ambiente per il calcolo statistiche e lingua Apri origine. Fornisce centinaia di funzioni statistiche e il proprio linguaggio di programmazione che combina aspetti della programmazione funziona e orientato agli oggetti. E offre funzionalità di grafica estese.

- [Installare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Personalizzazione di cluster utilizzata per installare Solr nei cluster HDInsight Hadoop. Solr consente di eseguire operazioni di ricerca potenti sui dati archiviati.
