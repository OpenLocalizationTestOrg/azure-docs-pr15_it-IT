<properties 
    pageTitle="Creare applicazioni che utilizzano argomenti Bus di servizio e abbonamenti | Microsoft Azure"
    description="Introduzione alla pubblicazione-sottoscrizione funzionalità offerte da argomenti Bus di servizio e le sottoscrizioni."
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Creare applicazioni che utilizzano le sottoscrizioni e gli argomenti della Bus di servizio

Azure Bus di servizio supporta un insieme di tecnologie middleware orientati ai messaggi, basata su cloud inclusi Accodamento affidabile e permanente pubblicazione/sottoscrizione messaggistica. In questo articolo si basa sui fornite in [creare applicazioni che utilizzano code Bus di servizio](service-bus-create-queues.md) e offre un'introduzione alle funzionalità di pubblicazione/sottoscrizione offerti dagli argomenti Bus di servizio.

## <a name="evolving-retail-scenario"></a>Scenario di vendita in evoluzione

In questo articolo continua lo scenario di vendita al dettaglio utilizzato in [Crea applicazioni che utilizzano code Bus di servizio](service-bus-create-queues.md). Richiamare i dati delle vendite da singolo punto di vendita (POS) terminali devono essere indirizzati a un sistema di gestione dell'inventario che utilizza i dati per determinare quando deve essere soddisfatta azionario. Ogni terminale POS vengono visualizzati i dati di vendita tramite l'invio di messaggi alla coda **DataCollectionQueue** dove rimangono fino a quando non vengono ricevuti dal sistema di gestione dell'inventario, come illustrato di seguito:

![Bus di servizio 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Per evolversi questo scenario, è stato aggiunto un nuovo requisito al sistema: il proprietario dell'archivio desidera essere in grado di monitorare le prestazioni dell'archivio in tempo reale.

Per risolvere questo requisito, il sistema deve "toccare" disattivare il flusso di dati di vendita. Vogliamo ancora ogni messaggio inviato da terminali POS devono essere inviati al sistema di gestione dell'inventario come prima, ma ci teniamo un'altra copia di ogni messaggio che è possibile utilizzare per presentare la visualizzazione di dashboard al proprietario store.

In qualsiasi situazione, ad esempio la verifica, in cui si richiede la ogni messaggio che verrà utilizzata da più parti, è possibile utilizzare servizio Bus *argomenti*. Negli argomenti sono un modello di pubblicazione/sottoscrizione in cui è disponibile uno o più abbonamenti registrati con l'argomento relativo a ogni messaggio pubblicato. Invece di code ogni messaggio viene visualizzato da un singolo consumer.

I messaggi vengono inviati a un argomento nello stesso modo come vengono inviate a una coda. Tuttavia, i messaggi non vengono ricevuti da un argomento direttamente. vengono ricevuti da abbonamenti. È possibile pensare di una sottoscrizione a un argomento come coda virtuale che riceve le copie dei messaggi inviati a questo argomento. Messaggi ricevuti da una sottoscrizione allo stesso modo quando vengono ricevuti da una coda.

Tornando allo scenario di vendita al dettaglio, coda viene sostituita da un argomento e aggiunta di una sottoscrizione, utilizza il componente del sistema di gestione dell'inventario. Il sistema è ora presente nel modo seguente:

![Servizio Bus 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

La configurazione qui avrà lo stesso comportamento per la progettazione di base coda precedente. I messaggi inviati all'argomento, ovvero vengono indirizzati all'abbonamento **inventario** da cui li utilizza il **Sistema di gestione dell'inventario** .

Per supportare il dashboard di gestione, viene creata una seconda sottoscrizione sull'argomento, come illustrato di seguito:

![Servizio Bus 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Con questa configurazione, ogni messaggio dai terminali POS viene resa disponibile per gli abbonamenti **Dashboard** e **inventario** .

## <a name="show-me-the-code"></a>Mostra il codice

L'articolo [creare applicazioni che utilizzano Bus di servizio code](service-bus-create-queues.md) viene descritto come iscriversi a un account Azure e creare uno spazio dei nomi del servizio. Per usare uno spazio dei nomi Bus di servizio, un'applicazione deve fare riferimento a assembly Bus di servizio, in particolare Microsoft.ServiceBus.dll. Il modo più semplice per fare riferimento a dipendenze Bus di servizio è per installare il Service Bus [pacchetto Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). È inoltre possibile trovare assembly come parte di Azure SDK. Il download è disponibile nella [pagina di download di Azure SDK](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Creare l'argomento e sottoscrizioni

Operazioni di gestione per Bus di servizio di messaggistica entità (argomenti code e pubblicazione/sottoscrizione) vengono eseguite mediante la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Credenziali appropriate sono necessarie per creare un'istanza di [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) per un determinato spazio dei nomi. Servizio Bus utilizza un modello di sicurezza in base [Condiviso accesso firma (SA)](service-bus-sas-overview.md) . La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) rappresenta un provider di token di sicurezza con metodi factory predefiniti restituzione alcuni provider di token conosciuti. Si userà il metodo [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) per contenere le credenziali SA. L'istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene quindi creata con l'indirizzo di base dello spazio dei nomi Bus di servizio e il provider di token.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornisce metodi per creare, enumerare ed eliminare entità messaggistica. Il codice è illustrato di seguito viene illustrato l'istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene creato e utilizzato per creare l'argomento **DataCollectionTopic** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Si noti che sono presenti overload del metodo [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) che consentono di impostare le proprietà dell'argomento. Ad esempio, è possibile impostare time to live (TTL) quello predefinito per i messaggi inviati all'argomento. Aggiungere le sottoscrizioni di **magazzino** e **Dashboard** .

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Inviare messaggi all'argomento

Per le operazioni in fase di esecuzione su entità Bus di servizio; ad esempio, inviare e ricevere messaggi, un'applicazione prima di tutto necessario creare un oggetto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Analogamente alla classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , viene creata istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) dall'indirizzo di base dello spazio dei nomi del servizio e il provider di token.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

I messaggi inviati a e ricevuto dagli argomenti Bus di servizio, sono istanze della classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Questa classe è costituito da un insieme di proprietà standard (ad esempio [etichette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dizionario che viene utilizzato per contenere le proprietà delle applicazioni e corpo dei dati dell'applicazione non autorizzato. Un'applicazione può impostare il corpo passando qualsiasi oggetto serializzabile (nell'esempio seguente passa in un oggetto **SalesData** che rappresenta i dati delle vendite dal terminale POS), che verrà utilizzato [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) per serializzare l'oggetto. In alternativa, è possibile fornire un oggetto di [flusso](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) .

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Il modo più semplice per inviare messaggi all'argomento consiste nell'utilizzare [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) per creare un oggetto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) direttamente dall'istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Ricezione di messaggi da una sottoscrizione

Simile all'utilizzo di code, per ricevere i messaggi da una sottoscrizione che è possibile utilizzare un oggetto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) che vanno creati direttamente da [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) utilizzando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). È possibile usare uno dei due diversi ricevere modalità (**ReceiveAndDelete** e **PeekLock**), come descritto in [creare applicazioni che utilizzano code Bus di servizio](service-bus-create-queues.md).

Si noti che quando si crea un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) per gli abbonamenti, il parametro *entityPath* del modulo `topicPath/subscriptions/subscriptionName`. Pertanto, per creare un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) per la sottoscrizione di **inventario** dell'argomento **DataCollectionTopic** , *entityPath* deve essere impostata su `DataCollectionTopic/subscriptions/Inventory`. Il codice viene visualizzata come segue:

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## <a name="subscription-filters"></a>Filtri di abbonamento

Finora, in questo scenario tutti i messaggi inviati all'argomento sono disponibili per tutte le sottoscrizioni registrate. La frase di chiave "è disponibile." Sebbene abbonamenti Bus di servizio visualizzare tutti i messaggi inviati all'argomento, è possibile copiare solo un sottoinsieme dei messaggi in coda virtuale abbonamento. Questa operazione viene eseguita mediante sottoscrizione *filtri*. Quando si crea una sottoscrizione, è possibile fornire un'espressione di filtro sotto forma di un predicato stile SQL92 che funziona tramite le proprietà del messaggio, le proprietà di sistema (ad esempio l' [etichetta](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) e le proprietà dell'applicazione, ad esempio **StoreName** nell'esempio precedente.

In evoluzione lo scenario a questo scopo, un secondo archivio è venga aggiunta a questo scenario di vendita al dettaglio. Dati sulle vendite da tutti i terminali POS da entrambe archivi ancora devono essere instradati al sistema di gestione dell'inventario centralizzato, ma un responsabile punto vendita tramite lo strumento dashboard è solo interessato le prestazioni dell'archivio. È possibile utilizzare la sottoscrizione filtro a tale scopo. Si noti che quando terminali POS i messaggi di pubblicazione, l'impostazione della **StoreName** applicazione proprietà nel messaggio. Dato due archivi, ad esempio **Redmond** e **Seattle**, terminali POS il Redmond memorizzano timbro i messaggi di dati delle vendite con un **StoreName** uguale a **Redmond**, mentre i terminali POS archivio Seattle utilizzano un **StoreName** uguale a **Seattle**. Gestione di archiviazione dell'archivio Redmond desidera solo visualizzare dati da suoi terminali POS. Il sistema viene visualizzata come segue:

![Servizio Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Per configurare il routing, è stato creato il **Dashboard** abbonamento come indicato di seguito:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Con questo filtro della sottoscrizione, verranno copiati solo i messaggi che hanno **StoreName** impostata a **Redmond** a coda virtuale per la sottoscrizione di **Dashboard** . Non esiste più al filtro sottoscrizione, tuttavia. Le applicazioni possono avere più regole di filtro per abbonamento oltre la possibilità di modificare le proprietà di un messaggio come passa a coda virtuale dell'abbonamento.

## <a name="summary"></a>Riepilogo

Tutti i motivi per utilizzare Accodamento descritto nella [creazione di applicazioni che utilizzano Bus di servizio code](service-bus-create-queues.md) applicate anche a argomenti, in particolare:

- Separazione temporale – messaggio produttori e consumatori non è necessario essere online nello stesso momento.

- Caricare il livellamento: picchi carico vengono smussate dall'argomento abilitare l'utilizzo di applicazioni per eseguire il provisioning per carico medio anziché punta.

- Il bilanciamento del carico, simile a una coda, è possibile creare più consumer concorrenti in ascolto su una singola sottoscrizione con ogni messaggio trasferita a una sola dei consumatori, in modo da bilanciamento del carico.

- Separati aggancio: possono evolversi rete messaggistica senza influenzare endpoint esistenti; ad esempio aggiungendo abbonamenti o modificando i filtri a un argomento per consentire di nuovi clienti.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come utilizzare code nello scenario retail POS, vedere [creazione di applicazioni che utilizzano code Bus di servizio](service-bus-create-queues.md) .