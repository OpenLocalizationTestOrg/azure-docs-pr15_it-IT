<properties
   pageTitle="Pila di comunicazione affidabile servizi WCF | Microsoft Azure"
   description="Lo stack di comunicazioni WCF incorporato nel servizio tessuti prevede comunicazioni WCF servizio client per servizi affidabili."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="bharatn"/>

# <a name="wcf-based-communication-stack-for-reliable-services"></a>Pila di comunicazione basata su WCF per servizi affidabili
Framework servizi affidabili consente agli autori di servizio scegliere stack di comunicazione da utilizzare per il servizio. È possibile collegare stack di comunicazioni scelta dall'utente tramite **ICommunicationListener** restituito da metodi [CreateServiceReplicaListeners o CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . Il framework fornisce un'implementazione dello stack di comunicazione basato su Windows Communication Foundation (WCF) per gli autori di servizio che desidera utilizzare la comunicazione basata su WCF.

## <a name="wcf-communication-listener"></a>Comunicare ascoltatore comunicazione WCF
L'implementazione di **ICommunicationListener** WCF specifica viene fornito dalla classe **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** .

Almeno di avere un contratto di servizio di tipo`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

È possibile creare un comunicare ascoltatore comunicazione WCF nel servizio descritto di seguito.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Scrittura di client per lo stack di comunicazione WCF
Per la scrittura ai client di comunicare con i servizi tramite WCF, il framework fornisce **WcfClientCommunicationFactory**, ossia l'implementazione di [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md)WCF specifica.

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Il canale di comunicazione WCF accessibili dal **WcfCommunicationClient** creato da **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Codice client può utilizzare **WcfCommunicationClientFactory** insieme **WcfCommunicationClient** t:System.Runtime.Serialization.XmlObjectSerializer **ServicePartitionClient** per determinare l'endpoint del servizio e comunicare con il servizio.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] Il valore predefinito ServicePartitionResolver presuppone che il client sia in esecuzione dello stesso cluster del servizio. Se ovvero non il caso, creare un oggetto ServicePartitionResolver e passare i punti finali connessione cluster.

## <a name="next-steps"></a>Passaggi successivi
* [Chiamata di procedura remota con i servizi remoti di servizi affidabili](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN in servizi affidabili](service-fabric-reliable-services-communication-webapi.md)

* [Protezione delle comunicazioni per servizi affidabili](service-fabric-reliable-services-secure-communication.md)
