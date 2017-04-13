<properties 
    pageTitle="Monitoraggio delle prestazioni per le applicazioni desktop di Windows e l'uso" 
    description="Analisi utilizzo e le prestazioni dell'applicazione desktop di Windows con HockeyApp e approfondimenti applicazione." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Utilizzo di monitoraggio e le prestazioni nelle applicazioni Desktop di Windows

*Informazioni dettagliate sui applicazione è in anteprima.*

[Visual Studio applicazione approfondimenti](app-insights-overview.md) e [HockeyApp](https://hockeyapp.net) consentono di controllare l'applicazione distribuita per l'utilizzo e le prestazioni.

> [AZURE.IMPORTANT] È consigliabile [HockeyApp](https://hockeyapp.net) per la distribuzione e monitorare le app desktop e dispositivi. Con HockeyApp, è possibile gestire i commenti degli utenti, i test in corso e distribuzione, nonché monitorare i report di utilizzo e l'arresto anomalo. È anche possibile [esportare e il telemetria con Analitica della query](app-insights-hockeyapp-bridge-app.md).

> Sebbene telemetria da inviare a informazioni dettagliate sui applicazione da un'applicazione desktop, si tratta principalmente utile ai fini di debug e sperimentazione.


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Per inviare telemetria a informazioni dettagliate sui applicazione da un'applicazione di Windows

1. Nel [portale di Azure](https://portal.azure.com), [creazione di una risorsa approfondimenti applicazione](app-insights-create-new-resource.md). Tipo di applicazione scegliere app ASP.NET.
2. Richiedere una copia della chiave strumentazione. Trovare la chiave nell'elenco a discesa nozioni di base della nuova risorsa che appena creata. 
3. In Visual Studio, modificare i pacchetti NuGet del progetto app e aggiungere Microsoft.ApplicationInsights.WindowsServer. (O scegliere Microsoft.ApplicationInsights se si vuole semplicemente il bare API, senza i moduli di insieme standard di telemetria.)
4. Impostare la chiave strumentazione nel codice:

    `TelemetryConfiguration.Active.InstrumentationKey = "`*il tasto*`";` 

    o in ApplicationInsights.config (se è stato installato uno dei pacchetti di telemetria standard):
 
    `<InstrumentationKey>`*il tasto*`</InstrumentationKey>` 

    Se si utilizza ApplicationInsights.config, verificare che le in Esplora soluzioni sono impostate su **operazione di compilazione = contenuto, copia nella Directory di Output = copia**.
5. [Usare l'API](app-insights-api-custom-events-metrics.md) per l'invio di telemetria.
6. Eseguire l'app e vedere telemetria in risorsa creata nel portale di Azure.

## <a name="telemetry"></a>Esempio di codice

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un dashboard](app-insights-dashboards.md)
* [Ricerca di diagnostica](app-insights-diagnostic-search.md)
* [Esplorare le metriche](app-insights-metrics-explorer.md)
* [Scrivere query Analitica](app-insights-analytics.md)
 
