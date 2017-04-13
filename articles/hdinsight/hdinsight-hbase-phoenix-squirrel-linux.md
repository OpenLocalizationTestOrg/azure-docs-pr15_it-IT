<properties 
   pageTitle="Utilizzare Apache Phoenix ed esce in HDInsight | Microsoft Azure" 
   description="Informazioni su come usare Apache Phoenix in HDInsight e come installare e configurare esce nella workstation per connettersi a un cluster di HBase in HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Utilizzare Phoenix Apache con i cluster basati su Linux HBase HDInsight  

Informazioni su come usare [Apache Phoenix](http://phoenix.apache.org/) in HDInsight e come usare SQLLine. Per ulteriori informazioni su Phoenix, vedere [Phoenix in 15 minuti](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Per il controllo grammaticale Phoenix, vedere [Phoenix grammatica](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Per informazioni sulla versione di Phoenix in HDInsight, vedere [quali sono le novità in versioni cluster Hadoop fornite da HDInsight?] [hdinsight-versions].

##<a name="use-sqlline"></a>Utilizzare SQLLine
[SQLLine](http://sqlline.sourceforge.net/) è un'utilità della riga di comando per eseguire SQL. 

###<a name="prerequisites"></a>Prerequisiti
Prima di poter usare SQLLine, è necessario disporre le operazioni seguenti:

- **Cluster A HBase in HDInsight**. Per informazioni sulla disposizione HBase cluster, vedere [Introduzione a HBase Apache in HDInsight][hdinsight-hbase-get-started].
- **Connettersi a cluster HBase tramite il protocollo desktop remoto**. Per ulteriori informazioni, vedere [gestire Hadoop cluster in HDInsight tramite il portale classica Azure][hdinsight-manage-portal].


Quando ci si connette a un cluster di HBase, sarà necessario connettersi a uno del Zookeepers. Ogni cluster HDInsight dispone 3 Zookeepers. 

**Per trovare il nome host Zookeeper**

1. Aprire Ambari Sfoglia per **https://<ClusterName>. azurehdinsight.net**.
2. Immettere il nome utente HTTP (cluster) e la password per accedere.
3. Fare clic su **ZooKeeper** dal menu a sinistra. Risulta 3 **ZooKeeper Server** nell'elenco.
4. Fare clic su uno dei **Server ZooKeeper** elencato. Nel riquadro di riepilogo, individuare il **nome host**. È simile a *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Usare SQLLine**

1. Connettersi al cluster utilizzando SSH. Per ulteriori informazioni, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Usare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) a seconda del computer client del sistema operativo.

2. Da SSH, eseguire i comandi seguenti per l'esecuzione SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Eseguire i comandi seguenti per creare una tabella HBase e inserire alcuni dati:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

Per ulteriori informazioni, vedere [SQLLine manuale](http://sqlline.sourceforge.net/#manual) e la [Grammatica Phoenix](http://phoenix.apache.org/language/index.html).


 
##<a name="next-steps"></a>Passaggi successivi
In questo articolo sono state come usare Apache Phoenix in HDInsight.  Per ulteriori informazioni, vedere

- [Panoramica di HDInsight HBase][hdinsight-hbase-overview]: HBase è un database di NoSQL Apri origine, quest'ultimo è basato su Hadoop che fornisce l'accesso casuale e la coerenza sicura per grandi quantità di dati non strutturati e semistructured.
- [Effettuare il provisioning di cluster HBase su Azure virtuali][hdinsight-hbase-provision-vnet]: con l'integrazione di rete virtuale cluster HBase può essere distribuito alla stessa rete virtuale delle applicazioni in modo che le applicazioni possono comunicare con HBase direttamente.
- [Replica di configurare HBase in HDInsight](hdinsight-hbase-geo-replication.md): informazioni su come configurare la replica HBase tra due Data Center Azure. 
- [Analizzare valutazione in Twitter con HBase in HDInsight][hbase-twitter-sentiment]: informazioni su come eseguire [analisi di valutazione in](http://en.wikipedia.org/wiki/Sentiment_analysis) tempo reale di dati utilizzando HBase in un cluster di Hadoop in HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
