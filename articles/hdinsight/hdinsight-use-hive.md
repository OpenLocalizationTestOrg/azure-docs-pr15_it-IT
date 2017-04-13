<properties
    pageTitle="Informazioni su Hive e come usare HiveQL | Microsoft Azure"
    description="Informazioni sulle Apache Hive e come utilizzarlo con Hadoop in HDInsight. Scegliere come eseguire il processo di Hive e consente di analizzare un file di esempio Apache log4j HiveQL."
    keywords="hiveql, che cos'è hive"
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
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>Usare Hive e HiveQL con Hadoop in HDInsight per analizzare un file di esempio Apache log4j

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


In questa esercitazione verrà imparare a usare Apache Hive in Hadoop su HDInsight e scegliere la modalità di eseguire il processo di Hive. È inoltre fornite informazioni su HiveQL e su come analizzare un file di esempio Apache log4j.

##<a id="why"></a>Che cos'è Hive e perché usare?
[Apache Hive](http://hive.apache.org/) è un sistema di data warehouse per Hadoop, che consente di riepilogo di dati, l'esecuzione di query e analisi dei dati utilizzando HiveQL (un linguaggio di query simile a SQL). Esplorare in modo interattivo i dati o per creare processi di elaborazione batch riutilizzabile, è possibile utilizzare hive.

Hive consente di struttura del progetto sul dati ampiamente non strutturati. Dopo avere definito la struttura, è possibile utilizzare Hive eseguire query su tali dati senza una conoscenza del linguaggio o MapReduce. **HiveQL** (linguaggio di query Hive) consente di creare query con le istruzioni che sono simili a T-SQL.

Hive siano in grado di utilizzare i dati strutturati e semistrutturati, ad esempio un file di testo in cui i campi sono delimitati da caratteri specifici. Hive supporta anche personalizzato **serializzatore/deserializers (SerDe)** per dati strutturati irregolare o complessi. Per ulteriori informazioni, vedere [come utilizzare un SerDe JSON personalizzato con HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>Funzioni definite dall'utente (utente)

È anche possibile estendere hive mediante **le funzioni definite dall'utente (utente)**. Un'utente è possibile implementare funzionalità o logica che non è facilmente modellare in HiveQL. Per un esempio dell'uso di funzioni definite dall'utente con Hive, vedere le operazioni seguenti:

* [Utilizzare un utente di linguaggio definite funzione con Hive](hdinsight-hadoop-hive-java-udf.md)

* [Utilizzo di Python con Hive e maialino in HDInsight](hdinsight-python.md)

* [Usare c# con Hive e maialino in HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Come aggiungere un utente Hive personalizzato a HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Esempio di Hive utente personalizzata per convertire i formati di data/ora in Hive timestamp](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Hive tabelle interne vs di tabelle esterne

Esistono alcune operazioni che è necessario conoscere la tabella interna Hive e tabella esterna:

- Il comando **Crea tabella** crea una tabella interna. Il file di dati deve trovarsi nel contenitore predefinito.
- Il comando **Crea tabella** si sposta il file di dati /hive/warehouse/<TableName> cartella.
- Il comando **Creazione di tabelle esterne** crea una tabella esterna. Il file di dati può trovarsi all'esterno del contenitore predefinito.
- Il comando **Creazione di tabelle esterne** non si sposta il file di dati.
- Il comando **Creazione di tabelle esterne** non consente di tutte le cartelle nella posizione. Questo è il motivo perché l'esercitazione viene creata una copia del file sample.log.

Per ulteriori informazioni, vedere [HDInsight: Hive interni ed esterni tabelle introduttivo][cindygross-hive-tables].


##<a id="data"></a>Sui dati di esempio, un file di log4j Apache

In questo esempio viene utilizzato un file di esempio *log4j* , archiviato in **/example/data/sample.log** nel contenitore di spazio di archiviazione blob. Ogni log all'interno del file è costituito da una riga di campi che contiene un `[LOG LEVEL]` campo per visualizzare il tipo e la gravità, ad esempio:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Nell'esempio precedente, l'errore è il livello di registrazione.

> [AZURE.NOTE] È possibile anche generare un file log4j utilizzando lo strumento registrazione [Log4j Apache](http://en.wikipedia.org/wiki/Log4j) e quindi caricare il file contenitore blob. Per istruzioni, vedere [Caricare dati da HDInsight](hdinsight-upload-data.md) . Per ulteriori informazioni sull'utilizzo di archiviazione Blob Azure con HDInsight, vedere [Usare l'archiviazione Blob Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

I dati di esempio vengono archiviati in archiviazione Blob Azure, che utilizza HDInsight come file system predefinito. HDInsight accedere ai file archiviati nei BLOB utilizzando il prefisso **wasb** . Ad esempio, per accedere al file sample.log, utilizzare la sintassi seguente:

    wasbs:///example/data/sample.log

Poiché l'archiviazione Blob Azure spazio di archiviazione predefinito per HDInsight, è possibile accedere anche il file tramite **/example/data/sample.log** da HiveQL.

> [AZURE.NOTE] La sintassi **wasbs: / / /**, viene utilizzato per accedere ai file archiviati nel contenitore di spazio di archiviazione predefinito per il cluster HDInsight. Se è stato specificato account spazio di archiviazione aggiuntivo quando viene completato il provisioning del cluster e si vuole accedere ai file archiviati in questi account, è possibile accedere i dati specificando il contenitore nome e lo spazio di archiviazione account indirizzo, ad esempio **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Processo di esempio: proiettare colonne su dati con valori delimitati da

Le seguenti istruzioni HiveQL verranno proiettare colonne su dati con valori delimitati da cui sono archiviati il **wasbs: / / / / dati di esempio** directory:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

Nell'esempio precedente, le istruzioni HiveQL eseguono le operazioni seguenti:

* __impostare hive.execution.engine=tez;__: imposta il motore di esecuzione usare Tez. Utilizzo di Tez anziché MapReduce può fornire un aumento delle prestazioni delle query. Per ulteriori informazioni su Tez, vedere la sezione [Uso Apache Tez per migliorare le prestazioni](#usetez) .

    > [AZURE.NOTE] L'istruzione è solo necessari quando si utilizza un cluster basato su Windows HDInsight. Tez è il motore di esecuzione predefinito per HDInsight basati su Linux.

* **Istruzione DROP TABLE**: consente di eliminare la tabella e il file di dati se la tabella esiste già.
* **Creazione di tabelle esterne**: consente di creare una nuova tabella **esterna** nell'Hive. Tabelle esterne memorizzare solo la definizione della tabella nell'Hive; i dati vengono mantenuti nella posizione originale e nel formato originale.
* **Formato di riga**: indica Hive modalità di formattazione. In questo caso, i campi in ogni log sono separati da uno spazio.
* **Percorso di file di testo come ARCHIVIATO**: indica Hive nel punto in cui i dati sono archiviate (directory/dati di esempio) e memorizzati come testo. I dati in un file, è possibile o distribuiti in più file all'interno della directory.
* **Selezionare**: seleziona un conteggio di tutte le righe in cui la colonna **t4** contiene il valore **[errore]**. In questo modo il valore **3** sono disponibili tre righe che contengono il valore.
* **INPUT__FILE__NAME come '%.log'** - indica Hive che si bisogna restituiscono solo dati da file che terminano con. log. Limita la ricerca del file sample.log che contiene i dati e continua da restituire dati da altri esempio file di dati che non corrispondono allo schema che è definiti.

> [AZURE.NOTE] Tabelle esterne dovrebbero essere usate quando si prevede che i dati sottostanti dovranno essere aggiornati da un'origine esterna, ad esempio un processo di caricamento automatica dei dati o da un'altra operazione MapReduce e si desidera sempre Hive query per utilizzare i dati più recenti.
>
> Eliminazione di una tabella esterna indica **non** eliminare i dati, vengono eliminati solo la definizione della tabella.

Dopo la creazione di tabelle esterne, le istruzioni riportate di seguito vengono utilizzate per creare una tabella **interna** .

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Queste istruzioni eseguono le operazioni seguenti:

* **Creare la tabella se non esiste**: creare una tabella, se non esiste già. Poiché non viene utilizzata la parola chiave **esterna** , questa è una tabella interna, che verrà archiviata nel data warehouse Hive e gestita completamente da Hive.
* **ARCHIVIATI come ORCO**: sono archiviati i dati nel formato ottimizzato riga colonne (ORCO). Si tratta di un formato altamente ottimizzato ed efficiente per l'archiviazione dei dati Hive.
* **Inserisci SOVRASCRIVI... Selezionare**: Seleziona le righe dalla tabella **log4jLogs** che contiene **[errore]**e quindi inserita i dati nella tabella **errorLogs** .

> [AZURE.NOTE] A differenza di tabelle esterne, eliminazione di una tabella interna elimina anche i dati sottostanti.

##<a id="usetez"></a>Utilizzare Apache Tez per migliorare le prestazioni

[Apache Tez](http://tez.apache.org) è una struttura che consente di applicazioni a elevato dati, ad esempio Hive da eseguire in modo più efficiente in scala. Nella versione più recente di HDInsight, Hive supporta l'esecuzione in Tez. Tez è attivata per impostazione predefinita per i cluster basati su Linux HDInsight.

> [AZURE.NOTE] Tez è attualmente disattivata per impostazione predefinita per i cluster HDInsight basato su Windows e deve essere abilitata. Per sfruttare Tez, è necessario impostare il valore seguente per una query Hive:
>
> ```set hive.execution.engine=tez;```
>
>Può essere inviato alla scala cronologica per query posizionandolo all'inizio della query. È inoltre possibile impostare in modo che sia per impostazione predefinita in un cluster impostando il valore di configurazione quando si crea il cluster. Sono disponibili ulteriori dettagli [Provisioning cluster HDInsight](hdinsight-provision-clusters.md).

[Hive nei documenti di progettazione Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contengono un numero di dettagli sulle opzioni di implementazione e ottimizzazione configurazioni.

Per facilitare il debug di processi è stato eseguito utilizzando Tez, HDInsight fornisce le interfacce utente che consentono di visualizzare i dettagli dei processi Tez web seguenti:

* [Utilizzare l'interfaccia utente Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)

* [Utilizzare la visualizzazione di Ambari Tez in HDInsight basati su Linux](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Scegliere la modalità di esecuzione del processo di HiveQL

HDInsight possono essere eseguiti processi HiveQL con una vasta gamma di metodi. Utilizzare la tabella seguente per decidere quale metodo appropriato, quindi fare clic sul collegamento per una procedura dettagliata.

| **Utilizzare questa opzione** se si desidera che...                                                     | .... is shell **interattivi** | ... elaborazione **batch** | via nuovo **sistema operativo del cluster** | via Soido questo **sistema operativo client** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Visualizzazione di hive](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | (Browser qualsiasi in base a) |
| [Comando beeline (da una sessione SSH)](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Comando hive (da una sessione SSH)](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Curvatura](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux o Windows                          | Linux, Unix, Mac OS X o Windows        |
| [Console di query](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | (Browser qualsiasi in base a)                            |
| [HDInsight tools per Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |
| [Desktop remoto](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Esecuzione di processi Hive su Azure HDInsight utilizzo locale SQL Server Integration Services

È anche possibile usare SQL Server Integration Services (SSIS) per eseguire un processo Hive. Azure Feature Pack per SSIS fornisce i seguenti componenti che funzionano con i processi di Hive su HDInsight.


- [Attività di Azure HDInsight Hive][hivetask]
- [Gestione di connessione abbonamento Azure][connectionmanager]


Altre informazioni su Azure Feature Pack per SSIS [qui][ssispack].


##<a id="nextsteps"></a>Passaggi successivi

Dopo aver appreso Hive è e come utilizzarlo con Hadoop in HDInsight, utilizzare i collegamenti seguenti per esplorare altre modalità per lavorare con Azure HDInsight.


- [Caricare dati HDInsight][hdinsight-upload-data]
- [Utilizzare maialino con HDInsight][hdinsight-use-pig]
- [Utilizzare Sqoop con HDInsight](hdinsight-use-sqoop.md)
- [Utilizzare Oozie con HDInsight](hdinsight-use-oozie.md)
- [Utilizzare processi MapReduce con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
