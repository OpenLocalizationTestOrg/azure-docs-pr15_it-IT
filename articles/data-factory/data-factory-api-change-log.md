<properties 
    pageTitle="Dati Factory - Log delle modifiche dell'API .NET | Microsoft Azure" 
    description="Descrive le ultime modifiche, funzionalità e correzioni di bug e così via... in una versione specifica dell'API di .NET per Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="spelluru"/>

# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - log delle modifiche API .NET 
In questo articolo fornisce informazioni sulle modifiche apportate a Azure dati Factory SDK in una versione specifica. È possibile trovare il pacchetto NuGet più recente per Azure Data Factory [qui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) 

## <a name="version-4110"></a>Versione 4.11.0
Caratteristica aggiunte:

- Sono stati aggiunti i tipi di servizi collegati seguenti:
    - [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
    - [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
    - [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
- Sono stati aggiunti i tipi di set di dati seguenti: 
    - [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
    - [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
- Sono stati aggiunti i tipi di origine copia seguenti:
    - [MongoDbSource](https://msdn.microsoft.com/en-US/library/mt765123.aspx)

## <a name="version-4100"></a>Versione 4.10.0
- Le proprietà facoltative seguenti sono state aggiunte in formato testo:
    - [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
    - [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
    - [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
- Sono stati aggiunti i tipi di servizi collegati seguenti:
    - [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
    - [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
- Sono stati aggiunti i tipi di set di dati seguenti:
    - [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
- Sono stati aggiunti i tipi di origine copia seguenti:
    - [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
- Aggiungere proprietà [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) AzureMLBatchExecutionActivity 
    - Attivare il passaggio web più input di servizio per una prova di apprendimento Azure


## <a name="version-491"></a>Versione 4.9.1

### <a name="bug-fix"></a>Correzione dei bug

- Impostare come deprecato l'autenticazione basata su WebApi per [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Versione 4.9.0

### <a name="feature-additions"></a>Caratteristica aggiunte

- Aggiungere le proprietà [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) CopyActivity. Per informazioni sulla funzionalità, vedere [copia a fasi](data-factory-copy-activity-performance.md#staged-copy) .


### <a name="bug-fix"></a>Correzione dei bug

- Presentare un overload del metodo [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) , che utilizza un'istanza di [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
- Contrassegnare come facoltativi in CopySink [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) .

## <a name="version-480"></a>Versione 4.8.0

### <a name="feature-additions"></a>Caratteristica aggiunte
- Le proprietà facoltative seguenti sono state aggiunte al tipo di attività Copia per abilitare l'ottimizzazione delle prestazioni di copia:
    - [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
    - [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Versione 4.7.0

### <a name="feature-additions"></a>Caratteristica aggiunte
* Aggiunta nuova StorageFormat tipo [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) per copiare i file in formato (ORCO) colonne riga ottimizzato.
* Aggiungere le proprietà [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings SqlDWSink.
    * Consente l'uso di PolyBase per copiare i dati nell'archivio di dati SQL.

## <a name="version-461"></a>Versione 4.6.1

### <a name="bug-fixes"></a>Correzioni di bug
* Consente di correggere richiesta HTTP per voce windows attività.
    * Rimuove il nome del gruppo di risorse e il nome del produttore dati dal carico utile richiesta.

## <a name="version-460"></a>Versione 4.6.0

### <a name="feature-additions"></a>Caratteristica aggiunte

- Le proprietà seguenti sono state aggiunte a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
    - [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
    - [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
    - [Set di dati](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Le proprietà seguenti sono state aggiunte a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
    - [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Aggiunta di nuovi [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) digitare tipo [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) per definire set di dati a cui dati sono in formato JSON. 

## <a name="version-450"></a>Versione 4.5.0

### <a name="feature-additions"></a>Caratteristica aggiunte
* Aggiunta di [operazioni di elenco per finestra attività](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Metodi di aggiunti per recuperare windows attività con i filtri in base ai tipi di entità (vale a dire factory dati, set di dati, tubazioni e attività).
* Sono stati aggiunti i tipi di servizi collegati seguenti: 
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Sono stati aggiunti i tipi di set di dati seguenti: 
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Sono stati aggiunti i tipi di origine copia seguenti:  
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Versione 4.4.0

### <a name="feature-additions"></a>Caratteristica aggiunte

- Il tipo di servizio collegato seguente è stato aggiunto come origini dati e sink per le attività di copia:
    - [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Per esempi e informazioni, vedere [Servizio collegata SA lo spazio di archiviazione di Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) . 

## <a name="version-430"></a>Versione 4.3.0

### <a name="feature-additions"></a>Caratteristica aggiunte

- Seguenti utili di tipi di servizi collegati stati aggiunti come origini dati per le attività di copia:
    - [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Per esempi e informazioni, vedere [spostare dati da HDFS utilizzo di dati](data-factory-hdfs-connector.md) . 
    - [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Per esempi e informazioni, vedere [spostare dati dall'ODBC memorizza con Azure Data Factory](data-factory-odbc-connector.md) . 

## <a name="version-420"></a>Versione 4.2.0

### <a name="feature-additions"></a>Caratteristica aggiunte

- È stato aggiunto il nuovo tipo di attività seguenti: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Per informazioni dettagliate sull'attività, vedere [modelli di aggiornamento di Azure ML utilizzando l'attività delle risorse di aggiornamento](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- È stata aggiunta una nuova proprietà facoltativa [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) per la [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx). 
- Proprietà [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) sono stati aggiunti alla classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) . 
- Consentire la configurazione dei timeout per le chiamate client al servizio dati Factory. 


## <a name="version-410"></a>Versione 4.1.0

### <a name="feature-additions"></a>Caratteristica aggiunte
* Sono stati aggiunti i tipi di servizi collegati seguenti: 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Sono state aggiunte le attività seguenti: 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Sono stati aggiunti i tipi di set di dati seguenti: 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* I tipi di origine e sink seguenti per l'attività di copia sono state aggiunte:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## <a name="version-401"></a>Versione 4.0.1

### <a name="breaking-changes"></a>Ultime modifiche
Le classi seguenti sono state rinominate. I nuovi nomi sono i nomi di classi originali prima 4.0.0 rilasciare. 
 
Assegnare un nome in 4.0.0 | Assegnare un nome in 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## <a name="version-400"></a>Versione 4.0.0

### <a name="breaking-changes"></a>Ultime modifiche



- Le classi/interfacce seguenti sono state rinominate.

| Nome precedente | Nuovo nome |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| tavolo | [Set di dati](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    

- I metodi di **elenco** restituiscono risultati ora. Se la risposta contiene una proprietà **NextLink** non vuoto, l'applicazione client deve continuare l'estrazione nella pagina successiva fino a quando non vengono restituite tutte le pagine.  Ecco un esempio: 

        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
    
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
    
            nextLink = nextResponse.NextLink;
        }
    
- **Elenco** pipeline API restituisce solo il riepilogo della pipeline anziché i dettagli completi. Ad esempio, le attività in un riepilogo pipeline contengano solo il nome e tipo.

### <a name="feature-additions"></a>Caratteristica aggiunte
- La classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) supporta due nuove proprietà, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**per supportare idempotente copia nell'archivio di dati di SQL Azure. Vedere l'articolo [Warehouse di dati di SQL Azure](data-factory-azure-sql-data-warehouse-connector.md) , in particolare, il [meccanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) e [2 meccanismo di](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) sezioni, per informazioni dettagliate su queste proprietà.

- È ora supportato esecuzione stored procedure rispetto a origini di Database SQL Azure e Azure SQL Data Warehouse come parte dell'attività di copia. Le classi [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) hanno le proprietà seguenti: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Vedere gli articoli di [Database SQL Azure](data-factory-azure-sql-connector.md#sqlsource) e [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) su Azure.com per informazioni dettagliate su queste proprietà.  