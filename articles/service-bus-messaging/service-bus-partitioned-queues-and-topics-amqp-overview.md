<properties 
    pageTitle="Supporto di AMQP 1.0 per servizio Bus suddiviso code e argomenti | Microsoft Azure" 
    description="Informazioni sull'uso avanzato messaggio Accodamento Protocol (AMQP) 1.0 con Service Bus suddiviso code e argomenti." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>Supporto di AMQP 1.0 per servizio Bus suddiviso code e argomenti 

Azure Bus di servizio supporta ora avanzate messaggio Accodamento protocollo (**AMQP**) 1.0 per servizio Bus **suddiviso code e gli argomenti della.**

**AMQP** è un protocollo Accodamento messaggio Apri standard che consente di sviluppo di applicazioni multipiattaforma utilizzando diversi linguaggi di programmazione. Per informazioni generali su AMQP Bus di servizio di supporto, vedere [1.0 AMQP Bus di servizio di supporto](service-bus-amqp-overview.md).

**Code partizionati e gli argomenti della**, noto anche come *entità partizionata*, offrono maggiore disponibilità, l'affidabilità e trasmissione maggiore rispetto a convenzionale code non suddiviso e argomenti. Per ulteriori informazioni sulle entità partizionata, vedere [Partizionata messaggistica entità](service-bus-partitioning.md).

L'aggiunta di AMQP 1.0 come protocollo per comunicare con gli argomenti e code partizionate consente di creare applicazioni interoperabile che possono usufruire di maggiore disponibilità, l'affidabilità e nell'intera offerti dai entità Bus di servizio suddiviso.

Per informazioni dettagliate filo a livello AMQP 1.0 protocollo, che spiega come servizio Bus implementa e si basa sulla specifica tecnica AMQP OASIS, vedere [AMQP 1.0 Bus di servizio Azure e hub evento protocollo Guida](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Utilizzare AMQP con code partizionate

Code sono utili per gli scenari che richiedono la separazione temporale, carico livellamento, bilanciamento del carico e aggancio separato. Con una coda autori inviano messaggi alla coda e consumatori ricevano i messaggi dalla coda, nei casi in cui un messaggio può essere ricevuto solo una volta. Un buon esempio di questo è un sistema di inventario in cui terminali di punti vendita pubblicano i dati a una coda anziché direttamente al sistema di gestione dell'inventario. Il sistema di gestione dell'inventario utilizza quindi i dati in qualsiasi momento per gestire le scorte. Questa operazione diversi vantaggi, tra cui il sistema di gestione dell'inventario non è necessario essere online in qualsiasi momento. Per ulteriori informazioni sulle code Bus di servizio, vedere [creare applicazioni che utilizzano code Bus di servizio](service-bus-create-queues.md). 

Una coda partizionata ulteriormente aumenta la disponibilità, l'affidabilità e velocità per le applicazioni, come tali code suddivisa in più Broker messaggio e messaggistica.     

### <a name="create-partitioned-queues"></a>Creare code partizionate

È possibile creare una coda partizionata utilizzando [Azure portale classica][] e Service Bus SDK. Per creare una coda partizionata, impostare la proprietà [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) su **true** dell'istanza [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) . Il codice seguente viene illustrato come creare una coda partizionata mediante Service Bus SDK. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Inviare e ricevere messaggi che utilizzano AMQP

È possibile inviare e ricevere messaggi da una coda partizionata utilizzando AMQP come protocollo, impostando la proprietà [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) a [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) , come nell'esempio seguente.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Utilizzare AMQP con argomenti partizionati

Gli argomenti sono livello concettuale simili a quelle code, ma argomenti instradare una copia dello stesso messaggio a più *abbonamenti*. In un argomento autori inviare messaggi all'argomento e consumatori ricevano messaggi da abbonamenti. Nello scenario inventario sistema punto vendita terminali di pubblicano i dati all'argomento. Il sistema di gestione dell'inventario riceve quindi i messaggi da un abbonamento. Inoltre, un sistema di monitoraggio può ricevere lo stesso messaggio da una sottoscrizione diversa. Per ulteriori informazioni su argomenti Bus di servizio e le sottoscrizioni, vedere [creare applicazioni che utilizzano le sottoscrizioni e gli argomenti della Bus di servizio](service-bus-create-topics-subscriptions.md). 

Come con code, ulteriormente argomenti partizionati aumentano disponibilità, l'affidabilità e velocità per le applicazioni, perché questi argomenti e le sottoscrizioni suddivisa in più Broker messaggio e messaggistica. 

### <a name="create-partitioned-topics"></a>Creare argomenti partizionati

È possibile creare un argomento partizionato tramite il [portale classica Azure][] e Service Bus SDK. Per creare un argomento partizionato, impostare la proprietà [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) su **true** dell'istanza [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . Il codice seguente viene illustrato come creare un argomento partizionato utilizzando Service Bus SDK.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Inviare e ricevere messaggi che utilizzano AMQP

È possibile inviare e ricevere messaggi da una sottoscrizione argomento partizionata tramite AMQP come protocollo, impostando la proprietà [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) su [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), come nell'esempio seguente.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni seguenti per altre informazioni sull'entità messaggistica partizionata, nonché AMQP.

*    [Partizionata entità messaggistica](service-bus-partitioning.md)
*    [OASIS avanzate Message Accodamento Protocol (AMQP) versione 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Supporto di AMQP 1.0 in Bus di servizio](service-bus-amqp-overview.md)
*    [1.0 AMQP nella Guida di protocollo Bus di servizio Azure e hub di evento](service-bus-amqp-protocol-guide.md)
*    [Come usare Java Message Service (JMS) API con Bus di servizio e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Come utilizzare 1.0 AMQP con l'API di .NET Bus del servizio](service-bus-dotnet-advanced-message-queuing.md)

[Portale classica Azure]: http://manage.windowsazure.com
