<properties
   pageTitle="Utilizzare Hadoop maialino latino in HDInsight | Microsoft Azure"
   description="Informazioni su come usare curvatura per eseguire processi maialino latino in un cluster di Hadoop in Azure HDInsight."
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

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Eseguire processi maialino con Hadoop su HDInsight tramite curvatura

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

In questo documento verrà descritto come utilizzare curvatura per eseguire processi maialino latino in un cluster di Azure HDInsight. Il linguaggio di programmazione latino maialino consente di descrivere trasformazioni che si applicano ai dati di input per generare l'output desiderato.

Curvatura verrà utilizzato per dimostrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati dei processi maialino. Questo funziona utilizzando WebHCat API REST (precedentemente noto come Templeton) fornito con il cluster HDInsight.

> [AZURE.NOTE] Se si ha già familiarità con le server basati su Linux Hadoop ma si ha familiarità con HDInsight, vedere [Suggerimenti HDInsight basati su Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster di Azure HDInsight (Hadoop in HDInsight) (basato su Linux o basato su Windows)

* [Curvatura](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Eseguire i processi di maialino con curvatura

> [AZURE.NOTE] Quando si usa curvatura o altre comunicazioni di resto con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password per il cluster HDInsight. È anche necessario utilizzare il nome del cluster come parte di uniforme risorsa identificatore (URI) utilizzato per inviare le richieste al server.
>
> Per i comandi in questa sezione, sostituire **nomeutente** con l'utente per eseguire l'autenticazione al cluster e sostituire **PASSWORD** con la password per l'account utente. Sostituire **nome cluster** con il nome del cluster.
>
> API REST è protetto tramite [l'autenticazione di base di access](http://en.wikipedia.org/wiki/Basic_access_authentication). È consigliabile eseguire sempre le richieste tramite HTTP sicura (HTTPS) per garantire che le credenziali vengono inviate in modo sicuro nel server.

1. Dalla riga di comando, utilizzare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Si riceverà una risposta simile al seguente:

        {"status":"ok","version":"v1"}

    I parametri utilizzati in questo comando sono i seguenti:

    * **-u**: il nome utente e la password utilizzati per autenticare la richiesta
    * **Della lettera G**: indica che si tratta di una richiesta GET

    Inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**saranno gli stessi per tutte le richieste. Il percorso **/status**indica che la richiesta per restituire lo stato di WebHCat (noto anche come Templeton) per il server.

2. Usare il codice riportato di seguito per inviare un processo maialino latino al cluster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    I parametri utilizzati in questo comando sono i seguenti:

    * **-d**: perché `-G` non viene utilizzata la richiesta per impostazione predefinita per il metodo POST. `-d`Specifica i valori dei dati inviati con la richiesta.

        * **User.Name**: l'utente che esegue il comando
        * **eseguire**: il maialino latino istruzioni da eseguire
        * **statusdir**: directory scritta per lo stato del processo

    > [AZURE.NOTE] Si noti che gli spazi nelle istruzioni sull'alfabeto latino maialino vengono sostituiti dalla `+` carattere utilizzato latino.

    Questo comando deve restituire un ID di processo che può essere utilizzato per controllare lo stato del processo, ad esempio:

        {"id":"job_1415651640909_0026"}

3. Per controllare lo stato del processo, utilizzare il comando seguente. Sostituire **ID processo** con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è stata `{"id":"job_1415651640909_0026"}`, quindi **ID processo** sarebbe `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se il processo è terminato, lo stato sarà **completato**.

    > [AZURE.NOTE] La richiesta di curvatura restituisce un documento JavaScript Object Notation (JSON) con informazioni sul processo e jq viene utilizzato per recuperare solo il valore di stato.

##<a id="results"></a>Visualizzare i risultati

Quando lo stato del processo è cambiato in **completato**, è possibile recuperare i risultati del processo dallo spazio di archiviazione Blob Azure. Il `statusdir` parametro passato con la query contiene il percorso del file di output. In questo caso, **wasbs: / / / esempio/pigcurl**. Questo indirizzo archivia l'output del processo nella directory di **esempio/pigcurl** nel contenitore di spazio di archiviazione predefinito utilizzato per il cluster HDInsight.

È possibile elencare e scaricare questi file utilizzando [CLI Azure](../xplat-cli-install.md). Ad esempio, per elencare i file di **esempio/pigcurl**, utilizzare il comando seguente:

    azure storage blob list <container-name> example/pigcurl

Per scaricare un file, usare le operazioni seguenti:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] È necessario specificare il nome dell'account di archiviazione che contiene il blob utilizzando il `-a` e `-k` parametri o set il **AZURE\_lo spazio di archiviazione\_ACCOUNT** e **AZURE\_lo spazio di archiviazione\_accesso\_chiave** variabili.

##<a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile utilizzare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi maialino il cluster HDInsight.

Per ulteriori informazioni sull'interfaccia resto utilizzato in questo articolo, vedere la [Guida di riferimento WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su maialino in HDInsight:

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
