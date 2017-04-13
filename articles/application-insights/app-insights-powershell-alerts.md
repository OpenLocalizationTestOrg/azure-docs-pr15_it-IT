<properties
    pageTitle="Usare Powershell per impostare gli avvisi nell'applicazione approfondimenti"
    description="Automatizzare la configurazione dell'applicazione approfondimenti per ottenere i messaggi di posta elettronica sulle modifiche metriche."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Usare PowerShell per impostare gli avvisi nell'applicazione approfondimenti

È possibile automatizzare la configurazione di [avvisi](app-insights-alerts.md) in [Visual Studio applicazione approfondimenti](app-insights-overview.md).

Inoltre, è possibile [impostare webhooks per automatizzare la risposta a un avviso](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="one-time-setup"></a>Iniziali di configurazione

Se non è stato usato PowerShell con l'abbonamento Azure prima di:

Installare il modulo di Azure Powershell nel computer in cui si vuole eseguire gli script.

 * Installare [installazione guidata piattaforma Web Microsoft (v5 o versioni successive)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Utilizzare per installare Microsoft Azure Powershell


## <a name="connect-to-azure"></a>Connettersi a Azure

Avviare PowerShell Azure e [connettersi al proprio abbonamento](../powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>Ricevere avvisi

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Aggiungere un avviso


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>Esempio 1

Inviare tramite posta elettronica personali se la risposta del server per le richieste HTTP, Media, 5 minuti, è inferiore a 1 secondo. La risorsa applicazione approfondimenti è denominata IceCreamWebApp e si trova nel gruppo risorse Fabrikam. Si è il proprietario della sottoscrizione Azure.

GUID è l'ID di abbonamento (non la chiave strumentazione dell'applicazione).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Esempio 2

Dispone di un'applicazione in cui utilizzare [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) per segnalare un'unità di misura metriche denominato "salesPerHour". Inviare che un messaggio di posta elettronica ai miei colleghi, se "salesPerHour" inferiore a 100, calcolare la media di supera le 24 ore.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Utilizzare la stessa regola per la metrica segnalata tramite il [parametro misura](app-insights-api-custom-events-metrics.md#properties) della verifica un'altra chiamata, ad esempio TrackEvent o trackPageView.

## <a name="metric-names"></a>Nomi metrici

Nome metriche | Nome della schermata | Descrizione
---|---|---
`basicExceptionBrowser.count`|Eccezioni browser|Numero di eccezioni non rilevate nel browser.
`basicExceptionServer.count`|Eccezioni di server|Conteggio di eccezioni non gestite generate dall'app
`clientPerformance.clientProcess.value`|Tempo di elaborazione client|Tempo che intercorre tra la ricezione ultimo byte di un documento finché non viene caricato DOM. Richieste asincrone possono comunque essere la trasformazione.
`clientPerformance.networkConnection.value`|Tempo di connessione di rete carico pagina| Tempo il browser per connettersi alla rete. Può essere 0 se memorizzati nella cache.
`clientPerformance.receiveRequest.value`|Ricezione di tempo di risposta| Tempo tra il browser invio richiesta per iniziare a ricevere risposta.
`clientPerformance.sendRequest.value`|Invio di tempo richiesta| Tempo impiegato dal browser per inviare una richiesta.
`clientPerformance.total.value`|Fase di caricamento di pagina del browser|Ora da richiesta utente finché non vengono caricate DOM, fogli di stile, script e immagini.
`performanceCounter.available_bytes.value`|Memoria disponibile|Memoria fisica immediatamente disponibile per un processo o per il sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Frequenza IO processo|Totali byte al secondo leggere e scrivere i file, rete e dispositivi.
`performanceCounter.number_of_exceps_thrown_per_sec`|tasso di eccezione|Eccezioni al secondo.
`performanceCounter.percentage_processor_time.value`|Processo CPU|La percentuale di tempo di tutti i thread di processo utilizzato dal processore per eseguire istruzioni per il processo di applicazioni.
`performanceCounter.percentage_processor_total.value`|Processore|La percentuale di tempo impiegato dal processore in thread attivi.
`performanceCounter.process_private_bytes.value`|Byte privati del processo|Memoria esclusivamente assegnata ai processi dell'applicazione.
`performanceCounter.request_execution_time.value`|Tempo di esecuzione richiesta ASP.NET|Tempo di esecuzione della richiesta più recente.
`performanceCounter.requests_in_application_queue.value`|Richieste di ASP.NET nella coda di esecuzione|Lunghezza della coda di richiesta dell'applicazione.
`performanceCounter.requests_per_sec`|Frequenza di richieste ASP.NET|Tasso di tutte le richieste per l'applicazione al secondo da ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Errori di relazione|Conteggio delle chiamate non riuscite effettuate dall'applicazione server a risorse esterne.
`request.duration`|Tempo di risposta server|Tempo che intercorre tra la ricezione di una richiesta HTTP e finitura inviare la risposta.
`request.rate`|Frequenza richieste|Tasso di tutte le richieste per l'applicazione al secondo.
`requestFailed.count`|Richieste non riuscite|Conteggio di richieste che hanno generato un codice di risposta > = 400
`view.count`|Visualizzazioni di una pagina|Numero di richieste di utente di client per una pagina web. Il traffico sintetico viene filtrato.
{il nome metrico personalizzato}|{Nome metrico}|Il valore metrico segnalato da [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) o nel [parametro le misure di una chiamata di verifica](app-insights-api-custom-events-metrics.md#properties).

I criteri di misurazione vengono inviati da moduli di telemetria diversi:

Gruppo metrica | Modulo di Raccoglitore
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>visualizzazione | [Browser JavaScript](app-insights-javascript.md)
performanceCounter | [Prestazioni](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dipendenza](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
richiesta,<br/>RequestFailed si è verificato|[Richiesta di server](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

È possibile [automatizzare la risposta a un avviso](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure eseguiranno una chiamata un indirizzo web di propria scelta quando viene generato un avviso.

## <a name="see-also"></a>Vedere anche


* [Script per configurare l'applicazione approfondimenti](app-insights-powershell-script-create-resource.md)
* [Creare approfondimenti applicazione e risorse di test web dai modelli](app-insights-powershell.md)
* [Automatizzare aggancio diagnostica Microsoft Azure a informazioni dettagliate sui applicazione](app-insights-powershell-azure-diagnostics.md)
* [Automatizzare la risposta a un avviso](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
