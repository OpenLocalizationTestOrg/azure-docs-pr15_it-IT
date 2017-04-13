<properties
   pageTitle="MapReduce e Desktop remoto con Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come utilizzare Desktop remoto per connettersi a Hadoop in HDInsight ed eseguire i processi di MapReduce."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Usare MapReduce in Hadoop in HDInsight con Desktop remoto

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo articolo si imparerà a connettersi a un Hadoop cluster HDInsight mediante Desktop remoto, quindi eseguire MapReduce processi tramite il comando Hadoop.

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster basato su Windows HDInsight (Hadoop in HDInsight)

* Un computer client con Windows 10, Windows 8 o Windows 7

##<a id="connect"></a>Connettersi con Desktop remoto

Attivare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [connessione a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Usare il comando Hadoop

Quando si è connessi al desktop per il cluster HDInsight, utilizzare la procedura seguente per eseguire un processo MapReduce tramite il comando Hadoop:

1. Iniziare la **riga di comando Hadoop**desktop HDInsight. Verrà aperto un nuovo prompt di **c:\apps\dist\hadoop-&lt;numero di versione >** directory.

    > [AZURE.NOTE] Il numero di versione viene modificato quando vengono aggiornati Hadoop. La variabile di ambiente **HADOOP_HOME** può essere utilizzata per trovare il percorso. Ad esempio `cd %HADOOP_HOME%` Cambia directory alla directory Hadoop senza dover conoscere il numero di versione.

2. Per utilizzare il comando **Hadoop** per eseguire un processo di MapReduce di esempio, utilizzare il comando seguente:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Verrà avviata la classe **wordcount** , contenuta nel file **hadoop-mapreduce-examples.jar** nella directory corrente. Come input, viene utilizzato il documento **wasbs://example/data/gutenberg/davinci.txt** e output archiviata in: **wasbs: / / / esempio/dati/WordCountOutput**.

    > [AZURE.NOTE] Per ulteriori informazioni su questo processo MapReduce e i dati di esempio, vedere <a href="hdinsight-use-mapreduce.md">Usare MapReduce in HDInsight Hadoop</a>.

2. Il processo genera dettagli durante l'elaborazione e restituisce informazioni simile al seguente una volta completato il processo:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Una volta completato il processo, utilizzare il comando seguente per visualizzare un elenco di file di output memorizzati in **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Dovrebbe essere visualizzata due file, **_SUCCESS** e **parte-r-00000**. La **parte-r-00000** contenente l'output per il processo.

    > [AZURE.NOTE] Alcuni processi MapReduce possono dividere i risultati in più file **parte-r-# # #** . In questo caso, utilizzare il # # # suffisso per indicare l'ordine dei file.

4. Per visualizzare l'output, utilizzare il comando seguente:

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Verrà visualizzato un elenco delle parole contenute nel file **wasbs://example/data/gutenberg/davinci.txt** , insieme al numero di volte in cui che si è verificato ogni parola. Di seguito è illustrato un esempio dei dati contenuti nel file:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Riepilogo

Come si può notare, il comando Hadoop fornisce un modo semplice per eseguire i processi di MapReduce in un cluster di HDInsight e quindi visualizzare l'output processo.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Utilizzare MapReduce su HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)
