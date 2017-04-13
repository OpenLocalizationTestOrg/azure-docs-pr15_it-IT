<properties
   pageTitle="API REST degli avvisi di log Analitica"
   description="I Log Analitica avviso all'API REST consente di creare e gestire gli avvisi in operazioni di gestione famiglia di prodotti (OMS).  In questo articolo fornisce i dettagli dell'API e alcuni esempi per eseguire diverse operazioni."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>Registro Analitica un avviso all'API REST

I Log Analitica avviso all'API REST consente di creare e gestire gli avvisi in operazioni di gestione famiglia di prodotti (OMS).  In questo articolo fornisce i dettagli dell'API e alcuni esempi per eseguire diverse operazioni.

API REST ricerca Analitica Log è RESTful e sono accessibili tramite il REST API di Azure Manager delle risorse. In questo documento sono disponibili esempi in cui si accede all'API dalla riga di comando PowerShell mediante [ARMClient](https://github.com/projectkudu/ARMClient), uno strumento di riga di comando Apri origine che semplifica richiamare l'API di gestione risorse di Azure. L'utilizzo di PowerShell e ARMClient è una delle numerose opzioni per accedere all'API di ricerca Analitica Log. Con questi strumenti è possibile utilizzare l'API di gestione risorse Azure REST per effettuare chiamate alle aree di lavoro OMS ed eseguire i comandi di ricerca al loro interno. L'API fornirà risultati della ricerca per l'utente nel formato JSON, che consente di utilizzare i risultati della ricerca in molti modi diversi a livello di programmazione.

## <a name="prerequisites"></a>Prerequisiti
Avvisi attualmente, possono essere creati solo con una ricerca salvata nel registro Analitica.  È possibile fare riferimento all' [API REST di registro ricerca](log-analytics-log-search-api.md) per altre informazioni.

## <a name="schedules"></a>Pianificazioni
Una ricerca salvata può avere una o più pianificazioni. La pianificazione definisce con quale frequenza la ricerca viene eseguita e l'intervallo di tempo in cui viene identificato i criteri.
Le pianificazioni hanno le proprietà nella tabella seguente.

| Proprietà  | Descrizione |
|:--|:--|
| Intervallo | Con quale frequenza viene eseguita la ricerca. Misurato in minuti. |
| QueryTimeSpan | L'intervallo di tempo in cui viene valutata i criteri. Deve essere maggiore o uguale a quello dell'intervallo. Misurato in minuti. |
| Versione | La versione dell'API in uso.  Attualmente, questo deve sempre essere impostato su 1. |

Si consideri ad esempio una query di eventi a un intervallo di 15 minuti e un intervallo di tempo di 30 minuti. In questo caso, la query da eseguire ogni 15 minuti e sarebbe attivato un avviso se i criteri continua da risolvere true su un intervallo di 30 minuti.

### <a name="retrieving-schedules"></a>Recuperare le pianificazioni
Utilizzare il metodo Get per recuperare tutte le pianificazioni per una ricerca salvata.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilizzare il metodo Get con un ID di programmazione per recuperare una determinata pianificazione di una ricerca salvata.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Di seguito è una risposta di esempio per una programmazione.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Creazione di una pianificazione
Per creare una nuova programmazione, utilizzare il metodo di caricamento con un ID pianificazione univoca.  Si noti che due pianificazioni non possono avere lo stesso ID anche se sono associati diversi ricerche salvate.  Quando si crea una pianificazione nella console di OMS, viene creato un GUID per l'ID di programmazione.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>La modifica di una pianificazione
Utilizzare il metodo di caricamento con un ID pianificazione esistente per la stessa ricerca salvata per modificare la pianificazione.  Corpo della convocazione deve includere etag della pianificazione.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Eliminazione di pianificazioni
Utilizzare il metodo Delete con un ID di programmazione per eliminare una pianificazione.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Azioni
Una pianificazione può includere più azioni. Un'azione può definire i processi di uno o più da eseguire, ad esempio l'invio di un messaggio di posta o avviare un runbook oppure riferirsi una soglia che determina quando i risultati di una ricerca corrispondono alcuni criteri.  Alcune azioni definiranno entrambi in modo che i processi vengono eseguiti quando viene raggiunta la soglia.

Tutte le azioni sono le proprietà nella tabella seguente.  Diversi tipi di avvisi che hanno diverse proprietà aggiuntive descritte di seguito.

| Proprietà | Descrizione |
|:--|:--|
| Tipo | Tipo di azione.  I valori possibili sono attualmente avviso e Webhook. |
| Nome | Nome visualizzato per l'avviso. |
| Versione | La versione dell'API in uso.  Attualmente, questo deve sempre essere impostato su 1. |

### <a name="retrieving-actions"></a>Recupero azioni
Utilizzare il metodo Get per recuperare tutte le azioni di un impiego.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilizzare il metodo Get con l'ID di azione per recuperare un'azione specifica di un impiego.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Creazione o modifica di azioni
Utilizzare il metodo di caricamento con un ID azione univoco per la pianificazione per creare una nuova azione.  Quando si crea un'azione nella console di OMS, un GUID riguarda l'ID azione.

Utilizzare il metodo di caricamento con un ID azione esistente per la stessa ricerca salvata per modificare la pianificazione.  Corpo della convocazione deve includere etag della pianificazione.

Il formato della richiesta per la creazione di una nuova azione varia in base al tipo di azione in modo che in questi esempi vengono forniti nelle sezioni seguenti.

### <a name="deleting-actions"></a>Eliminazione di azioni
Utilizzare il metodo di eliminazione con l'ID di azione per eliminare un'azione.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Azioni degli avvisi
Una pianificazione deve avere una sola azione avviso.  Azioni avviso intrattenere una o più delle sezioni nella tabella seguente.  Descritti in dettaglio più avanti.

| Sezione | Descrizione |
|:--|:--|
| Soglia | Criteri per quando viene eseguita l'azione. |  
| EmailNotification | Inviare messaggi a più destinatari. |
| Monitoraggio e aggiornamento | Avviare un runbook in Azure automazione per tentare di risolvere il problema identificato. |

#### <a name="thresholds"></a>Soglie
Un'azione avviso deve avere un unico soglia.  Quando i risultati della ricerca salvata corrispondano la soglia di un'azione associata a tale ricerca, vengono eseguiti gli altri processi in tale azione.  Un'azione può inoltre contenere solo un limite in modo che può essere utilizzato con le azioni di altri tipi che non contengono le soglie.

Soglie di avere le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Operatore | Operatore di confronto soglia. <br> gt = maggiore di <br> lt = minore di |
| Valore | Valore di soglia. |

Si consideri ad esempio una query di eventi con un intervallo di 15 minuti, un intervallo di tempo di 30 minuti e un limite superiore a 10. In questo caso, la query da eseguire ogni 15 minuti e sarebbe attivato un avviso se restituito 10 gli eventi che sono stati creati in un intervallo di 30 minuti.

Di seguito è una risposta di esempio per un'azione con solo un limite.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Utilizzare il metodo di caricamento con un ID univoco azione per creare una nuova azione soglia di un impiego.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilizzare il metodo di caricamento con un ID azione esistente per modificare un'azione soglia di un impiego.  Corpo della convocazione deve includere etag dell'azione.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notifica tramite posta elettronica
Notifiche tramite posta elettronica inviare messaggi a uno o più destinatari.  Includono le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Destinatari | Elenco di indirizzi di posta elettronica. |
| Oggetto | L'oggetto del messaggio. |
| Allegato | Gli allegati non sono attualmente supportati, in modo che questo avrà sempre un valore di "Nessuno". |

Di seguito è una risposta di esempio per un'azione di notifica di messaggio di posta elettronica con una determinata soglia.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Utilizzare il metodo di caricamento con un ID univoco azione per creare una nuova azione di posta elettronica di un impiego.  Nell'esempio seguente viene creata una notifica tramite posta elettronica con una determinata soglia in modo che la posta viene inviata quando i risultati della ricerca salvata superano la soglia.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Utilizzare il metodo di caricamento con un ID azione esistente per modificare un'azione di posta elettronica di un impiego.  Corpo della convocazione deve includere etag dell'azione.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Azioni di monitoraggio e aggiornamento
Rimedi avviare un runbook in automazione Azure che tenta di risolvere il problema identificato dall'avviso.  È necessario creare un webhook per runbook utilizzato in un'operazione di monitoraggio e quindi specificare l'URI nella proprietà WebhookUri.  Quando si crea questa azione tramite la console OMS, viene creata automaticamente una nuova webhook per dal runbook.

Rimedi includono le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| RunbookName | Nome dal runbook. Deve corrispondere un runbook pubblicati nella finestra account automazione configurato della soluzione di automazione nell'area di lavoro OMS. |
| WebhookUri | URI della webhook.
| Scadenza | La data di scadenza e l'ora del webhook.  Se il webhook non è una data di scadenza, questa può essere una data futura valido. |

Di seguito è una risposta di esempio per un'azione di monitoraggio e aggiornamento con una determinata soglia.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Utilizzare il metodo di caricamento con un ID univoco azione per creare una nuova azione di monitoraggio e aggiornamento di un impiego.  Nell'esempio seguente crea un rimedio con una soglia in modo che dal runbook viene avviato quando i risultati della ricerca salvata superano la soglia.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Utilizzare il metodo di caricamento con un ID azione esistente per modificare un'azione di monitoraggio e aggiornamento di un impiego.  Corpo della convocazione deve includere etag dell'azione.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Esempio
Di seguito è un esempio completo per creare un nuovo avviso di posta elettronica.  In questo modo si crea una nuova programmazione insieme a un'azione che contiene una soglia e la posta elettronica.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Azioni Webhook
Azioni Webhook avviano un processo chiamando un URL e fornendo facoltativamente un payload da inviare.  Sono simili alle azioni di correzione, ad eccezione di sono utilizzati per webhooks che può richiamare processi diversa da quella runbook automazione Azure.  Forniscono inoltre anche l'opzione di fornire un payload recapitato al processo remoto.

Azioni Webhook non dispone di una determinata soglia ma invece devono essere aggiunto a una programmazione con un'azione avviso con una determinata soglia.  È possibile aggiungere più azioni Webhook che verranno tutti eseguite quando viene raggiunta la soglia.

Azioni Webhook includono le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| WebhookUri | L'oggetto del messaggio. |
| CustomPayload | Payload personalizzato devono essere inviati i webhook.  Il formato dipenderà che cos'è previsto il webhook. |

Di seguito è una risposta di esempio per l'azione webhook e un'azione avviso associata con una determinata soglia.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Creare o modificare un'azione webhook
Utilizzare il metodo di caricamento con un ID univoco azione per creare una nuova azione webhook di un impiego.  Nell'esempio seguente crea un'azione Webhook e un'azione avviso con una determinata soglia in modo che verrà attivata la webhook quando i risultati della ricerca salvata superano la soglia.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Utilizzare il metodo di caricamento con un ID azione esistente per modificare un'azione webhook di un impiego.  Corpo della convocazione deve includere etag dell'azione.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Passaggi successivi

- Utilizzare l' [API REST per eseguire ricerche di log](log-analytics-log-search-api.md) nel Log Analitica.
