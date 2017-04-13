<properties 
    pageTitle="Esercitazione servizio Bus inoltro | Microsoft Azure"
    description="Compilare un client Bus di servizio applicazioni e servizi tramite un inoltro Bus servizio."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>Esercitazione servizio Bus inoltro

In questa esercitazione viene illustrato come creare una semplice applicazione client Bus di servizio servizio e utilizzando le funzionalità di "inoltro" Bus di servizio. Per un'esercitazione corrispondente che utilizza Bus di servizio [gestito messaggistica](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), vedere [Servizio Bus negoziate esercitazione messaggistica .NET](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Esecuzione di questa esercitazione fornisce un per comprendere i passaggi necessari per creare un'applicazione client e il servizio Bus di servizio. Ad esempio controparti WCF un servizio è un costrutto esposti uno o più endpoint, ognuno dei quali espone una o più operazioni di servizio. Endpoint del servizio specifica un indirizzo in cui è possibile trovare il servizio, un'associazione che contiene le informazioni che un client deve comunicare con il servizio e un contratto che definisce la funzionalità fornita dal servizio ai client. La differenza tra un WCF e un servizio di assistenza Bus principale è il punto finale viene esposta nel cloud anziché in locale nel computer in uso.

Dopo aver lavorato tramite la sequenza di argomenti in questa esercitazione, si verificherà un servizio in esecuzione e un client in cui è possibile richiamare operazioni del servizio. Il primo argomento viene descritto come configurare un account. Passaggi successivi viene descritto come definire un servizio che utilizza un contratto, come implementare il servizio e su come configurare il servizio codice. Vengono inoltre descritti come host ed eseguire il servizio. Il servizio che viene creato è indipendente e il client e il servizio eseguire nello stesso computer. È possibile configurare il servizio tramite il codice o un file di configurazione.

I tre passaggi finali viene descritto come creare un'applicazione client, configurare l'applicazione client e creare e utilizzano un client che possa accedere alle funzionalità dell'host.

Tutti gli argomenti in questa sezione presuppongono che si sta utilizzando Visual Studio come ambiente di sviluppo.

## <a name="sign-up-for-an-account"></a>Iscriversi a un account

Il primo passaggio consiste per creare uno spazio dei nomi e per ottenere una chiave di firma di Access condiviso (SA). Uno spazio dei nomi fornisce un limite di applicazione per ogni applicazione esposto tramite Bus di servizio. Una chiave SA viene generata automaticamente dal sistema quando viene creato un spazio dei nomi di servizio. La combinazione dei nomi di servizio e chiave SA fornisce le credenziali per Bus di servizio per l'accesso a un'applicazione di autenticazione.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Definire un contratto di servizio WCF da usare con Bus di servizio

Il contratto di servizio specifica le operazioni che supporta il servizio (la terminologia di servizio Web per metodi o funzioni). Contratti vengono creati mediante la definizione di un'interfaccia C++, c# o Visual Basic. Ogni metodo nell'interfaccia corrisponde a un'operazione di servizio specifico. Ogni interfaccia deve avere l'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) applicata e le operazioni devono avere l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) applicata. Se l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) non dispone di un metodo di un'interfaccia contenente l'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) , tale metodo non viene esposto. Il codice per queste attività viene fornito nell'esempio seguendo la procedura descritta. Per una descrizione più grande dei contratti e dei servizi, vedere [Progettazione e implementazione Services](https://msdn.microsoft.com/library/ms729746.aspx) nella documentazione WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Per creare un contratto di servizio Bus con un'interfaccia

1. Aprire Visual Studio come amministratore facendo clic su programma nel menu **Start** e scegliere **Esegui come amministratore**.

2. Creare un nuovo progetto applicazione console. Fare clic sul menu **File** e selezionare **Nuovo**, quindi fare clic su **progetto**. Nella finestra di dialogo **Nuovo progetto** , fare clic su **Visual c#** (se **c#** non viene visualizzato, cercare in **Altre lingue**). Fare clic sul modello **Applicazione Console** e denominarla **EchoService**. Fare clic su **OK** per creare il progetto.

    ![][2]

3. Installare il pacchetto di servizio Bus NuGet. Questo pacchetto aggiunge automaticamente i riferimenti agli raccolte Bus di servizio, nonché la WCF **ServiceModel**. [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) è quello che consente di accedere a livello di programmazione le funzionalità di base di WCF. Servizio Bus utilizza molti degli oggetti e attributi di WCF per definire i contratti di servizio.

    In Esplora soluzioni fare doppio clic la soluzione e quindi fare clic su **Gestisci pacchetti NuGet per soluzione**. Fare clic sulla scheda **Sfoglia** , quindi cercare `Microsoft Azure Service Bus`. Verificare che il nome del progetto sia selezionato nella finestra di **versioni** . Fare clic su **installazione**e accettare le condizioni di utilizzo.

    ![][3]

3. In Esplora soluzioni fare doppio clic sul file Program.cs per aprirlo nell'editor, se non è già aperto.

4. Aggiungere le seguenti istruzioni using nella parte superiore del file:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Modificare il nome dello spazio dei nomi da nome predefinito di **EchoService** a **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] In questa esercitazione utilizza c# spazio dei nomi **Microsoft.ServiceBus.Samples**, ossia lo spazio dei nomi del tipo di contratto gestito che viene usato nel file di configurazione nella schermata [Configura il client WCF](#configure-the-wcf-client) . È possibile specificare qualsiasi spazio dei nomi da quando si creazione in questo esempio; Tuttavia, l'esercitazione non funzionerà a meno che non si modificare gli spazi dei nomi del contratto e il servizio di conseguenza, il file di configurazione dell'applicazione. Lo spazio specificato nel file app deve essere lo stesso spazio dei nomi specificato nei file di c#.

1. Subito dopo il `Microsoft.ServiceBus.Samples` dichiarazione dello spazio dei nomi, ma all'interno dello spazio dei nomi, definire una nuova interfaccia denominata `IEchoContract` e applicare la `ServiceContractAttribute` dell'attributo all'interfaccia con un valore dello spazio dei nomi di **http://samples.microsoft.com/ServiceModel/Relay/**. Il valore dello spazio dei nomi diverso dallo spazio dei nomi utilizzate nell'ambito del codice. Se, tuttavia, il valore dello spazio dei nomi viene usato come un identificatore univoco per il presente contratto. Specificare lo spazio dei nomi in modo esplicito impedisce l'aggiunta al nome del contratto il valore dello spazio dei nomi predefinito.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] In genere, lo spazio dei nomi del contratto di servizio contiene uno schema di denominazione che include le informazioni sulla versione. Incluse le informazioni sulla versione dello spazio dei nomi del contratto di servizio consente ai servizi di isolare principali modifiche apportate mediante la definizione di un nuovo contratto di servizio con un nuovo spazio dei nomi ed esposizione in un nuovo endpoint. In questo modo client possono continuare a usare il contratto di servizio precedente senza dover aggiornare. Informazioni sulla versione possono essere composti da una data o un numero di build. Per ulteriori informazioni, vedere [Il controllo delle versioni di servizio](http://go.microsoft.com/fwlink/?LinkID=180498). Ai fini di questa esercitazione, lo schema di denominazione dei nomi di contratto di servizio non contiene informazioni sulla versione.

1. All'interno di `IEchoContract` interfaccia, dichiarare un metodo per l'operazione singola il `IEchoContract` contratto esposti nell'interfaccia e applicare il `OperationContractAttribute` dell'attributo al metodo che si desidera esporre come parte del contratto di servizio Bus pubblico.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Subito dopo il `IEchoContract` definizione di interfaccia, dichiarare un canale da cui eredita entrambe `IEchoContract` e anche alla `IClientChannel` interfaccia, come illustrato di seguito:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Un canale è l'oggetto WCF mediante il quale il client e host passare informazioni tra loro. In un secondo momento, scrivere codice per il canale da restituire informazioni tra le due applicazioni.

1. Dal menu **Compila** fare clic su **Compila soluzione** oppure premere **Ctrl + MAIUSC + B** per confermare l'accuratezza del proprio lavoro finora.

### <a name="example"></a>Esempio

Il codice riportato di seguito mostra l'interfaccia di base che definisce un contratto di servizio Bus.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Una volta creato l'interfaccia, è possibile implementare l'interfaccia.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Implementare il contratto WCF usare Bus di servizio

Creazione di un inoltro Bus di servizio, è necessario creare innanzitutto il contratto, definito mediante un'interfaccia. Per ulteriori informazioni sulla creazione dell'interfaccia, vedere il passaggio precedente. Il passaggio successivo consiste nel implementare l'interfaccia. Questa operazione consiste nel creare una classe denominata `EchoService` che implementa definite dall'utente `IEchoContract` interfaccia. Dopo avere implementato l'interfaccia, configurare l'interfaccia utilizzando un file di configurazione App. File di configurazione contiene le informazioni necessarie per l'applicazione, ad esempio il nome del servizio, il nome del contratto e il tipo di protocollo utilizzato per comunicare con Bus di servizio. Il codice utilizzato per le attività seguenti viene fornito nell'esempio seguendo la procedura descritta. Per informazioni generali su come implementare un contratto di servizio, vedere [L'implementazione contratti di servizio](https://msdn.microsoft.com/library/ms733764.aspx) nella documentazione WCF.

1. Creare una nuova classe denominata `EchoService` subito dopo la definizione del `IEchoContract` interfaccia. Il `EchoService` classe di `IEchoContract` interfaccia. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Analogamente alle altre interfacce, è possibile implementare la definizione in un altro file. Per questa esercitazione, tuttavia, si trova l'implementazione nello stesso file di definizione dell'interfaccia e i `Main` metodo.

1. Applicare l'attributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) per il `IEchoContract` interfaccia. L'attributo che specifica il nome del servizio e spazio dei nomi. Al termine dell'operazione, il `EchoService` classe viene visualizzata come segue:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementare la `Echo` metodo definito nel `IEchoContract` interfaccia nel `EchoService` classe. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Fare clic su **Genera**e quindi fare clic su **Compila soluzione** per verificare l'accuratezza del proprio lavoro.

### <a name="to-define-the-configuration-for-the-service-host"></a>Per definire la configurazione per l'host del servizio

1. File di configurazione è molto simile a un file di configurazione WCF. Include il nome del servizio, endpoint (ovvero, la posizione servizio Bus espone per i client e host comunicare con loro) e l'associazione (il tipo del protocollo che viene utilizzato per comunicare). La differenza principale è che l'endpoint servizio configurato fa riferimento a un'associazione [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) , che non fa parte di .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) corrisponde a uno delle associazioni definite dal servizio Bus.

1. In **Esplora soluzioni**fare doppio clic sul file App per aprirlo nell'editor di Visual Studio.

2. Nel `<appSettings>` elemento, sostituire i segnaposto con il nome di spazio dei nomi di servizio e le SA chiave che sono stati copiati in un passaggio precedente. 

1. All'interno di `<system.serviceModel>` tag, aggiungere un `<services>` elemento. È possibile definire più applicazioni di servizio Bus in un singolo file di configurazione. Tuttavia, in questa esercitazione definisce una sola.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. All'interno di `<services>` elemento, aggiungere un `<service>` elemento per definire il nome del servizio.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. All'interno di `<service>` elemento, definire la posizione del contratto dell'endpoint e il tipo di associazione per l'endpoint.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Il punto finale definisce in cui il client la ricerca per l'applicazione host. In un secondo momento, l'esercitazione Usa questo passaggio per creare un URI esposti completamente host tramite Bus di servizio. L'associazione dichiara che si utilizza TCP come protocollo per comunicare con Bus di servizio.

1. Dal menu **Genera** scegliere **Genera soluzione** per verificare l'accuratezza del proprio lavoro.

### <a name="example"></a>Esempio

Il codice seguente viene illustrata l'implementazione del contratto di servizio.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Il codice seguente mostra il formato di base del file app associato con l'host del servizio.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Ospitare ed eseguire un servizio Web di base per registrare con Bus di servizio

Questo passaggio viene descritto come eseguire un servizio di assistenza Bus base.

### <a name="to-create-the-service-bus-credentials"></a>Per creare le credenziali Bus di servizio

1. In `Main()`, creare due variabili in cui memorizzare lo spazio dei nomi e le SA principali che vengono letti dalla finestra della console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Il tasto SA verrà utilizzato in un secondo momento per accedere a un progetto Bus di servizio. Spazio dei nomi viene passato come parametro per `CreateServiceUri` per creare un servizio URI.

4. Utilizzo di un oggetto [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) , dichiarare che si utilizzeranno una chiave sa come tipo di credenziali. Aggiungere il codice seguente subito dopo il codice aggiunto nell'ultimo passaggio.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Per creare un indirizzo di base per il servizio

1. Seguire il codice aggiunto nell'ultimo passaggio, creare un `Uri` istanza per l'indirizzo di base del servizio. Questo URI specifica la combinazione di Bus di servizio, lo spazio dei nomi e il percorso dell'interfaccia del servizio.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" è un'abbreviazione per la combinazione di Bus di servizio e indica che si utilizza il protocollo TCP. Questa operazione anche in precedenza indicata nel file di configurazione, quando [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) è stata specificata come l'associazione.
    
    Per questa esercitazione, è l'URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Per creare e configurare l'host del servizio

1. Impostare la modalità di integrazione applicativa su `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    La modalità di integrazione applicativa descrive il protocollo del servizio utilizzato per comunicare con Bus di servizio; HTTP o TCP. L'impostazione predefinita del `AutoDetect`, il servizio tenta di connettersi al servizio Bus su TCP se è disponibile e HTTP se TCP non è disponibile. Nota che è diverso dal protocollo del servizio specifica per la comunicazione client. Protocollo è l'associazione utilizzata. Ad esempio, un servizio può utilizzare l'associazione [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) che specifica che il punto finale (esposto sul servizio Bus) comunica con i client su HTTP. Lo stesso servizio Impossibile specificare **ConnectivityMode.AutoDetect** in modo che il servizio comunica con Bus di servizio su TCP.

1. Creare l'host del servizio mediante l'URI creata in precedenza in questa sezione.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    L'host del servizio è l'oggetto WCF che crea un'istanza del servizio. In questo caso, si passa il tipo di servizio che si desidera creare (un `EchoService` tipo) e anche per l'indirizzo al quale si desidera esporre il servizio.

1. Nella parte superiore del file Program.cs, aggiungere riferimenti a [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Di nuovo in `Main()`, configurare l'endpoint per abilitare l'accesso pubblico.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Questo passaggio si informa Bus di servizio che si trova l'applicazione pubblicamente esaminando ATOM di Bus servizio feed per il progetto. Se si imposta **DiscoveryType** su **private**, il client sarebbe comunque in grado di accedere al servizio. Tuttavia, il servizio non sarà presente durante la ricerca dello spazio dei nomi Bus di servizio. Se, tuttavia, il client avrebbe conoscere il percorso di endpoint in precedenza.

1. Applicare le credenziali del servizio per gli endpoint del servizio definiti nel file App:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Come indicato nel passaggio precedente, si potrebbero avere dichiarati più servizi e i punti finali nel file di configurazione. Se si dispone, questo codice attraversano il file di configurazione e cercare ogni endpoint a cui è necessario applicare le credenziali. Per questa esercitazione, tuttavia, il file di configurazione ha solo un endpoint.

### <a name="to-open-the-service-host"></a>Per aprire l'host del servizio

1. Aprire il servizio.

    ```
    host.Open();
    ```

1. Informare l'utente che esegue il servizio e viene illustrato come arrestare il servizio.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Al termine, chiudere l'host del servizio.

    ```
    host.Close();
    ```

1. Premere **Ctrl + MAIUSC + B** per compilare il progetto.

### <a name="example"></a>Esempio

Nell'esempio seguente include il contratto di servizio e implementazione nei passaggi precedenti nell'esercitazione e ospita il servizio in un'applicazione console. Compilare le operazioni seguenti in un file eseguibile denominato EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Creare un client WCF per il contratto di servizio

Il passaggio successivo consiste nel creare un'applicazione client Bus di servizio e definire il contratto di servizio che implementare nei passaggi successivi. Nota che molti dei passaggi è simile a quella utilizzata per creare un servizio: la definizione di un contratto, la modifica di un'app di file, utilizza le credenziali per connettersi a Bus di servizio e così via. Il codice utilizzato per le attività seguenti viene fornito nell'esempio seguendo la procedura descritta.

1. Creare un nuovo progetto nella soluzione Visual Studio corrente per il client eseguendo le operazioni seguenti:
    1. In Esplora soluzioni nella stessa soluzione che contiene il servizio, fare doppio clic soluzione corrente (non il progetto) e fare clic su **Aggiungi**. Fare clic su **Nuovo progetto**.
    2. Nella finestra di dialogo **Aggiungi nuovo progetto** , fare clic su **Visual c#** (se **c#** non viene visualizzato, cercare in **Altre lingue**), selezionare il modello **Applicazione Console** e denominarla **EchoClient**.
    3. Fare clic su **OK**.
<br />

1. In Esplora soluzioni fare doppio clic sul file Program.cs nel progetto **EchoClient** per l'apertura dell'editor fare se non è già aperto.

1. Modificare il nome dello spazio dei nomi da nome predefinito di `EchoClient` a `Microsoft.ServiceBus.Samples`.

1. Installare il [pacchetto NuGet Bus di servizio](https://www.nuget.org/packages/WindowsAzure.ServiceBus). In Esplora soluzioni fare clic sul progetto **EchoClient** e quindi fare clic su **Gestisci pacchetti NuGet**. Fare clic sulla scheda **Sfoglia** , quindi cercare `Microsoft Azure Service Bus`. Fare clic su **installazione**e accettare le condizioni di utilizzo.

    ![][3]

1. Aggiungere un `using` istruzione per lo spazio dei nomi di [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) nel file Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Aggiungere la definizione di contratto di servizio allo spazio dei nomi, come illustrato nell'esempio seguente. Si noti che questa definizione corrisponde alla definizione utilizzata nel progetto di **servizio** . È necessario aggiungere questo codice nella parte superiore di `Microsoft.ServiceBus.Samples` spazio dei nomi.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Premere **Ctrl + MAIUSC + B** per compilare il client.

### <a name="example"></a>Esempio

Il codice seguente mostra lo stato corrente del file Program.cs nel progetto EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurare il client WCF

In questo passaggio si crea un file di App per un'applicazione client di base che accede al servizio creato in precedenza in questa esercitazione. Questo file app definisce il contratto, l'associazione e nome dell'endpoint. Il codice utilizzato per le attività seguenti viene fornito nell'esempio seguendo la procedura descritta.

1. In Esplora soluzioni fare doppio clic su **App** per aprire il file nell'editor di Visual Studio nel progetto **EchoClient** .

2. Nel `<appSettings>` elemento, sostituire i segnaposto con il nome di spazio dei nomi di servizio e le SA chiave che sono stati copiati in un passaggio precedente.

1. All'interno dell'elemento ServiceModel, aggiungere un `<client>` elemento.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Questo passaggio dichiara la definizione di un'applicazione client WCF stile.

1. All'interno di `client` elemento, definire il nome, contratto e tipo di associazione per l'endpoint.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Questo passaggio definisce il nome dell'endpoint, il contratto definito nel servizio e al fatto che l'applicazione client utilizza TCP per comunicare con Bus di servizio. Il nome dell'endpoint viene utilizzato nel passaggio successivo per collegare questa configurazione endpoint con l'URI del servizio.

1. Fare clic su **File**e quindi **Salva tutto**.

## <a name="example"></a>Esempio

Il codice seguente mostra il file di App per il client eco.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implementare il client WCF per chiamare Bus di servizio

In questo passaggio è implementare un'applicazione client di base che accede il servizio che è stato creato in precedenza in questa esercitazione. Analogamente al servizio, il client esegue molte delle stesse operazioni per accedere a Bus di servizio:

1. Imposta la modalità di integrazione applicativa.
1. Crea URI che individua il servizio host.
1. Consente di definire le credenziali di sicurezza.
1. Applica le credenziali per la connessione.
1. Apre la connessione.
1. Consente di eseguire le attività specifiche dell'applicazione.
1. Chiude la connessione.

Uno dei principali differenze invece che l'applicazione client utilizza un canale a cui connettersi Bus di servizio, mentre il servizio utilizza una chiamata a **ServiceHost**. Il codice utilizzato per le attività seguenti viene fornito nell'esempio seguendo la procedura descritta.

### <a name="to-implement-a-client-application"></a>Per implementare un'applicazione client

1. Impostare la modalità di integrazione applicativa di **rilevamento automatico**. Aggiungere il codice seguente all'interno di `Main()` metodo dell'applicazione **EchoClient** .

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definire le variabili per mettere in attesa i valori per il servizio dello spazio dei nomi e la chiave sa che vengono letti dalla console.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Creare l'URI che definisce la posizione dell'host di un progetto Bus di servizio.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Creare l'oggetto credenziali per l'endpoint del servizio di spazio dei nomi.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Creare la channel factory che carica la configurazione descritta nel file app.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Una channel factory è un oggetto WCF che consente di creare un canale mediante il quale comunicano le applicazioni di servizio e client.

1. Applicare le credenziali Bus di servizio.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Creare e aprire il canale al servizio.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Scrittura di interfaccia utente di base per l'eco.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Si noti che il codice utilizza l'istanza dell'oggetto canale come proxy per il servizio.

1. Chiudere il canale e chiudere il produttore.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Per eseguire le applicazioni

1. Premere **Ctrl + MAIUSC + B** per creare la soluzione. Consente di creare il progetto client e il progetto di servizio creato in precedenza.

2. Prima di eseguire l'applicazione client, è necessario assicurarsi che l'applicazione del servizio sia in esecuzione. In Esplora soluzioni in Visual Studio, fare doppio clic soluzione **EchoService** , quindi fare clic su **proprietà**.

3. Nella finestra di dialogo Proprietà soluzione, fare clic su **Progetto di avvio**, quindi fare clic sul pulsante **più progetti di avvio** . Assicurarsi che **EchoService** viene visualizzata per prima nell'elenco. 

4. Impostare la casella **azione** dei progetti **EchoService** ed **EchoClient** per **iniziare**.

    ![][5]

5. Fare clic su **dipendenze del progetto**. Nella casella **progetti** selezionare **EchoClient**. Nella casella **dipendente da** verificare che sia selezionata **EchoService** .

    ![][6]

6. Fare clic su **OK** per chiudere la finestra di dialogo **proprietà** .

7. Premere **F5** per eseguire entrambi i progetti.

8. In entrambe le finestre di console aprire e chiesto di specificare il nome dello spazio dei nomi. Il servizio deve eseguire prima, quindi nella finestra della console **EchoService** immettere lo spazio dei nomi e quindi premere **INVIO**.

9. Successivamente, viene richiesto per trovare il codice SA. Immettere un codice SA e premere INVIO.

    Di seguito è riportato output dalla finestra della console. Si noti che i valori forniti sono ad esempio solo a scopo.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    Applicazione del servizio di stampa nella finestra della console l'indirizzo in cui è in attesa, come illustrato nell'esempio seguente.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Nella finestra della console **EchoClient** immettere le stesse informazioni immesse in precedenza per l'applicazione del servizio. Seguire i passaggi precedenti per immettere gli stesso spazio dei nomi di servizio e i valori di chiave SA per l'applicazione client.

11. Dopo aver immesso questi valori, il client viene aperto un canale al servizio e viene richiesto di immettere testo, come illustrato nell'esempio di output console seguente.

    `Enter text to echo (or [Enter] to exit):` 

    Immettere del testo per l'invio all'applicazione di servizio e premere INVIO. Questo testo inviato al servizio tramite l'operazione di servizio eco e verrà visualizzato nella finestra della console servizio come output di esempio seguente.

    `Echoing: My sample text`

    L'applicazione client riceve il valore restituito il `Echo` operazioni, che sono il testo originale e stampa alla finestra console. Di seguito è riportato output dalla finestra di console del client.

    `Server echoed: My sample text`

12. È possibile continuare l'invio di SMS dal client al servizio in questo modo. Al termine, premere INVIO nelle finestre di console client e il servizio per chiudere entrambe le applicazioni.

## <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato come creare un'applicazione client, come le operazioni del servizio di chiamata e come chiudere il client al termine della chiamata operazione.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione viene illustrato come creare un client Bus di servizio applicazione di servizio e utilizzando le funzionalità di "inoltro" Bus di servizio. Per un'esercitazione simile che usa il servizio Bus [messaggistica](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), vedere [Servizio Bus negoziate esercitazione messaggistica .NET](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Per ulteriori informazioni su Bus di servizio, vedere gli argomenti seguenti.

- [Cenni preliminari sulla messaggistica Bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)
- [Nozioni fondamentali su Bus di servizio](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Architettura di Bus di servizio](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
