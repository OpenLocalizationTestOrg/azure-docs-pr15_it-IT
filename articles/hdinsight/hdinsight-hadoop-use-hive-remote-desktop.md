<properties
   pageTitle="Utilizzare Hive Hadoop e Desktop remoto in HDInsight | Microsoft Azure"
   description="Informazioni su come connettersi a cluster Hadoop in HDInsight con Desktop remoto e quindi eseguire query Hive tramite l'interfaccia di riga di comando Hive."
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Usare Hive con Hadoop in HDInsight con Desktop remoto

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo verranno informazioni su come connettersi a un cluster HDInsight tramite Desktop remoto e quindi eseguire query Hive tramite Hive interfaccia riga di comando (CLI).

> [AZURE.NOTE] In questo documento non forniscono una descrizione dettagliata delle affermazioni HiveQL utilizzati negli esempi. Per informazioni su HiveQL utilizzati in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster basato su Windows HDInsight (Hadoop in HDInsight)

* Un computer client con Windows 10, finestra 8 o Windows 7

##<a id="connect"></a>Connettersi con Desktop remoto

Attivare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [connessione a cluster HDInsight mediante RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hive"></a>Usare il comando Hive

Quando si è connessi al desktop per il cluster HDInsight, utilizzare la procedura seguente per l'uso con Hive:

1. Iniziare la **riga di comando Hadoop**desktop HDInsight.

2. Immettere il seguente comando per avviare CLI Hive:

        %hive_home%\bin\hive

    Dopo l'avvio di CLI, verrà visualizzato il prompt di CLI Hive: `hive>`.

3. Usa CLI, immettere le istruzioni seguenti per creare una nuova tabella denominata **log4jLogs** utilizzando i dati di esempio:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Queste istruzioni eseguono le operazioni seguenti:

    * **Istruzione DROP TABLE**: consente di eliminare la tabella e il file di dati se la tabella esiste già.

    * **Creazione di tabelle esterne**: crea una nuova tabella 'esterna' nell'Hive. Tabelle esterne memorizzata solo la definizione della tabella nell'Hive (i dati a sinistra nella posizione originale).

        > [AZURE.NOTE] Tabelle esterne devono essere utilizzate quando si prevede che i dati sottostanti dovranno essere aggiornati da un'origine esterna (ad esempio un processo di caricamento automatica dei dati) o da un'altra operazione MapReduce, ma si desidera sempre Hive query per utilizzare i dati più recenti.
        >
        > Eliminazione di una tabella esterna indica **non** Elimina i dati, solo la definizione della tabella.

    * **Formato di riga**: indica Hive modalità di formattazione. In questo caso, i campi in ogni log sono separati da uno spazio.

    * **Percorso di file di testo come ARCHIVIATO**: indica Hive nel punto in cui i dati sono archiviate (directory/dati di esempio) e memorizzati come testo.

    * **Selezionare**: seleziona un conteggio di tutte le righe in cui colonna **t4** contiene il valore **[errore]**. In questo modo il valore **3** sono disponibili tre righe che contengono il valore.

    * **INPUT__FILE__NAME come '%.log'** - indica Hive che si bisogna restituiscono solo dati da file che terminano con. log. Limita la ricerca per il file sample.log che contiene i dati e continua da restituire dati da altri esempio file di dati che non corrispondono allo schema definito.


4. Utilizzare le istruzioni seguenti per creare una nuova tabella 'interna' denominata **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Queste istruzioni eseguono le operazioni seguenti:

    * **Creare la tabella se non esiste**: creare una tabella se non esiste già. Poiché non viene utilizzata la parola chiave **esterna** , questa è una tabella interna, che verrà archiviata nel data warehouse Hive e gestita completamente da Hive.

        > [AZURE.NOTE] A differenza di tabelle **esterne** , eliminazione di una tabella interna elimina anche i dati sottostanti.

    * **ARCHIVIATI come ORCO**: archiviare i dati in un formato ottimizzato righe a colonne (ORCO). Si tratta di un formato altamente ottimizzato ed efficiente per l'archiviazione dei dati Hive.

    * **Inserisci SOVRASCRIVI... Selezionare**: consente di selezionare le righe dalla tabella **log4jLogs** contenenti **[errore]**, quindi inserire i dati nella tabella **errorLogs** .

    Per verificare che sono state archiviate solo le righe che contengono **[errore]** nella colonna t4 alla tabella **errorLogs** , utilizzare l'istruzione seguente per restituire tutte le righe da **errorLogs**:

        SELECT * from errorLogs;

    Tre righe di dati deve essere restituito, contenente tutti **[errore]** nella colonna t4.

##<a id="summary"></a>Riepilogo

Come si può notare, il comando Hive offre un modo semplice per eseguire query Hive in un cluster di HDInsight in modo interattivo, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Se si utilizza Tez con Hive, vedere i documenti per le informazioni di debug seguenti:

* [Utilizzare l'interfaccia utente Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)

* [Utilizzare la visualizzazione di Ambari Tez in HDInsight basati su Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

