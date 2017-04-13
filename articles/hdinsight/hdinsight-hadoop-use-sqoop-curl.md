<properties
   pageTitle="Utilizzare Hadoop Sqoop latino in HDInsight | Microsoft Azure"
   description="Informazioni su come inviare in remoto processi Sqoop a HDInsight utilizzando latino."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Eseguire i processi di Sqoop con Hadoop in HDInsight latino

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare curvatura per eseguire processi Sqoop in un cluster di Hadoop in HDInsight.

Curvatura verrà utilizzato per dimostrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati dei processi Sqoop. Il funzionamento tramite l'API REST delle WebHCat (in precedenza noto come Templeton) fornita dal cluster HDInsight.

> [AZURE.NOTE] Se si ha già familiarità con le server basati su Linux Hadoop, ma si ha HDInsight, vedere [informazioni sull'utilizzo di HDInsight su Linux](hdinsight-hadoop-linux-information.md).

##<a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Hadoop HDInsight cluster (Linux o basato su Windows)

* [Curvatura](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>Inviare i processi di Sqoop utilizzando curvatura

> [AZURE.NOTE] Quando si usa curvatura o altre comunicazioni di resto con WebHCat, è necessario autenticare le richieste di fornire il nome utente e la password per l'amministratore di cluster HDInsight. È anche necessario utilizzare il nome del cluster come parte della risorsa identificatore URI (Uniform) utilizzato per inviare le richieste al server.
>
> Per i comandi in questa sezione, sostituire **nomeutente** con l'utente per eseguire l'autenticazione al cluster e sostituire **PASSWORD** con la password per l'account utente. Sostituire **nome cluster** con il nome del cluster.
>
> API REST è protetto tramite [l'autenticazione di base](http://en.wikipedia.org/wiki/Basic_access_authentication). È consigliabile eseguire sempre le richieste tramite HTTP sicura (HTTPS) per garantire che le credenziali vengono inviate in modo sicuro nel server.

1. Dalla riga di comando, utilizzare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Si riceverà una risposta simile al seguente:

        {"status":"ok","version":"v1"}

    I parametri utilizzati in questo comando sono i seguenti:

    * **-u** - il nome utente e la password utilizzati per autenticare la richiesta.
    * **Della lettera G** - indica che si tratta di una richiesta GET.

    Inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**saranno gli stessi per tutte le richieste. Il percorso **/status**indica che la richiesta per restituire lo stato WebHCat (noto anche come Templeton) per il server. 

2. Per inviare un processo sqoop, utilizzare il seguente:


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    I parametri utilizzati in questo comando sono i seguenti:

    * **-d** - poiché `-G` non viene utilizzata la richiesta per impostazione predefinita per il metodo POST. `-d`Specifica i valori dei dati inviati con la richiesta.

        * **user.name** - l'utente che esegue il comando.

        * **comando** - Sqoop il comando da eseguire.

        * **statusdir** - directory scritta per lo stato del processo.

    Questo comando deve restituire un ID di processo che può essere utilizzato per controllare lo stato del processo.

        {"id":"job_1415651640909_0026"}

3. Per controllare lo stato del processo, utilizzare il comando seguente. Sostituire **ID processo** con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è stata `{"id":"job_1415651640909_0026"}`, quindi **ID processo** sarebbe `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se il processo è terminato, lo stato sarà **completato**.

    > [AZURE.NOTE] La richiesta di curvatura restituisce un documento JavaScript Object Notation (JSON) con informazioni sul processo; jq viene utilizzata per recuperare solo il valore di stato.

4. Una volta **completato**viene impostato lo stato del processo, è possibile recuperare i risultati del processo dallo spazio di archiviazione Blob Azure. Il `statusdir` parametro passato con la query contiene il percorso del file di output. In questo caso, **wasbs: / / / esempio/curvatura**. Questo indirizzo archivia l'output del processo nella directory di **esempio/curvatura** sul contenitore di spazio di archiviazione predefinito utilizzato per il cluster HDInsight.

    È possibile elencare e scaricare questi file utilizzando [CLI Azure](../xplat-cli-install.md). Per elencare i file di **esempio/latino**, ad esempio, utilizzare il comando seguente:

        azure storage blob list <container-name> example/curl

    Per scaricare un file, usare le operazioni seguenti:

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] È necessario specificare il nome dell'account di archiviazione che contiene il blob utilizzando il `-a` e `-k` parametri o set il **AZURE\_lo spazio di archiviazione\_ACCOUNT** e **AZURE\_lo spazio di archiviazione\_accesso\_chiave** variabili. Vedere < con un sottoindirizzo = "data.md di caricamento hdinsight" target = blank"per ulteriori informazioni.

##<a name="limitations"></a>Limitazioni

* Esporta in blocco - HDInsight basati su con Linux, il connettore Sqoop utilizzato per esportare i dati a Microsoft SQL Server o Database SQL Azure attualmente non supporta inserimenti di massa.

* Divisione in batch - con basati su Linux HDInsight quando si usa il `-batch` passa durante l'esecuzione di inserimenti, Sqoop eseguirà più inserimenti anziché in batch le operazioni di inserimento.

##<a name="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile utilizzare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Sqoop il cluster HDInsight.

Per ulteriori informazioni sull'interfaccia REST utilizzato in questo articolo, vedere la <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Guida all'API REST Sqoop</a>.

##<a name="next-steps"></a>Passaggi successivi

Per informazioni generali sull'Hive con HDInsight:

* [Utilizzare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


