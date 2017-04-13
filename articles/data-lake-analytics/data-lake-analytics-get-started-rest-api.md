<properties 
   pageTitle="Guida introduttiva a dati Lake Analitica utilizzando API REST | Microsoft Azure" 
   description="Consente di eseguire operazioni sui dati Lake Analitica API REST WebHDFS" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Guida introduttiva di Azure dati Lake Analitica utilizzando le API REST

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come utilizzare le API REST WebHDFS e le API REST di dati Lake Analitica per gestire gli account, processi e catalogo dati Lake Analitica. 

## <a name="prerequisites"></a>Prerequisiti

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Creare un'applicazione di Azure Active Directory**. Utilizzare l'applicazione di Azure Active Directory per autenticare l'applicazione di dati Lake Analitica con Azure Active Directory. Sono disponibili diversi metodi per l'autenticazione con Azure Active Directory, che sono **al servizio**o **l'autenticazione degli utenti finali** . Per ulteriori informazioni su come eseguire l'autenticazione e istruzioni, vedere [eseguire l'autenticazione con dati Lake Analitica utilizzando Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

- [voltare](http://curl.haxx.se/). In questo articolo viene utilizzata curvatura per illustrare effettuare chiamate all'API REST rispetto a un account di dati Lake Analitica.

## <a name="authenticate-with-azure-active-directory"></a>Eseguire l'autenticazione con Azure Active Directory

Esistono due metodi per l'autenticazione con Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticazione dell'utente finale (interattivo)

Con questo metodo, applicazione richiede all'utente di eseguire l'accesso e tutte le operazioni vengono eseguite nel contesto dell'utente. 

Seguire questa procedura per l'autenticazione interattivo:

1. Tramite l'applicazione, si viene reindirizzati all'utente l'URL seguente:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REINDIRIZZAMENTO URI > deve essere codificato per l'utilizzo in un URL. Pertanto, per https://localhost, utilizzare `https%3A%2F%2Flocalhost`)

    Per questa esercitazione, è possibile sostituire i valori di segnaposto nell'URL sopra e incollarla nella barra degli indirizzi del web browser. Si verrà reindirizzati per l'autenticazione mediante l'account di accesso Azure. Una volta è correttamente l'accesso, la risposta viene visualizzata nella barra degli indirizzi del browser. La risposta saranno nel formato seguente:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Acquisire il codice di autorizzazione dalla risposta. Per questa esercitazione, è possibile copiare il codice di autorizzazione dalla barra degli indirizzi del browser e passare nella richiesta POST all'endpoint token a come illustrato di seguito:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] In questo caso, il \<REINDIRIZZA URI > necessario non può essere codificato.

3. La risposta è un oggetto JSON che contiene un token di accesso (ad esempio, `"access_token": "<ACCESS_TOKEN>"`) e un token di aggiornamento (ad esempio, `"refresh_token": "<REFRESH_TOKEN>"`). L'applicazione utilizza il token di accesso quando si accede a Azure Lake archivio dati e il token di aggiornamento per ottenere un altro token di accesso quando scade un token di accesso.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Quando scade il token di accesso, è possibile richiedere un nuovo token di accesso utilizzando il token di aggiornamento, come illustrato di seguito:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Per ulteriori informazioni sull'autenticazione utente interattiva, vedere [il codice di autorizzazione concedere flusso](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Servizio di autenticazione (non interattiva)

Con questo metodo, l'applicazione fornisce il proprio le credenziali per eseguire le operazioni. A tale scopo, è necessario inviare una richiesta POST simile a quello illustrato di seguito: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

L'output della richiesta includerà un token di autenticazione (identificato da `access-token` nell'output riportata di seguito) che passerà successivamente con le chiamate API REST. Salvare questo token di autenticazione in un file di testo. è necessario più avanti in questo articolo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

In questo articolo viene utilizzato il **non interattiva** approccio. Per ulteriori informazioni sulle modalità interattiva (chiamate a servizio), vedere [servizio alle chiamate del servizio utilizzando le credenziali](https://msdn.microsoft.com/library/azure/dn645543.aspx).
## <a name="create-a-data-lake-analytics-account"></a>Creare un account di dati Lake Analitica

È necessario creare un gruppo di risorse Azure e un account di archivio Lake dati prima di creare un account di dati Lake Analitica.  Vedere [creare un account di archivio di dati Lake](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

Il comando curvatura seguente viene illustrato come creare un account:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Sostituire \< `REDACTED` \> con il token di autorizzazione, \< `AzureSubscriptionID` \> con il proprio ID abbonamento \< `AzureResourceGroupName` \> con un nome gruppo di risorse Azure esistente, e \< `NewAzureDataLakeAnalyticsAccountName` \> con un nuovo nome Account Analitica Lake dati. Il contenuto di richiesta per questo comando è contenuto nel file **CreateDatalakeAnalyticsAccountRequest.json** fornito per il `-d` parametro precedente. Il contenuto del file input.json simile al seguente:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Account Analitica Lake di dati di elenco in una sottoscrizione

Il comando curvatura seguente viene illustrato come elenco account di un abbonamento:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Sostituire \< `REDACTED` \> con il token di autorizzazione, \< `AzureSubscriptionID` \> con l'ID di sottoscrizione. L'output è simile a:

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Ottenere informazioni relative all'account Analitica Lake dati

Il comando curvatura seguente viene illustrato come ottenere un informazioni relative all'account:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Sostituire \< `REDACTED` \> con il token di autorizzazione, \< `AzureSubscriptionID` \> con il proprio ID abbonamento \< `AzureResourceGroupName` \> con un nome gruppo di risorse Azure esistente, e \< `DataLakeAnalyticsAccountName` \> con il nome di un Account di Analitica Lake dati esistente. L'output è simile a:

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Elenco dati Lake archivi di un account di dati Lake Analitica

Il comando curvatura seguente viene illustrato come elencare archivi Lake dati di un account:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Sostituire \< `REDACTED` \> con il token di autorizzazione, \< `AzureSubscriptionID` \> con il proprio ID abbonamento \< `AzureResourceGroupName` \> con un nome gruppo di risorse Azure esistente, e \< `DataLakeAnalyticsAccountName` \> con il nome di un Account di Analitica Lake dati esistente. L'output è simile a:

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Inviare i processi di U SQL

Il comando curvatura seguente viene illustrato come inviare un processo U SQL:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Sostituire \< `REDACTED` \> con il token di autorizzazione, \< `DataLakeAnalyticsAccountName` \> con il nome di un Account di Analitica Lake dati esistente. Il contenuto di richiesta per questo comando è contenuto nel file **SubmitADLAJob.json** fornito per il `-d` parametro precedente. Il contenuto del file input.json simile al seguente:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

L'output è simile a:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Elenca i processi U SQL

Il comando curvatura seguente viene illustrato come elencare processi U SQL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Sostituire \< `REDACTED` \> con il token di autorizzazione e \< `DataLakeAnalyticsAccountName` \> con il nome di un Account di Analitica Lake dati esistente. 


L'output è simile a:

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Ottenere gli elementi del catalogo

Il comando curvatura seguente viene illustrato come ottenere i database dal catalogo:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

L'output è simile a:

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Vedere anche

- Per una query più complessa, vedere [registri di sito Web analizza mediante Azure dati Lake Analitica](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a applicazioni U SQL, vedere [gli script di sviluppare U-SQL utilizzando dati Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni su U-SQL, vedere [Guida introduttiva a linguaggio Azure dati Lake Analitica U-SQL](data-lake-analytics-u-sql-get-started.md).
- Per le attività di gestione, vedere [gestire Azure dati Lake Analitica tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per ottenere una panoramica dei dati Lake Analitica, vedere [Panoramica di Azure dati Lake Analitica](data-lake-analytics-overview.md).
- Per visualizzare l'esercitazione stessa usare altri strumenti, fare clic su selettori scheda nella parte superiore della pagina.
- Per registrare le informazioni di diagnostica, vedere [accesso ai registri di diagnostica per Azure dati Lake Analitica](data-lake-analytics-diagnostic-logs.md)
