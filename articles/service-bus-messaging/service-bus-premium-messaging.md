<properties
    pageTitle="Servizio Bus Premium e la messaggistica Standard prezzi Panoramica livelli | Microsoft Azure"
    description="Servizio Bus Premium e la messaggistica Standard"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Servizio Bus Premium e livelli di messaggistica Standard 

Bus di servizio di messaggistica, che include entità, ad esempio code di messaggistica e argomenti, messaggistica funzionalità con RTF aziendale combina pubblicano sottoscrizione semantica scala cloud. Servizio Bus messaggistica viene usata come la dorsale di comunicazione per molti sofisticate soluzioni cloud.

Il livello di *Premium* Bus di servizio messaggistica indirizzi comuni richieste dei clienti intorno scala, le prestazioni e la disponibilità per applicazioni critiche. Anche se il set di funzionalità sono quasi identici, questi due livelli di servizio Bus messaggistica sono progettati per gestire casi di utilizzo diversi.

Nella tabella seguente vengono evidenziate alcune differenze di alto livello.

| Premium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Alta velocità                       | Velocità variabile            |
| Prestazioni prevedibili               | Latenza variabile               |
| Prezzi prevedibili                   | Prezzi variabili pagare in base all'uso |
| Possibilità di scalare su e giù carico di lavoro | N/D                            |
| > 256KB la dimensione dei messaggi                  | Dimensione dei messaggi è 256KB          |

**Servizio Bus Premium messaggistica** fornisce un isolamento di risorse al livello di CPU e memoria in modo che il carico di lavoro ogni cliente viene eseguito in isolamento. In questo contenitore di risorse è chiamato un *messaggistica unità*. Ogni spazio dei nomi premium allocata almeno un'unità SMS. È possibile acquistare 1, 2 o 4 unità di messaggistica per ogni servizio Bus Premium spazio dei nomi. Una singola carico di lavoro o un'entità può occupare più unità di messaggistica e il numero di unità messaggistica può essere modificato piacimento, sebbene la fatturazione in base alle tariffe tariffa giornaliera o 24 ore. Il risultato è prevedibili prestazioni per le soluzioni basate su Bus di servizio.

Non solo è questa prestazioni più prevedibili e disponibile, ma è veloce. Servizio Bus Premium messaggistica si basa sul motore di archiviazione introdotto in [Azure evento hub](https://azure.microsoft.com/services/event-hubs/). Grazie alla messaggistica Premium, sono molto più veloce con il livello Standard prestazioni ottimali.

## <a name="premium-messaging-technical-differences"></a>Differenze tecniche messaggistica Premium

Di seguito sono alcune differenze tra Premium e livelli di messaggistica Standard.

### <a name="partitioned-queues-and-topics"></a>Gli argomenti e code partizionate

Gli argomenti e code partizionate sono supportati in Premium messaggistica, ma non funzionano allo stesso modo come i livelli Basic e Standard di messaggistica Bus di servizio. Messaggistica Premium non utilizza SQL come archivio dati e non è più concorrenza risorsa possibili associata una piattaforma condivisa. Di conseguenza, partizione non è necessario. Inoltre, il numero totale di partizione è stato modificato da 16 partizioni i messaggi Standard a 2 partizioni Premium. Con due partizioni garantisce la disponibilità e un numero più appropriato per l'ambiente di runtime Premium. Per ulteriori informazioni sulle partizioni, vedere [code partizionati e argomenti](service-bus-partitioning.md).

### <a name="express-entities"></a>Entità Express

Poiché Premium messaggistica viene eseguito in un ambiente di runtime completamente isolato, entità express non sono supportate in Premium gli spazi dei nomi. Per ulteriori informazioni sulla caratteristica express, vedere la proprietà [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla messaggistica Bus di servizio, vedere gli argomenti seguenti.

- [Introduzione a Azure servizio Bus Premium messaggistica (post di blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introduzione a Azure servizio Bus Premium messaggistica (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Cenni preliminari sulla messaggistica Bus di servizio](service-bus-messaging-overview.md)
- [Come usare code Bus di servizio](service-bus-dotnet-get-started-with-queues.md)
