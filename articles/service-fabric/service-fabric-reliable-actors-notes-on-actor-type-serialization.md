<properties
   pageTitle="Note operatori affidabile attore digitare serializzazione | Microsoft Azure"
   description="Illustra i requisiti di base per la definizione di DLL che può essere utilizzato per definire interfacce e operatori affidabile tessuti servizio uniti"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Note sul servizio tessuti affidabile operatori digitare serializzazione


Gli argomenti di tutti i metodi, i tipi di risultati dei compiti restituiti da ogni metodo in un'interfaccia attore e gli oggetti archiviati in Gestione dello stato dell'attore devono essere [Contratto dati serializzabile](https://msdn.microsoft.com/library/ms731923.aspx).. Si applica anche agli argomenti dei metodi definiti in [interfacce eventi attore](service-fabric-reliable-actors-events.md#actor-events). (Attore evento interfaccia metodi restituiscono sempre void.)

## <a name="custom-data-types"></a>Tipi di dati personalizzati

In questo esempio, l'interfaccia attore seguente definisce un metodo che restituisce un tipo di dati personalizzati denominato `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

L'interfaccia è impelemented da un attore che utilizza il responsabile stato per archiviare un `VoicemailBox` oggetto:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

In questo esempio, il `VoicemailBox` l'oggetto è serializzato quando:
 - L'oggetto viene trasmesso tra un'istanza dell'attore e un chiamante.
 - L'oggetto è salvato il responsabile stato dove viene mantenuto nel disco rigido e replicata in altri nodi.
 
Il framework attore affidabile utilizza DataContract serializzazione. Di conseguenza, gli oggetti dati personalizzati e i relativi membri devono essere contrassegnate con gli attributi **DataContract** e **DataMember** , rispettivamente

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Passaggi successivi
 - [Insieme di ciclo di vita e garbage attore](service-fabric-reliable-actors-lifecycle.md)
 - [Promemoria e attore timer](service-fabric-reliable-actors-timers-reminders.md)
 - [Eventi attore](service-fabric-reliable-actors-events.md)
 - [Rientranza attore](service-fabric-reliable-actors-reentrancy.md)
 - [Polimorfismo attore e modelli di progettazione orientato agli oggetti](service-fabric-reliable-actors-polymorphism.md)
 - [Diagnostica attore e il monitoraggio delle prestazioni](service-fabric-reliable-actors-diagnostics.md)
