<properties
   pageTitle="Eventi operatori affidabili | Microsoft Azure"
   description="Introduzione agli eventi per servizio tessuti affidabile operatori."
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
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>Eventi attore
Eventi attore consentono di inviare notifiche usufruendo dall'attore ai client. Eventi attore sono progettati per la comunicazione attore per client e non devono essere utilizzati per la comunicazione dell'attore per attore.

Frammenti di codice seguenti viene illustrato come utilizzare gli eventi dell'attore nell'applicazione.

Definire un'interfaccia che descrive gli eventi pubblicati dall'autore. Questa interfaccia deve essere derivata dalla `IActorEvents` interfaccia. Gli argomenti dei metodi devono essere [contratto serializzabile dati](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). I metodi devono restituire void, come evento notifiche sono un modo e programmazione migliore.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Dichiarare gli eventi pubblicati da attore nell'interfaccia di attore.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

Sul lato client implementare il gestore eventi.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Sul client, creare un proxy per attore che pubblica l'evento e sottoscrivere eventi.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

In caso di failover attore potrebbe non riuscire su un altro processo o nodo. Proxy attore gestisce sottoscrizioni attive e automaticamente sottoscrive nuovamente loro. È possibile controllare l'intervallo di nuova sottoscrizione tramite il `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Per annullare la sottoscrizione, utilizzare la `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

In attore, è sufficiente pubblicare gli eventi in tempo reale. Se sono presenti sottoscrittori dell'evento, il runtime operatori inviare loro la notifica.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Passaggi successivi
 - [Rientranza attore](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnostica attore e il monitoraggio delle prestazioni](service-fabric-reliable-actors-diagnostics.md)
 - [Documentazione di riferimento attore API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Codice di esempio](https://github.com/Azure/servicefabric-samples)
