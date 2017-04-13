<properties 
   pageTitle="Visualizzazione dei registri diagnostici per Azure dati Lake Analitica | Microsoft Azure" 
   description="Informazioni su come installare e accedere registri diagnostici per analitica Lake di dati di Azure " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/11/2016"
   ms.author="larryfr"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accesso ai registri diagnostici per Azure dati Lake Analitica

Informazioni su come attivare la registrazione diagnostica per il proprio account dati Lake Analitica e su come visualizzare i registri raccolti per il proprio account.

Le organizzazioni possono attivare la registrazione diagnostica per il proprio account Azure dati Lake Analitica raccogliere dati access audit trail. Questi registri forniscono informazioni ad esempio:

* Un elenco di utenti cui accedere i dati.
* La frequenza di accesso ai dati.
* La quantità di dati vengono archiviati nella finestra account.

## <a name="prerequisites"></a>Prerequisiti

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Attivare l'abbonamento Azure** per dati Lake Analitica Public Preview. Vedere [le istruzioni](data-lake-analytics-get-started-portal.md#signup).
- **Account azure dati Lake Analitica**. Seguire le istruzioni nella [Guida introduttiva di Azure dati Lake Analitica tramite il portale di Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Attivare la registrazione

1. Effettuare l'accesso con il nuovo [portale Azure](https://portal.azure.com).

2. Aprire l'account di dati Lake Analitica e blade l'account dati Lake Analitica, fare clic su **Impostazioni**e quindi fare clic su **Impostazioni di diagnostica**.

3. In e il **diagnostica** , apportare le modifiche seguenti per configurare la registrazione diagnostica.

    ![Attivare la registrazione diagnostica] (./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Abilitare i registri diagnostici")

    * Impostare **lo stato** **su** per attivare la registrazione diagnostica.
    * È possibile scegliere di/processo di archiviazione dei dati in due modi diversi.
        * Un flusso di dati log a un Hub di evento Azure, selezionare **Esporta in Hub evento** . Usare questa opzione se si dispone di una pipeline di elaborazione downstream per analizzare i registri in arrivo in tempo reale. Se si seleziona questa opzione, è necessario fornire i dettagli per l'Hub di evento Azure che si desidera utilizzare.
        * Selezionare **Esporta in Account di archiviazione** per l'archiviazione dei registri a un account di archiviazione Azure. Usare questa opzione se si desidera archiviare i dati. Se si seleziona questa opzione, è necessario specificare un account di archiviazione Azure per salvare i log in.
    * Specificare se si desidera ottenere i log di controllo, i registri delle richieste o entrambi.
    * Specificare il numero di giorni per cui mantenere i dati.
    * Fare clic su **Salva**.

Dopo aver attivato le impostazioni di diagnostiche, è possibile controllare i registri nella scheda **Log diagnostici** .

## <a name="view-logs"></a>Visualizzazione dei registri

Esistono due modi per visualizzare i dati del registro per il proprio account dati Lake Analitica.

* Dalle impostazioni di account Analitica Lake dati
* Dall'account Azure lo spazio di archiviazione in cui sono archiviati i dati

### <a name="using-the-data-lake-analytics-settings-view"></a>Tramite la visualizzazione dati Lake Analitica impostazioni

1. Da blade di **Impostazioni** account i dati Lake Analitica, fare clic su **Registri diagnostici**.

    ![Registrazione diagnostica visualizzazione] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Visualizza i registri diagnostici") 

2. Si verrà visualizzato e il **Log diagnostici** , i registri categorizzati per **I log di controllo** e **Registri delle richieste**.
    * Registri delle richieste acquisiscono tutte le richieste di API inviate nella pagina account dati Lake Analitica.
    * Log di controllo sono simili alle richieste di ma fornisce una scomposizione molto più dettagliata delle operazioni eseguite nella pagina account dati Lake Analitica. Ad esempio, una chiamata di API singola carica in registri delle richieste può comportare più operazioni "Accodamento" i registri di controllo.

3. Fare clic sul collegamento **Download** di una voce del log scaricare i log.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Dall'account Azure lo spazio di archiviazione che contiene i dati di log

1. Aprire e il conto dello spazio di archiviazione di Azure associato a dati Lake Analitica per la registrazione e quindi fare clic su BLOB. E il **servizio Blob** Elenca due contenitori.

    ![Registrazione diagnostica visualizzazione] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visualizza i registri diagnostici")

    * Il contenitore **informazioni dettagliate sui registri di controllo** contiene i registri di controllo.
    * Il contenitore **le richieste di log che** contiene i registri di richiesta.

2. All'interno di contenitori, i registri sono archiviati in struttura seguente.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] Il `##` voci nel percorso contengono l'anno, mese, giorno e ora in cui è stato creato il registro. Dati Lake Analitica viene creato un file ogni ora, in modo `m=` contiene sempre un valore di `00`.

    Ad esempio, potrebbe essere il percorso completo per un log di controllo:
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Analogamente, potrebbe essere il percorso completo per un file di log richiesta:
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Struttura di log

I log di controllo e richiesta sono in formato JSON. In questa sezione è visualizzare la struttura di JSON per richiesta e registri di controllo.

### <a name="request-logs"></a>Registri delle richieste

Ecco una voce di esempio nel log delle richieste di formato JSON. Ogni blob con un oggetto radice chiamato **record** contenente una matrice di oggetti di log.

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schema del Registro di richiesta

| Nome            | Tipo   | Descrizione                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| ora            | Stringa | Il timestamp (in formato UTC) del log                                              |
| resourceId      | Stringa | Posizionare l'ID della risorsa operazione                            |
| categoria        | Stringa | La categoria di log. Ad esempio, **le richieste**.                                   |
| Invece   | Stringa | Nome dell'operazione che ha eseguito l'accesso. Ad esempio GetAggregatedJobHistory.              |
| dell'enumeratore      | Stringa | Lo stato dell'operazione, ad esempio, 200.                                 |
| callerIpAddress | Stringa | L'indirizzo IP del client effettua la richiesta                                |
| correlationId   | Stringa | L'id del registro. Questo valore può essere utilizzato per raggruppare un insieme di voci di log correlati |
| identità        | Oggetto | L'identità che ha generato il registro                                            |
| proprietà      | JSON   | Vedere la sezione successiva (schema di proprietà del Registro di richiesta) per informazioni dettagliate |

#### <a name="request-log-properties-schema"></a>Schema di proprietà del Registro di richiesta

| Nome                 | Tipo   | Descrizione                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | Stringa | Il metodo HTTP utilizzato per l'operazione. Ad esempio, è possibile ottenere. |
| Percorso                 | Stringa | Il percorso l'operazione è stato eseguito su                   |
| RequestContentLength | int    | La lunghezza del contenuto della richiesta HTTP                    |
| ClientRequestId      | Stringa | L'Id che identifica la richiesta              |
| Ora di inizio            | Stringa | L'ora in cui il server ha ricevuto la richiesta         |
| Ora fine              | Stringa | L'ora in cui il server ha inviato una risposta              |

### <a name="audit-logs"></a>Log di controllo

Ecco una voce di esempio nel Registro di controllo formato JSON. Ogni blob con un oggetto radice chiamato **record** contenente una matrice di oggetti di log

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schema del Registro di controllo

| Nome            | Tipo   | Descrizione                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| ora            | Stringa | Il timestamp (in formato UTC) del log                                              |
| resourceId      | Stringa | Posizionare l'ID della risorsa operazione                            |
| categoria        | Stringa | La categoria di log. Ad esempio **controllo**.                                      |
| Invece   | Stringa | Nome dell'operazione che ha eseguito l'accesso. Ad esempio JobSubmitted.              |
| dell'enumeratore | Stringa | Stato secondario per lo stato del processo (invece). |
| resultSignature | Stringa | Informazioni dettagliate sullo stato di processo (invece). |
| identità      | Stringa | L'utente che ha richiesto l'operazione. Ad esempio susan@contoso.com.                                 |
| proprietà      | JSON   | Vedere la sezione successiva (schema di proprietà del Registro di controllo) per informazioni dettagliate |

> [AZURE.NOTE] __dell'enumeratore__ __resultSignature__ fornire informazioni sul risultato di un'operazione e contengono solo un valore se è stata completata un'operazione. Ad esempio, contengono un valore se __invece__ contiene un valore di __JobStarted__ o __JobEnded__.

#### <a name="audit-log-properties-schema"></a>Schema di proprietà del Registro di controllo

| Nome       | Tipo   | Descrizione                              |
|------------|--------|------------------------------------------|
| ID processo | Stringa | L'ID assegnato al processo  |
| Specificare nome di processo | Stringa | Il nome fornito per il processo |
| JobRunTime | Stringa | Il runtime usato per elaborare il processo |
| SubmitTime | Stringa | Il tempo (in formato UTC) in cui il processo è stato inviato |
| Ora di inizio | Stringa | Ora che il processo avviato dopo l'invio (in formato UTC). |
| Ora fine | Stringa | Ora di fine il processo. |
| Parallelismo | Stringa | Il numero di unità di dati Lake Analitica richiesta per il processo durante la presentazione. |

> [AZURE.NOTE] __SubmitTime__, __ora di inizio__, __Fine__ e __parallelismo__ fornire informazioni su un'operazione e contiene un valore se un'operazione ha avviato o completate. Ad esempio, __SubmitTime__ contiene un valore dopo __invece__ indica __JobSubmitted__.

## <a name="process-the-log-data"></a>I dati del Registro di processo

Azure dati Lake Analitica fornisce un esempio su come elaborare e analizzare i dati del registro. È possibile trovare il codice di esempio in [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dei dati di Azure Lake Analitica](data-lake-analytics-overview.md)


