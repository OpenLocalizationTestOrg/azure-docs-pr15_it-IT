<properties
   pageTitle="Connessione MapReduce e SSH con Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come usare SSH per eseguire processi MapReduce utilizzando Hadoop in HDInsight."
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
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Utilizzare MapReduce con Hadoop in HDInsight con SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo articolo si imparerà a utilizzare Secure Shell (SSH) per connettersi a un Hadoop cluster HDInsight e quindi inviare i processi di MapReduce utilizzando i comandi Hadoop.

> [AZURE.NOTE] Se si ha già familiarità con l'utilizzo di server basati su Linux Hadoop, ma si conosce HDInsight, vedere [suggerimenti basati su Linux HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster basati su Linux HDInsight (Hadoop in HDInsight)

* Un client SSH. Sistemi operativi Linux, Unix e Mac devono accompagnato da un client SSH. Gli utenti di Windows è necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Connettersi a SSH

Connettere il nome di dominio completo (FQDN) del cluster HDInsight tramite il comando SSH. il FQDN sarà il nome assegnato cluster, seguito da **. azurehdinsight.net**. Ad esempio, di seguito da connettersi a un cluster denominato **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se si fornito una chiave del certificato per l'autenticazione SSH** al momento della creazione del cluster HDInsight, potrebbe essere necessario specificare la posizione della chiave privata nel sistema di client, ad esempio:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata specificata una password per l'autenticazione SSH** al momento della creazione del cluster HDInsight, sarà necessario immettere la password quando richiesto.

Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-clients"></a>PuTTY (client di Windows)

Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Utilizzare i comandi Hadoop

1. Dopo essersi connessi al cluster HDInsight, utilizzare il comando **Hadoop** seguente per avviare un processo MapReduce:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Verrà avviata la classe **wordcount** , contenuta nel file **hadoop-mapreduce-examples.jar** . Come input, viene utilizzato il documento **wasbs://example/data/gutenberg/davinci.txt** e output archiviata in **wasbs: / / / esempio/dati/WordCountOutput**.

    > [AZURE.NOTE] Per ulteriori informazioni su questo processo MapReduce e i dati di esempio, vedere [Usare MapReduce in Hadoop in HDInsight](hdinsight-use-mapreduce.md).

2. Il processo genera dettagli come elabora e restituisce informazioni simili ai seguenti al completamento del processo:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Al termine del processo, utilizzare il comando seguente per visualizzare un elenco di file di output vengono memorizzati in **wasbs://example/data/WordCountOutput**:

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    Dovrebbe essere visualizzata due file, **_SUCCESS** e **parte-r-00000**. La **parte-r-00000** contenente l'output per il processo.

    > [AZURE.NOTE] Alcuni processi MapReduce possono dividere i risultati in più file **parte-r-# # #** . In questo caso, utilizzare il # # # suffisso per indicare l'ordine dei file.

4. Per visualizzare l'output, utilizzare il comando seguente:

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Verrà visualizzato un elenco delle parole contenute nel file **wasbs://example/data/gutenberg/davinci.txt** e il numero di volte in cui che si è verificato ogni parola. Di seguito è illustrato un esempio dei dati contenuti nel file:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Riepilogo

Come si può notare, comandi Hadoop rappresentano un modo semplice per eseguire processi MapReduce in un cluster di HDInsight e quindi visualizzare l'output di processo.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Utilizzare MapReduce su HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)
