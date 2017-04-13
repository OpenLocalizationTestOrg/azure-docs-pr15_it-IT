<properties
    pageTitle="Guida introduttiva a hub di evento in linguaggio | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare Azure evento hub; gli eventi con linguaggio di invio e ricezione loro in c# utilizzando il EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="core"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Guida introduttiva a hub di evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub di evento è un sistema di acquisizione scalabilità che possono assunzione milioni di eventi al secondo, consentire a un'applicazione elaborare e analizzare grandi quantità di dati prodotti dei dispositivi collegati e delle applicazioni. Una volta raccolte in hub di evento, è possibile trasformare e archiviare i dati utilizzando qualsiasi provider di analitica in tempo reale o cluster di memoria.

Per ulteriori informazioni, vedere la [Panoramica hub evento][].

Questa esercitazione viene illustrato come caricare i messaggi a un Hub di eventi mediante un'applicazione console in linguaggio e recuperarli in parallelo tramite la raccolta c# [Host processore eventi][] .

Per completare questa esercitazione, è necessario quanto segue:

+ Un ambiente di sviluppo di linguaggio. Per questa esercitazione si presuppone [Eclisse](https://www.eclipse.org/).

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Versione di valutazione gratuita di Azure</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  Eseguire il progetto **ricevitore** da Visual Studio, quindi attendere che venga avviato i destinatari per tutte le partizioni.

    ![][21]

2.  Eseguire il progetto **mittente** .

    ![][22]

## <a name="next-steps"></a>Passaggi successivi

Ora che è già stata creata un'applicazione che crea un Hub di eventi e invia e riceve i dati, è possibile spostare per gli scenari seguenti:

- Un' [applicazione di esempio che utilizza evento hub][]completa.
- Esempio [scalabilità evento elaborazione con gli hub di evento][] .

Per ulteriori informazioni, vedere il [Centro per sviluppatori di linguaggio](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/java-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Evento processore Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scalabilità evento elaborazione con gli hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 