<properties
    pageTitle="Guida introduttiva a hub di evento in linguaggio con eccesso Apache | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare Azure evento hub; gli eventi con linguaggio di invio e ricezione loro in un cluster di Apache eccesso."
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="sethm"/>

# <a name="get-started-with-event-hubs"></a>Guida introduttiva a hub di evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub di evento è un sistema di acquisizione scalabilità che possono assunzione milioni di eventi al secondo, consentire a un'applicazione elaborare e analizzare grandi quantità di dati prodotti dei dispositivi collegati e delle applicazioni. Una volta raccolte in hub di evento, è possibile trasformare e archiviare i dati utilizzando qualsiasi provider di analitica in tempo reale o cluster di memoria.

Per ulteriori informazioni, vedere la [Panoramica hub evento][].

In questa esercitazione viene descritto come raccogliere i messaggi a un Hub di eventi mediante un'applicazione console Java e per recuperarli in parallelo utilizzando Apache eccesso.

Per completare questa esercitazione, è necessario quanto segue:

+ Ambiente di sviluppo di linguaggio configurato per l'esecuzione [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone [Eclisse](https://www.eclipse.org/).

+ Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  Eseguire la classe **LogTopology** da Eclisse e quindi attendere che venga avviato ricevitori per tutte le partizioni.

2.  Eseguire il progetto **mittente** , premere **INVIO** nella finestra della console e visualizzare gli eventi vengono visualizzati nella finestra del destinatario.

    ![][22]

> [AZURE.NOTE] In questa esercitazione solo usare eccesso in modalità locale in fase di sviluppo. Vedere la [Panoramica eccesso HDInsight][] e la documentazione [Apache eccesso][] ufficiale per ulteriori informazioni sulle installazioni eccesso e modelli.

## <a name="next-steps"></a>Passaggi successivi

Le risorse seguenti sono disponibili per lo sviluppo di applicazioni integrazione evento hub ed eccesso.

- [Analisi dei dati sensore con eccesso e HDInsight] è un'esercitazione scenario completo tramite hub di evento, eccesso e HBase per acquisire dati sensore in un cluster di Hadoop.
- [Sviluppare streaming applicazioni di elaborazione dei dati con SCP.NET e c# in eccesso e HDInsight][] è un'esercitazione su come scrivere pipeline eccesso utilizzando c#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md

[Apache eccesso]: https://storm.incubator.apache.org
[Panoramica di eccesso HDInsight]: ../hdinsight/hdinsight-storm-overview.md
[Analisi dei dati sensore con eccesso e HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Sviluppare le applicazioni di elaborazione dei dati con SCP.NET e c# in eccesso e HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 