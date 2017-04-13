<properties
    pageTitle="Configurare un webhook sugli avvisi registro attività Azure | Microsoft Azure"
    description="Informazioni su come usare gli avvisi di registro attività chiamare webhooks. "
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
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-activity-log-alerts"></a>Configurare un webhook su un avviso di Azure registro attività

Webhooks consentono di inviare un avviso Azure ad altri sistemi per le azioni post-elaborazione o personalizzate. È possibile utilizzare una webhook su un avviso per il routing per i servizi che inviare SMS, registrare gli errori, una notifica di un team tramite servizi di messaggistica/chat o eseguire qualsiasi numero di altre azioni. Questo articolo descrive come impostare una webhook su un avviso registro attività Azure e il contenuto per il POST HTTP a un webhook l'aspetto. Per informazioni sull'impostazione e dello schema per un avviso metrico Azure, [vedere la pagina invece](insights-webhooks-alerts.md). È anche possibile impostare un avviso registro attività per inviare posta elettronica quando attivato.

>[AZURE.NOTE] Questa funzionalità è attualmente in anteprima, pertanto prevedere le prestazioni e qualità variabili.

È possibile impostare un avviso registro attività tramite [I cmdlet di PowerShell di Azure](insights-powershell-samples.md#create-alert-rules), [Multipiattaforma CLI](insights-cli-samples.md#work-with-alerts)o [API REST di Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticazione di webhook
TThe webhook può eseguire l'autenticazione tramite uno di questi metodi:

1. **Autorizzazione basata su token** - webhook URI viene salvato con un ID token, ad esempio. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **Autorizzazione di base** - webhook URI viene salvato con un nome utente e password, ad esempio. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schema payload
Operazione POST contiene i seguenti payload JSON e lo schema per tutti gli avvisi basati su registro attività. Questo schema è simile a quella utilizzata per gli avvisi basati su unità di misura metriche.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

|Nome dell'elemento       |Descrizione|
|---                |---|
|stato             |Usato per gli avvisi metrici. Sempre impostata su "attivato" per gli avvisi di registro attività.|
|scelta rapida            |Contesto dell'evento.|
|resourceProviderName|Provider di risorse della risorsa interessata.|
|conditionType      |Sempre "evento".|
|nome               |Nome della regola di avviso.|
|ID                 |ID risorsa dell'avviso.|
|Descrizione        |Descrizione dell'avviso come set durante la creazione dell'avviso.|
|subscriptionId     |ID abbonamento Azure.|
|timestamp          |Ora in cui l'evento è stato generato dal servizio di Azure che la richiesta di elaborazione.|
|resourceId         |ID risorsa della risorsa interessata.|
|resourceGroupName  |Nome del gruppo di risorse per la risorsa interessata|
|proprietà         |Set di `<Key, Value>` coppie di parole (ad esempio `Dictionary<String, String>`) che include dettagli sull'evento.|
|evento              |Elemento che contiene i metadati sull'evento.|
|autorizzazione      |Proprietà RBAC dell'evento. Sono incluse in genere l'azione"", "ruolo" e "ambito".|
|categoria           |Categoria dell'evento. Valori supportati includono: amministrativi, un avviso, sicurezza, ServiceHealth, Recommendation.|
|chiamante             |Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, attestazioni UPN o Richiedi SPN in base alla disponibilità. Può essere null per determinate chiamate di sistema.|
|correlationId      |In genere GUID in formato stringa. Gli eventi con correlationId appartengono alla stessa operazione più grande e in genere condividono un correlationId.|
|eventDescription   |Descrizione testo statico dell'evento.|
|eventDataId        |Identificatore univoco per l'evento.|
|eventSource        |Nome del servizio Azure o infrastruttura che ha generato l'evento.|
|httpRequest        |In genere include "clientRequestId", "clientIpAddress" e "metodo" (metodo HTTP, ad esempio inserire).|
|livello              |Uno dei seguenti valori: "Critico", "Errore", "Avviso", "Informativo" e "Dettagliato."|
|operationId        |In genere GUID condivisi tra gli eventi corrispondente a singola operazione.|
|Invece      |Nome dell'operazione.|
|proprietà         |Proprietà dell'evento.|
|stato             |Stringa. Stato dell'operazione. I valori comuni includono: "A", "In corso", "Riuscita", "Non è riuscita", "Attiva", "Risolto".|
|stato secondario          |In genere include il codice di stato HTTP della chiamata resto corrispondente. Può inoltre includere altre stringhe che descrive un secondario. I valori di stato secondario comuni includono: OK (codice di stato HTTP: 200), creato (codice di stato HTTP: 201), accettato (codice di stato HTTP: 202), non il contenuto (codice di stato HTTP: 204), richiesta non valida (codice di stato HTTP: 400), non trovato (codice di stato HTTP: 404), conflitto (codice di stato HTTP: 409), errore interno del Server (codice di stato HTTP: 500), servizio non disponibile (codice di stato HTTP: 503), Timeout Gateway (codice di stato HTTP: 504)|

## <a name="next-steps"></a>Passaggi successivi
- [Ulteriori informazioni sul Log di attività](monitoring-overview-activity-logs.md)
- [Eseguire gli script di automazione di Azure (runbook) sugli avvisi Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Usare la logica App per inviare un SMS tramite Twilio da un avviso Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). In questo esempio è per gli avvisi metrici, ma può essere modificato per lavorare con un avviso registro attività.
- [Usare la logica App per inviare un messaggio da un avviso Azure margine di flessibilità](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). In questo esempio è per gli avvisi metrici, ma può essere modificato per lavorare con un avviso registro attività.
- [Usare la logica App per inviare un messaggio a una coda di Azure da un avviso Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). In questo esempio è per gli avvisi metrici, ma può essere modificato per lavorare con un avviso registro attività.
