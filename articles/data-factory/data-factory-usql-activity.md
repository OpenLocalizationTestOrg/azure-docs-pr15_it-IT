<properties 
    pageTitle="Esegui script U SQL Azure dati Lake Analitica da Azure Data Factory" 
    description="Informazioni su come elaborare i dati eseguendo script U SQL Azure dati Lake Analitica del servizio di calcolo." 
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
    ms.date="09/12/2016" 
    ms.author="spelluru"/>

# <a name="run-u-sql-script-on-azure-data-lake-analytics-from-azure-data-factory"></a>Esegui script U SQL Azure dati Lake Analitica da Azure Data Factory
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Maialino](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Streaming Hadoop](data-factory-hadoop-streaming-activity.md)
[Apprendimento](data-factory-azure-ml-batch-execution-activity.md) 
[Stored Procedure](data-factory-stored-proc-activity.md)
[Dati Lake Analitica U-SQL](data-factory-usql-activity.md)
[.NET personalizzato](data-factory-use-custom-activities.md)
 
Una pipeline di una factory di dati di Azure elabora i dati in servizi di archiviazione collegate mediante i servizi di calcolo collegato. La presentazione contiene una sequenza di attività in cui ogni attività esegue un'operazione di elaborazione specifico. In questo articolo vengono illustrati i **Dati Lake Analitica SQL U attività** uno script che esegue **SQL U** in un servizio di calcolo collegato **Azure dati Lake Analitica** . 

> [AZURE.NOTE] 
> Creare un account Azure dati Lake Analitica prima di creare una pipeline con un'attività di dati Lake Analitica U-SQL. Per informazioni su Azure dati Lake Analitica, vedere [Guida introduttiva di Azure dati Lake Analitica](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Verificare di [creare la prima esercitazione di pipeline](data-factory-build-your-first-pipeline.md) per istruzioni dettagliate per creare una factory di dati, servizi collegati, set di dati e una pipeline. Utilizzare frammenti di JSON con dati Factory Editor o Visual Studio o Azure PowerShell per creare entità Factory dati.

## <a name="azure-data-lake-analytics-linked-service"></a>Dati di Azure Lake Analitica collegato servizio
Si crea un servizio di **Azure dati Lake Analitica** collegati per collegare un servizio di elaborazione Analitica Lake dati di Azure una factory di dati di Azure. Attività dati Lake Analitica U-SQL nella pipeline fa riferimento a questo servizio collegato. 

Nell'esempio seguente fornisce JSON definizione per un servizio di Azure dati Lake Analitica collegato. 

    {
        "name": "AzureDataLakeAnalyticsLinkedService",
        "properties": {
            "type": "AzureDataLakeAnalytics",
            "typeProperties": {
                "accountName": "adftestaccount",
                "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
                "authorization": "<authcode>",
                "sessionId": "<session ID>", 
                "subscriptionId": "<subscription id>",
                "resourceGroupName": "<resource group name>"
            }
        }
    }


Nella tabella seguente vengono descritte le proprietà utilizzata nella definizione JSON. 

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
Tipo | La proprietà tipo deve essere impostata su: **AzureDataLakeAnalytics**. | Sì
nome account | Nome Account Azure dati Lake Analitica. | Sì
dataLakeAnalyticsUri | Dati di Azure Lake Analitica URI. |  No 
autorizzazione | Codice di autorizzazione viene recuperato automaticamente dopo facendo clic sul pulsante di **autorizzazione** nell'Editor Factory dati e il completamento di accesso OAuth.  | Sì 
subscriptionId | Id abbonamento Azure | Non (se non viene specificato, viene usato abbonamento della factory dati). 
resourceGroupName | Nome gruppo di risorse Azure |  Non (se non viene specificato, viene usato il gruppo di risorse della factory dati).
ID sessione | id della sessione della sessione di autorizzazione OAuth. Tutti gli id sessione sia univoco e può essere usato solo una volta. La sessione che ID viene generato automaticamente nell'Editor Factory dati. | Sì

Il codice di autorizzazione generate mediante il pulsante **autorizzazione** scade dopo un intervallo di tempo. Per i tempi di scadenza per diversi tipi di account utente, vedere la tabella seguente. Viene visualizzato l'errore seguente messaggio quando l' autenticazione **token scade**: errore operazione delle credenziali: invalid_grant - AADSTS70002: errore di convalida delle credenziali. AADSTS70008: La concessione di accesso fornito è scaduta o revocata. ID traccia: l'ID di correlazione d18629e8-af88-43c5-88e3-d8419eb1fca1: Timestamp fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21:09:31Z

 
| Tipo di utente | Scadenza |
| :-------- | :----------- | 
| Gli account utente non gestiti da Azure Active Directory (@hotmail.com, @live.com, ecc.) | 12 ore |
| Gli account gestiti da Azure Active Directory (AAD) | esecuzione di 14 giorni dopo l'ultima sezione. <br/><br/>90 giorni, se una sezione in base a servizio collegato basato su OAuth viene eseguito almeno una volta ogni 14 giorni. |

Per evitare o risolvere questo errore, riautorizzare con **autorizzazione** pulsante quando la **scadenza del token** e Ridistribuisci servizi collegati. È inoltre possibile generare i valori per le proprietà **ID sessione** e **l'autorizzazione** a livello di programmazione tramite codice nella sezione seguente. 

  
### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Per generare a livello di programmazione ID sessione e l'autorizzazione per i valori 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Fare clic su argomenti [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) per informazioni dettagliate su Factory dati utilizzate nel codice. Aggiungere un riferimento a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll per la classe WindowsFormsWebAuthenticationDialog. 
 
 
## <a name="data-lake-analytics-u-sql-activity"></a>Dati Lake Analitica SQL U attività 

Frammento JSON riportato di seguito definisce una pipeline con un'attività di dati Lake Analitica U-SQL. Definizione dell'attività contiene un riferimento al servizio di Azure dati Lake Analitica collegato creata in precedenza.   
  

    {
        "name": "ComputeEventsByRegionPipeline",
        "properties": {
            "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
            "activities": 
            [
                {
                    "type": "DataLakeAnalyticsU-SQL",
                    "typeProperties": {
                        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                        "scriptLinkedService": "StorageLinkedService",
                        "degreeOfParallelism": 3,
                        "priority": 100,
                        "parameters": {
                            "in": "/datalake/input/SearchLog.tsv",
                            "out": "/datalake/output/Result.tsv"
                        }
                    },
                    "inputs": [
                        {
                            "name": "DataLakeTable"
                        }
                    ],
                    "outputs": 
                    [
                        {
                            "name": "EventsByRegionTable"
                        }
                    ],
                    "policy": {
                        "timeout": "06:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "EventsByRegion",
                    "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
                }
            ],
            "start": "2015-08-08T00:00:00Z",
            "end": "2015-08-08T01:00:00Z",
            "isPaused": false
        }
    }


Nella tabella seguente vengono illustrati i nomi e le descrizioni delle proprietà specifiche per l'attività. 

Proprietà | Descrizione | Obbligatorio
:-------- | :----------- | :--------
tipo | La proprietà tipo deve essere impostata su **DataLakeAnalyticsU SQL**. | Sì
scriptPath | Percorso cartella contenente lo script U-SQL. Nome del file è maiuscole e minuscole. | N (se si utilizza uno script)
scriptLinkedService | Servizio collegata che collega lo spazio di archiviazione che contiene lo script su factory dati | N (se si utilizza uno script)
script | Specificare uno script all'interno del testo anziché specificando scriptPath e scriptLinkedService. Ad esempio: "script": "Test del DATABASE crea". | N (se si utilizza scriptPath e scriptLinkedService)
degreeOfParallelism | Numero massimo di nodi contemporaneamente utilizzato per eseguire il processo. | No
priorità | Determina quali processi disconnettersi da tutti gli elementi vengono accodate dovrebbero essere selezionate per eseguite per prime. Minore è il numero, priorità. | No 
parametri | Parametri per lo script SQL U | No 

Per la definizione di script, vedere [Definizione di Script SearchLogProcessing.txt](#script-definition) . 

## <a name="sample-input-and-output-datasets"></a>Esempio di input e set di dati di output

### <a name="input-dataset"></a>Set di dati di input
In questo esempio, i dati di input si trovano in un archivio di Lake di dati di Azure (SearchLog.tsv file nella cartella datalake/input). 

    {
        "name": "DataLakeTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/input/",
                "fileName": "SearchLog.tsv",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }   

### <a name="output-dataset"></a>Set di dati di output
In questo esempio, i dati di output generati da script SQL U archiviati in un archivio di Lake di dati di Azure (datalake/output cartella). 

    {
        "name": "EventsByRegionTable",
        "properties": {
            "type": "AzureDataLakeStore",
            "linkedServiceName": "AzureDataLakeStoreLinkedService",
            "typeProperties": {
                "folderPath": "datalake/output/"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="sample-data-lake-store-linked-service"></a>Archivio Lake dati di esempio collegato servizio
Ecco la definizione di esempio che archivio Lake dati di Azure collegato servizio utilizzato per i set di dati di ingresso/uscita. 

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

Vedere l'articolo [spostare i dati da e verso Azure dati Lake archivio](data-factory-azure-datalake-connector.md) per le descrizioni delle proprietà JSON. 

## <a name="sample-u-sql-script"></a>Script di esempio U SQL 

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv(nullEscape:"#NULL#");
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start <= DateTime.Parse("2012/02/19");
    
    OUTPUT @rs1   
        TO @out
          USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

I valori per **@in** e **@out** parametri nello script SQL U vengono passati in modo dinamico per alimentatore automatico utilizzando la sezione 'parametri'. Vedere la sezione 'parametri' nella definizione del pipeline.

È possibile specificare anche altre proprietà, ad esempio degreeOfParallelism e priorità nella definizione della pipeline per i processi che eseguono il servizio di Azure dati Lake Analitica.

## <a name="dynamic-parameters"></a>Parametri dinamici
Nella definizione del pipeline esempio avanti e indietro parametri vengono assegnati con valori hard-coded. 

    "parameters": {
        "in": "/datalake/input/SearchLog.tsv",
        "out": "/datalake/output/Result.tsv"
    }

È possibile utilizzare parametri dinamici. Per esempio: 

    "parameters": {
        "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
        "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
    }

In questo caso, file di input vengono comunque recuperati dalla cartella /datalake/input e file di output generati nella cartella /datalake/output. I nomi dei file sono dinamici in base alla data di inizio sezione.  