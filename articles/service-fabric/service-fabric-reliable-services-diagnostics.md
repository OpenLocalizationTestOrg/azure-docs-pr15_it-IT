<properties
   pageTitle="Informazioni sullo stato diagnostica servizi affidabili | Microsoft Azure"
   description="Funzionalità di diagnostica per servizi affidabili informazioni sullo stato"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funzionalità di diagnostica per servizi affidabili informazioni sullo stato
La classe di informazioni sullo stato affidabile servizi StatefulServiceBase genera eventi [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) che possono essere utilizzati per il debug del servizio, fornire informazioni dettagliate in come funziona il runtime e la risoluzione dei problemi.

## <a name="eventsource-events"></a>EventSource eventi
Il nome EventSource per la classe StatefulServiceBase servizi affidabili informazioni sullo stato è "Servizi ServiceFabric Microsoft". Gli eventi da origine degli eventi vengano visualizzati nella finestra di [Eventi di diagnostica](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando il servizio viene [eseguito il debug in Visual Studio](service-fabric-debugging-your-application.md).

Esempi di strumenti e tecnologie per la raccolta e/o di visualizzazione degli eventi EventSource sono [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Microsoft Azure diagnostica](../cloud-services/cloud-services-dotnet-diagnostics.md)e [Raccolta TraceEvent Microsoft](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventi

|Nome dell'evento|ID evento|Livello|Descrizione dell'evento|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informativo|Generato all'avvio di attività RunAsync servizio|
|StatefulRunAsyncCancellation|2|Informativo|Generato quando l'operazione RunAsync servizio è stato annullato|
|StatefulRunAsyncCompletion|3|Informativo|Creato al termine dell'operazione di servizio RunAsync|
|StatefulRunAsyncSlowCancellation|4|Avviso|Generato quando l'operazione RunAsync servizio impiega molto tempo per completare l'annullamento|
|StatefulRunAsyncFailure|5|Errore|Generato quando servizio RunAsync attività genera un'eccezione|

## <a name="interpret-events"></a>Interpretare gli eventi

Eventi StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation sono utili per il writer di servizio per comprendere il ciclo di vita di un servizio, nonché l'intervallo di quando un servizio viene avviato, annullata, è possibile o completato. Può essere utile quando il debug di problemi del servizio o la comprensione ciclo di vita del servizio.

Gli autori del servizio necessario prestare attenzione agli eventi StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure perché indicano problemi relativi al servizio.

StatefulRunAsyncFailure viene generato ogni volta che l'attività di RunAsync() servizio genera un'eccezione. In genere, un'eccezione indica un errore o bug nel servizio. Inoltre, l'eccezione, il servizio di esito negativo, in modo che il messaggio viene spostato un nodo diverso. Questa operazione può essere un'operazione costosa e può ritardare le richieste in arrivo mentre viene spostato il servizio. Gli autori del servizio devono determinare la causa dell'eccezione e, se possibile, ridurre.

StatefulRunAsyncSlowCancellation viene generato ogni volta che una richiesta di annullamento per l'attività RunAsync richiede più di quattro secondi. Quando un servizio impiega molto tempo per completare l'annullamento, influenza la possibilità di essere riavviato rapidamente in un altro nodo del servizio. È possibile configurare la disponibilità complessiva del servizio.
