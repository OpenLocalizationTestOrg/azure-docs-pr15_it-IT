<properties
   pageTitle="Polimorfismo il framework degli operatori affidabile | Microsoft Azure"
   description="Creare gerarchie di .NET interfacce e tipi del framework degli operatori affidabile riutilizzare funzionalità e le definizioni delle API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfismo il framework degli operatori affidabile

Il framework degli operatori affidabile consente di creare gli operatori con molti delle stesse tecniche utilizzate nella finestra di progettazione orientato agli oggetti. Una di queste tecniche è polimorfismo, che consente ai tipi e interfacce in modo che erediti da altre generalized genitori. Ereditarietà nel framework degli operatori affidabile segue in genere il modello .NET con alcuni vincoli aggiuntivi.

## <a name="interfaces"></a>Interfacce

Il framework degli operatori affidabile è necessario definire almeno un'interfaccia da implementare per il tipo di attore. Questa interfaccia viene utilizzata per generare una classe proxy che può essere usata dal client per comunicare con i soggetti. Le interfacce possono ereditare da altre interfacce come ogni interfaccia implementata da un tipo di attore e tutti i principali derivano da IActor. IActor è definito piattaforma interfaccia di base per gli operatori. Pertanto, nell'esempio di polimorfismo classica usando le forme può apparire simile al seguente:

![Gerarchia dell'interfaccia per gli operatori di forma][shapes-interface-hierarchy]


## <a name="types"></a>Tipi di

È anche possibile creare una gerarchia di tipi di attore, derivata dalla classe attore fornita dalla piattaforma. In caso di forme, è possibile avere una base `Shape` tipo:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtypes di `Shape` possibile ignorare metodi dalla base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Nota la `ActorService` attributo del tipo di attore. Questo attributo indica il framework attore affidabile che si desidera creare automaticamente un servizio per l'hosting gli operatori di questo tipo. In alcuni casi, che si desidera creare un tipo di base che è destinato esclusivamente per la condivisione di funzionalità con sottotipi e mai essere utilizzato per creare un'istanza di operatori concreti. In questi casi, è necessario utilizzare la `abstract` parola chiave per indicare che è non necessario creare mai un attore in base al tipo.


## <a name="next-steps"></a>Passaggi successivi

- Vedere [come l'infrastruttura di operatori affidabile utilizza la piattaforma tessuti servizio](service-fabric-reliable-actors-platform.md) per fornire l'affidabilità, scalabilità e stato coerente.
- Informazioni sulle [ciclo di vita dell'attore](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
