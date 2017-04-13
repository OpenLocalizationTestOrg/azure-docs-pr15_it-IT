<properties
    pageTitle="Memoria insufficiente (OOM) - impostazioni Hive | Microsoft Azure"
    description="Correggere un errore di memoria esaurita (OOM) da una query Hive Hadoop in HDInsight. Lo scenario cliente è una query in molte tabelle di grandi dimensioni."
    keywords="disconnettersi da impostazioni Hive errore OOM, della memoria"
    services="hdinsight"
    documentationCenter=""
    authors="rashimg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="rashimg;jgao"/>

# <a name="fix-an-out-of-memory-oom-error-with-hive-memory-settings-in-hadoop-in-azure-hdinsight"></a>Correggere un errore di memoria (OOM) con le impostazioni di memoria Hive Hadoop in Azure HDInsight

Uno dei problemi comuni nostro nominale clienti riceveranno un messaggio di errore di memoria (OOM) quando tramite Hive. In questo articolo vengono illustrati uno scenario di cliente e le impostazioni di Hive che si consiglia di risolvere il problema.

## <a name="scenario-hive-query-across-large-tables"></a>Scenario: Hive query tra tabelle di grandi dimensioni

Un cliente eseguita la query riportata di seguito utilizzando Hive.

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Alcuni aspetti correlati di questa query:

* T1 è un alias di tabella grande, Tabella1, che sono disponibili molti stringa tipi di colonna.
* Altre tabelle che non sono grandi ma dispongono di un numero elevato di colonne.
* Tutte le tabelle verranno aggiunti a altro, in alcuni casi con più colonne in TABLE1 e gli altri utenti.

Quando il cliente è stato eseguito la query che utilizza Hive in MapReduce su un nodo 24 cluster A3, la query eseguita in circa 26 minuti. Il cliente notato seguenti messaggi di avviso durante l'esecuzione della query in Hive MapReduce:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Poiché la query completata l'esecuzione di circa 26 minuti, il cliente ignorati questi avvisi e invece avviato per concentrarsi su come migliorare la ulteriormente le prestazioni della query.

Il cliente consultato [ottimizzare Hive query per Hadoop in HDInsight](hdinsight-hadoop-optimize-hive-query.md)e deciso di usare Tez motore di esecuzione. Dopo la stessa query è stata eseguita con l'impostazione Tez attivata la query eseguita per 15 minuti e quindi ha generato l'errore seguente:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Il cliente quindi deciso di usare una macchina virtuale (ad esempio D12) pensare una macchina virtuale più grande avrebbe più spazio heap. Anche in questo cliente continua visualizzare l'errore. Il cliente raggiunto al team di HDInsight per informazioni su come risolvere il problema di debug.

## <a name="debug-the-out-of-memory-oom-error"></a>Risolvere l'errore di memoria (OOM)

Il nostro team engineering insieme ha rilevato uno dei problemi che causano l'errore di memoria (OOM) e il supporto è stato un [problema descritto in JIRA Apache noto](https://issues.apache.org/jira/browse/HIVE-8306). Da descrizione nel JIRA:

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

È confermato che **hive.auto.convert.join.noconditionaltask** effettivamente è stato impostato su **true** riconoscibili in hive site.xml file:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
    </property>

In base all'avviso e il JIRA, il nostro ipotesi era mappa partecipare a causa dell'errore linguaggio Heap spazio OOM. Pertanto abbiamo esaminò più approfondita il problema.

Come post di blog [filati Hadoop le impostazioni della memoria in HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando Tez il motore di esecuzione è utilizzata lo spazio heap utilizzato effettivamente a cui appartiene il contenitore Tez. Vedere l'immagine di seguito che descrivono la memoria contenitore Tez.

![Diagramma di memoria contenitore Tez: Hive memoria insufficiente OOM](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)


Come suggerisce il post di blog, le seguenti impostazioni di due memoria definiscono la memoria contenitore per l'heap: **hive.tez.container.size** e **hive.tez.java.opts**. Dall'esperienza, l'eccezione OOM non significa che dimensioni contenitore sono troppo piccola. Indica che le dimensioni di heap Java (hive.tez.java.opts) sono troppo piccola. In modo che ogni volta che viene visualizzato OOM, è possibile provare ingrandire le dimensioni **hive.tez.java.opts**. Se necessario potrebbe essere necessario aumentare **hive.tez.container.size**. L'impostazione **java.opts** dovrebbe essere circa 80% delle **container.size**.

> [AZURE.NOTE]  L' impostazione **hive.tez.java.opts** deve essere inferiore a **hive.tez.container.size**.

Poiché una macchina D12 presenta 28GB di memoria, deciso di usare un formato di contenitore di 10GB (10240MB) e assegnare 80% java.opts. Questa operazione è stata eseguita nella console Hive mediante l'impostazione riportata di seguito:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

In base a queste impostazioni, la query è stato eseguito in dieci minuti.

## <a name="conclusion-oom-errors-and-container-size"></a>Conclusione: Errori OOM e le dimensioni contenitore

Viene visualizzato un errore OOM non necessariamente che le dimensioni del contenitore sono troppo piccola. Se, tuttavia, è necessario configurare le impostazioni della memoria in modo che le dimensioni di heap viene aumentata e inferiore all'80% delle dimensioni della memoria del contenitore.
