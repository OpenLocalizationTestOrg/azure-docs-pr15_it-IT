<properties 
    pageTitle="Configurazione dell'applicazione approfondimenti SDK con ApplicationInsights.config o XML" 
    description="Attivare o disattivare i moduli di raccolta dati e aggiungere contatori e gli altri parametri" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurare l'applicazione approfondimenti SDK con ApplicationInsights.config o XML

Applicazione approfondimenti .NET SDK è costituito da un numero di pacchetti NuGet. Il [pacchetto di base](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fornisce l'API per l'invio di telemetria a informazioni dettagliate sui applicazione. [Pacchetti aggiuntivi](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornisce telemetria _moduli_ e _inizializzatori_ per tenere traccia automaticamente di telemetria dall'applicazione e il relativo contesto. Modificando il file di configurazione, è possibile abilitare o disabilitare gli inizializzatori e moduli di telemetria e impostare i parametri per alcuni di essi.

Il file di configurazione è denominato `ApplicationInsights.config` o `ApplicationInsights.xml`, a seconda del tipo di applicazione. Vengono automaticamente aggiunti al progetto quando si [Installa la maggior parte delle versioni di SDK][start]. Viene aggiunto anche per un'app web dal [Controllo dello stato di un server IIS][redfield], o se si seleziona [l'estensione per un sito Web di Azure o macchine Virtuali](app-insights-azure-web-apps.md)di informazioni dettagliate sui termini.

Non è presente un file equivalente per controllare [SDK in una pagina web][client].

In questo documento vengono descritte le sezioni che viene visualizzato nella configurazione del file, come si controllano i componenti di SDK, e di pacchetti da NuGet caricare tali componenti.

## <a name="telemetry-modules-aspnet"></a>Moduli di telemetria (ASP.NET)

Ogni modulo di telemetria raccoglie uno specifico tipo di dati e viene utilizzata l'API principale per inviare i dati. I moduli vengono installati per diversi pacchetti NuGet, aggiungere anche le righe desiderate per il file config.

Esiste un nodo nel file di configurazione per ogni modulo. Per disattivare un modulo, eliminare il nodo o impostarla come commento.



### <a name="dependency-tracking"></a>Tenere traccia delle dipendenze

[Tenere traccia delle dipendenze](app-insights-dependencies.md) raccoglie telemetria sulle chiamate che l'app effettuate al database e servizi esterni e database. Per consentire questo modulo per l'uso in un server IIS, è necessario [installare controllo dello stato][redfield]. Per utilizzarla nelle App web Azure o macchine virtuali, [Selezionare l'estensione approfondimenti applicazione](app-insights-azure-web-apps.md).

È inoltre possibile scrivere il proprio dipendenza verifica codice utilizzando l' [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Pacchetto NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Raccolta di prestazioni

[Consente di raccogliere contatori delle prestazioni di sistema](app-insights-performance-counters.md) , ad esempio carico CPU e memoria rete da installazioni IIS. È possibile specificare quali contatori da raccogliere, inclusi contatori che è stata configurata se stessi.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Pacchetto NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .


### <a name="application-insights-diagnostics-telemetry"></a>Applicazione approfondimenti diagnostica telemetria

Il `DiagnosticsTelemetryModule` segnalati errori nel codice strumentazione approfondimenti applicazione stesso. Ad esempio, se il codice non è possibile accedere ai contatori prestazioni o un `ITelemetryInitializer` viene generata un'eccezione. Telemetria traccia verrà registrata in questo modulo viene visualizzato nella [Ricerca diagnostica][diagnostic]. Invia dati di diagnostica per dc.services.vsallin.net.
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* Pacchetto NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se si installa solo questo pacchetto, non viene creato automaticamente il file ApplicationInsights.config. 

### <a name="developer-mode"></a>Modalità di sviluppo

`DeveloperModeWithDebuggerAttachedTelemetryModule`forza approfondimenti applicazione `TelemetryChannel` per inviare i dati immediatamente, elemento di uno telemetria alla volta, quando un debugger al processo di applicazione. Questo riduce la quantità di tempo tra il momento quando l'applicazione rileva telemetria e quando viene visualizzata nel portale di informazioni approfondite dell'applicazione. Si ha un sovraccarico significativo la larghezza di banda della CPU e rete.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Applicazione approfondimenti Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacchetto NuGet

### <a name="web-request-tracking"></a>La gestione delle richieste Web

Visualizza il [codice di tempo e risultati risposta](app-insights-asp-net.md) richieste HTTP. 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Pacchetto NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

### <a name="exception-tracking"></a>Eccezione rilevamento modifiche

`ExceptionTrackingTelemetryModule`tracce eccezioni non gestite nelle applicazioni web. Vedere [errori ed eccezioni][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Pacchetto NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-tiene traccia delle [eccezioni di attività non osservato](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-tiene traccia delle eccezioni non gestite per i ruoli di lavoro, servizi di windows e applicazioni console.
* [Applicazione approfondimenti Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacchetto NuGet.

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

Il pacchetto Microsoft.ApplicationInsights fornisce [API principale](https://msdn.microsoft.com/library/mt420197.aspx) di SDK. È inoltre possibile [utilizzare per definire il proprio telemetria](app-insights-api-custom-events-metrics.md)altri moduli di telemetria utilizzano questo.

* Nessuna voce ApplicationInsights.config.
* Pacchetto NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se si installa solo questo NuGet, viene generato alcun file config.

## <a name="telemetry-channel"></a>Canale di telemetria

Il canale di telemetria gestisce il buffer e la trasmissione di telemetria per il servizio di informazioni approfondite dell'applicazione. 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`è il canale predefinito per i servizi. Buffer di dati in memoria.
* `Microsoft.ApplicationInsights.PersistenceChannel`costituisce un'alternativa per applicazioni console. È possibile salvare qualsiasi tipo di dati unflushed in un archivio permanente quando l'app viene chiusa verso il basso e invierà ad esso quando l'app viene riavviata.


## <a name="telemetry-initializers-aspnet"></a>Inizializzatori di telemetria (ASP.NET)

Inizializzatori di telemetria impostare le proprietà di contesto che vengono inviate insieme a tutti gli elementi di telemetria. 

È possibile [scrivere i proprio inizializzatori](app-insights-api-filtering-sampling.md#add-properties) per impostare le proprietà di contesto.

Inizializzatori standard sono tutte impostati tramite i pacchetti Web o WindowsServer NuGet:


* `AccountIdTelemetryInitializer`Imposta la proprietà AccountId.
* `AuthenticatedUserIdTelemetryInitializer`Imposta la proprietà AuthenticatedUserId l'impostazione SDK JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer`aggiornamenti di `RoleName` e `RoleInstance` proprietà del `Device` contesto per tutti gli elementi di telemetria con informazioni estratte dall'ambiente di runtime Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`aggiornamenti il `Version` proprietà del `Component` contesto per tutti gli elementi di telemetria con il valore estratte dalla `BuildInfo.config` file generato da MS Build.
* `ClientIpHeaderTelemetryInitializer`aggiornamenti `Ip` proprietà del `Location` in base a contesto di tutti gli elementi di telemetria il `X-Forwarded-For` intestazione HTTP della richiesta.
* `DeviceTelemetryInitializer`Aggiorna le proprietà seguenti del `Device` contesto per tutti gli elementi di telemetria.
 - `Type`è impostata su "PC"
 - `Id`è il nome di dominio del computer in cui viene eseguita l'applicazione web.
 - `OemName`impostare il valore estratte dalla `Win32_ComputerSystem.Manufacturer` campo utilizzando WMI.
 - `Model`impostare il valore estratte dalla `Win32_ComputerSystem.Model` campo utilizzando WMI.
 - `NetworkType`impostare il valore estratte dalla `NetworkInterface`.
 - `Language`impostare il nome della `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`aggiornamenti di `RoleInstance` proprietà del `Device` contesto per tutti gli elementi di telemetria con il nome di dominio del computer in cui viene eseguita l'applicazione web.
* `OperationNameTelemetryInitializer`aggiornamenti il `Name` proprietà del `RequestTelemetry` e la `Name` proprietà del `Operation` contesto di tutti gli elementi di telemetria in base a metodo HTTP, nonché i nomi dei controller MVC ASP.NET e azione richiamato per elaborare la richiesta.
* `OperationIdTelemetryInitializer`o `OperationCorrelationTelemetryInitializer` gli aggiornamenti di `Operation.Id` proprietà di contesto di tutti gli elementi di telemetria revisioni durante la gestione di una richiesta con generato automaticamente `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`aggiornamenti di `Id` proprietà del `Session` contesto per tutti gli elementi di telemetria con valore estratte dalla `ai_session` cookie generato dal codice di strumentazione ApplicationInsights JavaScript in esecuzione nel browser dell'utente. 
* `SyntheticTelemetryInitializer`o `SyntheticUserAgentTelemetryInitializer` gli aggiornamenti di `User`, `Session` e `Operation` proprietà contesti di tutti gli elementi di telemetria revisioni durante la gestione di una richiesta da un'origine sintetica, ad esempio una disponibilità testare o bot motore di ricerca. Per impostazione predefinita, [Metriche Explorer](app-insights-metrics-explorer.md) non vengono visualizzati telemetria sintetico. 

    Il `<Filters>` impostare le proprietà delle richieste di identificazione.
* `UserAgentTelemetryInitializer`aggiornamenti di `UserAgent` proprietà del `User` contesto di tutti gli elementi di telemetria in base la `User-Agent` intestazione HTTP della richiesta.
* `UserTelemetryInitializer`aggiornamenti di `Id` e `AcquisitionDate` proprietà di `User` contesto per tutti gli elementi di telemetria con valori estratti dalla `ai_user` cookie generato dal codice di strumentazione applicazione approfondimenti JavaScript in esecuzione nel browser dell'utente.
* `WebTestTelemetryInitializer`Imposta le proprietà di origine sintetici per le richieste HTTP provenienti da [test di disponibilità](app-insights-monitor-web-app-availability.md), l'id di sessione e id utente.
Il `<Filters>` impostare le proprietà delle richieste di identificazione.

## <a name="telemetry-processors-aspnet"></a>Processori di telemetria (ASP.NET)

Processori di telemetria possono filtrare e modificare ogni elemento di telemetria prima di inviarlo da SDK al portale.

È possibile [scrivere processori di telemetria](app-insights-api-filtering-sampling.md#filtering).


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processore di telemetria campioni adattata (da 2.0.0-beta3)

Questo è attivato per impostazione predefinita. Se l'app invia numerose telemetria, questo processore Rimuove parte di esso.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Il parametro fornisce la destinazione che l'algoritmo cerca di raggiungere. Ogni istanza di SDK funziona in modo indipendente, in modo se il server appartiene a un cluster di diversi computer, il volume effettivo di telemetria verrà moltiplicato conseguenza.

[Ulteriori informazioni sui campioni](app-insights-sampling.md).



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processore di telemetria campioni tasso fisso (da 2.0.0-beta1)

È inoltre disponibile un [processore di telemetria campioni](app-insights-api-filtering-sampling.md#sampling) standard (da 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parametri del canale (Java)

Questi parametri influiscono sulla modalità SDK linguaggio deve archiviare e cancellare i dati di telemetria raccoglie.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

Il numero di elementi di telemetria che possono essere archiviati in archiviazione in memoria del SDK. Quando si raggiunge questo numero, viene svuotato il buffer di telemetria, vale a dire gli elementi di telemetria inviati al server approfondimenti applicazione.

-   Min: 1
-   Max: 1000
-   Predefinito: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

Determina con quale frequenza i dati archiviati in archiviazione in memoria devono essere scaricati (inviati a informazioni dettagliate sui applicazione).

-   Min: 1
-   Max: 300
-   Predefinito: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

Determina le dimensioni massime in MB reso disponibile in un archivio permanente sul disco locale. Questo spazio di archiviazione viene utilizzato per mantenere gli elementi di telemetria che non è stata trasmessa all'endpoint approfondimenti applicazione. Quando vengono soddisfatte le dimensioni dello spazio di archiviazione, nuovi elementi di telemetria verranno ignorati.

-   Min: 1
-   Max: 100
-   Predefinito: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

Questa impostazione determina la risorsa applicazione approfondimenti in cui i dati verranno visualizzati. In genere si crea una risorsa separata, con una chiave separata per ognuna delle applicazioni.

Se si desidera impostare la chiave in modo dinamico, ad esempio, se si desidera inviare i risultati dell'applicazione diverse risorse, è possibile omettere la chiave dal file di configurazione e impostarlo nel codice.

Per impostare la chiave per tutte le istanze di TelemetryClient, inclusi i moduli di telemetria standard, impostare la chiave TelemetryConfiguration.Active. Eseguire questa operazione in un metodo di inizializzazione, ad esempio global.aspx.cs in un servizio ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Se si vuole semplicemente inviare un determinato set di eventi a una risorsa diversa, è possibile impostare la chiave per un TelemetryClient specifico:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Altre informazioni sull'API][api].

Per ottenere una nuova chiave, [creare una nuova risorsa nel portale di applicazione approfondimenti][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

