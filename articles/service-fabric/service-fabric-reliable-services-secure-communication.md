<properties
   pageTitle="Guida di comunicazione sicura per i servizi di infrastruttura di servizio | Microsoft Azure"
   description="Panoramica su come proteggere la comunicazione per i servizi di affidabile che sono in esecuzione in un cluster di Azure servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="suchiagicha"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="suchiagicha"/>

# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Guida di comunicazione sicura per i servizi di infrastruttura di servizio di Azure

Sicurezza corrisponde a uno degli aspetti più importanti di comunicazione. Il framework applicazione servizi affidabili fornisce alcuni stack di comunicazione predefiniti e strumenti che è possibile utilizzare per migliorare la protezione. In questo articolo venga fornite ulteriori informazioni migliorare la protezione quando si usa remoti di servizio e stack di comunicazioni di Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Proteggere un servizio, quando si usa remoti di servizio

Verrà utilizzato un [esempio](service-fabric-reliable-services-communication-remoting.md) esistente che spiega come configurare la gestione remota per servizi affidabili. Per proteggere un servizio quando si usa remoti di servizi, procedere come segue:

1. Creare un'interfaccia `IHelloWorldStateful`, che definisce i metodi che saranno disponibili per una chiamata di procedura remota del servizio. Il servizio utilizzerà `FabricTransportServiceRemotingListener`, dichiarato nel `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` spazio dei nomi. Si tratta di un `ICommunicationListener` implementazione che fornisce funzionalità di accesso remoto.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Aggiungere le impostazioni di comunicare ascoltatore e le credenziali di sicurezza.

    Assicurarsi che il certificato che si desidera utilizzare per la protezione delle comunicazioni di servizio è installato in tutti i nodi del cluster. Esistono due modi che è possibile specificare le impostazioni di comunicare ascoltatore e le credenziali di sicurezza:

    1. Fornire loro direttamente nel codice di servizio:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Fornire loro utilizzando un [pacchetto di configurazione](service-fabric-application-model.md):

        Aggiungere un `TransportSettings` sezione nel file Settings.

        ```xml
        <!--Section name should always end with "TransportSettings".-->
        <!--Here we are using a prefix "HelloWorldStateful".-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        In questo caso, il `CreateServiceReplicaListeners` metodo aspetto simile al seguente:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Se si aggiunge un `TransportSettings` sezione nel file Settings senza alcun prefisso `FabricTransportListenerSettings` verrà caricato tutte le impostazioni di questa sezione per impostazione predefinita.

         ```xml
         <!--"TransportSettings" section without any prefix.-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         In questo caso, il `CreateServiceReplicaListeners` metodo aspetto simile al seguente:

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Quando si chiamano metodi su un servizio protetto utilizzando l'elenco di gestione remota, invece di usare il `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` classe per creare un proxy del servizio, utilizzare `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passare `FabricTransportSettings`, che contiene `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se il codice client viene eseguito come parte di un servizio, è possibile caricare `FabricTransportSettings` dal file Settings. Creare una sezione TransportSettings è simile al codice servizio, come illustrato in precedenza. Apportare le modifiche seguenti al codice client:

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se il client non è in esecuzione come parte di un servizio, è possibile creare un file client_name.settings.xml nella stessa posizione in cui il client_name.exe. Creare una sezione TransportSettings in tale file.

    Simile al servizio, se si aggiunge un `TransportSettings` sezione senza alcun prefisso nel client settings.xml/client_name.settings.xml, `FabricTransportSettings` verrà caricato tutte le impostazioni di questa sezione per impostazione predefinita.

    In questo caso, il codice precedente viene ulteriormente semplificato:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Proteggere un servizio, quando si usa uno stack di comunicazioni basate su WCF

Si inizierà a usare un esistente [esempio](service-fabric-reliable-services-communication-wcf.md) che illustra come configurare un stack di comunicazioni basate su WCF per servizi affidabili. Per proteggere un servizio quando si usa uno stack di comunicazioni basate su WCF, procedere come segue:

1. Per il servizio, è necessario proteggere comunicare ascoltatore comunicazione WCF (`WcfCommunicationListener`) creati. A tale scopo, modificare il `CreateServiceReplicaListeners` metodo.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```

2. Nel client, il `WcfCommunicationClient` classe creata nell' [esempio](service-fabric-reliable-services-communication-wcf.md) precedente rimarrà invariato. Ma è necessario eseguire l'override di `CreateClientAsync` metodo di `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Usare `SecureWcfCommunicationClientFactory` per creare un client di comunicazione WCF (`WcfCommunicationClient`). Utilizzare il client per richiamare i metodi di servizio.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Web API con OWIN in servizi affidabili](service-fabric-reliable-services-communication-webapi.md)
