<properties
   pageTitle="Panoramica dell'archivio Lake dati di Azure | Microsoft Azure"
   description="Capire a cosa Azure Lake archivio dati e il valore fornito su altri archivi dati"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="overview-of-azure-data-lake-store"></a>Panoramica dell'archivio Lake dati di Azure

Azure Lake archivio in un archivio di hyper scala aziendale per carichi di lavoro analitico di dati. Azure dati Lake consente di acquisire i dati di qualsiasi dimensione, tipo e acquisizione velocità in una posizione centralizzata per operativi ed esplorativo analitica.

> [AZURE.TIP] Utilizzare il [percorso formativo: archivio dati Lake](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) per iniziare a esplorare il servizio di archiviazione Lake dati di Azure.

Archivio Lake dati di Azure accessibili dal Hadoop (disponibile con cluster HDInsight) utilizzando le API REST compatibile con WebHDFS. Progettato per consentire analitica sui dati archiviati e ottimizzato per prestazioni ottimali per scenari analitica dei dati. All'esterno della casella, che include tutte le funzionalità di livello aziendale, ovvero protezione, gestibilità, scalabilità, l'affidabilità e disponibilità, essenziali per casi di utilizzo aziendali reali.


![Lake di dati di Azure](./media/data-lake-store-overview/data-lake-store-concept.png)

Alcune delle funzionalità chiave di Lake di dati di Azure includono le seguenti.

### <a name="built-for-hadoop"></a>Progettate per Hadoop

L'archivio di Azure dati Lake è un sistema di file Hadoop Apache compatibile con Hadoop Distributed File System (HDFS) e funziona con ecosistema Hadoop.  Esistente HDInsight o servizi che utilizzano l'API WebHDFS possono facilmente integrare con dati Lake archivio. Archivio Lake dati esposta anche un'interfaccia compatibile con WebHDFS resto per le applicazioni

Dati archiviati nell'archivio dati Lake possono essere analizzati facilmente con Hadoop Framework analitico, ad esempio MapReduce o Hive. Cluster di Microsoft Azure HDInsight può essere completato il provisioning e configurato per accedere direttamente ai dati archiviati in archivio Lake dati.

### <a name="unlimited-storage-petabyte-files"></a>Archiviazione illimitata, petabyte file

Archivio Lake dati di Azure fornisce archiviazione illimitata ed è adatto per l'archiviazione di una serie di dati per analitica. Eventuali limiti per le dimensioni di account, le dimensioni del file o la quantità di dati memorizzati in un lake dati non viene causato. Singoli file possono variare da kilobyte a petabyte dimensioni effettua la scelta ideale per l'archiviazione di qualsiasi tipo di dati. I dati vengono archiviati in modo permanente rendendo più copie e non c'è alcun limite per la durata di apertura per il quale i dati possono essere creati in lake dati.

### <a name="performance-tuned-for-big-data-analytics"></a>Le prestazioni ottimizzate per analitica di dati

Archivio Lake dati di Azure integrato per sistemi su larga scala analitico che richiedono velocità enorme per eseguire query e analizzare grandi quantità di dati. Lake dati si estende parti di un file su un numero di server di archiviazione singola. Per migliorare la velocità di lettura quando la lettura del file in parallelo per l'esecuzione di analitica di dati.


### <a name="enterprise-ready-highly-available-and-secure"></a>Aziendale: Elevata disponibilità e sicuro

Archivio Lake dati di Azure fornisce l'affidabilità e la disponibilità di standard di settore. Risorse di dati vengono archiviate in modo permanente eseguendo copie ridondanti per prevenire eventuali errori imprevisti. Aziende è possono utilizzare Azure dati Lake nelle soluzioni come una parte importante della propria piattaforma dati esistenti.

Archivio dati Lake vengono forniti anche la protezione aziendale per i dati archiviati. Per ulteriori informazioni, vedere [protezione dei dati di Azure dati Lake Store](#DataLakeStoreSecurity).


### <a name="all-data"></a>Tutti i dati

Archivio Lake dati di Azure possibile memorizzare i dati nel formato originale, così come sono, senza richiedere tutte le trasformazioni precedente. Archivio dati Lake non richiede uno schema per essere definite prima che i dati vengono caricati, uscire da tutte le singole framework analitico per interpretare i dati e definire uno schema al momento dell'analisi. La possibilità di archiviare i file di dimensioni non autorizzate e i formati consente archivio Lake dati gestire i dati strutturati, semistrutturati e non strutturati.

I contenitori di Azure dati Lake archivio per i dati sono essenzialmente a cartelle e file. Si lavorare sui dati archiviati mediante SDK, portale Azure e Azure Powershell. Come si inseriscono dati nell'archivio utilizza queste interfacce e l'utilizzo di contenitori appropriati, è possibile memorizzare qualsiasi tipo di dati. Archivio dati Lake non esegue una gestione speciale dei dati in base al tipo di dati di archiviazione.


## <a name="DataLakeStoreSecurity"></a>Protezione dei dati nell'archivio Lake dati di Azure

Controllo dell'accesso gli elenchi per gestire l'accesso ai dati di Azure dati Lake archivio utilizzato Azure Active Directory per l'autenticazione.

| Caratteristica                                 | Descrizione                              |
|-----------------------------------------|------------------------------------------|
| Autenticazione | Archivio Lake dati di Azure si integra con Azure Active Directory (AAD) per la gestione delle identità e accesso per tutti i dati archiviati in archivio Lake dati di Azure. In seguito all'integrazione, vantaggi Azure dati Lake da tutte le caratteristiche AAD incluse autenticazione a più fattori, accesso condizionato, il controllo dell'accesso basato sui ruoli, monitoraggio sull'utilizzo dell'applicazione, sicurezza avviso e il monitoraggio e così via. Archivio Lake dati di Azure supporta il protocollo OAuth 2.0 per l'autenticazione con nell'interfaccia REST. |
| Controllo dell'accesso                          | Archivio Lake dati di Azure fornisce il controllo dell'accesso tramite il supporto di autorizzazioni di tipo POSIX esposte tramite il protocollo WebHDFS. Nella versione corrente ACL è possono attivare la cartella principale, le sottocartelle, nonché singoli file. Gli elenchi di controllo che applica nella cartella radice verrà anche applicabili a tutti i figlio cartelle o file anche.|

Vogliono maggiori informazioni sulla protezione dei dati nell'archivio Lake dati. Seguire i collegamenti seguenti.

* Per istruzioni su come la protezione dei dati nell'archivio Lake dati, vedere [protezione dei dati nell'archivio Lake dati di Azure](data-lake-store-secure-data.md).
* Si preferisce video? [Guardare questo video](https://mix.office.com/watch/1q2mgzh9nn5lx) su come proteggere i dati archiviati nell'archivio Lake dati.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Applicazioni compatibili con archivio Lake dati di Azure

Archivio Lake dati di Azure è compatibile con più aperti componenti di origine in ecosistema Hadoop. Si integra perfettamente con altri servizi di Azure. In questo modo archivio dati Lake un'opzione ideale per le esigenze di archiviazione dei dati. Seguire i collegamenti seguenti per informazioni su come archivio dati Lake può essere utilizzato sia con componenti Apri origine, nonché di altri servizi di Azure.

* Per un elenco di applicazioni open source con dati Lake Store, vedere [applicazioni e servizi compatibili con Azure dati Lake archivio](data-lake-store-compatible-oss-other-applications.md) .
* Vedere [integrazione con altri servizi di Azure](data-lake-store-integrate-with-other-services.md) a capire come archivio dati Lake utilizzabile con altri servizi di Azure per abilitare una vasta gamma di scenari.
* Vedere [scenari di utilizzo di archivio di dati Lake](data-lake-store-data-scenarios.md) per imparare a utilizzare dati Lake Store in scenari, ad esempio il caricamento di dati, l'elaborazione dei dati, il download dei dati e la visualizzazione dati.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Che cos'è archivio Lake dati di Azure file system (adl: / /)?

Archivio dati Lake accessibili tramite il nuovo file System, il AzureDataLakeFilesystem (adl: / /), in ambienti Hadoop (disponibili con cluster HDInsight). Le applicazioni e servizi che utilizzano adl: / / sono in grado di sfruttare ulteriormente l'ottimizzazione delle prestazioni non sono attualmente disponibili in WebHDFS. Di conseguenza, archivio dati Lake offre flessibilità a uno Calc le massime prestazioni con l'opzione consigliata dell'utilizzo adl: / / o gestire il codice esistente per continuare a utilizzare l'API WebHDFS direttamente. Azure HDInsight utilizza AzureDataLakeFilesystem per fornire le massime prestazioni dati Lake archivio.

È possibile accedere ai dati nell'archivio dati Lake mediante `adl://<data_lake_store_name>.azuredatalakestore.net`. Per ulteriori informazioni su come accedere ai dati nell'archivio Lake dati, vedere [le proprietà di visualizzazione dei dati archiviati](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Come inizia a usare archivio Lake dati di Azure?

Vedere [Guida introduttiva a archivio Lake dati tramite il portale di Azure](data-lake-store-get-started-portal.md), su come effettuare il provisioning di un archivio di Lake dati tramite il portale di Azure. Dopo avere completato il provisioning di Azure dati Lake, viene illustrato come utilizzare offerte di dati, ad esempio Azure dati Lake Analitica o Azure HDInsight con dati Lake archivio. È anche possibile creare un'applicazione .NET per creare un account Azure dati Lake Store e operazioni, ad esempio carica dati, scaricare dati e così via.

- [Guida introduttiva a dati di Azure Lake Analitica](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Guida introduttiva a archivio Lake Azure utilizzando .NET SDK](data-lake-store-get-started-net-sdk.md)


## <a name="data-lake-store-videos"></a>Video di archivio di dati Lake

Se si preferisce riproduzione di video per scoprire, archivio Lake dati fornisce video su un intervallo di funzionalità.

* [Creare un Account Azure dati Lake archivio](https://mix.office.com/watch/1k1cycy4l4gen)
* [Usare Esplora aree di dati per gestire i dati nell'archivio Lake dati di Azure](https://mix.office.com/watch/icletrxrh6pc)
* [Connettere dati Azure Lake Analitica all'archivio Lake dati di Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Accesso Azure dati Lake archivio tramite dati Lake Analitica](https://mix.office.com/watch/1n0s45up381a8)
* [Connettersi Azure HDInsight archivio Lake dati di Azure](https://mix.office.com/watch/l93xri2yhtp2)
* [Accesso archivio Lake dati di Azure tramite Hive e maialino](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Consente di copiare i dati nel e dall'archivio Lake dati di Azure DistCp (Hadoop Distributed copia)](https://mix.office.com/watch/1liuojvdx6sie)
* [Utilizzare Apache Sqoop per spostare dati tra origini relazionali e archivio Lake dati di Azure](https://mix.office.com/watch/1butcdjxmu114)
* [Orchestrazione dati utilizzando Azure Data Factory per archivio Lake dati di Azure](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Protezione dei dati nell'archivio Lake dati di Azure](https://mix.office.com/watch/1q2mgzh9nn5lx)



