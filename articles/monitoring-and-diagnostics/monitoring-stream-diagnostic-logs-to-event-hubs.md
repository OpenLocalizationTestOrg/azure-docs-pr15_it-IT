<properties
    pageTitle="Flusso Azure registri diagnostici a un evento hub | Microsoft Azure"
    description="Informazioni su come trasmettere Azure registri diagnostici a un hub di evento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Flusso Azure registri diagnostici a un hub di evento

**[Registri diagnostici Azure](monitoring-overview-of-diagnostic-logs.md)** può essere alterata quasi in tempo reale in qualsiasi applicazione utilizzando l'opzione "Esportare a evento hub" predefinito nel portale o abilitando l'Id della regola servizio Bus in un'impostazione di diagnostica tramite il cmdlet di PowerShell Azure o CLI Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Operazioni eseguibili con gli hub di eventi e registri di diagnostica
Ecco alcune modalità è possibile utilizzare la funzionalità di streaming per log diagnostici:

- **I registri di flusso per sistemi di telemetria e 3 ° parti logging** – nel tempo, hub evento streaming diventa meccanismo per inviare i registri diagnostici in SIEMs di terze parti e registrare le soluzioni analitica.

- **L'integrità dei servizi di visualizzazione per i dati di "percorso critico" per ottenere informazioni di flusso** con gli hub di evento flusso Analitica e ottenere informazioni, è possibile trasformare i dati di diagnostica in vicino approfondimenti in tempo reale con facilità i servizi di Azure. [In questo articolo documentazione fornita una panoramica su come configurare un hub di evento, elaborare dati con flusso Analitica e usare ottenere informazioni come un output](../stream-analytics/stream-analytics-power-bi-dashboard.md). Ecco alcuni suggerimenti per la configurazione con log diagnostici:
    - Gli hub di eventi di una categoria di registri diagnostici viene creato automaticamente quando si selezionare l'opzione nel portale di o l'attivazione tramite PowerShell, in modo che si desidera selezionare gli hub evento dello spazio dei nomi di servizio Bus con il nome che inizia con "approfondimenti-"
    - Ecco una query di flusso Analitica di esempio che è possibile utilizzare per analizzare semplicemente tutti i log dati a una tabella di ottenere informazioni:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Creare una piattaforma di registrazione e di telemetria personalizzato** : se è già una piattaforma di telemetria personalizzata o solo le informazioni sulla creazione di uno, elevata scalabilità pubblicazione sottoscrizione natura dell'evento hub consente di acquisire flessibile registri diagnostici. [Vedere Dan Rosanova della Guida all'utilizzo hub di evento in una piattaforma di telemetria di scala globale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Eseguire lo streaming dei registri diagnostici
È possibile attivare il flusso di registri diagnostici a livello di programmazione, tramite il portale o tramite l' [API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx). In entrambi i casi, si seleziona un Namespace Bus di servizio e viene creato un hub evento dello spazio dei nomi per ogni categoria di log che attiva. Una **Categoria di Log** diagnostici è un tipo di log potrebbe raccogliere una risorsa. È possibile selezionare le categorie di log che si desidera raccogliere per una determinata risorsa nel portale di Azure in e il diagnostica.

![Categorie del log nel portale](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] Attivazione e streaming registri diagnostici dal calcolo risorse (ad esempio macchine virtuali o servizio tessuti) [richiede un insieme diverso di passaggi](../event-hubs/event-hubs-streaming-azure-diags-data.md).

### <a name="via-powershell-cmdlets"></a>Tramite i cmdlet di PowerShell
Per abilitare streaming tramite i [Cmdlet di PowerShell Azure](insights-powershell-samples.md), è possibile utilizzare il `Set-AzureRmDiagnosticSetting` cmdlet insieme ai parametri seguenti:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

L'ID della regola Bus servizio è una stringa con questo formato: `{service bus resource ID}/authorizationrules/{key name}`, ad esempio `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### <a name="via-azure-cli"></a>Tramite CLI Azure
Per abilitare streaming tramite [CLI Azure](insights-cli-samples.md), è possibile utilizzare il `insights diagnostic set` comando come segue:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Utilizzare lo stesso formato per ID regola Bus del servizio, come illustrato per il Cmdlet di PowerShell.

###<a name="via-azure-portal"></a>Tramite il portale di Azure
Per abilitare la trasmissione tramite il portale di Azure, passare alle impostazioni di diagnostica di una risorsa e selezionare "Esportare Hub di evento".

![Esportare in hub di evento nel portale](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Per configurarlo, selezionare un Namespace Bus servizio esistente. Spazio dei nomi selezionato saranno nel punto in cui gli hub di evento viene creato (se questa è il prima volta flusso dei registri diagnostici) o in streaming per (se sono già presenti le risorse in streaming categoria log per questo spazio dei nomi), e il criterio consente di definire le autorizzazioni che contiene il meccanismo di trasmissione. Oggi, streaming a un hub evento richiede le autorizzazioni di gestire, lettura e invio. È possibile creare o modificare i criteri di accesso servizio Bus Namespace condivise nel portale di classica sotto la scheda "Configura" per le Namespace Bus di servizio. Per aggiornare una di queste impostazioni di diagnostica, il client deve disporre dell'autorizzazione ListKey della regola di autorizzazione Bus di servizio.

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Modalità di utilizzo di dati del registro dall'hub di evento
Ecco i dati di output di esempio da hub evento:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nome dell'elemento | Descrizione                                            |
|--------------|--------------------------------------------------------|
|record       | Matrice di tutti gli eventi del log di questo payload.            |
|ora          | Ora in cui si è verificato l'evento.                      |
|categoria      | Categoria di log per questo evento.                           |
|resourceId    | ID risorsa della risorsa che ha generato questo evento. |
|Invece | Nome dell'operazione.                                 |
|livello         | Facoltativo. Indica il livello di registrazione eventi.               |
|proprietà    | Proprietà dell'evento.                               |


È possibile visualizzare un elenco di tutti i provider di risorse che supportano la trasmissione all'evento Hub [qui](monitoring-overview-of-diagnostic-logs.md).

##<a name="next-steps"></a>Passaggi successivi
- [Per ulteriori informazioni sui registri diagnostici Azure](monitoring-overview-of-diagnostic-logs.md)
- [Guida introduttiva a hub di evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
