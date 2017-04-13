<properties
   pageTitle="Servizio la comunicazione con l'API Web ASP.NET | Microsoft Azure"
   description="Informazioni su come implementare la comunicazione dei servizi dettagliata utilizzando l'API Web ASP.NET con OWIN hosting dell'API Services affidabile."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Guida introduttiva: servizi di assistenza tessuti Web API con OWIN hosting

Azure tessuti servizio inserisce la potenza di mano per decidere la modalità di servizi per comunicare con gli utenti e tra loro. In questa esercitazione viene illustrata l'implementazione la comunicazione dei servizi con API Web ASP.NET Apri interfaccia Web per .NET (OWIN) hosting API Services affidabile dell'infrastruttura di servizio. Si verrà analizzano eccessivamente la comunicazione collegabile servizi affidabili API. Si userà API Web in un esempio dettagliato da visualizzare come configurare un comunicare ascoltatore comunicazione personalizzata.


## <a name="introduction-to-web-api-in-service-fabric"></a>Introduzione alle Web API nella struttura di servizio

Dell'API Web ASP.NET è una struttura più diffusa e potente per la creazione di APIs HTTP nella parte superiore di .NET Framework. Se non si già familiarità con il framework, vedere [Introduzione a ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) per ottenere ulteriori informazioni.

Dell'API Web tessuti servizio è la stessa API Web ASP.NET che ben conosci e apprezzi. La differenza è come si *host* un'applicazione Web API. Non è possibile utilizzare Microsoft Internet Information Services (IIS). Per comprendere meglio la differenza, è possibile interromperla in due parti:

 1. L'applicazione Web API (inclusi controller e modelli)
 2. Host (il server web, in genere IIS)

Non modifica un'applicazione di API Web. Vi è alcuna differenza dalle applicazioni API Web che scritto in passato e dovrebbe essere possibile semplicemente muovere la maggior parte del codice dell'applicazione. Ma se è stata ospitati in IIS, in cui si host dell'applicazione potrebbe essere leggermente diversa da quella usata per. Prima di passare alla parte di hosting, per iniziare a conoscere ottenere qualcosa di più: applicazione API Web.


## <a name="create-the-application"></a>Creare l'applicazione

Prima di tutto creare una nuova applicazione di servizio tessuti con un singolo servizio senza informazioni sullo stato in Visual Studio 2015:

![Creare una nuova applicazione di servizio tessuti](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Un modello di Visual Studio per un servizio senza informazioni sullo stato utilizzando API Web è disponibile per l'utente. In questa esercitazione verrà creata un progetto API Web da zero cui risultato è quello che si otterrebbe se si seleziona questo modello.

Selezionare un nuovo progetto senza informazioni sullo stato del servizio per informazioni su come creare un progetto API Web da zero oppure è possibile iniziare con il modello di API Web senza informazioni sullo stato del servizio e semplicemente seguirli.  

![Creare un singolo servizio senza informazioni sullo stato](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Il primo passaggio consiste in modo da estrarre alcuni pacchetti NuGet per API Web. Il pacchetto di cui che si desidera utilizzare è Microsoft.AspNet.WebApi.OwinSelfHost. Il pacchetto include tutti i pacchetti di API Web necessari e i pacchetti di *host* . Questo sarà importante in un secondo momento.

![Creare API Web tramite la gestione di pacchetti NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Dopo aver installati i pacchetti, è possibile iniziare costruire la struttura del progetto API Web base. Se sono state usate API Web, la struttura del progetto avrà un aspetto molto semplice. Iniziare aggiungendo un `Controllers` un controller di valori semplici e directory:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Aggiungere una classe di avvio nella radice del progetto per registrare il routing, formattatori e altre impostazioni di configurazione. È inoltre in API Web va collegato all' *host*, che sarà necessario riconfigurare un secondo momento. 

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

Ecco fatto per la parte dell'applicazione. A questo punto è stato creato solo il layout di progetto di API Web base. Finora, non sono molto diversa da progetti API Web che scritto in passato o dal modello di base API Web. La regola business sono visualizzate nelle controller di modelli di e come di consueto.

A questo punto cosa viene eseguito sull'hosting in modo che è possibile eseguire?

## <a name="service-hosting"></a>Servizio di hosting

Nel servizio tessuti, il servizio viene eseguito in un *processo host del servizio*, un file eseguibile viene eseguito il codice servizio. Quando si scrive un servizio tramite l'API servizi affidabile, progetto di servizio viene compilato solo in un file eseguibile che registra il tipo di servizio e viene eseguito il codice. Ciò avviene nella maggior parte dei casi, quando si scrive un servizio su infrastruttura di servizio in .NET. Quando si apre Program.cs nel progetto senza informazioni sullo stato del servizio, verrà visualizzato:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se che l'aspetto troppo il punto di ingresso per un'applicazione console, è perché.

Ulteriori informazioni sul processo host del servizio e la registrazione del servizio non rientrano nell'ambito di questo articolo. È importante conoscere per ora che *che il codice servizio è in esecuzione nel proprio processo*.

## <a name="self-host-web-api-with-an-owin-host"></a>L'hosting indipendente API Web con un host OWIN

Dato che il codice dell'applicazione Web API è ospitato nel proprio processo, come associarlo a un server web? Immettere [OWIN](http://owin.org/). OWIN è semplicemente un contratto tra le applicazioni web .NET e server web. In genere quando viene utilizzato ASP.NET (fino a 5 MVC), l'applicazione web strettamente a IIS tramite System. Tuttavia, API Web implementata OWIN, pertanto è possibile scrivere un'applicazione web che è separata dal server web che ospita. Per questi motivi, è possibile utilizzare un server web OWIN *indipendente* che è possibile avviare un processo personalizzato. Questa operazione si adatta perfettamente con il modello di hosting servizio tessuti che descritta in precedenza.

In questo articolo si userà Katana come host OWIN per l'applicazione di API Web. Katana è un'implementazione di host OWIN Apri origine basata su [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) e di Windows [API Server HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).

> [AZURE.NOTE] Per ulteriori informazioni su Katana, passare al [sito Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Per una breve panoramica illustra come utilizzare Katana per l'hosting indipendente API Web, vedere [Utilizzare OWIN per Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## <a name="set-up-the-web-server"></a>Configurare il server web

API dei servizi di affidabile fornisce un punto di ingresso comunicazioni nel punto in cui è possibile collegare gli stack di comunicazione che consentono agli utenti e i client per la connessione al servizio:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

Il server web (e qualsiasi altro stack di comunicazioni che è usare in futuro, ad esempio WebSockets) devono utilizzare l'interfaccia ICommunicationListener per integrare correttamente con il sistema. I motivi diventa più evidenti nei passaggi seguenti.

Prima di tutto creare una classe denominata OwinCommunicationListener che implementa ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

L'interfaccia ICommunicationListener sono disponibili tre metodi per gestire un comunicare ascoltatore comunicazioni per il servizio:

 - *OpenAsync*. Attendere le richieste.
 - *CloseAsync*. Interrompere l'ascolto delle richieste, completare tutte le richieste in corso e arrestato correttamente.
 - *Interrompere l'operazione*. Cancellare tutto e interrompere immediatamente.

Per iniziare, aggiungere membri della classe privati per le operazioni che sarà necessario comunicare ascoltatore di funzionare. Questi verranno inizializzati tramite il costruttore e utilizzati in un secondo momento, quando si configura l'URL in attesa.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>Implementare OpenAsync

Per configurare il server web, è necessario due tipi di informazioni:

 - *Prefisso dell'URL di un percorso*. Questa operazione è facoltativa, è consigliabile per poter impostare questa ora in modo che è possibile ospitare più servizi web in modo sicuro all'interno dell'applicazione.
 - *Una porta*.

Prima di ottenere una porta per il server web, è importante comprendere che tessuti servizio fornisce un livello di applicazione che funge da buffer tra l'applicazione e il sistema operativo eseguito sul. Come tale servizio tessuti offre un modo per configurare *i punti finali* per i servizi. Servizio tessuti garantisce che gli endpoint disponibili per il servizio da utilizzare. In questo modo, non è necessario configurarli personalmente nell'ambiente del sistema operativo sottostante. È possibile ospitare facilmente l'applicazione di servizio tessuti in ambienti diversi senza dover apportare le modifiche all'applicazione. (Ad esempio, è possibile ospitare la stessa applicazione in Azure o in Data Center.)

Configurare un endpoint HTTP PackageRoot\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Questo passaggio è importante perché il processo di host del servizio viene eseguito con le credenziali con restrizioni (servizio di rete in Windows). Ciò significa che il servizio non sarà possibile accedere a configurare un endpoint HTTP nella propria. Tramite la configurazione dell'endpoint servizio tessuti è in grado di configurare l'elenco di controllo di accesso (ACL) per l'URL che il servizio ascolterà sulla. Servizio tessuti offre una posizione standard per configurare i punti finali.


Di nuovo in OwinCommunicationListener.cs, è possibile iniziare l'implementazione OpenAsync. Si tratta in cui si avvia il server web. Prima di tutto, ottenere le informazioni sull'endpoint e creare l'URL che il servizio ascolterà sulla. L'URL sarà diversa a seconda che comunicare ascoltatore venga utilizzato in un servizio senza informazioni sullo stato o in un servizio informazioni sullo stato. Per un servizio informazioni sullo stato, comunicare ascoltatore deve creare un indirizzo univoco per ogni replica di informazioni sullo stato del servizio che è in attesa. Per i servizi senza informazioni sullo stato, l'indirizzo può essere molto più semplice. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Si noti che viene usata "http://+". Si tratta per assicurarsi che il server web è in attesa su tutti gli indirizzi disponibili, inclusi host locale, il nome di dominio completo e IP computer.

L'implementazione di OpenAsync è uno dei motivi principali perché il server web (o qualsiasi stack di comunicazioni) è implementata una ICommunicationListener, anziché la sola aperto direttamente da `RunAsync()` nel servizio. Il valore restituito da OpenAsync è l'indirizzo a cui il server web è in attesa. Quando questo indirizzo viene restituito al sistema, registra l'indirizzo con il servizio. Servizio tessuti fornisce un'API che consente ai client e altri servizi quindi chiedere queste informazioni per l'indirizzo in base al nome del servizio. È importante perché l'indirizzo del servizio non è statico. Servizi vengono spostati all'interno del cluster per scopi di bilanciamento del carico e la disponibilità delle risorse. Si tratta del meccanismo che consente di risolvere l'indirizzo di attesa per un servizio client.

Tenendo presente, OpenAsync avvia il server web e restituisce l'indirizzo a cui è in attesa. Nota è attesa su "http://+", ma prima che OpenAsync restituisce l'indirizzo di "+" viene sostituito con il IP o il nome di dominio completo del nodo si trova attualmente. L'indirizzo a cui viene restituito da questo metodo è registrato nel sistema. È inoltre cosa client e altri servizi visualizzato quando si richiede l'indirizzo del servizio. Per i client a cui connettersi correttamente, è necessario un effettivo IP o FQDN l'indirizzo.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Si noti che fa riferimento la classe di avvio passato a OwinCommunicationListener nel costruttore. Per eseguire l'avvio dell'applicazione API Web, questa istanza di avvio viene utilizzata il server web.

Il `ServiceEventSource.Current.Message()` riga verrà visualizzati nella finestra di eventi di diagnostica successivamente, quando si esegue l'applicazione per assicurarsi che il server web ha avviato correttamente.

## <a name="implement-closeasync-and-abort"></a>Implementare CloseAsync e interruzione

Infine, implementare CloseAsync e Interrompi per interrompere il server web. Il server web può essere interrotta eliminando il quadratino di server che è stato creato nel corso OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

In questo esempio implementazione CloseAsync e Interrompi interrompe semplicemente il server web. È possibile scegliere di eseguire un arresto in modo più efficace coordinato del server web in CloseAsync. Ad esempio, l'arresto potrebbe attendere le richieste in corso da completare prima l'invio.

## <a name="start-the-web-server"></a>Avviare il server web

A questo punto si è pronti creare e restituire un'istanza di OwinCommunicationListener per avviare il server web. Di nuovo in classe di servizio (WebService.cs), ignorare il `CreateServiceInstanceListeners()` metodo:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Verrà visualizzata nel punto in cui l' API Web *applicazione* e dei OWIN *host* infine soddisfano. Host (OwinCommunicationListener) viene assegnato un'istanza dell' *applicazione* (Web API) tramite la classe di avvio. Servizio tessuti gestisce quindi il ciclo di vita. Questo stesso modello può essere seguito in genere con qualsiasi stack di comunicazione.

## <a name="put-it-all-together"></a>In conclusione

In questo esempio, non è necessario eseguire alcuna `RunAsync()` metodo, in modo che eseguono l'override semplicemente può essere rimossa.

L'implementazione del servizio finale dovrebbe essere molto semplice. È necessario solo creare comunicare ascoltatore comunicazione:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

L'intero `OwinCommunicationListener` classe:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

Ora che è stato inserito tutti gli elementi in posizione, il progetto dovrebbe essere simile a un'applicazione Web API tipica con punti di ingresso affidabile API dei servizi e un host OWIN:


![Dell'API Web con punti di ingresso affidabile API dei servizi e host OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>Eseguire e connettersi tramite un web browser

Se non è fatto, [configurare l'ambiente di sviluppo](service-fabric-get-started.md).


È possibile compilare e distribuire il servizio. Premere **F5** in Visual Studio per creare e distribuire l'applicazione. Nella finestra di eventi di diagnostica, verrà visualizzato un messaggio che indica che il server web aperto in http://localhost:8281 /.


![Finestra di Visual Studio eventi di diagnostica](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Se la porta è già stato aperto da un altro processo nel computer in uso, potrebbe essere visualizzato un errore di seguito. Indica che non è stato aperto comunicare ascoltatore. In tal caso, provare a utilizzare una porta diversa per la configurazione dell'endpoint in ServiceManifest.xml.


Dopo il servizio è in esecuzione, aprire un browser e passare all' [api/http://localhost:8281/valori](http://localhost:8281/api/values) per eseguire il test.

## <a name="scale-it-out"></a>Ridimensionarla indietro

Scalabilità senza informazioni sullo stato web App in genere significa aggiungendo più macchine e rotazione dei web App su di essi. Motore di tessuti servizio eseguire questa operazione automaticamente ogni volta che verranno aggiunti nuovi nodi in un cluster. Quando si creano istanze di un servizio senza informazioni sullo stato, è possibile specificare il numero di istanze che si desidera creare. Servizio tessuti inserisce il numero di istanze nodi del cluster. E garantisce di non creare più istanze su qualsiasi un nodo. È inoltre possibile indicare tessuti servizio per creare un'istanza in ogni nodo specificando **-1** per il conteggio delle istanze. In questo modo che ogni volta che si aggiungono nodi ridimensionare il cluster, un'istanza del servizio senza informazioni sullo stato nel verrà creata i nuovi nodi. Questo valore è una proprietà dell'istanza del servizio, in modo che sia impostata quando si crea un'istanza del servizio. È possibile eseguire questa operazione tramite PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

È anche possibile eseguire questa operazione quando si definisce un servizio predefinito in un progetto senza informazioni sullo stato del servizio Visual Studio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Per ulteriori informazioni su come creare applicazione e istanze del servizio, vedere [distribuire un'applicazione](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Passaggi successivi

[Il debug dell'applicazione di servizio tessuti utilizzando Visual Studio](service-fabric-debugging-your-application.md)
