<properties
    pageTitle="Guida introduttiva a hub di evento in c# | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare Azure evento hub; invio di eventi in c# e averli ricevuti in Java utilizzando il EventProcessorHost."
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
    ms.topic="hero-article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Guida introduttiva a hub di evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub di evento è un servizio che elabora grandi quantità di dati dell'evento (telemetria) dalle applicazioni e i dispositivi connessi. Dopo aver raccolto dati nell'hub di evento, è possibile archiviare i dati utilizzando un cluster di memoria o trasformare i dati utilizzando un provider analitica in tempo reale. Questa funzionalità di elaborazione e raccolta evento su larga scala è un componente chiave di architetture di applicazione moderna incluse Internet di elementi (IoT).

In questa esercitazione viene illustrato come utilizzare il portale classico Azure per creare un Hub di evento. Viene inoltre come raccogliere i messaggi a un Hub di evento usando un'applicazione console scritta in c# e come recuperarli in parallelo tramite la raccolta linguaggio evento processore Host.

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un account Azure attivo. <br/>Se non si dispone di uno, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  Eseguire il progetto **ricevitore** .

    ![][21]

2.  Eseguire il progetto **mittente** .

    ![][22]

## <a name="next-steps"></a>Passaggi successivi

Ora che è già stata creata un'applicazione che crea un Hub di eventi e invia e riceve i dati, è possibile spostare per gli scenari seguenti:

- Un' [applicazione di esempio che utilizza evento hub][]completa.
- Esempio [scalabilità evento elaborazione con gli hub di evento][] .
- [Cenni preliminari sui hub di eventi][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scalabilità evento elaborazione con gli hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
