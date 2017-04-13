<properties
    pageTitle="Che cos'è Azure evento hub? | Microsoft Azure"
    description="Panoramica e la descrizione di Azure evento hub"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="sethm"/>

# <a name="what-is-azure-event-hubs"></a>Che cos'è Azure evento hub?

Azure hub di evento è un servizio di ingresso scalabilità dati che è in grado di acquisire milioni di eventi al secondo in modo che è possibile elaborare e analizzare grandi quantità di dati prodotti per i dispositivi connessi e le applicazioni. Hub evento funge da "porta anteriore" per una pipeline di eventi e una volta raccolti dati a un Hub di evento, può essere trasformato e memorizzati tramite qualsiasi provider in tempo reale analitica o le schede batch/lo spazio di archiviazione. Hub evento separa produzione di un flusso di eventi dal consumo di questi eventi, in modo che gli utenti di evento possano accedere gli eventi nella propria pianificazione. Per ulteriori informazioni e dettagli tecnici, vedere la [Panoramica hub evento](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Funzionalità di hub dell'evento

Hub di evento è un evento servizio che fornisce eventi e l'elaborazione di telemetria in scala enorme con latenza bassa e affidabilità di elaborazione. Questo servizio è particolarmente utile per:

- Strumentazione delle applicazioni
- Esperienza utente o elaborazione del flusso di lavoro
- Scenari di Internet di elementi (IoT)

Alcune altre funzionalità hub eventi importanti includono comportamento verifica in App per dispositivi mobili, il traffico di informazioni dal web farm degli eventi di gioco nella console giochi o telemetria acquisite dal computer industriali o veicoli connessi.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sull'hub di evento, vedere gli argomenti seguenti.

- [Cenni preliminari sui hub di eventi](event-hubs-overview.md)
- [Hub evento programmazione manuale](event-hubs-programming-guide.md)
- [Domande frequenti su disponibilità e supporto tecnico hub di evento](event-hubs-availability-and-support-faq.md)
- Guida introduttiva a un [evento hub esercitazione][]
- Un' [applicazione di esempio che utilizza evento hub][] completa

[Esercitazione hub di evento]: event-hubs-csharp-ephcs-getstarted.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
