<properties
   pageTitle="Usare Hadoop maialino in HDInsight | Microsoft Azure"
   description="Informazioni su come usare maialino con Hadoop in HDInsight."
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Utilizzare maialino con Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Maialino Apache](http://pig.apache.org/) è una piattaforma per la creazione di applicazioni per Hadoop utilizzando un linguaggio procedurale noto come *maialino latino*. Maialino è un'alternativa al linguaggio per la creazione di soluzioni *MapReduce* ed è incluso in Azure HDInsight.

In questo articolo si apprenderà come è possibile utilizzare maialino con HDInsight.

##<a id="why"></a>Perché usare maialino?

Uno dei problemi di elaborazione dei dati utilizzando MapReduce in Hadoop è implementazione della logica di elaborazione utilizzando una funzione di ridurre e solo una mappa. Per l'elaborazione complessa, è spesso necessario scomporre elaborazione in più operazioni MapReduce che sono concatenate in modo da ottenere il risultato desiderato.

Invece di usare solo mappa e ridurre funzioni forzare, maialino consente di definire elaborazione come una serie di trasformazioni che attraversa i dati per generare l'output desiderato.

Maialino latino consente di descrivere il flusso di dati di input non elaborato, tramite una o più trasformazioni per generare l'output desiderato. Maialino latino programmi seguano questo modello generale:

- **Condizioni di carico**: leggere i dati da modificare dal file system
- **Trasformare**: modificare i dati
- **Immagine della o store**: dati alla schermata di Output o archiviarlo per l'elaborazione

Maialino latino supporta anche le funzioni definite dall'utente (utente), che consente di richiamare componenti esterni che implementano logica difficile da modello in alfabeto latino maialino.

Per ulteriori informazioni sull'alfabeto latino maialino, vedere [maialino latino riferimento manuale 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e [latino maialino riferimento manuale 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Per un esempio dell'uso di funzioni definite dall'utente con maialino, vedere i documenti seguenti:

* [Utilizzare DataFu con maialino in HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu è una raccolta di funzioni definite dall'utente utile gestito da Apache

* [Usare Python con maialino e Hive in HDInsight](hdinsight-python.md)

* [Usare c# con Hive e maialino in HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Sui dati di esempio

In questo esempio viene utilizzato un file di esempio *log4j* , archiviato in **/example/data/sample.log** nel contenitore di spazio di archiviazione blob. Ogni log all'interno del file è costituito da una riga di campi che contiene un `[LOG LEVEL]` campo per visualizzare il tipo e la gravità, ad esempio:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Nell'esempio precedente, l'errore è il livello di registrazione.

> [AZURE.NOTE] È possibile anche generare un file log4j utilizzando lo strumento registrazione [Log4j Apache](http://en.wikipedia.org/wiki/Log4j) e quindi caricare il file per il blob. Per istruzioni, vedere [Caricare dati da HDInsight](hdinsight-upload-data.md) . Per ulteriori informazioni sull'utilizzo di BLOB di archiviazione Azure con HDInsight, vedere [Usare l'archiviazione Blob Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

I dati di esempio vengono archiviati in archiviazione Blob Azure, che utilizza HDInsight come il sistema di file predefinito per i cluster Hadoop. HDInsight accedere ai file archiviati nei BLOB utilizzando il prefisso **wasb** . Ad esempio, per accedere al file sample.log, utilizzare la sintassi seguente:

    wasbs:///example/data/sample.log

Poiché WASB è lo spazio di archiviazione predefinito per HDInsight, è possibile accedere anche il file tramite **/example/data/sample.log** da maialino latino.

> [AZURE.NOTE] La sintassi **wasbs: / / /**, viene utilizzato per accedere ai file archiviati nel contenitore di spazio di archiviazione predefinito per il cluster HDInsight. Account ulteriore spazio di archiviazione è specificato quando viene completato il provisioning del cluster e si vuole accedere ai file archiviati in questi account, è possibile accedere ai dati specificando l'indirizzo di account nome e lo spazio di archiviazione contenitore, ad esempio: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Informazioni sul processo di esempio

Il processo di maialino latino successivo Carica file **sample.log** da spazio di archiviazione predefinito per il cluster HDInsight. Quindi, esegue una serie di trasformazioni che restituiscono un conteggio del numero di volte in cui si è verificato ogni livello di registrazione nei dati di input. I risultati vengono trasferiti in STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Nella figura seguente mostra una suddivisione delle funzionalità di ogni trasformazione ai dati.

![Rappresentazione grafica delle trasformazioni][image-hdi-pig-data-transformation]

##<a id="run"></a>Eseguire il processo di maialino latino

HDInsight eseguire processi latino maialino con una vasta gamma di metodi. Utilizzare la tabella seguente per decidere quale metodo appropriato, quindi fare clic sul collegamento per una procedura dettagliata.

| **Utilizzare questa opzione** se si desidera che...                                   | .... is shell **interattivi** | ... elaborazione **batch** | via nuovo **sistema operativo del cluster** | via Soido questo **sistema operativo client** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X o Windows        |
| [Curvatura](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux o Windows                          | Linux, Unix, Mac OS X o Windows        |
| [.NET SDK per Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux o Windows                          | Windows (per ora)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux o Windows                          | Windows                                  |
| [Desktop remoto](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Esecuzione di processi maialino su Azure HDInsight utilizzo locale SQL Server Integration Services

È anche possibile usare SQL Server Integration Services (SSIS) per eseguire un processo maialino. Azure Feature Pack per SSIS fornisce i seguenti componenti che funzionano con i processi di maialino su HDInsight.


- [Attività di Azure HDInsight maialino][pigtask]
- [Gestione di connessione abbonamento Azure][connectionmanager]


Altre informazioni su Azure Feature Pack per SSIS [qui][ssispack].


##<a id="nextsteps"></a>Passaggi successivi

Ora che è stato illustrato come utilizzare maialino con HDInsight, utilizzare i collegamenti seguenti per esplorare altre modalità per lavorare con Azure HDInsight.

* [Caricare dati HDInsight][hdinsight-upload-data]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Utilizzare Sqoop con HDInsight](hdinsight-use-sqoop.md)
* [Utilizzare Oozie con HDInsight](hdinsight-use-oozie.md)
* [Utilizzare processi MapReduce con HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
