<properties
   pageTitle="Utilizzare MapReduce e curvatura con Hadoop in HDInsight | Microsoft Azure"
   description="Informazioni su come eseguire in remoto i processi di MapReduce con Hadoop su HDInsight utilizzando latino."
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

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Eseguire i processi di MapReduce con Hadoop su HDInsight con curvatura

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo documento verrà descritto come utilizzare curvatura per eseguire i processi di MapReduce su Hadoop cluster HDInsight.

Curvatura verrà utilizzato per dimostrare come è possibile interagire con HDInsight utilizzando richieste HTTP non elaborate per eseguire processi MapReduce. Il funzionamento tramite l'API REST delle WebHCat (in precedenza noto come Templeton) fornita dal cluster HDInsight.

> [AZURE.NOTE] Se si ha già familiarità con l'utilizzo di server basati su Linux Hadoop, ma si conosce HDInsight, vedere [che cos'è necessario conoscere basati su Linux Hadoop su HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Hadoop HDInsight cluster (Linux o basato su Windows)

* [Curvatura](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Eseguire i processi di MapReduce con curvatura

> [AZURE.NOTE] Quando si usa curvatura o altre comunicazioni di resto con WebHCat, è necessario autenticare le richieste fornendo il nome utente di amministratore cluster HDInsight e la password. È anche necessario utilizzare il nome del cluster come parte dell'URI utilizzato per inviare le richieste al server.
>
> Per i comandi in questa sezione, sostituire **nomeutente** con l'utente per eseguire l'autenticazione per il cluster e **la PASSWORD** con la password per l'account utente. Sostituire **nome cluster** con il nome del cluster.
>
> API REST vengono protetti mediante [l'autenticazione di base di access](http://en.wikipedia.org/wiki/Basic_access_authentication). È consigliabile eseguire sempre le richieste utilizzando HTTPS per garantire che le credenziali vengono inviate in modo sicuro nel server.

1. Da una riga di comando, utilizzare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Si riceverà una risposta simile al seguente:

        {"status":"ok","version":"v1"}

    I parametri utilizzati in questo comando sono i seguenti:

    * **-u**: indica il nome utente e la password utilizzati per autenticare la richiesta
    * **Della lettera G**: indica che si tratta di una richiesta GET

    Inizio URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**è uguale per tutte le richieste.

2. Per inviare un processo MapReduce, utilizzare il comando seguente:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    Fine dell'URI (/ mapreduce/vaso) indica WebHCat che la richiesta verrà avviato un processo MapReduce da una classe in un file di vaso. I parametri utilizzati in questo comando sono i seguenti:

    * **-d**: `-G` non viene usata in modo che la richiesta per impostazione predefinita per il metodo POST. `-d`Specifica i valori dei dati inviati con la richiesta.

        * **User.Name**: l'utente che esegue il comando
        * **vaso**: il percorso del file vaso contenente classe per essere eseguito
        * **corso**: della classe che contiene la logica MapReduce
        * **argomento**: gli argomenti da passare al processo MapReduce. In questo caso, il file di testo di input e la directory che vengono utilizzate per l'output

    Questo comando deve restituire un ID di processo che può essere utilizzato per controllare lo stato del processo:

        {"id":"job_1415651640909_0026"}

3. Per controllare lo stato del processo, utilizzare il comando seguente. Sostituire il **processo** con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è stata `{"id":"job_1415651640909_0026"}`, quindi gli ID processo sarebbe `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se il processo è stato completato, lo stato verrà essere "riuscito".

    > [AZURE.NOTE] La richiesta di curvatura restituisce un documento JSON con informazioni sul processo; jq viene utilizzata per recuperare solo il valore di stato.

4. Quando lo stato del processo è cambiato in **completato**, è possibile recuperare i risultati del processo dallo spazio di archiviazione Blob Azure. Il `statusdir` parametro passato con la query contiene il percorso del file di output. In questo caso, **wasbs: / / / esempio/curvatura**. Questo indirizzo archivia l'output del processo nella directory di **esempio/curvatura** nel contenitore di spazio di archiviazione predefinito utilizzato per il cluster HDInsight.

È possibile elencare e scaricare questi file utilizzando [CLI Azure](../xplat-cli-install.md). Per elencare i file di **esempio/latino**, ad esempio, utilizzare il comando seguente:

    azure storage blob list <container-name> example/curl

Per scaricare un file, usare le operazioni seguenti:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] È necessario specificare il nome dell'account di archiviazione che contiene il blob utilizzando il `-a` e `-k` parametri o set il **AZURE\_lo spazio di archiviazione\_ACCOUNT** e **AZURE\_lo spazio di archiviazione\_accesso\_chiave** variabili. Informazioni su [come caricare dati da HDInsight](hdinsight-upload-data.md) per ulteriori informazioni.

##<a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile utilizzare richiesta HTTP non elaborato per eseguire, monitorare e visualizzare i risultati dei processi Hive del cluster HDInsight.

Per ulteriori informazioni sull'interfaccia resto utilizzati in questo articolo, vedere la [Guida di riferimento WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)
