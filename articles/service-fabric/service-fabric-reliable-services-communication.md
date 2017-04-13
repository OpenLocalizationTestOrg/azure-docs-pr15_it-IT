<properties
   pageTitle="Panoramica di comunicazione affidabile servizi | Microsoft Azure"
   description="Panoramica del modello di comunicazione servizi affidabili, inclusi listener apertura sui servizi, come risolvere i punti finali e le comunicazioni tra i servizi."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>Come usare l'API di comunicazione tra servizi affidabili

Azure tessuti servizio come piattaforma è completamente indipendente sulle comunicazioni tra i servizi. Tutti i protocolli e pile accettabili, da UDP a HTTP. Dipende da sviluppatore del servizio per scegliere come devono comunicare servizi. La struttura dell'applicazione di servizi affidabili fornisce stack di comunicazione integrate, nonché API che è possibile utilizzare per creare i componenti di comunicazione personalizzata. 

## <a name="set-up-service-communication"></a>Configurare le comunicazioni di servizio

L'API servizi affidabile utilizza una semplice interfaccia per la comunicazione dei servizi. Per aprire un endpoint del servizio, è sufficiente implementare questa interfaccia:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

È quindi possibile aggiungere l'implementazione di comunicare ascoltatore comunicazione tramite la restituzione in override di un metodo di classe basate su servizi.

Per i servizi senza informazioni sullo stato:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Per i servizi di informazioni sullo stato:

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

In entrambi i casi, restituire un insieme di listener. In questo modo il servizio in ascolto più endpoint, potenzialmente utilizzando protocolli diversi, più listener. Ad esempio, potrebbe essere un comunicare ascoltatore HTTP di un ascolto di WebSocket separata. Ogni comunicare ascoltatore Ottiene un nome e l'insieme risultante di *nome: indirizzo* coppie di parole viene rappresentato come oggetto JSON quando un client richiede indirizzi di attesa per un'istanza del servizio o una partizione.

In un servizio senza l'override restituisce un insieme di ServiceInstanceListeners. Un ServiceInstanceListener contiene una funzione per creare un ICommunicationListener e fornisce un nome. Per i servizi di informazioni sullo stato, l'override restituisce un insieme di ServiceReplicaListeners. In questo modo leggermente diverso dalla corrispondente senza un ServiceReplicaListener è disponibile un'opzione per aprire un ICommunicationListener sulle repliche secondarie. Non solo è possibile utilizzare più listener di comunicazione in un servizio, ma è inoltre possibile specificare quali listener accettare le richieste sulle repliche secondarie e quelle ascolto solo su repliche principale.

Ad esempio, è possibile creare un ServiceRemotingListener che entrerà in chiamate RPC solo in repliche principale e comunicare ascoltatore una seconda, personalizzato che entrerà in lettura richiede sulle repliche secondarie su HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Quando si creano più listener di un servizio, ogni comunicare ascoltatore **deve** essere specificato un nome univoco.

Infine, descrivere i punti finali necessari per il servizio in cui il [servizio manifesto](service-fabric-application-model.md) sotto la sezione relativa all'endpoint.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Comunicare ascoltatore comunicazione possa accedere alle risorse endpoint assegnate a tale dalla `CodePackageActivationContext` nel `ServiceContext`. Comunicare ascoltatore quindi è possibile iniziare ad ascoltare le richieste quando viene aperto.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Risorse endpoint sono comuni per il pacchetto intero servizio e loro allocazione per tessuti servizio quando il pacchetto del servizio è attivato. Più repliche servizio ospitate in ServiceHost stesso possono condividere la stessa porta. Questo errore indica che comunicare ascoltatore comunicazioni deve supportare la condivisione della porta. È consigliabile eseguire questa operazione comunicare ascoltatore comunicazioni usare partizione ID e ID replica/istanza quando genera l'indirizzo di attesa.

### <a name="service-address-registration"></a>Registrazione indirizzo del servizio

Un servizio di sistema denominato *Naming Service* viene eseguito sul servizio tessuti cluster. Il servizio per la denominazione è un registrar per i servizi e gli indirizzi che ogni istanza o replica del servizio è in attesa. Quando il `OpenAsync` metodo di un `ICommunicationListener` completa, il suo ritorno valore viene registrata nel servizio di denominazione. Questo valore restituito che viene pubblicato nel servizio di denominazione è una stringa il cui valore verranno rimossi tutti. Questo valore stringa è quello che client vedranno quando chiedono l'indirizzo per il servizio dal servizio di denominazione.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Servizio tessuti fornisce API che consentono ai client e altri servizi quindi chiedere queste informazioni per l'indirizzo in base al nome del servizio. È importante perché l'indirizzo del servizio non è statico. Servizi vengono spostati all'interno del cluster per scopi di bilanciamento del carico e la disponibilità delle risorse. Si tratta del meccanismo che consente di risolvere l'indirizzo di attesa per un servizio client.

> [AZURE.NOTE] Per una procedura completa di come scrivere un `ICommunicationListener`, vedere [servizi API del servizio Web di tessuti con OWIN hosting](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>La comunicazione con un servizio
L'API servizi affidabile fornisce raccolte seguenti per scrivere i client di comunicano con i servizi.

### <a name="service-endpoint-resolution"></a>Risoluzione degli endpoint del servizio
Il primo passo per la comunicazione con un servizio è per risolvere un indirizzo endpoint della partizione o istanza del servizio che si desidera parlare. Il `ServicePartitionResolver` classe utility è una base primitiva che consente ai client di determinare l'endpoint del servizio in fase di esecuzione. Nella terminologia di servizio tessuti, il processo per determinare il punto finale di un servizio viene definito *la risoluzione degli endpoint del servizio*.

Per connettersi ai servizi in un cluster `ServicePartitionResolver` possono essere create con le impostazioni predefinite. Utilizzo consigliato per la maggior parte dei casi è:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Per connettersi ai servizi in un cluster diverso, un `ServicePartitionResolver` può essere creato con una serie di punti finali gateway cluster. Si noti che i punti finali gateway endpoint solo diversi per la connessione allo stesso cluster. Per esempio:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

In alternativa, `ServicePartitionResolver` può essere specificato una funzione per la creazione di un `FabricClient` per uso interno: 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`è l'oggetto che viene utilizzato per comunicare con il cluster tessuti servizio per diverse operazioni di gestione nel cluster. Questo è utile quando si desidera un maggiore controllo come `ServicePartitionResolver` interagisce con i cluster. `FabricClient`esegue la memorizzazione nella cache internamente ed è in genere costosa da creare, pertanto è importante riutilizzare `FabricClient` istanze il più possibile. 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

Un metodo Risolvi quindi viene utilizzato per recuperare l'indirizzo di un servizio o una partizione di servizio per i servizi di partizionata.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Un indirizzo del servizio può essere risolte facilmente tramite un `ServicePartitionResolver`, ma carico di lavoro è necessario per verificare l'indirizzo risolto può essere utilizzato in modo corretto. Il client dovrà rilevare se il tentativo di connessione non è riuscita a causa di un errore temporaneo e può essere ripetute (ad esempio servizio spostato o è temporaneamente non disponibile) o un errore permanente (ad esempio servizio è stato eliminato o la risorsa richiesta non esiste più). Istanze del servizio o repliche possono spostarsi dal nodo al livello di nodo in qualsiasi momento per diverse ragioni. L'indirizzo del servizio di risoluzione tramite `ServicePartitionResolver` può essere aggiornato quando il codice client tentativo di connessione. In questo caso nuovamente il client sarà necessario risolvere l'indirizzo. Fornire precedente `ResolvedServicePartition` indica che il sistema di risoluzione riprovare piuttosto che semplicemente recupera un indirizzo memorizzati nella cache.

In genere, il codice client necessario funziona con la `ServicePartitionResolver` direttamente. Viene creata e passare factory client di comunicazione dell'API Services affidabile. Le factory utilizzano il sistema di risoluzione internamente per generare un oggetto client che può essere utilizzato per comunicare con i servizi.

### <a name="communication-clients-and-factories"></a>Factory e i client di comunicazione

Una tipica Riprova gestione degli errori che facilita la ripetizione connessioni agli endpoint del servizio risolto di implementazione la libreria di factory comunicazioni. La raccolta di factory fornisce il meccanismo Riprova mentre si forniscono gestori di errori.

`ICommunicationClientFactory`definisce l'interfaccia di base implementata da una factory client di comunicazione che produce client può comunicare a un servizio di assistenza tessuti. L'implementazione del CommunicationClientFactory dipende stack di comunicazioni utilizzato dal servizio di assistenza tessuti nel punto in cui il client intende comunicare. L'API servizi affidabile fornisce un `CommunicationClientFactoryBase<TCommunicationClient>`. Fornisce un'implementazione di base del `ICommunicationClientFactory` l'interfaccia e consente di eseguire attività comuni a tutti gli stack di comunicazione. (Queste attività includono l'utilizzo di un `ServicePartitionResolver` per determinare l'endpoint del servizio). I clienti in genere implementano la classe CommunicationClientFactoryBase astratta per gestire la logica specifiche per stack di comunicazione.

Il client di comunicazioni è sufficiente riceve un indirizzo e viene utilizzato per connettersi a un servizio. Il cliente può utilizzare qualsiasi protocollo desiderati.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

Factory client è responsabile della creazione di comunicazione client. Per i client che non mantenere una connessione permanente, ad esempio un client HTTP, il produttore è sufficiente creare e restituire il client. Altri protocolli che mantengono una connessione permanente, ad esempio alcuni protocolli binari, devono essere convalidati anche da factory per determinare se non è necessario creare di nuovo la connessione.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Infine, un gestore eccezioni è prestazione per determinare l'azione da eseguire quando si verifica un'eccezione. Le eccezioni sono suddivise **riproducibile** e **non riproducibile**. 

 - **Non riproducibile** eccezioni semplicemente nuovamente generate al chiamante. 
 - Eccezioni **Retriable** sono suddivise ulteriormente **temporaneo** e **non temporanei**.
  - Eccezioni **temporanee** sono quelli che possono essere ripetute semplicemente senza nuovamente risolvere l'indirizzo endpoint del servizio. Questi verranno inclusi problemi temporanei di rete o risposte di errore servizio diversi da quelli che indicano che l'indirizzo endpoint del servizio non esiste. 
  - Eccezioni **temporanei non** sono quelli che richiedono l'indirizzo endpoint servizio deve essere eseguita nuovamente. Sono incluse le eccezioni che indicano che l'endpoint del servizio non è stato raggiunto, indicante che il servizio è spostato in un altro nodo. 

Il `TryHandleException` rende una decisione sulle eccezioni specificato. Se si **non riconosce i contenuti pertinenti** quali decisioni per rendere su un'eccezione, deve restituire **false**. Se si **sapere** cosa decisione per rendere, deve impostare di conseguenza il risultato e restituisce **true**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>Combinazione degli elementi
Con un `ICommunicationClient`, `ICommunicationClientFactory`, e `IExceptionHandler` basata su un protocollo di comunicazione, un `ServicePartitionClient` è include tutte insieme e fornisce il ciclo di risoluzione indirizzo partizione di gestione degli errori e servizi intorno a questi componenti.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>Passaggi successivi
 - Visualizzare un esempio di comunicazione HTTP tra i servizi di un [progetto di esempio in GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Chiamate di procedura remota con i servizi remoti di servizi affidabili](service-fabric-reliable-services-communication-remoting.md)

 - [Web API che utilizza OWIN in servizi affidabili](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicazione WCF tramite servizi affidabili](service-fabric-reliable-services-communication-wcf.md)
