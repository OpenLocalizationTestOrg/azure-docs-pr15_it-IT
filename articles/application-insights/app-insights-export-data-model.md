<properties 
    pageTitle="Modello di dati di applicazione approfondimenti" 
    description="Vengono descritte le proprietà esportati da esportare continua in JSON e utilizzati come filtri." 
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
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>Modello di dati di applicazione approfondimenti esportazione

Questa tabella sono elencate le proprietà di telemetria inviati da SDK [Applicazione approfondimenti](app-insights-overview.md) al portale. Si noterà queste proprietà nell'output dei dati da [Esportare continua](app-insights-export-telemetry.md).
Vengono visualizzati anche in filtri delle proprietà in [Esplora risorse di unità di misura metriche](app-insights-metrics-explorer.md) e [Ricerca diagnostica](app-insights-diagnostic-search.md).

Punti da tenere presente:

* `[0]`nelle tabelle indica un punto del percorso in cui è necessario inserire un indice. ma non è sempre 0.
* Durate sono in decimi di microsecondo, pertanto 10000000 = = 1 secondo.
* Date e ore sono ora UTC e vengono fornite in formato ISO`yyyy-MM-DDThh:mm:ss.sssZ`

Sono disponibili diversi [esempi](app-insights-export-telemetry.md#code-samples) che illustrano come utilizzarli.



## <a name="example"></a>Esempio

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>Scelta rapida

Tutti i tipi di telemetria sono accompagnati da una sezione di scelta rapida. Non più di questi campi vengono trasmessi con ogni punto dati.



|Percorso|Tipo|Note|
|---|---|---|
| Context.Custom.Dimensions [0]  | oggetto]  | Coppie di parole chiave valore stringa impostato dal parametro di proprietà personalizzate. Lunghezza massima chiave 100, lunghezza massima di valori 1024. Più di 100 valori univoci, la proprietà è possibile eseguire la ricerca, ma non può essere usata per segmentazione. Chiavi di 200 Max per ikey.  |
| Context.Custom.Metrics [0]  | oggetto]  | Coppie di parole chiave valore impostato dal parametro di unità di misura personalizzata e tramite TrackMetrics. Lunghezza massima chiave 100 valori possono essere numerici. |
| context.data.eventTime | stringa | ORA UTC |
| context.data.isSynthetic | valore booleano | Richiesta sembra provenire da un test web o bot. |
| context.data.samplingRate | numero | Percentuale di telemetria generato da SDK viene inviata al portale. Intervallo 0,0 100,0.|
| Context.Device | oggetto | Dispositivo client |
| Context.Device.browser | stringa | Internet Explorer, Chrome,... |
| context.device.browserVersion | stringa | Chrome 48.0,... |
| context.device.deviceModel | stringa | |
| context.device.deviceName | stringa | |
| Context.Device.ID | stringa | |
| Context.Device.locale | stringa | en-GB, de-DE,... |
| Context.Device.Network | stringa | |
| context.device.oemName | stringa | |
| context.device.osVersion | stringa | Sistema operativo host |
| context.device.roleInstance | stringa | ID dell'host di server |
| context.device.roleName | stringa | |
| Context.Device.Type | stringa | PC, Browser,... |
| Context.Location | oggetto | Derivate dai clientip. |
| Context.Location.City | stringa | Derivato da clientip, se noti  |
| Context.Location.ClientIP | stringa | Ultima ottagono è resi anonimi su 0. |
| Context.Location.Continent | stringa | |
| Context.Location.Country | stringa | |
| Context.Location.province | stringa | Provincia |
| Context.Operation.ID | stringa | Gli elementi con lo stesso id operazione vengono visualizzati come elementi correlati nel portale. In genere l'id della richiesta. |
| Context.Operation.Name | stringa | nome URL o richiesta |
| context.operation.parentId | stringa | Consente di annidata elementi correlati. |
| Context.Session.ID | stringa | ID di un gruppo di operazioni dalla stessa origine. Entro 30 minuti senza un'operazione segnala la fine di una sessione. |
| context.session.isFirst | valore booleano | |
| context.user.accountAcquisitionDate | stringa | |
| context.user.anonAcquisitionDate | stringa | |
| context.user.anonId | stringa | |
| context.user.authAcquisitionDate | stringa | [Utente autenticato](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | valore booleano | |
| internal.data.documentVersion | stringa | |
| Internal.Data.ID | stringa | |



## <a name="events"></a>Eventi

Eventi personalizzati generati da [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event). 


|Percorso|Tipo|Note|
|---|---|---|
| Conteggio eventi [0] | numero intero | 100 / (Tasso_int[campioni](app-insights-sampling.md) ). Ad esempio 4 =&gt; 25%. |
| nome dell'evento [0] | stringa | Nome dell'evento.  Lunghezza massima consentita 250. |
| url evento [0] | stringa | |
| evento [0] urlData.base | stringa | |
| evento [0] urlData.host | stringa | |

## <a name="exceptions"></a>Eccezioni

Report [eccezioni](app-insights-asp-net-exceptions.md) nel server e nel browser. 


|Percorso|Tipo|Note|
|---|---|---|
| assembly basicException [0] | stringa | |
| conteggio basicException [0] | numero intero | 100 / (Tasso_int[campioni](app-insights-sampling.md) ). Ad esempio 4 =&gt; 25%. |
| exceptionGroup basicException [0] | stringa | |
| exceptionType basicException [0] | stringa | |stringa | |
| failedUserCodeMethod basicException [0] | stringa | |
| failedUserCodeAssembly basicException [0] | stringa | |
| handledAt basicException [0] | stringa | |
| hasFullStack basicException [0] | valore booleano | |
| id basicException [0] | stringa | |
| metodo basicException [0] | stringa | |
| messaggio basicException [0] | stringa | Messaggio dell'eccezione. Lunghezza massima consentita 10 KB.|
| outerExceptionMessage basicException [0] | stringa | |
| outerExceptionThrownAtAssembly basicException [0] | stringa | |
| outerExceptionThrownAtMethod basicException [0] | stringa | |
| outerExceptionType basicException [0] | stringa | |
| outerId basicException [0] | stringa | |
| assembly parsedStack [0] basicException [0] | stringa | |
| nome del file parsedStack [0] basicException [0] | stringa | |
| livello di parsedStack [0] basicException [0] | numero intero | |
| riga parsedStack [0] basicException [0] | numero intero | |
| metodo parsedStack [0] basicException [0] | stringa | |
| pila di basicException [0] | stringa | Lunghezza massima consentita 10k|
| typeName basicException [0] | stringa | |



## <a name="trace-messages"></a>Tenere traccia dei messaggi

Inviato da [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)e da [schede la registrazione](app-insights-asp-net-trace-logs.md).


|Percorso|Tipo|Note|
|---|---|---|
| messaggio [0] loggerName | stringa ||
| parametri messaggi [0] | stringa ||
| messaggio [0] non elaborato | stringa | Il messaggio di log, lunghezza massima 10 KB. |
| messaggio [0] severityLevel | stringa | |



## <a name="remote-dependency"></a>Dipendenza remoto

Inviato da TrackDependency. Usato per le prestazioni dei report e l'utilizzo di [chiamate a dipendenze](app-insights-asp-net-dependencies.md) nel server e AJAX chiama nel browser.

|Percorso|Tipo|Note|
|---|---|---|
| remoteDependency [0] asincrono | valore booleano | |
| Base remoteDependency [0] | stringa |  |
| commandName remoteDependency [0] | stringa | Ad esempio "home/indice" |
| conteggio remoteDependency [0] | numero intero | 100 / (Tasso_int[campioni](app-insights-sampling.md) ). Ad esempio 4 =&gt; 25%. |
| dependencyTypeName remoteDependency [0] | stringa | HTTP, SQL,... |
| durationMetric.value remoteDependency [0] | numero | Ora dalla chiamata fino al completamento della risposta dalla dipendenza |
| id remoteDependency [0] | stringa | |
| nome remoteDependency [0] | stringa | URL. Lunghezza massima consentita 250.|
| resultCode remoteDependency [0] | stringa | da dipendenza HTTP |
| success remoteDependency [0] | valore booleano | |
| tipo di remoteDependency [0] | stringa | HTTP, Sql,... |
| url remoteDependency [0] | stringa |  Lunghezza massima consentita 2000 |
| urlData.base remoteDependency [0] | stringa | Lunghezza massima consentita 2000 |
| urlData.hashTag remoteDependency [0] | stringa | |
| urlData.host remoteDependency [0] | stringa | Lunghezza massima consentita 200 |


## <a name="requests"></a>Richieste di

Inviato da [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Moduli standard consente di tempo di risposta server di report, misurate sul server. 


|Percorso|Tipo|Note|
|---|---|---|
| richiedere il numero [0] | numero intero | 100 / (Tasso_int[campioni](app-insights-sampling.md) ). Ad esempio: 4 =&gt; 25%. |
| richiedere durationMetric.value [0] | numero | Ora dalla richiesta arrivato alla risposta. 1e7 = = 1s |
| id richiesta [0] | stringa | Id operazione |
| nome della richiesta [0] | stringa | GET/POST + url base.  Lunghezza massima consentita 250 |
| richiedere responseCode [0] | numero intero | Risposta HTTP inviata al client |
| success richiesta [0] | valore booleano | Predefinito = = (responseCode &lt; 400) |
| url richiesta [0] | stringa | Non inclusi host |
| richiedere urlData.base [0] | stringa | |
| richiedere urlData.hashTag [0] | stringa |  |
| richiedere urlData.host [0] | stringa | |


## <a name="page-view-performance"></a>Prestazioni di visualizzazione pagina

Inviato dal browser. Misura il tempo per l'elaborazione di una pagina, dall'utente che ha avviato la richiesta per la visualizzazione completa (escluse le chiamate AJAX asincrono).

I valori di scelta rapida mostrano client del sistema operativo e versione del browser. 


|Percorso|Tipo|Note|
|---|---|---|
| clientProcess.value clientPerformance [0] | numero intero | Ora di fine di ricevere il codice HTML per la visualizzazione della pagina. |
| nome clientPerformance [0] | stringa | |
| networkConnection.value clientPerformance [0] | numero intero | Tempo necessario per stabilire una connessione di rete. |
| receiveRequest.value clientPerformance [0] | numero intero | Ora di fine di inviare la richiesta per in risposta a ricevere il codice HTML. |
| sendRequest.value clientPerformance [0] | numero intero | Ora da adottare per inviare la richiesta HTTP. |
| total.value clientPerformance [0] | numero intero | Ora di iniziare a inviare la convocazione per la visualizzazione della pagina. |
| url clientPerformance [0] | stringa | URL della richiesta |
| urlData.base clientPerformance [0] | stringa | |
| urlData.hashTag clientPerformance [0] | stringa | |
| urlData.host clientPerformance [0] | stringa | |
| urlData.protocol clientPerformance [0] | stringa | |

## <a name="page-views"></a>Visualizzazioni di una pagina

Inviati da trackPageView() o [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Percorso|Tipo|Note|
|---|---|---|
| conteggio delle viste [0] | numero intero | 100 / (Tasso_int[campioni](app-insights-sampling.md) ). Ad esempio 4 =&gt; 25%. |
| visualizzare durationMetric.value [0] | numero intero | Valore eventualmente impostato in trackPageView() o dalla startTrackPage() - stopTrackPage(). Non corrispondono clientPerformance valori. |
| nome della visualizzazione [0] | stringa | Titolo della pagina.  Lunghezza massima consentita 250 |
| url di una visualizzazione [0] | stringa | |
| visualizzare urlData.base [0] | stringa | |
| visualizzare urlData.hashTag [0] | stringa | |
| visualizzare urlData.host [0] | stringa | |



## <a name="availability"></a>Disponibilità

Report di [test web disponibilità](app-insights-monitor-web-app-availability.md).

|Percorso|Tipo|Note|
|---|---|---|
| disponibilità [0] availabilityMetric.name | stringa | disponibilità |
| disponibilità [0] availabilityMetric.value | numero |1.0 oppure 0,0 |
| conteggio di disponibilità [0] | numero intero | 100 / (Tasso_int[campioni](app-insights-sampling.md) ). Ad esempio 4 =&gt; 25%. |
| disponibilità [0] dataSizeMetric.name | stringa | |
| disponibilità [0] dataSizeMetric.value | numero intero | |
| disponibilità [0] durationMetric.name | stringa | |
| disponibilità [0] durationMetric.value | numero | Durata del test. 1e7 = = 1s |
| messaggio di disponibilità [0] | stringa | Errore di diagnostico |
| risultato della disponibilità [0] | stringa | Esito positivo o negativo |
| disponibilità [0] runLocation | stringa | Origine geografico della richiesta http |
| testName disponibilità [0] | stringa | |
| disponibilità [0] testRunId | stringa | |
| disponibilità [0] testTimestamp | stringa | |




## <a name="metrics"></a>Metrica

Generati da TrackMetric().

Il valore metrico si trova in context.custom.metrics[0]

Per esempio:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Informazioni sui valori metrici

Valori metriche, sia in report metriche e in un'altra vengono segnalati con una struttura di oggetto standard. Per esempio:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Attualmente - attraverso il può cambiare in futuro, in tutti i valori segnalati da moduli SDK standard, `count==1` e solo il `name` e `value` i campi sono utili. L'unico caso in cui sono diversi sarebbe se scrivere chiamate TrackMetric in cui è impostare altri parametri. 

Lo scopo degli altri campi è consentire metriche da aggregare nel SDK, per ridurre il traffico al portale. Ad esempio, si possono calcolare la media di diversi valori successivi prima dell'invio ogni rapporto metrica. Quindi di calcolare le funzioni min, max, deviazione standard e valore aggregato (sum o Media) e impostare numero al numero di valori rappresentati dal report. 

Nelle tabelle, abbiamo omesso conteggio utilizzata raramente campi, le funzioni min, max, deviazione standard e sampledValue.

Invece di pre-aggregazione metriche, è possibile utilizzare [campioni](app-insights-sampling.md) se è necessario per ridurre il volume di telemetria.


### <a name="durations"></a>Durata

Se non diversamente specificato, la durata è indicata in decimi di microsecondo, pertanto 10000000.0 indica 1 secondo.



## <a name="see-also"></a>Vedere anche

* [Informazioni dettagliate sui applicazione](app-insights-overview.md) 
* [Esportazione continuo](app-insights-export-telemetry.md)
* [Esempi di codice](app-insights-export-telemetry.md#code-samples)


