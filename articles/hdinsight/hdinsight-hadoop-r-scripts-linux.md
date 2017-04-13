<properties
    pageTitle="Installazione R su basati su Linux HDInsight | Microsoft Azure"
    description="Informazioni su come installare e usare R per personalizzare i cluster Hadoop basati su Linux."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installare e usare R nei cluster HDInsight Hadoop

È possibile installare R qualsiasi tipo di cluster in Hadoop su HDInsight tramite **Script azione** cluster personalizzazione. In questo modo scienziati dati e analisti usare R per distribuire il framework di programmazione MapReduce/filati potente per l'elaborazione di grandi quantità di dati nei cluster Hadoop distribuiti in HDInsight.

> [AZURE.IMPORTANT] Il [livello di premium](https://azure.microsoft.com/pricing/details/hdinsight/) che offre la possibilità di HDInsight include Server R come parte del cluster HDInsight. In questo modo script R usare MapReduce e motori per eseguire calcoli distribuiti. Per ulteriori informazioni, vedere [Introduzione all'utilizzo di Server R HDInsight](hdinsight-hadoop-r-server-get-started.md). 


## <a name="what-is-r"></a>Che cos'è R?

<a href="http://www.r-project.org/" target="_blank">R progetto per il calcolo statistiche</a> è un ambiente per il calcolo statistiche e aprire origine lingua. R offre centinaia di compilazione funzioni statistiche e il proprio linguaggio di programmazione che combina aspetti della programmazione funziona e orientato agli oggetti. E offre funzionalità di grafica estese. R è l'ambiente di programmazione preferito per più professionale statistici e scienziati in un'ampia varietà di campi.

Nei cluster Hadoop in HDInsight personalizzate per l'utilizzo Script azione quando viene creato per installare l'ambiente R, è possibile eseguire script di R. R è compatibile con lo spazio di archiviazione Blob Azure (WASB) in modo che i dati archiviati sono possono essere elaborati utilizzando R su HDInsight.

## <a name="what-the-script-does"></a>Che cosa significa lo script

L'azione di script usato per installare R il cluster HDInsight installa i pacchetti Ubuntu seguenti, che forniscono un'installazione R base:

* [r base](http://packages.ubuntu.com/precise/r-base): pacchetto di Base GNU R
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): pacchetti Auxilliary GNU R

I pacchetti RHadoop seguenti vengono installati anche, che forniscono l'integrazione con MapReduce e HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): consente agli sviluppatori di R usare Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): consente agli sviluppatori di R usare Hadoop HDFS (WASB per HDInsight)

Inoltre, sono installati pacchetti R seguenti:

| Pacchetto R | Le caratteristiche |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Un livello basso R all'interfaccia di linguaggio. |
| [RCPP.](https://cran.r-project.org/web/packages/Rcpp/index.html) | Integrazione R e C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Oggetti serializzare/deserializzare R JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funzioni operazioni bit per bit sui vettori integer. |
| [classificazione](https://cran.r-project.org/web/packages/digest/index.html) | Creare classificazioni Hash di crittografia del R oggetti. |
| [funzionali](https://cran.r-project.org/web/packages/functional/index.html) | Alboni, scrittura e altre funzioni di ordine superiore |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Flessibile ristrutturazione e aggregare dati. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Wrapper semplice e coerente per operazioni sulle stringhe più comuni. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Strumenti per la divisione, l'applicazione e combinano i dati. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Strumenti per lo spostamento statistiche finestra, GIF, base 64, ROC AUC, e così via. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Arrotondamento corrispondenza di stringa e distanza funzioni stringa. |

## <a name="install-r-using-script-actions"></a>Installare R utilizzando le azioni Script

La seguente azione script viene utilizzata per installare R in un cluster di HDInsight. https://hdiconfigactions.BLOB.Core.Windows.NET/linuxrconfigactionv01/r-Installer-v01.sh
    
In questa sezione vengono fornite istruzioni su come utilizzare lo script quando si crea un nuovo cluster tramite il portale di Azure. 

> [AZURE.NOTE] PowerShell Azure, CLI Azure, HDInsight .NET SDK o Gestione risorse Azure modelli utilizzabili per applicare le azioni script. È inoltre possibile applicare le azioni script per già in esecuzione cluster. Per ulteriori informazioni, vedere [personalizzare HDInsight cluster con le azioni Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare il provisioning di un cluster utilizzando la procedura descritta in [cluster basati su Linux disposizione HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal), ma non completate il provisioning.

2. Nella e **Facoltativi** , selezionare **Le azioni Script**e fornire le informazioni seguenti:

    * __Nome__: immettere un nome descrittivo per l'azione script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __Testa__: selezionare questa opzione
    * __Lavoro__: selezionare questa opzione
    * __ZOOKEEPER__: selezionare questa opzione per installare nel nodo Zookeeper.
    * __Parametri__: lasciare questo campo vuoto

3. Nella parte inferiore delle **Azioni di Script**, utilizzare il pulsante **selezione** per salvare la configurazione. Infine, utilizzare il pulsante **Selezionare** nella parte inferiore della stessa e **Facoltativi** per salvare le informazioni di configurazione facoltativo.

4. Continuare il provisioning del cluster come descritto nei [cluster basati su Linux disposizione HDInsight](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="run-r-scripts"></a>Esecuzione di script R

Al termine di provisioning cluster, utilizzare la procedura seguente per utilizzare R per eseguire un'operazione MapReduce sul cluster.

1. Connettersi a cluster HDInsight utilizzando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere le operazioni seguenti:

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Dal `username@hn0-CLUSTERNAME:~$` richiesto, immettere il seguente comando per avviare una sessione di R interattiva:

        R

3. Immettere il seguente programma R. Verrà generato i numeri da 1 a 100 e moltiplicando per 2.

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    La prima riga chiama rmr2 di raccolta RHadoop, che viene utilizzato per le operazioni di MapReduce.

    Nella seconda riga genera valori 1-100, quindi archiviarli all'uso di sistema file Hadoop `to.dfs`.

    La terza riga viene creato un processo MapReduce utilizzando le funzionalità offerte dalla rmr2 e inizia elaborazione. Verrà visualizzato più righe scorrere, oltre all'inizio dell'elaborazione.

4. Per visualizzare il percorso temporaneo memorizzati per l'output MapReduce successivamente, utilizzare le operazioni seguenti:

        print(calc())

    Dovrebbe essere simile a `/tmp/file5f615d870ad2`. Per visualizzare l'output effettivo, utilizzare le operazioni seguenti:

        print(from.dfs(calc))

    L'output dovrebbe risultare analoga alla seguente:

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Per uscire R, immettere quanto segue:

        q()


## <a name="next-steps"></a>Passaggi successivi

- [Installare e utilizzare cluster di tonalità in HDInsight](hdinsight-hadoop-hue-linux.md). Tonalità è un'interfaccia utente che semplifica la creazione, esecuzione e Salva processi maialino e Hive, oltre al tipo Sfoglia spazio di archiviazione predefinito per il HDInsight cluster web.

- [Installare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install.md). Personalizzazione di cluster utilizzata per installare Giraph nei cluster HDInsight Hadoop. Giraph consente di eseguire l'elaborazione di grafico utilizzando Hadoop e può essere utilizzato con Azure HDInsight.

- [Installare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install.md). Personalizzazione di cluster utilizzata per installare Solr nei cluster HDInsight Hadoop. Solr consente di eseguire le operazioni di ricerca potenti sui dati archiviati.

- [Installare tonalità sui cluster HDInsight](hdinsight-hadoop-hue-linux.md). Personalizzazione di cluster utilizzata per installare tonalità nei cluster HDInsight Hadoop. Tonalità è un insieme di applicazioni Web utilizzato per interagire con un cluster di Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
