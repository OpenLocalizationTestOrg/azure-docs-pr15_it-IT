<properties
    pageTitle="Tutti gli argomenti di servizio dati Factory | Microsoft Azure"
    description="Tabella di tutti gli argomenti per il servizio di Azure denominata Factory dati presenti in http://azure.microsoft.com/documentation/articles/, titolo e descrizione."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Tutti gli argomenti per servizio Factory di dati di Azure

In questo argomento sono elencati tutti gli argomenti che si applicano direttamente al servizio **Factory dati** di Azure. È possibile cercare in questa pagina Web per le parole chiave mediante **Ctrl + F**per trovare gli argomenti di interesse corrente.




## <a name="new"></a>Nuovo

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 1 | [Spostare i dati da Amazon Redshift utilizzo dei dati di Azure](data-factory-amazon-redshift-connector.md) | Informazioni su come spostare i dati da Amazon Redshift utilizzo dei dati di Azure. |
| 2 | [Spostare i dati da Amazon semplice servizio di archiviazione di utilizzo dei dati di Azure](data-factory-amazon-simple-storage-service-connector.md) | Informazioni su come spostare i dati dal servizio di archiviazione semplice Amazon (S3) con Azure Data Factory. |
| 3 | [Dati di Azure Factory - Copia guidata](data-factory-azure-copy-wizard.md) | Informazioni su come utilizzare dati Factory Azure Copia guidata per copiare i dati da origini dati supportate in sink. |
| 4 | [Esercitazione: Compilare il prima factory Azure dati utilizzando API REST Factory dati](data-factory-build-your-first-pipeline-using-rest-api.md) | In questa esercitazione, si crea una pipeline di Azure Data Factory esempio utilizzando API REST Factory dati. |
| 5 | [Esercitazione: Creare una pipeline con attività di copia tramite l'API di .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) | In questa esercitazione, creare una pipeline di Azure Data Factory con un'attività di copia tramite l'API di .NET. |
| 6 | [Esercitazione: Creare una pipeline con attività di copia mediante API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | In questa esercitazione, creare una pipeline di Azure Data Factory con un'attività di copia tramite API REST. |
| 7 | [Dati Factory Copia guidata](data-factory-copy-wizard.md) | Informazioni su come utilizzare dati Factory Copia guidata per copiare i dati da origini dati supportate in sink. |
| 8 | [Gateway di gestione dati](data-factory-data-management-gateway.md) | Configurare un gateway di dati per spostare i dati tra locale e nel cloud. Usare Gateway di gestione dati di Azure Data Factory per spostare i dati. |
| 9 | [Spostare i dati da un database di Cassandra locali con Azure Data Factory](data-factory-onprem-cassandra-connector.md) | Informazioni su come spostare i dati da un database di Cassandra locali con Azure Data Factory. |
| 10 | [Spostare i dati da MongoDB utilizzo dei dati di Azure](data-factory-on-premises-mongodb-connector.md) | Informazioni su come spostare i dati da un database MongoDB usando Azure Data Factory. |
| 11 | [Spostare i dati da Salesforce tramite Factory di dati di Azure](data-factory-salesforce-connector.md) | Informazioni su come spostare i dati da Salesforce utilizzando Azure Data Factory. |


## <a name="updated-articles-data-factory"></a>Articoli aggiornati, Data Factory

In questa sezione sono elencati gli articoli che sono stati aggiornati di recente, nel punto in cui l'aggiornamento è stato grande o significativa. Per ogni articolo aggiornato, viene visualizzato un frammento approssimativo del testo delle vendite promozionali aggiunte. Gli articoli sono stati aggiornati nell'intervallo di date del **2016-08-22** - **2016-10-05**.

| &nbsp; | Articolo | Testo aggiornato, frammento di codice | Aggiornamento |
| --: | :-- | :-- | :-- |
| 12 | [Azure Data Factory - log delle modifiche API .NET](data-factory-api-change-log.md) | In questo articolo fornisce informazioni sulle modifiche apportate a Azure dati Factory SDK in una versione specifica. È possibile trovare il pacchetto NuGet più recente per Azure Data Factory qui (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **versione 4.11.0** caratteristica aggiunte: / sono stati aggiunti i tipi di servizi collegati seguenti: AmazonRedshiftLinkedService - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / sono stati aggiunti i tipi di set di dati seguenti: AmazonS3Dataset - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - (https://msdn.microsoft.com/library/mt765112.aspx) / sono stati aggiunti i seguenti tipi di origine di copia :-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) **versione 4.10.0** / le seguenti proprietà facoltative sono stati aggiunti in formato testo:-sci | 2016-09-22 |
| 13 | [Spostare i dati da e verso Blob Azure tramite Factory di dati di Azure](data-factory-azure-blob-connector.md) |  / copyBehavior / definisce il comportamento di copia quando l'origine è BlobSource o file System.  / **PreserveHierarchy:** mantiene la gerarchia di file nella cartella di destinazione. Il percorso del file di origine alla cartella di origine è uguale al percorso del file di destinazione alla cartella di destinazione. br /.. br /. **FlattenHierarchy:** tutti i file dalla cartella di origine sono presenti nel primo livello della cartella di destinazione. I file di destinazione hanno nome generato automaticamente. .br /.. br /. **MergeFiles: (impostazione predefinita)** unisce tutti i file dalla cartella di origine di un file. Se il nome di File/Blob viene specificato, il nome del file unite è il nome specificato. in caso contrario, è il nome di file generato automaticamente.  / Non / **BlobSource** supporta anche queste due proprietà per la compatibilità. / **treatEmptyAsNull**: Specifica se considerare una stringa vuota o null come valore null. / **skipHeaderLineCount** - specifica il numero di righe necessario ignorato. È applicabile solo al set di dati input Usa formato testo. Analogamente, **BlobSink** supporta esimo | 2016-09-28 |
| 14 | [Creare pipeline stima mediante le attività di apprendimento Azure](data-factory-azure-ml-batch-execution-activity.md) | **Servizio Web richiede più input** Se il servizio web richiede più input, utilizzare la proprietà **webServiceInputs** invece di usare **WebServiceInputActivity**. Set di dati che fanno riferimento **webServiceInputs** devono essere inclusi anche le attività **input**. Nel test di Azure ML input servizio web e le porte di output e parametri globali hanno nomi predefiniti ("input1", "Ingresso2") che è possibile personalizzare. I nomi utilizzati per webServiceInputs, webServiceOutputs e impostazioni globalParameters devono corrispondere esattamente i nomi agli esperimenti. È possibile visualizzare il payload di richiesta di esempio nella pagina Guida esecuzione Batch per l'endpoint di Azure ML verificare il mapping previsto.    {"nome": "PredictivePipeline", "proprietà": {"descrizione": "modello AzureML", "attività": {"nome": "MLActivity", "tipo": "AzureMLBatchExecution", "descrizione": "stima analisi batch input", "input": {"nome": "inputDataset1"}, {"nome": "inputDatase | 2016-09-13 |
| 15 | [Copiare le prestazioni di attività e Guida all'ottimizzazione](data-factory-copy-activity-performance.md) | 1. **stabilire una previsione**. Durante la fase di sviluppo, verificare la pipeline utilizzando Copia attività rispetto a un campione di dati representative. È possibile utilizzare Data Factory sezionare modello (dati-factory-pianificazione-e-execution.md time-series-datasets-and-data-slices) per limitare la quantità di dati di che cui si collabora.  Raccogliere il tempo di esecuzione e prestazioni utilizzando il **monitoraggio e gestione delle App**. Scegliere **Monitor e Gestisci** nella home page Factory dati. Nella visualizzazione struttura, scegliere il **set di dati di output**. Nell'elenco **Attività di Windows** , scegliere l'attività Copia eseguire. Elenchi di **Windows di attività** della durata di attività di copia e le dimensioni dei dati che vengono copiate. La velocità viene elencata in **Esplora risorse attività**. Per ulteriori informazioni sull'app, vedere monitorare e gestire pipeline di Azure Data Factory utilizzando il monitoraggio e gestione App (dati-factory-monitor-gestire-app.md).  ! Dettagli (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn esecuzione di attività | 2016-09-27 |
| 16 | [Esercitazione: Creare una pipeline con attività di copia utilizzando Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Nota i seguenti punti:-set di dati di **tipo** è impostato su **AzureBlob**.     - **linkedServiceName** è impostato su **AzureStorageLinkedService**. Questo servizio collegato creato nel passaggio 2.     - **folderPath** è impostato su contenitore **adftutorial** . È anche possibile specificare il nome di un blob all'interno della cartella utilizzando la proprietà **nome file** . Dal momento che non si specifica il nome del blob, dati da tutti i BLOB nel contenitore viene considerati come un input di dati.    - **tipo** di formato è impostato su **formato testo** - sono presenti due campi di testo file ΓÇô **nome** e **Cognome** ΓÇô separati da una virgola (**columnDelimiter**), la **disponibilità** è impostato su **ogni ora** (**frequenza** è impostata su **ora** e **intervallo** è impostato su **1**). Di conseguenza, Factory dati consente di cercare i dati di input ogni ora nella cartella radice del contenitore di blob (**adftutorial**) specificata.  Se non si specifica un **nome di file** per un set di dati di **input** , tutti i file/BLOB dalla cartella input (**folderPath**) sono consid | 2016-09-29 |
| 17 | [Creare, monitorare e gestire factory dati Azure mediante dati Factory .NET SDK](data-factory-create-data-factories-programmatically.md) | Nota verso il basso l'ID dell'applicazione e la password (segreto del client) e utilizzare nella procedura dettagliata. **Abbonamento ottenere Azure e tenant ID** Se non si dispone di versione più recente di PowerShell installato nel computer in uso, seguire le istruzioni su come installare e configurare Azure PowerShell (... / configure.md di installazione powershell) articolo per installarlo. 1. Avviare Azure PowerShell ed eseguire il comando seguente 2. Eseguire il comando seguente e immettere il nome utente e password utilizzati per accedere al portale di Azure.         Accesso AzureRmAccount se che si dispone di un solo abbonamento Azure associato a questo account non è necessario eseguire due passaggi successivi. 3. Eseguire il comando seguente per visualizzare tutte le sottoscrizioni per l'account.       Get-AzureRmSubscription 4. Eseguire il seguente comando per selezionare l'abbonamento a cui si desidera utilizzare. Sostituire **NameOfAzureSubscription** con il nome del proprio abbonamento Azure.       Get-AzureRmSubscription - SubscriptionName NameOfAzureSubscription / AzureRmCo Set | 2016-09-14 |
| 18 | [Tubazioni e attività nei dati di Azure Factory](data-factory-create-pipelines.md) |       "avvio": "2016-07-12T00:00:00Z", "fine": "2016-07-13T00:00:00Z"}} noti quanto segue: / nella sezione attività esiste una sola attività il cui **tipo** sia impostata su **Copia**. / Input per l'attività è impostata su **InputDataset** e output per l'attività è impostato su **OutputDataset**. / Nella sezione **typeProperties** **BlobSource** viene specificato come tipo di origine e **SqlSink** viene specificato come tipo di sink. Per una procedura dettagliata completamento della creazione di questa pipeline, vedere Esercitazione: copiare i dati dallo spazio di archiviazione Blob al Database SQL (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Pipeline di trasformazione di esempio** Nella pipeline di esempio seguente, esiste un'attività di tipo **HDInsightHive** nella sezione **attività** . In questo esempio, l'attività HDInsight Hive (dati factory-hive activity.md) Trasforma i dati da un archivio Blob Azure eseguendo un file di script Hive in un cluster di Azure HDInsight Hadoop.  {"nome": "TransformPipeline", "p | 2016-09-27 |
| 19 | [Trasformare i dati di Azure Data Factory](data-factory-data-transformation-activities.md) | Dati Factory supporta le seguenti attività di trasformazione dati che può essere aggiunto al pipeline (dati-factory-creare-pipelines.md) singolarmente oppure concatenare con un'altra attività. .  AZURE. Nota per una procedura dettagliata con istruzioni dettagliate, vedere creare una pipeline con Hive articolo di trasformazione (data-factory-build-your-first-pipeline.md). **Attività HDInsight Hive** Attività HDInsight Hive nella pipeline di dati Factory esegue query Hive privatamente o cluster basato su Windows/Linux HDInsight su richiesta. Vedere l'articolo (dati factory-hive activity.md) Hive attività per informazioni dettagliate sull'attività corrente. **Maialino HDInsight attività** Attività HDInsight maialino nella pipeline di dati Factory esegue query maialino autonomamente o cluster basato su Windows/Linux HDInsight su richiesta. Vedere l'articolo (dati factory-maialino activity.md) maialino attività per informazioni dettagliate sull'attività corrente. **HDInsight MapReduce attività** Attività HDInsight MapReduce nella pipeline di dati Factory esegue programmi MapReduce su y | 2016-09-26 |
| 20 | [Dati Factory pianificazione e l'esecuzione](data-factory-scheduling-and-execution.md) | CopyActivity2 verrà eseguita solo se la CopyActivity1 è stato eseguito correttamente e Dataset2 è disponibile. Ecco la pipeline di esempio JSON: {"nome": "ChainActivities", "proprietà": {"descrizione": "Esegui attività in sequenza", "attività": {"tipo": "Copiare", "typeProperties": {"origine": {"tipo": "BlobSource"}, "sink": {"tipo": "BlobSink", "copyBehavior": "PreserveHierarchy", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "input": {"nome": "Dataset1"}, "Visualizza": {"nome": "Dataset2"}, "criteri": {"timeout": "01: 00:00"}, "utilità di pianificazione": {"frequenza": "Ora", "intervallo": 1}, "nome": "CopyFromBlob1ToBlob2", "descrizione": "Copiare i dati da un blob a un'altra"}, {"tipo": "Copiare", "typeProperties": {"origine": {"tipo": "BlobSource"}, "sink" : {"tipo": "BlobSink", "writeBatchSize": 0, "writeBatchTimeout": "00: 00:00"}}, "in | 2016-09-28 |





## <a name="tutorials"></a>Esercitazioni

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 21 | [Esercitazione: Creare il prima pipeline per elaborare i dati utilizzando Hadoop cluster](data-factory-build-your-first-pipeline.md) | In questa esercitazione Azure Data Factory viene illustrato come creare e pianificare una factory di dati che consente di elaborare dati utilizzando Hive script in un cluster di Hadoop. |
| 22 | [Esercitazione: Compilare il prima factory Azure dati utilizzando il modello di gestione risorse di Azure](data-factory-build-your-first-pipeline-using-arm.md) | In questa esercitazione, verrà creata una pipeline di Azure Data Factory esempio utilizzando un modello di gestione risorse di Azure. |
| 23 | [Esercitazione: Creare la prima factory Azure dati tramite il portale di Azure](data-factory-build-your-first-pipeline-using-editor.md) | In questa esercitazione, verrà creata una pipeline di Azure Data Factory di esempio con dati Factory Editor nel portale di Azure. |
| 24 | [Esercitazione: Compilare il prima factory dati Azure tramite PowerShell Azure](data-factory-build-your-first-pipeline-using-powershell.md) | In questa esercitazione, verrà creata una pipeline di Azure Data Factory esempio tramite PowerShell Azure. |
| 25 | [Esercitazione: Factory di dati creare il primo Azure con Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | In questa esercitazione, verrà creata una pipeline di Azure Data Factory esempio utilizzando Visual Studio. |
| 26 | [Esercitazione: Creare una pipeline con attività di copia tramite il portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | In questa esercitazione, creare una pipeline di Azure Data Factory con un'attività di copia tramite l'Editor di Factory dati nel portale di Azure. |
| 27 | [Esercitazione: Creare una pipeline con attività di copia tramite PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md) | In questa esercitazione, creare una pipeline di Azure Data Factory con un'attività di copia tramite PowerShell Azure. |
| 28 | [Esercitazione: Creare una pipeline con attività di copia utilizzando Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | In questa esercitazione, creare una pipeline di Azure Data Factory con un'attività di copia tramite Visual Studio. |
| 29 | [Copiare i dati dallo spazio di archiviazione Blob al Database SQL di utilizzo di dati](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | In questa esercitazione viene illustrato come utilizzare Copia attività in una pipeline di Azure Data Factory per copiare i dati dallo spazio di archiviazione Blob al database SQL. |
| 30 | [Esercitazione: Creare una pipeline con attività di copia con dati Factory Copia guidata](data-factory-copy-data-wizard-tutorial.md) | In questa esercitazione è creare una pipeline di Azure Data Factory con un'attività di copia tramite Copia guidata supportato dal produttore dati |



## <a name="data-movement"></a>Spostamento dei dati

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 31 | [Spostare i dati da e verso Blob Azure tramite Factory di dati di Azure](data-factory-azure-blob-connector.md) | Informazioni su come copiare dati blob Azure Data Factory. Utilizzare un campione: copia dei dati nel e dall'archivio Blob Azure e Database di SQL Azure. |
| 32 | [Spostare i dati nel e dall'archivio Lake Azure utilizzando Factory di dati di Azure](data-factory-azure-datalake-connector.md) | Informazioni su come spostare i dati da/verso archivio Lake Azure utilizzando Factory di dati di Azure |
| 33 | [Spostare i dati da e verso DocumentDB utilizzo dei dati di Azure](data-factory-azure-documentdb-connector.md) | Informazioni su come spostare i dati a / dall'insieme di Azure DocumentDB utilizzo dei dati di Azure |
| 34 | [Spostare i dati da e verso il Database di SQL Azure con Azure Data Factory](data-factory-azure-sql-connector.md) | Informazioni su come spostare i dati nel o dal Database di SQL Azure con Azure Data Factory. |
| 35 | [Spostare i dati da e verso Warehouse dati di SQL Azure tramite Factory di dati di Azure](data-factory-azure-sql-data-warehouse-connector.md) | Informazioni su come spostare i dati da/verso Warehouse dati di SQL Azure tramite Factory di dati di Azure |
| 36 | [Spostare i dati da e verso tabella Azure usando Factory di dati di Azure](data-factory-azure-table-connector.md) | Informazioni su come spostare i dati da/verso archivio tabelle Azure con Azure Data Factory. |
| 37 | [Copiare le prestazioni di attività e Guida all'ottimizzazione](data-factory-copy-activity-performance.md) | Informazioni sulle principali fattori che influenzano le prestazioni di spostamento dei dati di Azure Data Factory quando si utilizza Copia attività. |
| 38 | [Spostare i dati usando Copia attività](data-factory-data-movement-activities.md) | Informazioni sulle spostamento dei dati in dati Factory pipeline: la migrazione dei dati tra gli archivi cloud e tra un archivio locale e un punto vendita cloud. Utilizzare attività di copia. |
| 39 | [Note sulla versione di Gateway di gestione dati](data-factory-gateway-release-notes.md) | Note sulla versione brano di Gateway di gestione dati |
| 40 | [Spostare i dati da HDFS locali con Azure Data Factory](data-factory-hdfs-connector.md) | Informazioni su come spostare i dati da HDFS locali con Azure Data Factory. |
| 41 | [Monitorare e gestire pipeline di Azure Data Factory utilizzando nuovo monitoraggio e gestione App](data-factory-monitor-manage-app.md) | Informazioni su come utilizzare Monitoraggio e gestione delle App per monitorare e gestire tubazioni e dati di Azure factory. |
| 42 | [Spostare i dati tra origini locali e cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) | Configurare un gateway di dati per spostare i dati tra locale e nel cloud. Usare Gateway di gestione dati di Azure Data Factory per spostare i dati. |
| 43 | [Spostare i dati di origine da OData tramite Factory di dati di Azure](data-factory-odata-connector.md) | Informazioni su come spostare i dati da origini OData tramite Azure Data Factory. |
| 44 | [Spostare dati dall'ODBC dati archivi utilizzo dei dati di Azure](data-factory-odbc-connector.md) | Informazioni su come spostare i dati di utilizzo dei dati di Azure archivi di dati ODBC. |
| 45 | [Spostare i dati da DB2 utilizzo dei dati di Azure](data-factory-onprem-db2-connector.md) | Informazioni su come spostare i dati da un Database DB2 usando Factory di dati di Azure |
| 46 | [Spostare i dati da e verso un file system locali con Azure Data Factory](data-factory-onprem-file-system-connector.md) | Informazioni su come spostare i dati da e verso un file system locale utilizzando Azure Data Factory. |
| 47 | [Spostare i dati da MySQL utilizzo dei dati di Azure](data-factory-onprem-mysql-connector.md) | Informazioni su come spostare i dati da un database MySQL usando Azure Data Factory. |
| 48 | [Spostare i dati da/verso Oracle locali con Azure Data Factory](data-factory-onprem-oracle-connector.md) | Informazioni su come spostare i dati nel o dal database Oracle locali con Azure Data Factory. |
| 49 | [Spostare i dati da PostgreSQL utilizzo dei dati di Azure](data-factory-onprem-postgresql-connector.md) | Informazioni su come spostare i dati da un PostgreSQL Database usando Azure Data Factory. |
| 50 | [Spostare i dati da Sybase utilizzo dei dati di Azure](data-factory-onprem-sybase-connector.md) | Informazioni su come spostare i dati da un Sybase Database usando Azure Data Factory. |
| 51 | [Spostare i dati da Teradata utilizzo dei dati di Azure](data-factory-onprem-teradata-connector.md) | Informazioni su Teradata Connector per il servizio Factory dati che consente di spostare dati da Teradata Database |
| 52 | [Spostare i dati da e SQL Server locale o su IaaS (Azure macchine Virtuali) utilizzo dei dati di Azure](data-factory-sqlserver-connector.md) | Informazioni su come spostare i dati o dal database di SQL Server locale o in una macchina virtuale Azure con Azure Data Factory. |
| 53 | [Spostare i dati da un'origine di tabella Web usando Factory di dati di Azure](data-factory-web-table-connector.md) | Informazioni su come spostare i dati locali una tabella in una pagina Web utilizzando Azure Data Factory. |



## <a name="data-transformation"></a>Trasformazione dei dati

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 54 | [Creare pipeline stima mediante le attività di apprendimento Azure](data-factory-azure-ml-batch-execution-activity.md) | Viene descritto come creare creare pipeline previsione utilizzando Azure Data Factory e apprendimento Azure |
| 55 | [Calcolare servizi collegati](data-factory-compute-linked-services.md) | Informazioni sulle enviornments calcolo che è possibile utilizzare in Azure Data Factory pipeline di trasformazione/elaborare dati. |
| 56 | [Processo su larga scala set di dati con dati Factory e Batch](data-factory-data-processing-using-batch.md) | In questo articolo viene descritto come elaborare grandi quantità di dati in una pipeline di Azure Data Factory utilizzando la funzionalità di elaborazione in parallelo del Batch di Azure. |
| 57 | [Trasformare i dati di Azure Data Factory](data-factory-data-transformation-activities.md) | Informazioni su come trasformare i dati o grafici in Azure Data Factory usando Hadoop, apprendimento o Azure dati Lake Analitica. |
| 58 | [Attività di flusso Hadoop](data-factory-hadoop-streaming-activity.md) | Informazioni su come è possibile utilizzare l'attività di flusso Hadoop di una factory di dati di Azure per eseguire programmi Hadoop Streaming in un cluster HDInsight personalizzato nella richiesta/il. |
| 59 | [Attività di hive](data-factory-hive-activity.md) | Informazioni su come utilizzare in una factory di dati di Azure attività Hive per eseguire query Hive in un cluster HDInsight personalizzato nella richiesta/il. |
| 60 | [Richiamare programmi MapReduce dal produttore di dati](data-factory-map-reduce.md) | Informazioni su come elaborare i dati eseguendo MapReduce programmi in un cluster di Azure HDInsight da una factory di dati di Azure. |
| 61 | [Maialino attività](data-factory-pig-activity.md) | Informazioni su come utilizzare in una factory di dati di Azure attività maialino per eseguire gli script maialino in un cluster HDInsight personalizzato nella richiesta/il. |
| 62 | [Richiamare programmi motori da Factory dati](data-factory-spark.md) | Informazioni su come richiamare programmi motori da una factory di Azure dati utilizzando l'attività MapReduce. |
| 63 | [Stored Procedure attività di SQL Server](data-factory-stored-proc-activity.md) | Informazioni su come utilizzare le attività di SQL Server archiviati procedura per richiamare una stored procedure in un Database SQL Azure o Azure SQL Data Warehouse da una pipeline di dati Factory. |
| 64 | [Usare le attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md) | Informazioni su come creare attività personalizzate e utilizzarli in una pipeline di Azure Data Factory. |
| 65 | [Esegui script U SQL Azure dati Lake Analitica da Azure Data Factory](data-factory-usql-activity.md) | Informazioni su come elaborare i dati eseguendo script U SQL Azure dati Lake Analitica del servizio di calcolo. |



## <a name="samples"></a>Esempi

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 66 | [Dati di Azure Factory - esempi](data-factory-samples.md) | Vengono fornite informazioni dettagliate sugli esempi forniti con il servizio di Azure Data Factory. |



## <a name="use-cases"></a>Use case

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 67 | [Case study](data-factory-customer-case-studies.md) | Informazioni sulle modalità alcuni dei clienti usano Azure Data Factory. |
| 68 | [Use Case - profilo clienti](data-factory-customer-profiling-usecase.md) | Informazioni su come Factory di dati di Azure viene utilizzato per creare un basati sui dati flusso di lavoro (pipeline) per i clienti di gioco del profilo. |
| 69 | [Use Case - consigli sui prodotti](data-factory-product-reco-usecase.md) | Informazioni su un caso di utilizzo implementato tramite Azure Data Factory insieme altri servizi. |



## <a name="monitor-and-manage"></a>Monitorare e gestire

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 70 | [Monitorare e gestire pipeline di Azure Data Factory](data-factory-monitor-manage-pipelines.md) | Informazioni su come usare portale Azure e Azure PowerShell per monitorare e gestire dati di Azure factory e pipeline che è stato creato. |



## <a name="sdk"></a>SDK

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 71 | [Azure Data Factory - log delle modifiche API .NET](data-factory-api-change-log.md) | Descrive le ultime modifiche, funzionalità e correzioni di bug e così via... in una versione specifica dell'API di .NET per Azure Data Factory. |
| 72 | [Creare, monitorare e gestire factory dati Azure mediante dati Factory .NET SDK](data-factory-create-data-factories-programmatically.md) | Informazioni su come creare, monitorare e gestire factory dati Azure tramite SDK Factory dati a livello di programmazione. |
| 73 | [Dati di Azure Factory di riferimento per sviluppatori](data-factory-sdks.md) | Informazioni sui diversi modi per creare, monitorare e gestire factory dati Azure |



## <a name="miscellaneous"></a>Varie

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 74 | [Dati di Azure Factory - domande frequenti](data-factory-faq.md) | Domande frequenti su Factory di dati di Azure. |
| 75 | [Dati di Azure Factory - funzioni e variabili di sistema](data-factory-functions-variables.md) | Fornisce un elenco di funzioni di Azure dati fabbrica e le variabili di sistema |
| 76 | [Dati di Azure Factory - regole di denominazione](data-factory-naming-rules.md) | Descrive le regole di denominazione per le entità Factory dati. |
| 77 | [Risoluzione dei problemi di Factory dati](data-factory-troubleshoot.md) | Informazioni su come risolvere i problemi relativi all'utilizzo di Azure Data Factory. |

