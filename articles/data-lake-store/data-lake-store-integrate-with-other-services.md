<properties
   pageTitle="Integrazione archivio Lake dati con altri servizi Azure | Microsoft Azure"
   description="Comprendere come archivio dati Lake integrazione con altri servizi di Azure"
   documentationCenter=""
   services="data-lake-store"
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrazione archivio Lake dati con altri servizi Azure

Archivio Lake dati di Azure è utilizzabile in combinazione con altri servizi di Azure per abilitare una vasta gamma di scenari. L'articolo seguente sono elencati i servizi che è possibile integrare dati Lake Store con.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Usare dati Lake archivio con Azure HDInsight

È possibile eseguire il provisioning di un cluster di [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) che usa dati Lake archivio man mano che HDFS conforme. In questa versione, per Hadoop ed eccesso cluster in Windows e Linux, è possibile utilizzare dati Lake archivio solo come un ulteriore spazio di archiviazione. Tali cluster utilizzano lo spazio di archiviazione Azure (WASB) come spazio di archiviazione predefinito. Tuttavia, per i cluster HBase in Windows e Linux, è possibile utilizzare dati Lake archivio come spazio di archiviazione predefinito e/o spazio di archiviazione aggiuntivo.

Per istruzioni su come effettuare il provisioning di un cluster di HDInsight con dati Lake Store, vedere:

* [Effettuare il provisioning di un cluster di HDInsight con archivio Lake dati nel portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Effettuare il provisioning di un cluster di HDInsight con archivio di Lake dati tramite PowerShell Azure](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Si preferisce video?** Seguire i collegamenti seguenti per guardare i video con le istruzioni su come utilizzare archivio Lake dati con i cluster HDInsight.

* [Creare un cluster di HDInsight con l'accesso ai dati Lake archivio](https://mix.office.com/watch/l93xri2yhtp2)
* Dopo aver cluster configurato, [dati di Access nell'archivio Lake dati usando gli script Hive e maialino](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Usare dati Lake archivio con dati di Azure Lake Analitica

[Azure dati Lake Analitica](../data-lake-analytics/data-lake-analytics-overview.md) consente di lavorare con dati di grandi dimensioni in scala cloud. In modo dinamico, esegue il provisioning di risorse e consente di eseguire analitica terabyte o Exabyte anche dei dati memorizzati in un numero di origini dati supportate, uno di essi da archivio Lake dati. Dati Lake Analitica appositamente è ottimizzato per l'uso con Azure Lake archivio - fornire elevato delle prestazioni, velocità e parallelizzazione i carichi di lavoro di dati.

Per istruzioni su come utilizzare dati Lake Analitica con archivio Lake dati, vedere [Guida introduttiva a dati Lake Analitica utilizzando archivio Lake dati](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Si preferisce video?** Seguire i collegamenti seguenti per guardare i video con le istruzioni su come utilizzare archivio Lake dati con i cluster HDInsight.

* [Connettere dati Azure Lake Analitica all'archivio Lake dati di Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Accesso Azure dati Lake archivio tramite dati Lake Analitica](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>Usare dati Lake Store con dati di Azure Factory

È possibile utilizzare [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) per acquisire dati da tabelle Azure, Database SQL Azure, data warehouse di SQL Azure, lo spazio di archiviazione BLOB e database locale. Da un positivo prima classe nell'ecosistema Azure, Azure dati Factory può essere utilizzata per gestire l'acquisizione di dati in questa origine all'archivio Lake dati di Azure.

Per istruzioni su come utilizzare Factory di dati di Azure con archivio Lake dati, vedere [spostare i dati nel e dall'archivio Lake Factory dati](../data-factory/data-factory-azure-datalake-connector.md).

**Nuovo video!** Vedere [Orchestrazione dati utilizzando Azure Data Factory per archivio Lake dati di Azure](https://mix.office.com/watch/1oa7le7t2u4ka). 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copiare dati lo spazio di archiviazione BLOB archivio Lake dati

Archivio Lake dati di Azure fornisce uno strumento della riga di comando, AdlCopy, che consente di copiare un account di archivio di dati Lake dati dallo spazio di archiviazione Blob Azure. Per ulteriori informazioni, vedere [copiare dati da Azure lo spazio di archiviazione BLOB all'archivio Lake dati](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copiare i dati tra Database SQL Azure e archivio Lake dati

È possibile utilizzare Apache Sqoop per importare ed esportare i dati tra Database SQL Azure e archivio Lake dati. Per ulteriori informazioni, vedere [copiare i dati tra Lake archivio dati e database di SQL Azure con Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Guardare questo video** sul [Utilizzando Apache Sqoop per spostare i dati tra origini relazionali e Azure dati Lake Store](https://mix.office.com/watch/1butcdjxmu114).

## <a name="use-data-lake-store-with-stream-analytics"></a>Usare dati Lake archivio con Analitica flusso

È possibile utilizzare dati Lake archivio come uno degli output per archiviare i dati in streaming utilizzando Azure flusso Analitica. Per ulteriori informazioni, vedere [flusso dati da Azure lo spazio di archiviazione Blob all'archivio dati Lake mediante Azure flusso Analitica](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Usare dati Lake Store con Power BI

È possibile usare Power BI per importare dati da un account di archivio Lake dati da analizzare e visualizzare i dati. Per ulteriori informazioni, vedere [dati analizza nell'archivio Lake dati mediante Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Usare dati Lake Store con il catalogo dati

È possibile registrare dati dall'archivio dati Lake nel catalogo dei dati di Azure per rendere il in tutta l'organizzazione dei dati. Per ulteriori informazioni, vedere [registrare i dati dall'archivio di dati Lake nel catalogo dati di Azure](data-lake-store-with-data-catalog.md).


## <a name="see-also"></a>Vedere anche

- [Panoramica dell'archivio Lake dati di Azure](data-lake-store-overview.md)
- [Guida introduttiva a archivio Lake dati nel portale](data-lake-store-get-started-portal.md)
- [Guida introduttiva a archivio di Lake dati tramite PowerShell](data-lake-store-get-started-powershell.md)  
