<properties
    pageTitle="Configurare webhooks sugli avvisi metrici Azure | Microsoft Azure"
    description="Reindirizzare gli avvisi di Azure ad altri sistemi non Azure."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurare un webhook su un avviso metrico Azure

Webhooks consentono di inviare un avviso Azure ad altri sistemi per le azioni post-elaborazione o personalizzate. È possibile utilizzare una webhook su un avviso per il routing per i servizi che inviare SMS, registrare gli errori, una notifica di un team tramite servizi di messaggistica/chat o eseguire qualsiasi numero di altre azioni. Questo articolo descrive come impostare una webhook su un avviso metrico Azure e il contenuto per il POST HTTP a un webhook l'aspetto. Per informazioni sull'impostazione e dello schema per un Log di attività di Azure un avviso (avviso gli eventi), [vedere la pagina invece](./insights-auditlog-to-webhook-email.md).

Avvisi Azure HTTP POST il contenuto degli avvisi in JSON, schema del formato definito sotto, a un webhook URI fornite durante la creazione dell'avviso. Questo URI deve essere un endpoint HTTP o HTTPS valido. Azure pubblica una voce per ogni richiesta all'attivazione di un avviso.

## <a name="configuring-webhooks-via-the-portal"></a>Configurazione webhooks tramite il portale

È possibile aggiungere o aggiornare webhook URI nella schermata Crea/Aggiorna avvisi nel [portale](https://portal.azure.com/).

![Aggiungere una regola di avviso](./media/insights-webhooks-alerts/Alertwebhook.png)

È inoltre possibile configurare un avviso per inserire un post in un webhook URI tramite [I cmdlet di PowerShell di Azure](./insights-powershell-samples.md#create-alert-rules), [Multipiattaforma CLI](./insights-cli-samples.md#work-with-alerts)o [API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticazione di webhook

Il webhook può eseguire l'autenticazione tramite uno di questi metodi:

1. **Autorizzazione basata su token** - webhook URI viene salvato con un ID token, ad esempio. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorizzazione di base** - webhook URI viene salvato con un nome utente e password, ad esempio. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schema payload

Operazione POST contiene i seguenti payload JSON e lo schema per tutti gli avvisi basati su unità di misura metriche.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Obbligatorio | Gruppo fisso di valori | Note |
| :-------------| :-------------   | :-------------   | :-------------   |
|stato|Y|"Attivato", "Risolto"|Stato dell'avviso in base alle condizioni è stata impostata.|
|scelta rapida| Y | | Il contesto di avviso.|
|timestamp| Y | | L'ora in cui è stato attivato l'avviso.|
|ID | Y | | Id univoco ogni regola avviso.|
|nome               |Y                  |                   | Il nome dell'avviso.|
|Descrizione        |Y                  |                           |Descrizione dell'avviso.|
|conditionType      |Y                  |"Metriche", "Evento"          |Due tipi di avvisi sono supportati. Uno in base a una condizione metrica e l'altro in base a un evento nel Log delle attività. Utilizzare questo valore per verificare se l'avviso si basa sull'unità di misura metriche o un evento.|
|condizione          |Y                  |                           | I campi specifici per cercare in base alla conditionType.|
|metricName         |per gli avvisi metrici  |                           |Nome dell'unità di misura metriche che definisce la regola controlla.|
|metricUnit         |per gli avvisi metrici  |"Byte", "BytesPerSecond", "Contare", "CountPerSecond", "Percentuale", "Secondi"|     Unità consentita l'unità di misura metriche. [Di seguito sono elencati i valori consentiti](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue        |per gli avvisi metrici  |                           |Il valore effettivo di unità di misura metriche che ha causato l'avviso.|
|soglia          |per gli avvisi metrici  |                           |Il valore di soglia in cui è attivata l'avviso.|
|windowSize         |per gli avvisi metrici  |                           |Periodo di tempo in cui viene utilizzato per monitorare l'attività di avviso in base alla soglia. Deve essere compreso tra 1 giorno e 5 minuti. Formato di durata ISO8601.|
|Aggregazione temporale    |per gli avvisi metrici  |"Media" e "Cognome", "Massimo", "Minimo", "Nessuna", "Total" | I dati raccolti come devono essere combinati nel tempo. Il valore predefinito è Media. [Di seguito sono elencati i valori consentiti](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|operatore           |per gli avvisi metrici  |                           |L'operatore per confrontare i dati metrici correnti per la soglia specificata.|
|subscriptionId     |Y                  |                           |ID abbonamento Azure.|
|resourceGroupName  |Y                  |                           |Nome del gruppo di risorse per la risorsa interessata.|
|resourceName       |Y                  |                           |Nome di risorsa della risorsa interessata.|
|tipo di risorsa       |Y                  |                           |Tipo di risorsa della risorsa interessata.|
|resourceId         |Y                  |                           |ID risorsa della risorsa interessata.|
|resourceRegion     |Y                  |                           |L'area geografica o il percorso della risorsa interessata.|
|portalLink         |Y                  |                           |Collegamento diretto alla pagina di riepilogo delle risorse portale.|
|proprietà         |N                  |Facoltativo                   |Set di `<Key, Value>` coppie di parole (ad esempio `Dictionary<String, String>`) che include dettagli sull'evento. Il campo proprietà è facoltativo. Un personalizzate dell'interfaccia utente o logica basate su app flusso di lavoro, gli utenti possono immettere/valori chiave che possono essere passati tramite il payload. Il modo alternativo per passare proprietà personalizzate di webhook consiste nell'utilizzare l'uri webhook (come parametri query)|


>[AZURE.NOTE] Il campo proprietà può essere impostato solo tramite l' [API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli avvisi Azure e webhooks in video [Integrare gli avvisi di Azure con PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Eseguire gli script di automazione di Azure (runbook) sugli avvisi Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Usare la logica di App per inviare un SMS tramite Twilio da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Usare la logica App per inviare un messaggio margine di flessibilità da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Usare la logica App per inviare un messaggio a una coda di Azure da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
