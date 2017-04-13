<properties 
    pageTitle="Servizio Bus code, argomenti e le sottoscrizioni | Microsoft Azure"
    description="Panoramica del servizio Bus messaggistica entità."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/14/2016"
    ms.author="sethm" />

# <a name="service-bus-queues-topics-and-subscriptions"></a>Abbonamenti, argomenti e code Bus di servizio

Bus di servizio di Microsoft Azure supporta un insieme di tecnologie basate su cloud, middleware orientato ai messaggi tra Accodamento affidabile e permanente pubblicazione/sottoscrizione messaggistica. Queste funzionalità di messaggistica gestita possono essere considerate come disaccoppiati messaggistica caratteristiche supporto pubblicazione sottoscrizione temporale separazione e scenari di utilizzo di Bus di servizio di messaggistica tessuti di bilanciamento del carico. Comunicazione disaccoppiata offre molti vantaggi; ad esempio, client e server possono connettersi in base alle esigenze ed eseguire le operazioni in modo asincrono.

Le entità messaggistica che costituiscono il nucleo delle funzionalità di messaggistica gestite nel servizio Bus sono code, argomenti/sottoscrizioni e regole/azioni.

## <a name="queues"></a>Code

Code offrono prima In recapito dei messaggi FIFO (First Out) a uno o più consumer concorrenti. Ovvero messaggi sono in genere dovrebbe essere ricevuti ed elaborati per i destinatari nell'ordine in cui sono stati aggiunti alla coda e ogni messaggio viene ricevuta ed elaborata da consumer solo un messaggio. Dei principali vantaggi dell'utilizzo di code è ottenere "separazione temporale" dei componenti dell'applicazione. In altre parole, produttori (mittenti) e consumatori (ricevitori) non è necessario inviare e ricevere messaggi contemporaneamente, perché sono archiviati in modo permanente i messaggi nella coda. Inoltre, il produttore non è necessario attendere una risposta dal consumer per continuare a elaborare e inviare messaggi.

Dei vantaggi correlato è "caricare il livellamento," che consente a produttori e consumatori di inviare e ricevere messaggi a tassi diversi. In molte applicazioni, il carico di sistema varia nel tempo; Tuttavia, il tempo necessario per ogni unità di lavoro è in genere costante. Produttori messaggio intermediating e clienti con una coda indica che l'applicazione che solo effettuare il provisioning per poter gestire carico medio anziché punta. Antero-posteriore della coda aumenta e contratti del carico in arrivo. In questo modo direttamente denaro per quanto riguarda l'importo dell'infrastruttura necessaria per soddisfare il carico dell'applicazione. Quando il carico aumenta, è possono aggiungere più processi di lavoro da leggere dalla coda. Ogni messaggio viene elaborato solo da uno dei processi di lavoro. Inoltre, questo il pull bilanciamento del carico consente per un uso ottimale dei computer di lavoro anche se il computer di lavoro si differenziano per quanto riguarda la potenza di elaborazione, come estraggono messaggi sua velocità massima. Questo modello è spesso detto motivo "concorrenti consumer".

Utilizzo di code intermedio tra consumatori e dei produttori messaggio offre un inerente aggancio separati tra i componenti. Poiché produttori e consumatori non siano a conoscenza a altro, è possibile aggiornare un consumer senza alcun effetto sul produttore.

Creazione di una coda è un processo a più passaggi. Eseguire operazioni di gestione per Bus di servizio di messaggistica entità (code e argomenti) tramite la classe [Microsoft.ServiceBus.NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , che sia impostata specificando l'indirizzo di base dello spazio dei nomi Bus di servizio e le credenziali utente. [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornisce metodi per creare, enumerare ed eliminare entità messaggistica. Dopo aver creato un oggetto [Microsoft.ServiceBus.TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) dal nome SA e chiave e un oggetto di gestione dello spazio dei nomi del servizio, è possibile utilizzare il metodo [Microsoft.ServiceBus.NamespaceManager.CreateQueue](https://msdn.microsoft.com/library/azure/hh293157.aspx) per creare la coda. Per esempio:

```
// Create management credentials
TokenProvider credentials = TokenProvider. CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

È possibile creare un oggetto coda e una factory messaggistica con l'URI Bus servizio come argomento. Per esempio:

```
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

È quindi possibile inviare messaggi alla coda. Ad esempio, se si dispone di un elenco dei messaggi gestiti chiamato `MessageList`, il codice è simile al seguente:

```
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

È quindi possibile ricevere messaggi dalla coda, come indicato di seguito:

```
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

In modalità [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) l'operazione di ricezione è singola ripresa; vale a dire quando Bus di servizio riceve la richiesta, contrassegna il messaggio come utilizzata e restituisce all'applicazione. Modalità di [ReceiveAndDelete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) il modello più semplice e funziona meglio per gli scenari in cui l'applicazione tollerabile non l'elaborazione di un messaggio in caso di errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus contrassegna il messaggio come consumate, quando l'applicazione riavvia e inizia a utilizzare nuovamente i messaggi, essa verrà perse il messaggio che è stato utilizzato prima l'arresto anomalo.

In modalità [PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , l'operazione di ricezione diventa due fasi, che consente alle applicazioni di supporto che non è possibile tollerare messaggi mancanti. Quando servizio Bus riceve la richiesta, trova il messaggio successivo per l'utilizzo, in modo da impedire che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamando [completata](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) nel messaggio ricevuto. Quando servizio Bus rileva [completa](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) chiamata, contrassegna il messaggio come utilizzata.

Se l'applicazione è in grado di elaborare il messaggio per qualche motivo, è possibile chiamare il metodo [abbandonare](https://msdn.microsoft.com/library/azure/hh181837.aspx) sul messaggio ricevuto (invece di [completamento](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). In questo modo Bus di servizio sbloccare il messaggio e renderlo disponibile per la ricezione di nuovo dal consumer stesso o da un altro consumer concorrente. In secondo luogo, è presente un timeout associato al blocco e se l'applicazione non riesce a elaborare il messaggio prima del blocco timeout scade (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio si aprono il messaggio e si rende disponibile al ricevuti nuovamente (essenzialmente eseguire un'operazione [abbandonare](https://msdn.microsoft.com/library/azure/hh181837.aspx) per impostazione predefinita).

Si noti che nel caso in cui l'applicazione determina l'arresto anomalo dopo l'elaborazione del messaggio, ma prima di rilasciata la richiesta di [completamento](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) , il messaggio viene recapitato nuovamente dell'applicazione quando si riavvia. Spesso detta *Almeno una volta *elaborazione; vale a dire ogni messaggio viene elaborato almeno una volta. Tuttavia, in alcuni casi potrebbe recapitato nuovamente lo stesso messaggio. Se lo scenario non è possibile tollerare trattamento duplicati, quindi è necessario altro codice nell'applicazione per rilevare i duplicati che possono essere raggiunto in base alle proprietà **ID messaggio** del messaggio, rimane costante tra tentativi di recapito. Tale valore è noto come *Una sola volta* l'elaborazione.

Per ulteriori informazioni e un esempio di utilizzo di come creare e inviare messaggi a e dalle code, vedere [servizio Bus negoziate messaggistica .NET esercitazione](service-bus-brokered-tutorial-dotnet.md).

## <a name="topics-and-subscriptions"></a>Gli argomenti e le sottoscrizioni

A differenza di code, in cui ogni messaggio viene elaborato da un utente singolo, *argomenti* e *le sottoscrizioni* offrono una maschera uno-a-molti di comunicazione, in un modello di *pubblicazione/sottoscrizione* . Utile per il ridimensionamento di un numero elevato di destinatari, ogni messaggio pubblicato viene resa disponibile per ciascuna sottoscrizione registrate con l'argomento. I messaggi inviati a un argomento e recapitati a una o più sottoscrizioni associate, a seconda delle regole di filtro che è possibile impostare una scala cronologica per abbonamento. Le sottoscrizioni è possono usare altri filtri per limitare i messaggi che si desiderano ricevere. I messaggi vengono inviati a un argomento nello stesso modo vengono inviate a una coda, ma i messaggi non vengono ricevuti direttamente da un argomento. Se, tuttavia, vengono ricevuti da abbonamenti. Una sottoscrizione argomento è simile a una coda virtuale che riceve le copie dei messaggi inviati all'argomento. I messaggi vengono ricevuti da una sottoscrizione allo stesso modo il modo in cui che vengono ricevuti da una coda.

Titolo di confronto, la funzionalità di invio di messaggi di una coda mapping direttamente a un argomento e le relative funzionalità di ricezione del messaggio associato a un abbonamento. Ad esempio, ciò significa che le sottoscrizioni supportano gli stessi modelli descritti in precedenza in questa sezione per quanto riguarda code: consumer concorrenti, separazione temporale, carico livellamento e il bilanciamento del carico.

La creazione di un argomento è simile alla creazione di una coda, come illustrato nell'esempio nella sezione precedente. Creare l'URI del servizio e quindi utilizzare la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) per creare il client di spazio dei nomi. È quindi possibile creare un argomento utilizzando il metodo [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) . Per esempio:

```
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Aggiungere quindi abbonamenti nel modo desiderato:

```
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

È quindi possibile creare un client argomento. Per esempio:

```
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Usando il mittente del messaggio, è possibile inviare e ricevere messaggi da e verso l'argomento come illustrato nella sezione precedente. Per esempio:

```
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Analogamente alle code, i messaggi vengono ricevuti da una sottoscrizione tramite un oggetto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) invece che con un oggetto [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Creare il client di abbonamento, passando il nome dell'argomento, il nome della sottoscrizione e, facoltativamente, la modalità di ricezione come parametri. Ad esempio, con la sottoscrizione di **inventario** :

```
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Le regole e azioni

In molti scenari, i messaggi che hanno caratteristiche specifiche devono essere elaborati in modi diversi. A tale scopo, è possibile configurare le sottoscrizioni per trovare i messaggi che hanno bene accolta proprietà e quindi apportare alcune modifiche a tali proprietà. Mentre le sottoscrizioni di servizio Bus vedere tutti i messaggi inviati all'argomento, è possibile copiare solo un sottoinsieme dei messaggi in coda virtuale abbonamento. Eseguire questa operazione utilizzare i filtri di sottoscrizione. Tali modifiche sono denominate *operazioni filtro*. Quando si crea una sottoscrizione, è possibile fornire un'espressione di filtro che opera sulle proprietà del messaggio, la proprietà-sistema (ad esempio l' **etichetta**) e proprietà personalizzate dell'applicazione (ad esempio **StoreName**.) Espressione di filtro SQL è facoltativo. senza un'espressione di filtro SQL qualsiasi azione filtro definito un abbonamento verrà eseguita su tutti i messaggi per la sottoscrizione.

Nel caso dell'esempio precedente, per filtrare i messaggi provenienti solo da **1**, è necessario creare la sottoscrizione di Dashboard come indicato di seguito:

```
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Con questo filtro della sottoscrizione in posizione, solo i messaggi contenenti la `StoreName` impostata su `Store1` vengono copiati nella coda virtuale per la `Dashboard` sottoscrizione.

Per ulteriori informazioni sui valori di filtro possibili, vedere la documentazione per le classi [SqlFilter](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx) e [SqlRuleAction](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlruleaction.aspx) . Vedere anche la [negoziate messaggistica: filtri avanzati](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) ed esempi di [Filtri di argomento](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) .

## <a name="next-steps"></a>Passaggi successivi

Vedere le seguenti avanzate argomenti per ulteriori informazioni ed esempi di utilizzo Bus di servizio negoziate messaggistica entità.

- [Cenni preliminari sulla messaggistica Bus di servizio](service-bus-messaging-overview.md)
- [Servizio Bus negoziate messaggistica .NET esercitazione](service-bus-brokered-tutorial-dotnet.md)
- [Servizio Bus negoziate messaggistica resto esercitazione](service-bus-brokered-tutorial-rest.md)
- [Esempio di filtri argomento](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters)
- [Messaggistica gestita: Avanzate esempio filtri](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

