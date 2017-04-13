<properties 
    pageTitle="Applicazione di filtri e pre-elaborazione in applicazione approfondimenti SDK | Microsoft Azure" 
    description="Scrivere processori di telemetria e inizializzatori di telemetria per SDK per filtrare o aggiungere proprietà ai dati prima di telemetria viene inviata al portale di informazioni approfondite dell'applicazione." 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Applicazione di filtri e pre-elaborazione telemetria in SDK approfondimenti applicazione

*Informazioni dettagliate sui applicazione è in anteprima.*

È possibile scrivere e configurare i plug-in per SDK approfondimenti applicazione personalizzare l'acquisizione ed elaborata prima di inviarlo al servizio di applicazione approfondimenti telemetria. 

Queste caratteristiche sono attualmente disponibili per SDK ASP.NET.

* [Campionamento](app-insights-sampling.md) consente di ridurre il volume di telemetria senza influenzare le statistiche. Conserva insieme correlati punti dati in modo che consente di spostarsi tra di esse viene rilevato un problema. Nel portale di conteggi totali vengono moltiplicati per compensa il campione.
* [Filtro con processori di telemetria](#filtering) consente di selezionare o modificare telemetria in SDK prima di inviarlo al server. Ad esempio, è Impossibile ridurre il volume di telemetria escludendo le richieste dal robot. Ma il filtro è un approccio più semplice per ridurre il traffico di esempio. Consente un maggiore controllo quali trasmissione, ma è necessario tenere presente che interessa le statistiche - ad esempio, se filtrare tutte le richieste di esito negativo.
* [Inizializzatori di telemetria Aggiungi proprietà](#add-properties) a qualsiasi telemetria inviato dell'App, tra cui telemetria da moduli standard. Ad esempio, è possibile aggiungere valori calcolati. o i numeri di versione in base al quale filtrare i dati nel portale.
* [L'API SDK](app-insights-api-custom-events-metrics.md) viene utilizzato per inviare metriche ed eventi personalizzati.


Prima di iniziare:

* Installare l' [applicazione approfondimenti SDK per ASP.NET v2](app-insights-asp-net.md) nell'app. 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>Filtro: ITelemetryProcessor

Questa tecnica offre un controllo più diretto su cosa includere o meno dal flusso di telemetria. È possibile usare in combinazione con campione, o separatamente.

Per filtrare telemetria, scrivere un processore di telemetria e registrare con SDK. Tutti telemetria attraversa il processore e si può scegliere di eliminare dal flusso o Aggiungi proprietà. Sono inclusi telemetria da moduli standard, ad esempio il raccoglitore richiesta HTTP e il raccoglitore dipendenza, oltre che telemetria che è stato scritto personalmente. Ad esempio filtrare telemetria sulle richieste dal robot o chiamate dipendenza esito negativo. 

> [AZURE.WARNING] Filtro telemetria inviato da SDK utilizzando processori può inclinare le statistiche che viene visualizzato nel portale e rendere difficile da seguire elementi correlati.
> 
> Si consiglia di utilizza [campione](app-insights-sampling.md).

### <a name="create-a-telemetry-processor"></a>Creare un processore di telemetria

1. Verificare che il SDK approfondimenti applicazione di un progetto versione 2.0.0 o versione successiva. Pulsante destro del mouse sul progetto in Esplora soluzioni Visual Studio e scegliere Gestisci pacchetti NuGet. In Gestione pacchetto NuGet, selezionare Microsoft.ApplicationInsights.Web.

1. Per creare un filtro, implementare ITelemetryProcessor. Si tratta di un altro punto di estendibilità come modulo di telemetria, inizializzatore di telemetria e canale di telemetria. 

    Si noti che processori telemetria costruire una catena di elaborazione. Quando si crea un'istanza di un processore di telemetria, passare il processore successivo nella catena di un collegamento. Quando un punto dati di telemetria viene passato al metodo di processo, non il proprio lavoro e quindi chiama il processore di telemetria successivo nella catena.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Inserire in ApplicationInsights.config: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Questa è la stessa sezione in cui è inizializzare un filtro di esempio).

È possibile passare valori stringa dal file config fornendo pubblica proprietà denominate in una classe. 

> [AZURE.WARNING] Prestare attenzione per far corrispondere il nome del tipo e i nomi delle proprietà nel file config ai nomi di classe e proprietà nel codice. Se il file. config fa riferimento a un tipo inesistente di proprietà, il SDK automaticamente potrebbe non riuscire a inviare eventuali telemetria.

 
**In alternativa,** è possibile inizializzare il filtro nel codice. In una classe inizializzazione adatto, ad esempio AppStart in Global.asax.cs - inserire il processore nella catena di:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients creato dopo questo punto utilizzeranno i processori.

### <a name="example-filters"></a>Filtri di esempio

#### <a name="synthetic-requests"></a>Richieste di sintetiche

Filtrare i test web e BOT. Sebbene metriche Explorer offre l'opzione per filtrare le origini sintetiche, questa opzione si riduce il traffico la funzionalità di filtro in SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Autenticazione non riuscita

Filtrare le richieste con una risposta "401". 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrare rapidamente remoto dipendenza chiamate

Se si vuole solo per la diagnosi chiamate lente, escludere quelli veloci. 

> [AZURE.NOTE] Questo sarà inclinare statistiche viene visualizzato nel portale. Il grafico dipendenza si presenterà come se le chiamate dipendenza sono tutti gli errori.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticare i problemi di dipendenza

[In questo blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descrive un progetto per diagnosticare i problemi di dipendenza inviando automaticamente ping normale a dipendenze.


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>Aggiungere proprietà: ITelemetryInitializer

Utilizzare inizializzatori di telemetria per definire le proprietà globali inviati con tutti telemetria; e ignorare selezionato comportamento dei moduli di telemetria standard. 

Ad esempio approfondimenti applicazione pacchetto Web raccoglie telemetria sulle richieste HTTP. Per impostazione predefinita, contrassegna come non riuscito di qualsiasi richiesta con un codice di risposta > = 400. Ma se si desidera considerare 400 come esito positivo, è possibile specificare un inizializzatore di telemetria che imposta la proprietà Success.

Se si specifica un inizializzatore di telemetria, chiamarlo ogni volta che uno dei metodi Track*() si chiama. Sono inclusi metodi chiamati in base ai moduli di telemetria standard. Per convenzione, questi moduli non impostare le proprietà che è già stata impostata da un inizializzatore. 

**Definire l'inizializzatore**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Caricare l'inizializzatore**

In ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*In alternativa,* è possibile creare un'istanza di inizializzatore nel codice, ad esempio in Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Visualizzare un'area maggiore di questo esempio.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>Inizializzatori di telemetria JavaScript

*JavaScript*

Inserire un inizializzatore di telemetria immediatamente dopo il codice di inizializzazione ottenuto dal portale di: 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Per un riepilogo delle proprietà personalizzate non disponibili nel telemetryItem, vedere il [modello di dati](app-insights-export-data-model.md#lttelemetrytypegt).

È possibile aggiungere tanti inizializzatori desiderato. 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Che cos'è la differenza tra processori di telemetria e inizializzatori di telemetria?

* Esistono alcune sovrapposizioni in operazioni eseguibili con loro: entrambe può essere utilizzati per aggiungere proprietà telemetria.
* TelemetryInitializers sempre eseguite prima TelemetryProcessors.
* TelemetryProcessors consente di sostituire o eliminare un elemento di telemetria completamente.
* TelemetryProcessors non elaborare telemetria contatore prestazioni.



## <a name="persistence-channel"></a>Persistenza canale 

Se l'app viene eseguita nel punto in cui la connessione internet non è sempre disponibile o lenta, è consigliabile tramite il canale persistenza invece che con il canale in memoria predefinito. 

Il canale in memoria predefinito perde qualsiasi telemetria che non è stato inviato al momento si chiude l'app. Sebbene sia possibile utilizzare `Flush()` per tentare di inviare i dati rimanente nel buffer, ancora perde dati se nessuna connessione internet o se l'app viene chiuso prima di trasmissione è stata completata.

Se invece il canale persistenza buffer telemetria in un file, prima di inviarlo al portale di. `Flush()`garantisce che i dati vengono memorizzati nel file. Se uno qualsiasi dei dati non vengono inviate al momento si chiude l'app, rimane nel file. Al riavvio dell'app, i dati verranno inviati, quindi, nel caso di una connessione a internet. Dati sono indicati nel file lungo fino a quando non è disponibile una connessione. 

### <a name="to-use-the-persistence-channel"></a>Come utilizzare il canale persistenza

1. Importare il pacchetto NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Includere il codice nell'app, in una posizione appropriata inizializzazione:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Usare `telemetryClient.Flush()` prima la chiusura di app, per assicurarsi che dati venga inviati al portale di o salvare il file.

    Si noti che Flush icona per il canale persistenza ma asincrono per altri canali.

 
Il canale persistenza è ottimizzato per gli scenari di dispositivi, in cui il numero di eventi generati dall'applicazione è relativamente e la connessione è spesso origini non affidabile. Il canale verrà scrivere eventi sul disco in archiviazione affidabile prima di tutto e quindi il tentativo di inviarlo. 

#### <a name="example"></a>Esempio

Supponiamo che si desidera eseguire il monitoraggio delle eccezioni non gestite. Si sottoscrive il `UnhandledException` evento. Nella richiamata, includere una chiamata a svuotamento per assicurarsi che la telemetria viene mantenuto.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Quando l'app viene chiusa, verrà visualizzato un file in `%LocalAppData%\Microsoft\ApplicationInsights\`, che contiene gli eventi compressi. 
 
Al successivo avvio dell'applicazione, il canale sollevare il file e offrire telemetria a informazioni dettagliate sui applicazione in caso affermativo.

#### <a name="test-example"></a>Esempio di test

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Il codice del canale persistenza sia [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel). 


## <a name="reference-docs"></a>Documenti di riferimento

* [Introduzione all'API](app-insights-api-custom-events-metrics.md)

* [Guida di riferimento ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>Codice SDK

* [SDK di base di ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Passaggi successivi


* [Ricerca eventi e registri][diagnostic]
* [Esempio](app-insights-sampling.md)
* [Risoluzione dei problemi][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
