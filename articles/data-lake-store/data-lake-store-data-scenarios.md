<properties 
   pageTitle="Scenari di dati relativi dati Lake archivio | Microsoft Azure" 
   description="Comprendere i diversi scenari e gli strumenti utilizzando dati che possono caricamento, elaborata, scaricato e visualizzato in un archivio di Lake dati" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Usare Azure dati Lake archivio per requisiti di dati

In grande elaborazione dei dati sono disponibili quattro fasi principali:

* Il caricamento di grandi quantità di dati in un archivio di dati in tempo reale o in batch
* Elaborazione dei dati
* Scaricare i dati
* La visualizzazione di dati

In questo articolo si osserva il queste fasi per quanto riguarda archivio Lake dati di Azure per comprendere le opzioni e gli strumenti disponibili per soddisfare le esigenze di dati.

## <a name="ingest-data-into-data-lake-store"></a>Acquisire dati all'archivio dati Lake

In questa sezione evidenzia le diverse origini dei dati e i diversi modi in cui è possano caricamento i dati in un account di archivio di dati Lake.

![Dati di caricamento in archivio Lake dati] (./media/data-lake-store-data-scenarios/ingest-data.png "Dati di caricamento in archivio Lake dati")

### <a name="ad-hoc-data"></a>Dati ad hoc

Questo rappresenta più piccoli set di dati utilizzato per la creazione di prototipi un'applicazione di dati. Esistono diversi modi per il caricamento dati ad hoc a seconda dell'origine dati.

| Origine dati        | Acquisizione utilizzando                                                                        |
|--------------------|----------------------------------------------------------------------------------------|
| Computer locale     | <ul> <li>[Portale di Azure](/data-lake-store-get-started-portal.md)</li> <li>[PowerShell Azure](data-lake-store-get-started-powershell.md)</li> <li>[Azure multipiattaforma CLI](data-lake-store-get-started-cli.md)</li> <li>[Utilizzo di dati Lake Tools per Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Archivio Blob Azure dello spazio di archiviazione | <ul> <li>[Dati di Azure Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp in esecuzione su cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### <a name="streamed-data"></a>Flussi di dati

Questo rappresenta i dati che possono essere generati da diverse origini, ad esempio applicazioni, dispositivi, sensori e così via. Questi dati possano caricamento in un punto vendita Lake dati con strumenti diversi. Questi strumenti vengono in genere acquisire ed elaborare i dati in modo da evento in tempo reale e quindi scrivere gli eventi in batch in archivio Lake dati in modo che possono essere elaborati ulteriormente. 

Di seguito sono strumenti che è possibile usare:
 
* [Flusso azure Analitica] (.. / flusso analitica-dati-lake-uscita) - eventi caricamento in evento hub è possono scrivere Azure dati Lake utilizzando un output archivio Lake dati di Azure.
* [Azure HDInsight eccesso](../hdinsight/hdinsight-storm-write-data-lake-store.md) - scrivere i dati direttamente all'archivio dati Lake dal cluster eccesso.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – è possibile ricevere gli eventi da hub di evento e quindi scrivere essa Lake archivio dati utilizzando [Dati Lake archivio .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dati relazionali

È anche possibile origine dati dai database relazionali. Per un periodo di tempo, database relazionali di raccolgono grandi quantità di dati che possono fornire informazioni di base sulla se elaborato pipeline di dati. È possibile utilizzare gli strumenti seguenti per spostare tali dati in archivio Lake dati.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Dati di Azure Factory](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dati dei log server Web (caricamento tramite applicazioni personalizzate)

Questo tipo di set di dati è indicato in particolare perché l'analisi dei dati del Registro di server web è un caso di utilizzo comune per le applicazioni di dati e richiede volumi elevati di file di log da caricare all'archivio Lake dati. È possibile usare uno dei seguenti strumenti per scrivere script o applicazioni di caricare tali dati.

* [Azure multipiattaforma CLI](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Archivio Lake dati di Azure .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Dati di Azure Factory](../data-factory/data-factory-data-movement-activities.md)

Per caricare dati dei log server web, nonché per il caricamento di altri tipi di dati (ad esempio i dati di social networking rispettivi), è un buon approccio per scrivere applicazioni personalizzate script personalizzate, poiché offre la possibilità di includere i dati durante il caricamento componente come parte dell'applicazione di dati più grande. In alcuni casi questo codice potrebbe richiedere la forma di script o utilità semplice riga di comando. In altri casi, il codice può essere utilizzato per integrare grande elaborazione dei dati in un applicazione aziendale o una soluzione.


### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dati associati a cluster di Azure HDInsight

La maggior parte dei tipi di cluster HDInsight (Hadoop, HBase, eccesso) supportano archivio Lake dati come un archivio dati. HDInsight cluster accedere ai dati di Azure lo spazio di archiviazione BLOB (WASB). Per ottenere prestazioni migliori, è possibile copiare i dati da WASB in un account di archivio Lake dati associato al cluster. È possibile utilizzare gli strumenti seguenti per copiare i dati.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Servizio AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Dati di Azure Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>I dati archiviati in locale o IaaS Hadoop cluster

Grandi quantità di dati possono essere archiviate in cluster Hadoop esistenti, in locale nei computer che eseguono HDFS. I cluster Hadoop potrebbero essere in una distribuzione in locale o potrebbero essere all'interno di un cluster di IaaS in Azure. Potrebbero essersi requisiti consente di copiare tali dati Azure dati Lake archivio per un approccio tantum o in modo ricorrente. Sono disponibili varie opzioni che è possibile utilizzare per eseguire questa operazione. Di seguito è riportato un elenco di alternative e compromessi associati.

| Approccio  | Dettagli | Vantaggi   | Considerazioni  |
|-----------|---------|--------------|-----------------|
| Consente di copiare i dati direttamente da cluster Hadoop all'archivio Lake dati di Azure Azure dati Factory (alimentatore automatico) | [Alimentatore automatico supporta HDFS come origine dati](../data-factory/data-factory-hdfs-connector.md) | Alimentatore automatico fornisce supporto della casella per HDFS e gestione di prima classe-to-end e il monitoraggio | Richiede Gateway di gestione di dati per la distribuzione in locale o in cluster IaaS |
| Esportare dati da Hadoop come file. Copiare i file di archivio di Lake dati di Azure tramite meccanismo appropriato.                                   | È possibile copiare i file all'uso di Azure dati Lake Store: <ul><li>[Azure PowerShell per Windows OS](data-lake-store-get-started-powershell.md)</li><li>[Azure multipiattaforma CLI per OS non Windows](data-lake-store-get-started-cli.md)</li><li>App personalizzate tramite qualsiasi SDK di archivio dati Lake</li></ul> | Veloce iniziare. Possibili caricamento personalizzato                                                   | Processo a più passaggi che include più tecnologie. Gestione e il monitoraggio crescerà fino al essere un'operazione impegnativa nel tempo natura personalizzate degli strumenti |
| Utilizzare Distcp per copiare i dati da Hadoop allo spazio di archiviazione Azure. Copiare quindi dati dallo spazio di archiviazione di Azure archivio Lake meccanismo appropriato. | È possibile copiare dati dallo spazio di archiviazione di Azure all'uso di archivio Lake dati: <ul><li>[Dati di Azure Factory](../data-factory/data-factory-data-movement-activities.md)</li><li>[Strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache in esecuzione su cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| È possibile utilizzare gli strumenti Apri origine. | Processo a più passaggi che include più tecnologie |

### <a name="really-large-datasets"></a>Molto grandi set di dati

Per il caricamento di set di dati in un intervallo terabyte, utilizzando i metodi descritti sopra volte essere lenta e costi. In tal caso, è possibile utilizzare le opzioni seguenti.

* **Utilizzo di Azure ExpressRoute**. Azure ExpressRoute consente di creare connessioni private tra Azure Data Center e infrastruttura in locale. Fornisce un'opzione affidabile per il trasferimento di grandi quantità di dati. Per ulteriori informazioni, vedere [la documentazione di Azure ExpressRoute](../expressroute/expressroute-introduction.md).


* **"Offline" Carica dei dati**. Se con Azure ExpressRoute non è possibile per qualsiasi motivo, è possibile utilizzare [il servizio di Azure Importa/Esporta](../storage/storage-import-export-service.md) per spedire unità disco rigido con i dati a un centro di dati di Azure. I dati viene caricati prima di tutto lo spazio di archiviazione BLOB. È quindi possibile utilizzare [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) o [strumento AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) per copiare i dati da Azure lo spazio di archiviazione BLOB all'archivio Lake dati.

    >[AZURE.NOTE] Durante l'utilizzo del servizio di importazione/esportazione, le dimensioni dei file su dischi spediti al centro dati Azure non deve essere maggiore di 200 GB.


## <a name="process-data-stored-in-data-lake-store"></a>Elabora i dati archiviati nell'archivio dati Lake

Una volta i dati sono disponibili nell'archivio Lake dati è possibile eseguire analisi su tali dati con le applicazioni di dati supportati. Attualmente, è possibile utilizzare per l'esecuzione di processi di analisi dei dati sui dati archiviati nell'archivio dati Lake Azure HDInsight e Azure dati Lake Analitica. 

![Analizza dati nell'archivio dati Lake] (./media/data-lake-store-data-scenarios/analyze-data.png "Analizza dati nell'archivio dati Lake")

È possibile esaminare gli esempi seguenti.

* [Creare un cluster di HDInsight con dati Lake Store come spazio di archiviazione](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## <a name="download-data-from-data-lake-store"></a>Scaricare dati da archivio Lake dati.

È anche possibile scaricare o spostare i dati da Azure dati Lake archivio per gli scenari ad esempio:

* Spostare i dati ad altri repository per interagire con la pipeline di elaborazione dei dati esistente. È ad esempio spostare i dati dall'archivio Lake dati al Database SQL Azure o SQL Server locale.
* Scaricare i dati nel computer locale per l'elaborazione in ambienti IDE durante la creazione di prototipi di applicazioni.

![Dati di uscita dall'archivio dati Lake] (./media/data-lake-store-data-scenarios/egress-data.png "Dati di uscita dall'archivio dati Lake")

In tal caso, è possibile utilizzare una delle opzioni seguenti:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Dati di Azure Factory](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Utilizzare i metodi seguenti per scrivere script/applicazione per scaricare i dati dall'archivio Lake dati.

* [Azure multipiattaforma CLI](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Archivio Lake dati di Azure .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualizzare i dati nell'archivio dati Lake

È possibile utilizzare una combinazione dei servizi per creare una rappresentazione visiva di dati archiviati in archivio Lake dati.

![Visualizza dati nell'archivio dati Lake] (./media/data-lake-store-data-scenarios/visualize-data.png "Visualizza dati nell'archivio dati Lake")

* È possibile iniziare con [Azure Data Factory per spostare i dati dall'archivio Lake dati nell'archivio di dati di SQL Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* In seguito, è possibile [integrare Power BI con Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) per creare una rappresentazione visiva dei dati.
