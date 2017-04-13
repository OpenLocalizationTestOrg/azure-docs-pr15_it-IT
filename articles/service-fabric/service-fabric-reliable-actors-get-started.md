<properties
   pageTitle="Guida introduttiva a servizio tessuti affidabile operatori | Microsoft Azure"
   description="In questa esercitazione è illustrata la procedura di creazione, debug e distribuzione di un servizio basato su attore semplice con operatori affidabile tessuti servizio."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Guida introduttiva di operatori affidabile

> [AZURE.SELECTOR]
- [C# in Windows](service-fabric-reliable-actors-get-started.md)
- [Linguaggio su Linux](service-fabric-reliable-actors-get-started-java.md)

In questo articolo vengono illustrate le nozioni di base di Azure servizio tessuti affidabile e illustra tramite la creazione, debug e distribuzione di un'applicazione affidabile attore semplice in Visual Studio.

## <a name="installation-and-setup"></a>Installazione e configurazione
Prima di iniziare, verificare di disporre dell'ambiente di sviluppo di servizio tessuti configurare nel computer in uso.
Se è necessario configurare l'account, vedere istruzioni dettagliate su [come configurare l'ambiente di sviluppo](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Concetti di base
Per iniziare a utilizzare gli operatori affidabile, è necessario conoscere alcuni concetti di base:

 * **Servizio dell'attore**. Gli operatori affidabili sono inclusi in servizi affidabili che possono essere distribuiti dell'infrastruttura di servizio tessuti. Le istanze dell'attore sono attivate in un'istanza di servizio in questione.
 
 * **Registrazione dell'attore**. Come con i servizi di affidabile, un servizio attore affidabile deve essere registrato con il runtime del servizio tessuti. Inoltre, il tipo di attore deve essere registrato con il runtime attore.
 
 * **Interfaccia attore**. L'interfaccia attore viene utilizzata per definire l'interfaccia fortemente pubblica di un attore. Nella terminologia di modello attore affidabile, l'interfaccia attore definisce i tipi di messaggi riconoscibili attore e processo. L'interfaccia attore viene utilizzato da altri operatori e le applicazioni client per "Invia" (asincrono) messaggi all'attore. Gli operatori affidabili possono implementare più interfacce.
 
 * **ActorProxy classe**. La classe ActorProxy viene utilizzata dalle applicazioni client per richiamare i metodi esposti tramite l'interfaccia attore. La classe ActorProxy fornisce due funzionalità importanti:
    * Risoluzione dei nomi: è possibile individuare l'attore cluster (trova il nodo del cluster nel punto in cui è ospitato).
    * Gestione degli errori: può riprovare chiamate e risolvere la posizione dell'attore dopo, ad esempio, un errore che richiede l'attore essere spostata in un altro nodo del cluster.

Le seguenti regole relative alle interfacce attore sono che è opportuno menzionare:

- Attore interfaccia non essere overload.
- Interfaccia attore metodi non devono avere fuori ref o parametri facoltativi.
- Interfacce generiche non sono supportate.

## <a name="create-a-new-project-in-visual-studio"></a>Creare un nuovo progetto in Visual Studio
Dopo aver installato gli strumenti di servizio tessuti per Visual Studio, è possibile creare nuovi tipi di progetto. I nuovi tipi di progetto disponibili nella categoria **Cloud** della finestra di dialogo **Nuovo progetto** .


![Strumenti di servizio tessuti per Visual Studio - nuovo progetto][1]

Nella finestra di dialogo successiva, è possibile scegliere il tipo di progetto che si desidera creare.

![Modelli di progetto tessuti servizio][5]

Per il progetto HelloWorld, utilizzare il servizio di assistenza tessuti affidabile operatori.

Dopo aver creato la soluzione, verrà visualizzata la struttura seguente:

![Struttura del progetto tessuti servizio][2]

## <a name="reliable-actors-basic-building-blocks"></a>Affidabile operatori blocchi predefiniti

Una soluzione affidabile operatori tipica è costituita da tre progetti:

* **Il progetto di applicazione (MyActorApplication)**. Si tratta del progetto che tutti i servizi insieme per la distribuzione di pacchetti. Contiene gli script di PowerShell e *ApplicationManifest.xml* per la gestione dell'applicazione.

* **Il progetto di interfaccia (MyActor.Interfaces)**. Questo è il progetto che contiene la definizione dell'interfaccia per l'attore. Nel progetto MyActor.Interfaces, è possibile definire le interfacce che verranno utilizzate dalle persone della soluzione. Le interfacce attore possono essere definite in qualsiasi progetto con un nome qualsiasi, ma l'interfaccia definisce il contratto attore condivisi dall'implementazione dell'attore e client di chiamare l'attore, in modo che in genere risulta utile definire in un assembly è distinto dall'implementazione dell'attore e può essere condivisi da altri progetti multipli.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Il progetto di servizio attore (MyActor)**. Questo è il progetto utilizzato per definire il servizio di assistenza tessuti che agirà da ospitare attore. Contiene l'implementazione dell'attore. Implementazione dell'attore è una classe che deriva da un tipo di base `Actor` e implementa le interfacce definite nel progetto MyActor.Interfaces. Una classe attore anche necessario implementare un costruttore che accetta un `ActorService` istanza e un `ActorId` e li passa alla base `Actor` classe. In questo modo per l'inserimento delle dipendenze costruttore di dipendenze della piattaforma.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Il servizio attore deve essere registrato con un tipo di servizio in fase di esecuzione del servizio tessuti. Affinché il servizio attore eseguire le istanze dell'attore, è necessario che anche il tipo di attore sia registrato con il servizio attore. Il `ActorRuntime` metodo di registrazione esegue il corretto funzionamento per gli operatori.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se si dispone soltanto una definizione dell'attore iniziare da un nuovo progetto di Visual Studio, la registrazione è incluso per impostazione predefinita nel codice Visual Studio genera. Se si definiscono gli altri nel servizio, è necessario aggiungere la registrazione dell'attore utilizzando:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] Il runtime del servizio tessuti operatori genera alcuni [contatori delle prestazioni e gli eventi correlati a metodi attore](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Possono essere utili nella diagnostica e il monitoraggio delle prestazioni.


## <a name="debugging"></a>Il debug

Gli strumenti di servizio tessuti per Visual Studio supportano il debug sul computer locale. È possibile avviare una sessione di debug premendo il tasto F5. Visual Studio genera, se necessario, pacchetti. Inoltre, distribuisce l'applicazione nel cluster tessuti servizio locale e si connette verrà.

Durante il processo di distribuzione, è possibile visualizzare lo stato di avanzamento nella finestra di **Output** .

![Finestra di output debug tessuti servizio][3]


## <a name="next-steps"></a>Passaggi successivi
 - [Utilizzo di piattaforma tessuti servizio operatori affidabile](service-fabric-reliable-actors-platform.md)
 - [Gestione dello stato dell'attore](service-fabric-reliable-actors-state-management.md)
 - [Insieme di ciclo di vita e garbage attore](service-fabric-reliable-actors-lifecycle.md)
 - [Documentazione di riferimento attore API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Codice di esempio](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
