<properties
   pageTitle="Usare con Hive nel HDInsight (Hadoop) Beeline | Microsoft Azure"
   description="Informazioni su come usare SSH per connettersi a un cluster di Hadoop in HDInsight e quindi inviare in modo interattivo Hive query utilizzando Beeline. Beeline è un'utilità per l'utilizzo di HiveServer2 su JDBC."
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
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Usare Hive con Hadoop in HDInsight con Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si imparerà a utilizzare Secure Shell (SSH) per connettersi a un cluster basati su Linux HDInsight e quindi inviare in modo interattivo Hive query utilizzando lo strumento della riga di comando [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [AZURE.NOTE] Beeline utilizza JDBC per connettersi a Hive. Per ulteriori informazioni sull'utilizzo di JDBC con Hive, vedere [connettersi a Hive su Azure HDInsight con il driver JDBC Hive](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Hadoop basati su Linux cluster HDInsight.

* Un client SSH. Linux, Unix e Mac OS deve accompagnato da un client SSH. Gli utenti di Windows è necessario scaricare un client, ad esempio [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Connettersi a SSH

Connettere il nome di dominio completo (FQDN) del cluster HDInsight tramite il comando SSH. il FQDN sarà il nome assegnato cluster, quindi **. azurehdinsight.net**. Ad esempio, di seguito da connettersi a un cluster denominato **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se si fornito una chiave del certificato per l'autenticazione SSH** al momento della creazione del cluster HDInsight, potrebbe essere necessario specificare la posizione della chiave privata nel sistema client:

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** al momento della creazione del cluster HDInsight, sarà necessario immettere la password quando richiesto.

Per ulteriori informazioni sull'utilizzo di SSH con HDInsight, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (basato su Windows client)

Windows non è disponibile un client SSH incorporato. È consigliabile utilizzare **PuTTY**, che può essere scaricato dal [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per ulteriori informazioni sull'uso di PuTTY, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Usare il comando Beeline

1. Una volta connessa, utilizzare le operazioni seguenti per avviare Beeline:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Verrà avviato il client Beeline e connettersi all'url JDBC. In questo caso, `localhost` viene utilizzata poiché HiveServer2 viene eseguito su entrambi i nodi testa cluster e in corso Beeline direttamente nel headnode principale.
    
    Dopo avere completato il comando, verranno recapitati in un `jdbc:hive2://localhost:10001/>` prompt dei comandi.

3. I comandi beeline in genere iniziano con un `!` carattere, ad esempio `!help` viene visualizzata la Guida. Tuttavia la `!` spesso può essere omesso. Ad esempio `help` funzionano anche.

    Se si visualizza la Guida, si noterà `!sql`, che consente di eseguire istruzioni HiveQL. Tuttavia, HiveQL è così comune che è possibile omettere precedente `!sql`. Le due istruzioni seguenti sono esattamente gli stessi risultati. visualizzare le tabelle disponibili tramite Hive:
    
        !sql show tables;
        show tables;
    
    In un cluster di nuovo, dovrebbe essere elencata solo una tabella: __hivesampletable__.

4. Per visualizzare lo schema per il hivesampletable, utilizzare le operazioni seguenti:

        describe hivesampletable;
        
    Restituirà le informazioni seguenti:
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Consente di visualizzare le colonne della tabella. Mentre è possibile eseguire alcune query su questi dati, invece creare una nuova tabella in cui viene illustrato come caricare dati Hive e applicare uno schema.
    
5. Immettere le istruzioni seguenti per creare una nuova tabella denominata **log4jLogs** utilizzando i dati di esempio forniti con il cluster HDInsight:

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
    * **INPUT__FILE__NAME come '%.log'** - indica Hive che si bisogna restituiscono solo dati da file che terminano con. log. In genere è solo dati con lo stesso schema all'interno della stessa cartella durante la ricerca con hive, tuttavia è archiviato il file di log di esempio con altri formati di dati.

    > [AZURE.NOTE] Tabelle esterne dovrebbero essere usate quando si prevede che i dati sottostanti essere aggiornati tramite un'origine esterna, ad esempio un processo di caricamento automatica dei dati o da un'altra operazione MapReduce, ma sempre Hive query per utilizzare i dati più recenti.
    >
    > Eliminazione di una tabella esterna indica **non** Elimina i dati, solo la definizione della tabella.
    
    L'output del comando dovrebbe essere simile al seguente:
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Per uscire dalla Beeline, utilizzare `!quit`.

##<a id="file"></a>Eseguire un file HiveQL

Beeline può anche essere utilizzato per eseguire un file contenente le istruzioni HiveQL. Utilizzare la procedura seguente per creare un file, quindi eseguirlo tramite Beeline.

1. Utilizzare il comando seguente per creare un nuovo file denominato __query.hql__:

        nano query.hql
        
2. Una volta aperto l'editor, utilizza la seguente come il contenuto del file. Questa query verrà creata una nuova tabella 'interna' denominata **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Queste istruzioni eseguono le operazioni seguenti:

    * **Creare la tabella se non esiste** - crea una tabella, se non esiste già. Poiché non viene utilizzata la parola chiave **esterna** , si tratta di una tabella interna, che verrà archiviata nel data warehouse Hive e gestita completamente da Hive.
    * **ARCHIVIATI come ORCO** - sono archiviati i dati nel formato ottimizzato riga colonne (ORCO). Si tratta di un formato altamente ottimizzato ed efficiente per l'archiviazione dei dati Hive.
    * **Inserisci SOVRASCRIVI... Selezionare** - seleziona le righe dalla tabella **log4jLogs** contenenti **[errore]**, quindi inserire i dati nella tabella **errorLogs** .
    
    > [AZURE.NOTE] A differenza di tabelle esterne, l'eliminazione di una tabella interna eliminerà dati sottostanti.
    
3. Per salvare il file, usare __Ctrl__+__X___, quindi immettere __Y__e infine __INVIO__.

4. Utilizzare le operazioni seguenti per eseguire il file utilizzando Beeline. Sostituire __HOSTNAME__ con il nome ottenuto in precedenza per il nodo principale e __la PASSWORD__ con la password per l'account dell'amministratore:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] Il `-i` parametro inizia Beeline esegue le istruzioni nel file query.hql e rimane in Beeline nel `jdbc:hive2://localhost:10001/>` prompt dei comandi. È anche possibile eseguire un file mediante la `-f` parametro, che restituisce Bash dopo l'elaborazione di file.

5. Per verificare che la tabella **errorLogs** è stata creata, utilizzare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

        SELECT * from errorLogs;

    Tre righe di dati deve essere restituito, contenente tutti **[errore]** nella colonna t4:
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Altre informazioni, vedere la connettività Beeline

La procedura descritta in questo documento usare `localhost` a cui connettersi HiveServer2 in esecuzione su headnode cluster. Mentre è anche possibile usare il nome host o il nome di dominio completo del headnode quelli richiedono ulteriori passaggi per il processo (passaggi per trovare il nome host o il nome di dominio completo). Utilizzo `localhost` è sufficiente quando si utilizza Beeline dalla headnode.

Se si dispone di un nodo del bordo del cluster, con Beeline installato, è necessario usare il nome host o il FQDN del headnode per la connessione.

Se si dispone Beeline installato su un client di fuori del cluster, è possibile connettersi tramite il comando seguente. Sostituire __nome cluster__ con il nome del cluster HDInsight. Sostituire __PASSWORD__ con la password per l'account dell'amministratore (accesso HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Si noti che i parametri/URI è diverso quando viene eseguita direttamente in un headnode oppure da un nodo del bordo all'interno del cluster. In questo modo la connessione al cluster da internet utilizza un gateway pubblico che indirizza il traffico attraverso la porta 443. Inoltre, diversi altri servizi vengono esposti tramite il gateway pubblico sulla porta 443, in modo che l'URI è diverso da quello durante la connessione direttamente. Quando ci si connette da internet è anche necessario autenticare la sessione fornendo la password.

##<a id="summary"></a><a id="nextsteps"></a>Passaggi successivi

Come si può notare, il comando Beeline fornisce un modo semplice per eseguire in modo interattivo Hive query in un cluster di HDInsight.

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

