<properties
    pageTitle="Guida introduttiva a evento hub con C e Apache eccesso | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare Azure evento hub; invio di eventi in C e averli ricevuti in un cluster di Apache eccesso."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="java"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Guida introduttiva a hub di evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub di evento è un sistema di acquisizione scalabilità che possono assunzione milioni di eventi al secondo, consentire a un'applicazione elaborare e analizzare grandi quantità di dati prodotti dei dispositivi collegati e delle applicazioni. Una volta raccolte in hub di evento, è possibile trasformare e archiviare i dati utilizzando qualsiasi provider di analitica in tempo reale o cluster di memoria.

Per ulteriori informazioni, vedere [Panoramica di hub di evento].

In questa esercitazione verranno fornite come effettuare l'acquisizione di messaggi a un Hub di eventi mediante un'applicazione console c e recuperarli in parallelo utilizzando Apache eccesso.

Per completare questa esercitazione, è necessario quanto segue:

+ Un ambiente di sviluppo C. Per questa esercitazione si presuppone stack gcc in un [Supporto virtuale Linux Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04. Vengono fornite istruzioni per altri ambienti in collegamenti esterni.

+ Ambiente di sviluppo di linguaggio configurato per l'esecuzione [Maven](http://maven.apache.org/). Per questa esercitazione si presuppone [Eclisse](https://www.eclipse.org/).

+ Un account Azure attivo. Se non si dispone di un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto si è pronti eseguire le applicazioni.

1.  Eseguire la classe **LogTopology** da Eclisse e quindi attendere che venga avviato ricevitori per tutte le partizioni.

2.  Eseguire il programma **mittente** e visualizzare gli eventi vengono visualizzati nella finestra del destinatario.

    ![][23]

> [AZURE.NOTE] In questa esercitazione solo usare eccesso in modalità locale in fase di sviluppo. Fare riferimento alla [Panoramica eccesso HDInsight] e la documentazione [Apache eccesso] ufficiale per ulteriori informazioni sulle installazioni eccesso e modelli.

## <a name="next-steps"></a>Passaggi successivi

Le risorse seguenti sono disponibili per lo sviluppo di applicazioni integrazione evento hub ed eccesso.

- [Analisi dei dati sensore con eccesso e HDInsight][] è un'esercitazione scenario completo tramite hub di evento, eccesso e HBase per acquisire dati sensore in un cluster di Hadoop.
- [Sviluppare streaming applicazioni di elaborazione dei dati con SCP.NET e c# in eccesso e HDInsight][] è un'esercitazione su come scrivere pipeline eccesso utilizzando c#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md

[Apache eccesso]: https://storm.incubator.apache.org
[Panoramica di eccesso HDInsight]: ../hdinsight/hdinsight-storm-overview.md/
[Analisi dei dati sensore con eccesso e HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Sviluppare le applicazioni di elaborazione dei dati con SCP.NET e c# in eccesso e HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
