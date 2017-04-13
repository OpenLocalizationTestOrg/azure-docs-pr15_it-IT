<properties 
    pageTitle="Transazioni Bus di servizio | Microsoft Azure" 
    description="Panoramica delle transazioni atomiche Bus di servizio Azure e inviarlo tramite" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>Panoramica dell'elaborazione delle transazioni Bus di servizio

Questo articolo vengono illustrate le funzionalità di transazione del Bus di servizio Azure. Gran parte della discussione viene illustrata dalla [Atomica transazioni con Bus di servizio di esempio](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). In questo articolo è limitato a una panoramica dell'elaborazione delle transazioni e la funzionalità *Invia tramite* Bus di servizio, mentre il campione transazioni atomica è più complessa e più ampi nell'ambito.

## <a name="transactions-in-service-bus"></a>Transazioni nel Bus di servizio

Una [transazione](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) Raggruppa due o più operazioni in un *ambito di esecuzione*. Per natura, ad esempio una transazione necessario assicurarsi che tutte le operazioni che appartengono a un determinato gruppo di operazioni esito positivo o negativo congiuntamente. A questo proposito transazioni fungono da un'unità, è spesso in *atomicità*. 

Servizio Bus è un gestore di messaggi di transazione e garantisce integrità per tutte le operazioni interne con gli archivi il messaggio. Tutti i trasferimenti di messaggi all'interno di Bus di servizio, ad esempio lo spostamento di messaggi a una [coda inattiva](service-bus-dead-letter-queues.md) o [inoltro automatico](service-bus-auto-forwarding.md) dei messaggi tra entità, sono transazioni. Di conseguenza, se Bus di servizio accetta un messaggio, già archiviato e con un numero di sequenza etichetta. In poi, i trasferimenti di messaggi all'interno del servizio Bus sono operazioni coordinate tra entità e non comporta perdita (ha avuto esito positivo di origine e destinazione ha esito negativo) o per la duplicazione (si verifica un errore di origine e destinazione ha avuto esito positivo) del messaggio.

Servizio Bus supporta operazioni di raggruppamento in base a una singola entità messaggistica (coda, argomento, abbonamento) all'interno dell'ambito di una transazione. Ad esempio, è possibile inviare messaggi diversi a una coda dall'interno di un ambito di transazione e i messaggi solo verrà eseguito il commit log della coda quando la transazione viene completata correttamente.

## <a name="operations-within-a-transaction-scope"></a>Operazioni all'interno di un ambito di transazione 

Le operazioni che possono essere eseguite all'interno di un ambito di transazione sono i seguenti:

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: inviare, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: completa, CompleteAsync, Abandon, AbandonAsync, messaggi non recapitabili, DeadletterAsync, rinviare la gestione, DeferAsync, RenewLock, RenewLockAsync 

Ricevere operazioni non sono incluse, perché si presuppone che l'applicazione acquisisce i messaggi utilizzando la modalità di [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , all'interno di alcuni ricevono Esegui ciclo continuo o con un [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) callback e quindi solo viene aperto un ambito di transazione per l'elaborazione del messaggio.

Disposizione del messaggio (completo, abandon, inattiva, rinviare) verrà generato nell'ambito di e dipendenti nel risultato complessivo della transazione.

## <a name="transfers-and-send-via"></a>Trasferimenti e "Invia tramite"

Per abilitare transazione passaggio dei dati da una coda a un processore e quindi in un'altra coda, Bus di servizio supporta *i trasferimenti*. In un'operazione di trasferimento, coda di trasferimento immediatamente si sposta il messaggio nella coda di destinazione prevista utilizzando la stessa implementazione di trasferimento affidabile la funzionalità di inoltro automatico si basa su un mittente prima di tutto Invia un messaggio a una coda di trasferimento"". Il messaggio non è mai si impegna a log della coda trasferimento in modo da rendere visibile per gli utenti della coda trasferimento.

La potenza di questa funzionalità transazione risulta evidente quando la coda di trasferimento rappresenta l'origine dei messaggi di input del mittente. In altre parole, Bus di servizio possibile trasferire il messaggio alla coda di destinazione "tramite" coda trasferimento durante l'esecuzione di una completa (o rinviare, o lettera inattiva) operazione nel messaggio di input, in una singola operazione atomica. 

### <a name="see-it-in-code"></a>Vedere nel codice

Per configurare tali trasferimenti, si crea un mittente del messaggio destinato a coda di destinazione tramite la coda di trasferimento. È anche un ricevitore che recupera i messaggi provenienti da tale coda stessa. Per esempio:

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Una semplice transazione quindi utilizza questi elementi, come illustrato nell'esempio seguente:

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per ulteriori informazioni sulle code Bus di servizio:

- [Concatenazione entità Bus di servizio con inoltro automatico](service-bus-auto-forwarding.md)
- [Esempio di inoltro automatico](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Transazioni atomiche con Bus di servizio](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Azure code e servizio Bus code compared](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Come usare code Bus di servizio](service-bus-dotnet-get-started-with-queues.md)