<properties
   pageTitle="Utilizzare Hive Hadoop latino in HDInsight | Microsoft Azure"
   description="Informazioni su come inviare in remoto processi maialino a HDInsight utilizzando latino."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-with-hadoop-in-hdinsight-with-curl"></a>Eseguire query Hive con Hadoop in HDInsight latino

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo documento si imparerà a utilizzare curvatura per eseguire query Hive su Hadoop cluster Azure HDInsight.

Curvatura verrà utilizzato per dimostrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati delle query Hive. Il funzionamento tramite l'API REST delle WebHCat (in precedenza noto come Templeton) fornita dal cluster HDInsight.

> [AZURE.NOTE] Se si ha già familiarità con le server basati su Linux Hadoop, ma si ha HDInsight, vedere [che cos'è necessario conoscere Hadoop su HDInsight basati su Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Hadoop HDInsight cluster (Linux o basato su Windows)

* [Curvatura](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Eseguire query Hive tramite curvatura

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

    Inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**saranno gli stessi per tutte le richieste. Il percorso **/status**indica che la richiesta per restituire lo stato WebHCat (noto anche come Templeton) per il server. È inoltre possibile richiedere la versione di Hive utilizzando il comando seguente:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Deve restituire una risposta simile al seguente:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Per creare una nuova tabella denominata **log4jLogs**, utilizzare il seguente:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    I parametri utilizzati in questo comando sono i seguenti:

    * **-d** - poiché `-G` non viene utilizzata la richiesta per impostazione predefinita per il metodo POST. `-d`Specifica i valori dei dati inviati con la richiesta.

        * **user.name** - l'utente che esegue il comando.

        * **eseguire** - HiveQL di istruzioni da eseguire.

        * **statusdir** - directory scritta per lo stato del processo.

    Queste istruzioni eseguono le operazioni seguenti:

    * **Istruzione DROP TABLE** - Elimina la tabella e il file di dati, se la tabella esiste già.

    * **Creazione di tabelle esterne** - crea una nuova tabella 'esterna' nell'Hive. Tabelle esterne memorizzata solo la definizione della tabella nell'Hive. I dati siano a sinistra nella posizione originale.

        > [AZURE.NOTE] Tabelle esterne dovrebbero essere usate quando si prevede che i dati sottostanti essere aggiornati tramite un'origine esterna, ad esempio un processo di caricamento automatica dei dati o da un'altra operazione MapReduce, ma sempre Hive query per utilizzare i dati più recenti.
        >
        > Eliminazione di una tabella esterna indica **non** Elimina i dati, solo la definizione della tabella.

    * **Formato di riga** - indica Hive modalità di formattazione. In questo caso, i campi in ogni log sono separati da uno spazio.

    * **ARCHIVIATI come file di testo percorso** - indica Hive nel punto in cui i dati sono archiviate (directory/dati di esempio) e che non viene memorizzato come testo.

    * **Selezionare** - seleziona un conteggio di tutte le righe in cui colonna **t4** contiene il valore **[errore]**. Questa operazione deve restituire un valore pari a **3** vi sono tre righe che contengono il valore.

    > [AZURE.NOTE] Si noti che gli spazi tra le istruzioni HiveQL vengono sostituiti dalla `+` carattere utilizzato latino. I valori tra virgolette che contengono uno spazio, ad esempio il delimitatore non devono essere sostituiti da `+`.

    * **INPUT__FILE__NAME come '% 25.log'** - questo limita la ricerca a solo usare i file che terminano in. log. Se non è presenta, Hive tenterà di tutti i file di ricerca in questa directory e le relative sottodirectory, inclusi i file che non corrispondono allo schema di colonna definito per questa tabella.

    > [AZURE.NOTE] Si noti che 25% è l'URL codificata sotto forma di %, in modo che la condizione effettiva è `like '%.log'`. La % ha da codificare, come verrà considerato come un carattere speciale nell'URL.

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

6. Utilizzare le istruzioni seguenti per creare una nuova tabella 'interna' denominata **errorLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Queste istruzioni eseguono le operazioni seguenti:

    * **Creare la tabella se non esiste** - crea una tabella, se non esiste già. Poiché non viene utilizzata la parola chiave **esterna** , si tratta di una tabella interna, che verrà archiviata nel data warehouse Hive e gestita completamente da Hive.

        > [AZURE.NOTE] A differenza di tabelle esterne, l'eliminazione di una tabella interna eliminerà dati sottostanti.

    * **ARCHIVIATI come ORCO** - sono archiviati i dati nel formato ottimizzato riga colonne (ORCO). Si tratta di un formato altamente ottimizzato ed efficiente per l'archiviazione dei dati Hive.
    * **Inserisci SOVRASCRIVI... Selezionare** - seleziona le righe dalla tabella **log4jLogs** contenenti **[errore]**, quindi inserire i dati nella tabella **errorLogs** .
    * **Selezionare** - Seleziona tutte le righe dalla nuova tabella **errorLogs** .

7. Usare l'ID di processo restituito per controllare lo stato del processo. Una volta ha esito positivo, utilizzare Azure CLI per Mac, Linux e Windows come descritto in precedenza per scaricare e visualizzare i risultati. L'output deve contenere tre righe, ciascuna delle quali contiene **[errore]**.


##<a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile utilizzare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Hive il cluster HDInsight.

Per ulteriori informazioni sull'interfaccia REST utilizzato in questo articolo, vedere la <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">Guida di riferimento WebHCat</a>.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sull'Hive con HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Se si utilizza Tez con Hive, vedere i documenti per le informazioni di debug seguenti:

* [Utilizzare l'interfaccia utente Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)

* [Utilizzare la visualizzazione di Ambari Tez in HDInsight basati su Linux](hdinsight-debug-ambari-tez-view.md)

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


