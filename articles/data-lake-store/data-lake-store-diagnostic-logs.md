<properties 
   pageTitle="Visualizzazione dei registri diagnostici per archivio Lake dati di Azure | Microsoft Azure" 
   description="Informazioni su come installare e accedere registri diagnostici per archivio Lake dati di Azure " 
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
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Accesso ai registri diagnostici per archivio Lake dati di Azure

Informazioni su come attivare la registrazione diagnostica per l'account di archivio di dati Lake e su come visualizzare i registri raccolti per il proprio account.

Le organizzazioni possono attivare la registrazione diagnostica per il proprio account Azure dati Lake Store raccogliere audit trail di access dati che fornisce informazioni, ad esempio l'elenco di utenti che accedono a dati, la frequenza di accesso ai dati, la quantità di dati è archiviato in account, e così via.

## <a name="prerequisites"></a>Prerequisiti

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Account azure dati Lake Store**. Seguire le istruzioni nella [Guida introduttiva di Azure dati Lake archivio tramite il portale di Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Attivare la registrazione diagnostica per l'account di archivio di dati Lake

1. Effettuare l'accesso con il nuovo [Portale Azure](https://portal.azure.com).

2. Aprire l'account di archivio di dati Lake e blade l'account archivio Lake dati, fare clic su **Impostazioni**e quindi fare clic su **Impostazioni di diagnostica**.

3. In e il **diagnostica** , apportare le modifiche seguenti per configurare la registrazione diagnostica.

    ![Attivare la registrazione diagnostica] (./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Abilitare i registri diagnostici")

    * Impostare **lo stato** **su** per attivare la registrazione diagnostica.
    * È possibile scegliere di/processo di archiviazione dei dati in due modi diversi.
        * Selezionare l'opzione per **esportare in evento Hub** un flusso di dati log a un Hub di evento Azure. È probabile che si utilizzerà questa opzione se si dispone di una pipeline di elaborazione downstream per analizzare i registri in arrivo in tempo reale. Se si seleziona questa opzione, è necessario fornire i dettagli per l'Hub di evento Azure che si desidera utilizzare.
        * Selezionare l'opzione per **esportare in Account di archiviazione** per l'archiviazione dei registri a un account di archiviazione Azure. Utilizzare questa opzione se si desidera archiviare i dati che saranno elaborazione batch in una data successiva. Se si seleziona questa opzione è necessario specificare un account di archiviazione Azure per salvare i log in.
    * Specificare se si desidera ottenere i log di controllo, i registri delle richieste o entrambi.
    * Specificare il numero di giorni per cui mantenere i dati.
    * Fare clic su **Salva**.

Dopo aver attivato le impostazioni di diagnostiche, è possibile controllare i registri nella scheda **Log diagnostici** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Visualizza i registri diagnostici per l'account di archivio di dati Lake

Esistono due modi per visualizzare i dati del registro per il proprio account archivio Lake dati.

* Dall'account archivio dati Lake impostazioni visualizzazione
* Dall'account Azure lo spazio di archiviazione in cui sono archiviati i dati

### <a name="using-the-data-lake-store-settings-view"></a>Tramite la visualizzazione dati Lake impostazioni relative all'archivio

1. Da blade di **Impostazioni** account l'archivio Lake dati, fare clic su **Registri diagnostici**.

    ![Registrazione diagnostica visualizzazione] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visualizza i registri diagnostici") 

2. Si verrà visualizzato e il **Log diagnostici** , i registri categorizzati per **I log di controllo** e **Registri delle richieste**.
    * Registri delle richieste acquisiscono tutte le richieste di API inviate nella pagina account archivio Lake dati.
    * Log di controllo sono simili alle richieste di ma fornisce una scomposizione molto più dettagliata delle operazioni eseguite nella pagina account archivio Lake dati. Ad esempio, una chiamata di API singola carica in registri delle richieste potrebbe più operazioni "Accodamento" nei registri di controllo.

3. Fare clic sul collegamento **Download** rispetto a ogni voce del log per scaricare i log.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Dall'account Azure lo spazio di archiviazione che contiene i dati di log

1. Aprire e il conto dello spazio di archiviazione di Azure associato all'archivio Lake dati per la registrazione e quindi fare clic su BLOB. E il **servizio Blob** Elenca due contenitori.

    ![Registrazione diagnostica visualizzazione] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visualizza i registri diagnostici")

    * Il contenitore **informazioni dettagliate sui registri di controllo** contiene i registri di controllo.
    * Il contenitore **le richieste di log che** contiene i registri di richiesta.

2. All'interno di contenitori, i registri sono archiviati in struttura seguente.

    ![Registrazione diagnostica visualizzazione] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visualizza i registri diagnostici")

    Ad esempio, potrebbe essere il percorso completo per un log di controllo`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Allo stesso modo, potrebbe essere il percorso completo per un file di log richiesta`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Comprendere la struttura dei dati di registrazione

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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| Invece   | Stringa | Nome dell'operazione che ha eseguito l'accesso. Ad esempio getfilestatus.              |
| dell'enumeratore      | Stringa | Lo stato dell'operazione, ad esempio, 200.                                 |
| callerIpAddress | Stringa | L'indirizzo IP del client effettua la richiesta                                |
| correlationId   | Stringa | L'id del log che possono essere utilizzati per raggruppare un insieme di voci di log correlati |
| identità        | Oggetto | L'identità che ha generato il registro                                            |
| proprietà      | JSON   | Per informazioni dettagliate, vedere sotto                                                          |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| Invece   | Stringa | Nome dell'operazione che ha eseguito l'accesso. Ad esempio getfilestatus.              |
| dell'enumeratore      | Stringa | Lo stato dell'operazione, ad esempio, 200.                                 |
| correlationId   | Stringa | L'id del log che possono essere utilizzati per raggruppare un insieme di voci di log correlati |
| identità        | Oggetto | L'identità che ha generato il registro                                            |
| proprietà      | JSON   | Per informazioni dettagliate, vedere sotto                                                          |

#### <a name="audit-log-properties-schema"></a>Schema di proprietà del Registro di controllo

| Nome       | Tipo   | Descrizione                              |
|------------|--------|------------------------------------------|
| StreamName | Stringa | Il percorso l'operazione è stato eseguito su  |


## <a name="samples-to-process-the-log-data"></a>Esempi per elaborare i dati di log

Azure dati Lake Store viene fornito un esempio su come elaborare e analizzare i dati del registro. È possibile trovare il codice di esempio in [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="see-also"></a>Vedere anche

- [Panoramica dell'archivio Lake dati di Azure](data-lake-store-overview.md)
- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)

