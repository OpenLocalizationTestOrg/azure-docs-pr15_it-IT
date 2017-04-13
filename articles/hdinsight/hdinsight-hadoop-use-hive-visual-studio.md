<properties
   pageTitle="Hive query con Hadoop tools per Visual Studio | Microsoft Azure"
   description="Informazioni su come usare Hive con Hadoop in HDInsight utilizzando gli strumenti di Visual Studio Hadoop."
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Eseguire query Hive usando gli strumenti di HDInsight per Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si imparerà a inviare query Hive a un cluster di HDInsight utilizzando gli strumenti di HDInsight per Visual Studio.

> [AZURE.NOTE] In questo documento non forniscono una descrizione dettagliata delle istruzioni HiveQL utilizzate negli esempi. Per informazioni su HiveQL utilizzati in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Prerequisiti

Per completare la procedura descritta in questo articolo, è necessario quanto segue.

* Un cluster di Azure HDInsight (Hadoop in HDInsight) (Linux o basato su Windows)

* Visual Studio (uno dei seguenti versioni):

    Visual Studio 2013 Community/Professional/Premium/Ultimate con [aggiornamento 4](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 (Community/Enterprise)

- HDInsight tools per Visual studio. Per informazioni sull'installazione e gli strumenti di configurazione, vedere [Introduzione all'utilizzo di strumenti di Visual Studio Hadoop per HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) .

##<a id="run"></a>Eseguire query Hive usando gli strumenti di HDInsight per Visual Studio

1. Aprire **Visual Studio** e selezionare **Nuovo** > **progetto** > **HDInsight** > **Hive applicazione**. Specificare un nome per il progetto.

2. Aprire il file **Script.hql** creato con il progetto e incollare nelle istruzioni HiveQL seguenti:

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
    * **Selezionare**: selezionare un conteggio di tutte le righe in cui colonna **t4** contengono il valore **[errore]**. In questo modo il valore **3** sono disponibili tre righe che contengono il valore.
    * **INPUT__FILE__NAME come '%.log'** - indica Hive che si bisogna restituiscono solo dati da file che terminano con. log. Limita la ricerca del file sample.log che contiene i dati e continua da restituire dati da altri esempio file di dati che non corrispondono allo schema che è definiti.

3. Sulla barra degli strumenti selezionare il **HDInsight Cluster** che si desidera utilizzare per la query e quindi selezionare **Invia a WebHCat** per eseguire le istruzioni come processo Hive utilizzando WebHCat. È inoltre possibile inviare il processo utilizzando il pulsante __Esegui tramite HiveServer2__ se HiveServer2 è disponibile nella versione cluster. Verrà visualizzato il **Riepilogo Hive** informazioni sul processo in esecuzione. Utilizzare il collegamento **Aggiorna** per aggiornare le informazioni sul processo, finché non assume lo **Stato del processo** **completato**.

4. Utilizzare il collegamento di **Output processo** per visualizzare l'output del processo. Deve essere visualizzato `[ERROR] 3`, che rappresenta il valore restituito dall'istruzione SELECT.

5. È anche possibile eseguire query Hive senza creare un progetto. Uso di **Esplora Server**, espandere **Azure** > **HDInsight**mouse sul server HDInsight e quindi selezionare **scrivere una Query Hive**.

6. Nel documento **temp.hql** che viene visualizzata, aggiungere le istruzioni HiveQL seguenti:

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Queste istruzioni eseguono le operazioni seguenti:

    * **Creare la tabella se non esiste**: creare una tabella se non esiste già. Poiché non viene utilizzata la parola chiave **esterna** , questa è una tabella interna, che verrà archiviata nel data warehouse Hive e gestita completamente da Hive.

        > [AZURE.NOTE] A differenza di tabelle **esterne** , eliminazione di una tabella interna elimina anche i dati sottostanti.

    * **ARCHIVIATI come ORCO**: archiviare i dati in un formato ottimizzato righe a colonne (ORCO). Si tratta di un formato altamente ottimizzato ed efficiente per l'archiviazione dei dati Hive.
    * **Inserisci SOVRASCRIVI... Selezionare**: consente di selezionare le righe dalla tabella **log4jLogs** contenenti **[errore]**, quindi inserire i dati nella tabella **errorLogs** .

7. Sulla barra degli strumenti, selezionare **Invia** per eseguire il processo. Utilizzare lo **Stato del processo** per determinare che il processo è stata completata.

8. Per verificare che il processo completato e creata una nuova tabella, usare **Esplora Server** ed espandere **Azure** > **HDInsight** > cluster HDInsight > **Hive database** > e **predefinito**. Verrà visualizzata la tabella **errorLogs** e la tabella **log4jLogs** .

##<a id="summary"></a>Riepilogo

Come si può notare, il gli strumenti di HDInsight per Visual Studio forniscono un modo semplice per eseguire query Hive in un cluster di HDInsight, monitorare lo stato del processo e recuperare l'output.

##<a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altri modi è possibile utilizzare le Hadoop su HDInsight:

* [Utilizzare maialino con Hadoop in HDInsight](hdinsight-use-pig.md)

* [Utilizzare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per ulteriori informazioni sugli strumenti HDInsight per Visual Studio:

* [Guida introduttiva di HDInsight tools per Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
