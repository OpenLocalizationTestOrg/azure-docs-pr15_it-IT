<properties
   pageTitle="Utilizzare shell Hive in HDInsight (Hadoop) | Microsoft Azure"
   description="Informazioni su come utilizzare shell Hive con un cluster basati su Linux HDInsight. Verrà informazioni su come connettersi a cluster HDInsight utilizzando SSh, quindi utilizzare Shell Hive eseguire in modo interattivo query."
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>Usare Hive con Hadoop in HDInsight con SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si imparerà a utilizzare Secure Shell (SSH) per connettersi a un Hadoop su Azure HDInsight cluster e quindi inviare in modo interattivo Hive query utilizzando l'interfaccia della riga di comando Hive (CLI).

> [AZURE.IMPORTANT] Mentre il comando Hive è disponibile nei cluster basati su Linux HDInsight, è consigliabile utilizzare Beeline. Beeline è un client più recente per l'utilizzo di Hive ed è incluso con i cluster HDInsight. Per ulteriori informazioni sull'uso di questa operazione, vedere [Usare Hive con Hadoop in HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Hadoop basati su Linux cluster HDInsight.

* Un client SSH. Linux, Unix e Mac OS deve accompagnato da un client SSH. Gli utenti di Windows è necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Connettersi a SSH

Connettere il nome di dominio completo (FQDN) del cluster HDInsight tramite il comando SSH. il FQDN sarà il nome assegnato cluster, quindi **. azurehdinsight.net**. Ad esempio, di seguito da connettersi a un cluster denominato **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se si fornito una chiave del certificato per l'autenticazione SSH** al momento della creazione del cluster HDInsight, potrebbe essere necessario specificare la posizione della chiave privata nel sistema client:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se è stata specificata una password per l'autenticazione SSH** al momento della creazione del cluster HDInsight, sarà necessario immettere la password quando richiesto.

Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (basato su Windows client)

Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Usare il comando Hive

2. Una volta connessa, avviare CLI Hive utilizzando il comando seguente:

        hive

3. Usa CLI, immettere le istruzioni seguenti per creare una nuova tabella denominata **log4jLogs** utilizzando i dati di esempio:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Queste istruzioni eseguono le operazioni seguenti:

    * **Istruzione DROP TABLE** - Elimina la tabella e il file di dati, nel caso in cui la tabella esiste già.
    * **Creazione di tabelle esterne** - crea una nuova tabella 'esterna' nell'Hive. Tabelle esterne memorizzata solo la definizione della tabella nell'Hive. I dati siano a sinistra nella posizione originale.
    * **Formato di riga** - indica Hive modalità di formattazione. In questo caso, i campi in ogni log sono separati da uno spazio.
    * **ARCHIVIATI come file di testo percorso** - indica Hive nel punto in cui i dati sono archiviate (directory/dati di esempio) e che non viene memorizzato come testo.
    * **Selezionare** - seleziona un conteggio di tutte le righe in cui colonna **t4** contiene il valore **[errore]**. Questa operazione deve restituire un valore pari a **3** vi sono tre righe che contengono il valore.
    * **INPUT__FILE__NAME come '%.log'** - indica Hive che si bisogna restituiscono solo dati da file che terminano con. log. Limita la ricerca del file sample.log che contiene i dati e continua da restituire dati da altri esempio file di dati che non corrispondono allo schema che è definiti.

    > [AZURE.NOTE] Tabelle esterne dovrebbero essere usate quando si prevede che i dati sottostanti essere aggiornati tramite un'origine esterna, ad esempio un processo di caricamento automatica dei dati o da un'altra operazione MapReduce, ma sempre Hive query per utilizzare i dati più recenti.
    >
    > Eliminazione di una tabella esterna indica **non** Elimina i dati, solo la definizione della tabella.

4. Utilizzare le istruzioni seguenti per creare una nuova tabella 'interna' denominata **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Queste istruzioni eseguono le operazioni seguenti:

    * **Creare la tabella se non esiste** - crea una tabella, se non esiste già. Poiché non viene utilizzata la parola chiave **esterna** , si tratta di una tabella interna, che verrà archiviata nel data warehouse Hive e gestita completamente da Hive.
    * **ARCHIVIATI come ORCO** - sono archiviati i dati nel formato ottimizzato riga colonne (ORCO). Si tratta di un formato altamente ottimizzato ed efficiente per l'archiviazione dei dati Hive.
    * **Inserisci SOVRASCRIVI... Selezionare** - seleziona le righe dalla tabella **log4jLogs** contenenti **[errore]**, quindi inserire i dati nella tabella **errorLogs** .

    Per verificare che sono state archiviate solo le righe contenenti **[errore]** nella colonna t4 alla tabella **errorLogs** , utilizzare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

        SELECT * from errorLogs;

    Tre righe di dati deve essere restituito, contenente tutti **[errore]** nella colonna t4.

    > [AZURE.NOTE] A differenza di tabelle esterne, l'eliminazione di una tabella interna eliminerà dati sottostanti.

##<a id="summary"></a>Riepilogo

Come si può notare, il comando Hive fornisce un modo semplice per eseguire query Hive in un cluster di HDInsight in modo interattivo, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sull'Hive in HDInsight:

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

