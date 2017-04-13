<properties
    pageTitle="Guida introduttiva a hub di evento in c# con eccesso Apache | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare Azure evento hub; invio di eventi in c# e averli ricevuti in un cluster di Apache eccesso."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Guida introduttiva a hub di evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub di evento è un sistema di acquisizione scalabilità che possono assunzione milioni di eventi al secondo, consentire a un'applicazione elaborare e analizzare grandi quantità di dati prodotti dei dispositivi collegati e delle applicazioni. Una volta raccolte in hub di evento, è possibile trasformare e archiviare i dati utilizzando qualsiasi provider di analitica in tempo reale o cluster di memoria.

Per ulteriori informazioni, vedere la [Panoramica hub evento].

In questa esercitazione verranno fornite come effettuare l'acquisizione di messaggi a un Hub di evento in un'applicazione console c# e recuperarli in parallelo utilizzando Apache eccesso.

Per completare questa esercitazione è necessario quanto segue:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Ambiente di sviluppo di linguaggio configurato per l'esecuzione [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone [Eclisse](https://www.eclipse.org/)

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Versione di valutazione gratuita di Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  Eseguire la classe **LogTopology** da Eclisse e quindi attendere che venga avviato ricevitori per tutte le partizioni.

2.  Eseguire il progetto **mittente** , premere **INVIO** nella finestra della console e visualizzare gli eventi vengono visualizzati nella finestra del destinatario.

    ![][22]

## <a name="next-steps"></a>Passaggi successivi

Ora che è già stata creata un'applicazione che crea un Hub di eventi e invia e riceve i dati, è possibile spostare per gli scenari seguenti:

- Un' [applicazione di esempio che utilizza evento hub][]completa.
- Esempio [scalabilità evento elaborazione con gli hub di evento][] .

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scalabilità evento elaborazione con gli hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 