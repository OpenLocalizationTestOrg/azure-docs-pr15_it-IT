<properties
    pageTitle="Guida introduttiva a hub di evento in C e c# | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare Azure evento hub; invio di eventi C e la ricezione di orlo in c# utilizzando il EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Guida introduttiva a hub di evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub di evento è un sistema di acquisizione scalabilità che possono assunzione milioni di eventi al secondo, consentire a un'applicazione elaborare e analizzare grandi quantità di dati prodotti dei dispositivi collegati e delle applicazioni. Una volta raccolte in hub di evento, è possibile trasformare e archiviare i dati utilizzando qualsiasi provider di analitica in tempo reale o cluster di memoria.

Per ulteriori informazioni, vedere [Panoramica di hub di evento][].

In questa esercitazione verranno fornite come effettuare l'acquisizione di messaggi a un Hub di eventi mediante un'applicazione console c e recuperarli in parallelo tramite la raccolta c# [Host processore eventi][] .

Per completare questa esercitazione, è necessario quanto segue:

+ Un ambiente di sviluppo C. Per questa esercitazione si presuppone stack gcc in un [Supporto virtuale Linux Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04. Vengono fornite istruzioni per altri ambienti in collegamenti esterni.

+ Microsoft Visual Studio Express per Windows

+ Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  Eseguire il progetto **ricevitore** in Visual Studio, quindi attendere che venga avviato ricevitori per tutte le partizioni.

    ![][21]

2.  Eseguire il programma **mittente** e visualizzare gli eventi vengono visualizzati nella finestra del destinatario.

    ![][24]

## <a name="next-steps"></a>Passaggi successivi

Ora che è già stata creata un'applicazione che crea un Hub di eventi e invia e riceve i dati, è possibile spostare per gli scenari seguenti:

- Un' [applicazione di esempio che utilizza evento hub][]completa.
- Esempio [scalabilità evento elaborazione con gli hub di evento][] .
- [Cenni preliminari sui hub di eventi][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Evento processore Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scalabilità evento elaborazione con gli hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
