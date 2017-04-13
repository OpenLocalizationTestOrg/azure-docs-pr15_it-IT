<properties
    pageTitle="Come utilizzare relay Service Bus con .NET | Microsoft Azure"
    description="Informazioni su come utilizzare il servizio di inoltro Bus di servizio Azure per connettere due applicazioni ospitate in un'altra posizione."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Come utilizzare il servizio di inoltro Bus di servizio Azure

In questo articolo viene descritto come utilizzare il servizio di inoltro Bus di servizio. Gli esempi scritto in c# e utilizzano l'API di Windows Communication Foundation (WCF) con estensioni contenute in assembly Bus di servizio. Per ulteriori informazioni sull'inoltro Bus di servizio, vedere la panoramica [Bus di servizio di inoltro dei messaggi](service-bus-relay-overview.md) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>Che cos'è l'inoltro Bus di servizio?

Il servizio di [assistenza Bus *inoltro* ](service-bus-relay-overview.md) consente di compilare applicazioni ibride che eseguono in un Data Center Azure e ambiente aziendale locale. Relay Service Bus semplifica l'esecuzione di questa operazione, consentendo di sicuro espongono i servizi di Windows Communication Foundation (WCF) che si trovano all'interno di una rete dell'organizzazione aziendale nel cloud pubblico, senza dover aprire una connessione firewall o richiedere intrusive modifiche a un'infrastruttura di rete aziendale.

![Concetti di inoltro](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Relay Service Bus consente ai servizi WCF host nell'ambiente aziendale esistente. È quindi possibile delegare in attesa di posta in arrivo sessioni e le richieste di questi servizi WCF al servizio Bus di servizio in esecuzione all'interno di Azure. In questo modo è possibile esporre questi servizi al codice dell'applicazione in esecuzione in Azure oppure a utenti mobili o ambienti extranet partner. Bus di servizio consente di sicuro controllare chi può accedere a questi servizi a un livello diffusamente. Fornisce un modo efficace e sicuro per esporre funzionalità dell'applicazione e le soluzioni aziendali esistenti ai dati e sfruttare i vantaggi dal cloud.

In questo articolo viene illustrato come utilizzare inoltro Bus di servizio per creare un servizio web WCF, esposto tramite un'associazione di canale TCP, che implementa una conversazione protetta tra due parti.

## <a name="create-a-service-namespace"></a>Creare uno spazio dei nomi del servizio

Per iniziare a utilizzare relay Service Bus in Azure, è innanzitutto necessario creare uno spazio dei nomi. Uno spazio dei nomi fornisce un contenitore per indirizzare le risorse Bus di servizio all'interno dell'applicazione.

Per creare uno spazio dei nomi di servizio:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Ottenere il pacchetto di servizio Bus NuGet

Il [pacchetto del servizio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) è il modo più semplice per ottenere l'API di Bus del servizio e per configurare l'applicazione con tutte le dipendenze Bus di servizio. Per installare il pacchetto NuGet nel progetto, eseguire le operazioni seguenti:

1.  In Esplora soluzioni rapida **riferimenti**e quindi fare clic su **Gestisci pacchetti NuGet**.
2.  Cercare "Servizio Bus" e selezionare l'elemento **Bus di servizio di Microsoft Azure** . Fare clic su **Installa** per completare l'installazione, quindi chiudere la finestra di dialogo seguenti:

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Utilizzare Bus di servizio per esporre e utilizzare un servizio web SOAP con TCP

Per esporre un servizio web WCF SOAP esistente per l'utilizzo esterno, è necessario apportare modifiche alle associazioni a servizi e gli indirizzi. Questa operazione può richiedere le modifiche apportate ai file di configurazione o potrebbe richiedere modifiche al codice, a seconda di come è stato impostato e configurato i servizi WCF. Si noti che WCF consente di disporre di più endpoint di rete sul servizio stesso, in modo da mantenere gli endpoint interni esistenti durante l'aggiunta di endpoint Bus di servizio per l'accesso esterno nello stesso momento.

In questa attività verranno creare un semplice servizio WCF e aggiungervi un comunicare ascoltatore Bus di servizio. Questo esercizio presuppone familiarità con Visual Studio e pertanto non scorrere tutti i dettagli della creazione di un progetto. Se, tuttavia, incentrata sul codice.

Prima di iniziare questa procedura, completare la procedura seguente per configurare l'ambiente:

1.  In Visual Studio, creare un'applicazione console che contiene due progetti "Client" e "Servizio", all'interno della soluzione.
2.  Aggiungere il pacchetto di Microsoft Azure servizio Bus NuGet a entrambi i progetti. Questo pacchetto aggiunge tutti i riferimenti di assembly necessari per i progetti.

### <a name="how-to-create-the-service"></a>Come creare il servizio

Creare innanzitutto il servizio. Qualsiasi servizio WCF è costituito da almeno tre parti distinte:

-   Definizione di un contratto che descrive i messaggi vengono scambiati e quali sono le operazioni da richiamare.
-   Implementazione di tale contratto.
-   Host che ospita il servizio WCF e i punti finali diversi vengono esposte.

Esempi di codice in questa sezione indirizzo ognuno di essi.

Il contratto definisce una singola operazione `AddNumbers`, che consente di aggiungere due numeri e restituisce il risultato. Il `IProblemSolverChannel` interfaccia consente al client gestire più facilmente la durata del proxy. Creazione di tale interfaccia è una buona norma. È consigliabile includere la definizione di questo contratto in un file separato, in modo che è possibile fare riferimento a tale file da progetti sia il "Client" e "Servizio", ma è anche possibile copiare il codice in entrambi i progetti.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Con contratto in posizione, l'implementazione è molto semplice.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurare un host del servizio a livello di programmazione

Con contratto e implementazione in posizione, è ora possibile ospitare il servizio. Hosting viene eseguita all'interno di un oggetto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) , si occupa di gestione delle istanze del servizio e ospita i punti finali che ascoltare i messaggi. Il codice seguente consente di configurare il servizio con un endpoint locale normale e un endpoint servizio Bus per illustrare l'aspetto affiancati, di endpoint interne ed esterne. Sostituire la stringa *dello spazio dei nomi* con il nome dello spazio dei nomi e *yourKey* insieme al tasto SA ottenuto nel passaggio precedente il programma di installazione.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

Nell'esempio, creare due endpoint presenti la stessa implementazione di contratto. Uno locale e una proiezione tramite Bus di servizio. Differenze tra di esse sono associazioni; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) per quello locale e [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) dell'endpoint del servizio Bus e gli indirizzi. L'endpoint locale contiene un indirizzo di rete locale con una porta distinto. Endpoint del servizio Bus ha un indirizzo endpoint composto della stringa `sb`, il nome dello spazio dei nomi e il percorso "Risolutore." Il risultato è l'URI `sb://[serviceNamespace].servicebus.windows.net/solver`, che identifica l'endpoint del servizio come un endpoint del servizio Bus TCP con un nome completo del DNS esterno. Se si inserisce il codice di sostituire i segnaposto nel `Main` funzione dell'applicazione del **servizio** , si verificherà un servizio funzionale. Se si desidera che il servizio in ascolto esclusivamente Bus di servizio, rimuovere la dichiarazione endpoint locale.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurare un host del servizio in App.

È inoltre possibile configurare host utilizzando il file app. Nell'esempio seguente viene visualizzato il servizio di hosting codice in questo caso.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Le definizioni di endpoint spostare nel file app. Pacchetto NuGet ha già aggiunto un intervallo di definizioni al file App, che sono le estensioni di configurazione necessarie per Bus di servizio. Nell'esempio seguente, che corrisponde esattamente di codice precedente, dovrebbero essere visualizzate direttamente sotto l'elemento **ServiceModel** . In questo esempio si presuppone che lo spazio dei nomi di progetto c# denominato **servizio**.
Sostituire i segnaposto con spazio dei nomi di servizio Bus di servizio e chiave SA.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Dopo aver apportato queste modifiche, verrà avviato il servizio come in precedenza, ma con due punti finali live: una locale e in attesa una nel cloud.

### <a name="create-the-client"></a>Creazione del client

#### <a name="configure-a-client-programmatically"></a>Configurare un client a livello di programmazione

Per utilizzare il servizio, è possibile creare un client WCF utilizzando un oggetto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) . Servizio Bus utilizza un modello basato su token di sicurezza implementato tramite SA. La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) rappresenta un provider di token di sicurezza con metodi factory predefinite che restituiscono alcuni provider di token conosciuti. Nell'esempio seguente viene utilizzato il metodo [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) per gestire l'acquisizione del token SA appropriato. Il nome e la chiave sono quelli ottenuto dal portale di come descritto nella sezione precedente.

Prima di tutto, fare riferimento o copiare il `IProblemSolver` contratto codice dal servizio nel progetto client.

Sostituire quindi il codice di `Main` metodo del client nuovamente sostituire il testo segnaposto con il servizio Bus dello spazio dei nomi e la chiave SA.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

È ora possibile generare il client e il servizio eseguirli (eseguire prima di tutto il servizio) e il client chiama il servizio e stampa **9**. È possibile eseguire il client e server in diversi computer, anche attraverso le reti e la comunicazione continuano a funzionare. Il codice client anche possibile eseguire nel cloud, o in locale.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configurare un client in App.

Il codice seguente viene illustrato come configurare il client utilizzando il file app.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Le definizioni di endpoint spostare nel file app. Nell'esempio seguente, che è uguale al codice elencato in precedenza, dovrebbero essere visualizzate direttamente sotto l'elemento **ServiceModel** . In questo caso, come in precedenza, è necessario sostituire i segnaposto con il servizio Bus dello spazio dei nomi e la chiave SA.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base del servizio di inoltro Bus di servizio, seguire questi collegamenti per altre informazioni.

- [Servizio Bus inoltrati Cenni preliminari sulla messaggistica](service-bus-relay-overview.md)
- [Panoramica dell'architettura di Azure Bus di servizio](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- Esempi di servizio Bus degli [esempi di Azure][] o, vedere [Panoramica di campioni di Bus di servizio][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Esempi di Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [Panoramica degli esempi Bus di servizio]: ../service-bus-messaging/service-bus-samples.md