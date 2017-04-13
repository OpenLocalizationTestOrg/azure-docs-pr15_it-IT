<properties
    pageTitle="Che cos'è HBase in HDInsight? | Microsoft Azure"
    description="Introduzione a HBase Apache in HDInsight, un database NoSQL essere incrementali Hadoop. Informazioni sui casi di utilizzo e confrontare HBase con altri cluster Hadoop."
    keywords="che cos'è hbase bigtable, nosql,"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Che cos'è HBase in HDInsight: NoSQL un database che offre funzionalità di tipo mi piace BigTable per Hadoop

Apache HBase è un database NoSQL Apri origine, che viene generato in Hadoop e basato su Google BigTable. HBase offre accesso casuale e la coerenza sicura per grandi quantità di dati non strutturati e semistructured in un database schemaless organizzato per le famiglie di colonna.

Archiviazione dei dati nelle righe di una tabella e raggruppamento dei dati all'interno di una riga dalla famiglia di colonna. HBase è un database schemaless nel senso che le colonne né il tipo di dati memorizzati che è necessario definire prima di utilizzarli. Il codice sorgente Apri scale lineare per gestire petabyte dei dati in migliaia di nodi. Si basa sulla ridondanza dei dati, elaborazione batch e altre funzionalità offerte dalle applicazioni distribuite ecosistema Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Come HBase è implementata in Azure HDInsight?

HDInsight HBase viene presentato come un cluster gestito è integrato con l'ambiente di Azure. I cluster sono configurati per archiviare i dati direttamente nel sistema di archiviazione Blob Azure, che fornisce bassa latenza e la maggiore flessibilità nelle scelte prestazioni e costi. In questo modo ai clienti di creare siti Web interattivi che funzionano con grandi set di dati per compilare i servizi che memorizzano dati sensore e telemetria da milioni di punti finali e analizzare i dati con i processi di Hadoop. HBase e Hadoop sono buon punto di partenza per il progetto di dati di Azure; in particolare, consentono alle applicazioni in tempo reale di utilizzare grandi set di dati.

L'implementazione di HDInsight utilizza l'architettura di scalabilità di HBase per fornire sharding automatica delle tabelle coerenza avanzata per la lettura e scrittura e failover automatico. È migliore per la memorizzazione nella cache in memoria per la lettura e streaming per scrive ad alta velocità. È anche disponibile per HDInsight HBase virtuali il provisioning. Per informazioni dettagliate, vedere [cluster di provisioning HDInsight su Azure virtuali] [hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Come viene gestiti dati in HDInsight HBase?

Dati possono essere gestiti in HBase utilizzando il `create`, `get`, `put`, e `scan` comandi dalla shell HBase. Dati scritti nel database utilizzando `put` e leggere utilizzando `get`. Il `scan` comando viene utilizzato per ottenere i dati da più righe in una tabella. Dati possono essere gestiti tramite l'HBase c# API, che fornisce una libreria client nella parte superiore dell'API REST HBase. Un database di HBase è possibile eseguire query anche utilizzando Hive. Per un'introduzione a questi modelli di programmazione, vedere [Introduzione all'utilizzo di HBase con Hadoop in HDInsight][hbase-get-started]. CO-processori sono inoltre disponibili, che consentono di elaborazione dei dati nei nodi che ospitano il database.


## <a name="scenarios-use-cases-for-hbase"></a>Scenari: Casi di utilizzo per HBase
È stato creato il caso di utilizzo canonico per quali BigTable (e di conseguenza, HBase) è stata ricerca sul web. Motori di ricerca creano indici di cui eseguire il mapping di termini per le pagine web che li contengono. Ma sono disponibili molti altri casi di utilizzo è adatto per HBase, ovvero molti dei quali dettagli in questa sezione.

- Valore chiave store

    HBase può essere utilizzato come un valore di chiave store ed è appropriata per la gestione dei sistemi dei messaggi. Facebook utilizza HBase per il sistema di messaggistica ed è ideale per l'archiviazione e gestione delle comunicazioni Internet. WebTable utilizza HBase per cercare e gestire le tabelle estratte da pagine Web.

- Dati sensore

    HBase è utile per acquisire i dati raccolti in modo incrementale da diverse origini. Sono inclusi social analitica, serie temporale, mantenere aggiornati con contatori e le tendenze dei dashboard interattivi e gestione dei sistemi di log di controllo. Esempi operatore Bloomberg terminale e ora serie nel Database aperto (OpenTSDB), che archivia e consente di accedere a metriche raccolte sull'integrità dei sistemi server.

- Query in tempo reale

    [Phoenix](http://phoenix.apache.org/) è un motore di query SQL per Apache HBase. È possibile accedervi come un driver JDBC e consente di esecuzione di query e gestione delle tabelle di HBase utilizzando SQL.

- HBase come piattaforma

    Applicazioni possono eseguire nella parte superiore di HBase utilizzando come un archivio dati. Esempi Phoenix, OpenTSDB, Kiji e Titan. Applicazioni possono inoltre essere integrato con HBase. Esempi Hive, maialino, Solr, eccesso, Flume, Impala, motori, trigeminali e drill-down.


##<a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare HBase con Hadoop in HDInsight][hbase-get-started]
- [Effettuare il provisioning di cluster HDInsight su Azure virtuali] [hbase-provision-vnet]
- [Configurare la replica HBase HDInsight](hdinsight-hbase-geo-replication.md)
- [Analizzare valutazione in Twitter con HBase in HDInsight][hbase-twitter-sentiment]
- [Utilizzare Maven delle applicazioni Java che utilizzano HBase HDInsight (Hadoop)][hbase-build-java-maven]

##<a name="see-also"></a>Vedere anche

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable: Un sistema di archiviazione distribuita per i dati strutturati](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
