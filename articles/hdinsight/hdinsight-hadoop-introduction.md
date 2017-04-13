 <properties
    pageTitle="Introduzione a Hadoop - che cos'è Hadoop su HDInsight? | Microsoft Azure"
    description="Introduzione a Hadoop, distribuito grande elaborazione di dati e analisi e i componenti dell'ecosistema Hadoop nel cloud HDInsight."
    keywords="analisi dei dati grande, Introduzione a hadoop, che cos'è hadoop, hadoop tecnologia stack, ecosistema hadoop"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Introduzione all'ecosistema Hadoop su Azure HDInsight

 In questo articolo viene fornita un'introduzione a Hadoop su Azure HDInsight con le relative ecosistema e dati. Informazioni sui componenti Hadoop, terminologia più comune e scenari per l'analisi dei dati di grandi dimensioni.

## <a name="what-is-hadoop-on-hdinsight"></a>Che cos'è Hadoop su HDInsight?

Hadoop fa riferimento a un insieme di software Apri origine è un framework per l'elaborazione distribuito, l'archiviazione offre e l'analisi di grande set di dati nei cluster di computer. Azure HDInsight rende disponibili i componenti Hadoop dalla distribuzione **Hortonworks dati piattaforma (HDP)** nel cloud e lo distribuisce e disposizioni cluster gestito con affidabilità e disponibilità.  

Apache Hadoop è stato il progetto Apri origine originale per grande elaborazione dei dati. Di seguito è riportato lo sviluppo di software correlati e utilità considerato parte dello stack di tecnologia Hadoop, inclusi Apache Hive, HBase Apache, ad Apache e molti altri. Per informazioni dettagliate, vedere [Panoramica dell'ecosistema Hadoop in HDInsight](#overview) .

## <a name="what-is-big-data"></a>Che cos'è grandi dati?

Dati descrivono qualsiasi corpo volumi delle informazioni digitali, nel testo in un Twitter feed, alle informazioni sensore da apparecchiature industriali, per informazioni sui clienti esplorazione e acquisti in un sito Web. Possono essere dati cronologici (ovvero dati memorizzati) o in tempo reale (ovvero in streaming direttamente dal sito di origine). Dati sono stati raccolti in volumi mai escalation, velocità sempre più alti e un'espansione diversi formati.

Per i dati grandi fornire informazioni o intelligence con funzioni specifiche, è necessario raccogliere dati rilevanti e porre domande destra. È anche necessario verificare che i dati sono accessibili, pulito, analizzare e quindi visualizzata in modo utile. Ecco dove consente di analisi dati Hadoop in HDInsight.

## <a name="overview"></a>Panoramica dell'ecosistema Hadoop in HDInsight

HDInsight è una distribuzione cloud in Microsoft Azure dello stack di tecnologia Apache Hadoop rapida espansione per l'analisi di dati. Includa implementazioni dei motori di Apache, HBase, eccesso, maialino, Hive, Sqoop, Oozie, Ambari e così via. HDInsight si integra anche con gli strumenti di business intelligence (BI), ad esempio Power BI, Excel, SQL Server Analysis Services e SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, motori, eccesso e cluster personalizzate

HDInsight offre configurazioni cluster per Hadoop Apache, motori, HBase o eccesso. In alternativa, è possibile [personalizzare i cluster con le azioni script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: consente la memorizzazione dati affidabile con [HDFS](#hdfs)e un semplice modello di programmazione [MapReduce](#mapreduce) per elaborare e analizzare i dati in parallelo.

* **<a target="_blank" href="http://spark.apache.org/">Motori Apache</a>**: una struttura di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni delle applicazioni di analisi dei dati grande, riguardo works per SQL, i dati, flusso di produzione e risorse. Vedere [Panoramica: che cos'è motori Apache in HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: un database NoSQL basato su Hadoop che fornisce l'accesso casuale e la coerenza sicura per grandi quantità di dati non strutturati e semistrutturati - potenzialmente miliardi di righe volte milioni di colonne. Vedere [Panoramica delle HBase in HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache eccesso</a>**: un sistema di calcolo distribuito e in tempo reale per l'elaborazione di flussi di dati di grandi dimensioni rapidamente. Eccesso viene presentato come un cluster gestito in HDInsight. Vedere [i dati in tempo reale sensore analizza utilizzando eccesso e Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Script di personalizzazione di esempio

Azioni di script sono script eseguiti durante il provisioning di cluster che può essere utilizzato per installare componenti aggiuntivi nel cluster. Per i cluster basati su Linux sono Bash script.

Gli script di esempio seguenti vengono forniti dal team di HDInsight:

* [Tonalità](hdinsight-hadoop-hue-linux.md): set di applicazioni web utilizzato per interagire con un cluster. Solo cluster Linux.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): grafico elaborazione per le relazioni del modello tra elementi o persone.

* [R](hdinsight-hadoop-r-scripts-linux.md): un ambiente per il calcolo statistici usati in apprendimento e lingua Apri origine.

* [Solr](hdinsight-hadoop-solr-install-linux.md): una piattaforma ricerca scala aziendale che consente di ricerca full-text sui dati.

Per informazioni sullo sviluppo delle Script azioni personalizzate, vedere [sviluppo di Script azione con HDInsight](hdinsight-hadoop-script-actions-linux.md).


## <a name="what-are-the-hadoop-components-and-utilities"></a>Quali sono i componenti Hadoop e le utilità?

I seguenti componenti e utilità sono inclusi nei cluster HDInsight.

* **[Ambari](#ambari)**: Cluster di provisioning, gestione, monitoraggio e utilità.

* **[Avro](#avro)** (Libreria Microsoft .NET per Avro): serializzazione dei dati per l'ambiente Microsoft .NET.

* **[Hive & HCatalog](#hive)**: linguaggio SQL (Structured Query)-come query e un livello di gestione di tabella e lo spazio di archiviazione.

* **[Mahout](#mahout)**: formazione applicazioni per computer scalable.

* **[MapReduce](#mapreduce)**: framework Legacy per Hadoop distributed gestione elaborazione e delle risorse. Vedere [filati](#yarn), il framework avanguardia risorsa.

* **[Oozie](#oozie)**: gestione del flusso di lavoro.

* **[Phoenix](#phoenix)**: livello di database relazionali su HBase.

* **[Maialino](#pig)**: gli script più semplice per le trasformazioni MapReduce.

* **[Sqoop](#sqoop)**: importazione ed esportazione di dati.

* **[Tez](#tez)**: consente di eseguire in modo efficiente in scala dei processi di uso intensivo di dati.

* **[Filati](#yarn)**: parte della libreria di base Hadoop e nuova generazione di framework software MapReduce.

* **[ZooKeeper](#zookeeper)**: coordinazione dei processi di sistemi distribuiti.

> [AZURE.NOTE] Per informazioni sui componenti specifici e informazioni sulla versione, vedere [Hadoop componenti, controllo delle versioni e offerte di servizi in HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari riguarda il provisioning, la gestione di monitoring Apache Hadoop cluster. Include un insieme di strumenti di operatore intuitivo e un set di API che nascondere la complessità della Hadoop, semplificando l'operazione di cluster efficaci. HDInsight basati su Linux cluster forniscono entrambe le Ambari web dell'interfaccia utente e API REST Ambari, mentre i cluster basato su Windows offrono un sottoinsieme dell'API REST. Le viste Ambari sui HDInsight cluster consentono capacità di interfaccia utente plug-in.

Vedere [cluster di gestire HDInsight mediante Ambari](hdinsight-hadoop-manage-ambari.md) (solo Linux), [cluster Hadoop Monitor in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md)e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">riferimento all'API Ambari Apache</a>.

### <a name="avro"></a>Avro (libreria Microsoft .NET per Avro)

Il formato di interscambio dati binari compatta Apache Avro per serializzazione per l'ambiente Microsoft .NET di implementazione la libreria di Microsoft .NET per Avro. Utilizza <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> per definire uno schema indipendente dalla lingua che copra l'interoperabilità lingua, dati significato serializzati in una lingua possono essere letti in un altro. Sono disponibili informazioni dettagliate sul formato nel < un valore di destinazione = con sottoindirizzo "vuota" _ = "http://avro.apache.org/docs/current/spec.html" > Apache Avro specifica</a>.
Il formato di file Avro supporta il modello di programmazione MapReduce distribuito. I file sono "splittable", ovvero è possibile cercare qualsiasi point in un file e avviare la lettura da un blocco specifico. Per ulteriori informazioni, vedere [dati con la raccolta di Microsoft .NET per Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) è un file system distribuito che con MapReduce e filati, è essenziale dell'ecosistema Hadoop. HDFS è il sistema di file standard per cluster Hadoop su HDInsight.

### <a name="hive"></a>Hive & HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> è un software di magazzino dati basato su Hadoop che consente di eseguire una query e gestire grandi set di dati in archiviazione distribuita utilizzando un linguaggio SQL denominato HiveQL. Hive, ad esempio maialino, è un'astrazione nella parte superiore di MapReduce. Quando si esegue, Hive converte query in una serie di processi MapReduce. Hive livello concettuale supera per un sistema di gestione dei database relazionali maialino, pertanto è appropriato per l'utilizzo con i dati più strutturati. Per dati non strutturati, maialino è la scelta migliore. Vedere [usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> è un livello di gestione di tabella e lo spazio di archiviazione per Hadoop che presenta gli utenti con una visualizzazione relazionale dei dati. In HCatalog, è possibile leggere e scrivere i file in qualsiasi formato per il quale è possibile scrivere un SerDe Hive (serializzatore deserializzatore).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> è una raccolta scalable di apprendimento algoritmi eseguito Hadoop. Principi delle statistiche di applicazioni di apprendimento machine addestrare sistemi di imparare dai dati e utilizzare oltre risultati per determinare il comportamento futuro. Vedere i [suggerimenti di filmato genera utilizzando Mahout in Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce è il framework software legacy per Hadoop per la creazione di applicazioni in batch processo grande set di dati in parallelo. Un processo MapReduce divide grandi set di dati e consente di organizzare i dati in coppie di parole chiave valore per l'elaborazione.

[Filati](#yarn) il Framework Hadoop avanguardia risorsa gestore e l'applicazione e viene indicato come MapReduce 2.0. MapReduce processi eseguiti su filati.

Per ulteriori informazioni sulla MapReduce, vedere <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> nel Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> è un sistema di coordinazione del flusso di lavoro che consente di gestire processi di Hadoop. È integrato con stack Hadoop e supporta processi Hadoop per MapReduce, maialino, Hive e Sqoop. Può inoltre essere utilizzato per pianificare processi specifici di un sistema, ad esempio programmi Java o script di shell. Vedere [usare Oozie con Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Phoenix Apache</a> è un livello di database relazionali su HBase. Phoenix include un driver JDBC che consente agli utenti di query e gestire direttamente le tabelle SQL. Phoenix converte query e altre istruzioni in chiamate API NoSQL native - anziché con MapReduce - consentendo processore applicazioni nella parte superiore di NoSQL stores. Vedere [usare Apache Phoenix ed esce con i cluster HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Maialino
<a  target="_blank" href="http://pig.apache.org/">Maialino Apache</a> è una piattaforma di alto livello che consente di eseguire trasformazioni complesse MapReduce grandi set di dati utilizzando un linguaggio di script semplice chiamato maialino latino. Maialino converte gli script latino maialino in modo che verrà eseguito all'interno di Hadoop. È possibile creare funzioni definite dall'utente per estendere maialino latino. Vedere [usare maialino con Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> è strumento che trasferimenti blocco dei dati tra Hadoop e database relazionali, ad esempio SQL o altri archivi dati strutturati in modo ottimale. Vedere [usare Sqoop con Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> è un'infrastruttura di applicazioni basata su filati Hadoop che esegue complessi, aciclici grafici generale dell'elaborazione dei dati. È un successore più flessibile e potente a framework MapReduce che consente di processi di uso intensivo di dati, ad esempio Hive da eseguire in modo più efficiente in scala. Vedere ["Utilizzo di Apache Tez per migliorare le prestazioni" in uso Hive e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>FILATI
FILATI Apache è la nuova generazione di MapReduce (MapReduce 2.0 o MRv2) e supporta gli scenari di elaborazione dei dati oltre elaborazione con maggiore scalabilità e in tempo reale elaborazione batch MapReduce. FILATI fornisce la gestione delle risorse e un quadro di applicazione distribuita. MapReduce processi eseguiti su filati.

Per informazioni su filati, vedere <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (filati)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordinate processi in grandi sistemi distribuiti mediante uno spazio dei nomi gerarchico condivisa dei registri di dati (znodes). Znodes contengono piccole quantità di informazioni metatag necessario per coordinare processi: stato, posizione, configurazione e così via.

## <a name="programming-languages-on-hdinsight"></a>Linguaggi di programmazione in HDInsight

HDInsight cluster - Hadoop, HBase, eccesso e motori i cluster - supportano un numero di linguaggi di programmazione, ma alcune non vengono installati per impostazione predefinita. Per le raccolte moduli o pacchetti non è installati per impostazione predefinita, utilizzano un'azione script per installare il componente. Vedere [sviluppo di azione di Script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Predefinito il supporto del linguaggio di programmazione

Per impostazione predefinita, HDInsight cluster di supporto:

* Java

* Python

Altre lingue possono essere installate utilizzando le azioni script: [sviluppo di azione di Script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Lingue macchina virtuale (JVM) Java

Più lingue ad eccezione di linguaggio possono essere eseguite tramite Java virtual machine (JVM); Tuttavia, l'esecuzione di alcune delle lingue può richiedere componenti aggiuntivi installati nel cluster.

Queste lingue basate su JVM sono supportate nei cluster HDInsight:

* Clojure

* Jython (Python per Java)

* Scala

### <a name="hadoop-specific-languages"></a>Linguaggi specifici di Hadoop

HDInsight cluster supportano le lingue seguenti sono specifiche della ecosistema Hadoop:

* Maialino latino per i processi di maialino

* HiveQL per SparkSQL e processi Hive


## <a name="advantage"></a>Vantaggi della Hadoop nel cloud

Come parte dell'ecosistema cloud Azure, Hadoop in HDInsight offre numerosi vantaggi, tra cui:

* Preparazione automatica dei Hadoop cluster. HDInsight cluster sono molto più facile creare più configurazione manuale cluster Hadoop. Per informazioni dettagliate, vedere [cluster di provisioning Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componenti di Hadoop dell'immagine. Per informazioni dettagliate, vedere [Hadoop componenti, controllo delle versioni e offerte di servizi in HDInsight][component-versioning].

* Disponibilità e l'affidabilità dei cluster. Per informazioni dettagliate, vedere [disponibilità e l'affidabilità dei cluster Hadoop in HDInsight](hdinsight-high-availability-linux.md) .

* Archiviazione dei dati economico ed efficiente con archiviazione Blob Azure, un'opzione di compatibile con Hadoop. Per informazioni dettagliate, vedere [lo spazio di archiviazione Blob Azure usare con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md) .

* Integrazione con altri servizi di Azure, inclusi [Web App](https://azure.microsoft.com/documentation/services/app-service/web/) e [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/).

* Altre macchine Virtuali dimensioni e i tipi di per l'esecuzione di cluster HDInsight. Vedere [Hadoop componenti, controllo delle versioni e offerte di servizi in HDInsight] [ component-versioning] per informazioni dettagliate.

* Cluster proporzioni dei caratteri. Ridimensionamento cluster consente di modificare il numero dei nodi di un cluster di HDInsight in esecuzione senza dover eliminare o ricrearlo.

* Supporto di rete virtuale. HDInsight cluster utilizzabili con Azure virtuali per il supporto di isolamento di risorse cloud o scenari ibrido collegare risorse cloud con quelli del Data Center.

* Bassa immissione dei costi. Avviare una [versione di valutazione gratuita](https://azure.microsoft.com/free/)o consultare [informazioni sui prezzi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Per ulteriori informazioni sui vantaggi in Hadoop in HDInsight, vedere la [pagina funzioni Azure HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e Premium HDInsight

HDInsight fornisce offerte cloud di dati in due categorie, Standard e Premium. HDInsight Standard fornisce un cluster di scala aziendale che le organizzazioni possono utilizzare per eseguire i carichi di lavoro di dati. HDInsight Premium si basa su tale e fornisce avanzate analytical e funzionalità di sicurezza per un cluster di HDInsight. Per ulteriori informazioni, vedere [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Risorse per ottenere ulteriori informazioni su analisi dati grande, Hadoop e HDInsight

Compilare questo Introduzione al Hadoop nel cloud e analisi dei dati con le risorse seguenti.


### <a name="hadoop-documentation-for-hdinsight"></a>Documentazione Hadoop HDInsight

* [Documentazione HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): pagina documentazione per Hadoop su Azure HDInsight con collegamenti ad articoli, video e altre risorse.

* [Guida introduttiva a Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): esercitazione avvio rapido per il provisioning di cluster HDInsight Hadoop e l'esecuzione di query Hive di esempio.

* [Guida introduttiva ad in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): esercitazione rapida per la creazione di un cluster di motori e l'esecuzione di query SQL ad interattive.

* [Usare R Server HDInsight](hdinsight-hadoop-r-server-get-started.md): utilizzo di Server R in HDInsight Premium.

* [Disposizione HDInsight cluster](hdinsight-hadoop-provision-linux-clusters.md): informazioni su come effettuare il provisioning di un cluster di HDInsight Hadoop tramite il portale di Azure, CLI Azure e Azure PowerShell.


### <a name="apache-hadoop"></a>Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: ulteriori informazioni sulla raccolta di software Apache Hadoop, un framework che consente di elaborare distribuito i grandi set di dati tra cluster di computer.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: ulteriori informazioni sull'architettura e della struttura di Hadoop Distributed File System, il sistema di archiviazione principale utilizzato da applicazioni Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Esercitazione MapReduce</a>: acquisire familiarità con l'infrastruttura di programmazione per la creazione di applicazioni Hadoop elaborate rapidamente grandi quantità di dati in parallelo su cluster di grandi dimensioni dei nodi di calcolo.


### <a name="microsoft-business-intelligence"></a>Funzionalità di business intelligence Microsoft

Strumenti di familiarità business intelligence (BI) - ad esempio Excel, PowerPivot, SQL Server Analysis Services e SQL Server Reporting Services - recupero, analizzare e dati integrati con HDInsight utilizzando il componente aggiuntivo Power Query o il Driver ODBC Hive Microsoft dei report.

Questi strumenti BI consentono nell'analisi dei dati grande:

* [Connettere Excel a Hadoop con Power Query](hdinsight-connect-excel-power-query.md): informazioni su come connettere Excel per l'account di archiviazione Azure contenente i dati associati il cluster HDInsight tramite Microsoft Power Query per Excel. Workstation Windows necessari. Funziona con cluster basato su Windows o Linux.

* [Connettere Excel a Hadoop con il Driver ODBC Hive Microsoft](hdinsight-connect-excel-hive-odbc-driver.md): informazioni su come importare dati da HDInsight con il Driver ODBC Hive Microsoft. Workstation Windows necessari. Funziona con cluster basato su Windows o Linux.

* [Piattaforma Microsoft Cloud](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): informazioni su Power BI per Office 365, scaricare la versione di valutazione di SQL Server e configurare SharePoint Server 2013 e Business Intelligence di SQL Server.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
