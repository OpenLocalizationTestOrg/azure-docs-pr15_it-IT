<properties 
    pageTitle="Scrivere le applicazioni che utilizzano code Bus di servizio | Microsoft Azure"
    description="Come scrivere un'applicazione basata su coda semplice che utilizza Bus di servizio."
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>Creare applicazioni che utilizzano code Bus di servizio

In questo argomento descrive code Bus di servizio e viene illustrato come scrivere un'applicazione basata su coda semplice che utilizza Bus di servizio.

Valutare la possibilità di uno scenario dal mondo della retail in cui devono essere indirizzati dati sulle vendite da singoli terminali punto vendita (POS) a un sistema di gestione dell'inventario che utilizza i dati per determinare quando deve essere soddisfatta azionario. Questa soluzione utilizza Bus di servizio di messaggistica per le comunicazioni tra i terminali e il sistema di gestione dell'inventario, come illustrato nella figura seguente:

![Immagine di code Bus di servizio 1](./media/service-bus-create-queues/IC657161.gif)

Ogni terminale POS invio di messaggi da **DataCollectionQueue**segnala i dati di vendita. Questi messaggi rimangono nella coda finché vengono recuperati dal sistema di gestione dell'inventario. Questo modello è spesso definito *messaggistica asincrono*, perché terminale POS non è necessario attendere una risposta dal sistema di gestione dell'inventario per continuare l'elaborazione.

## <a name="why-queuing"></a>Perché Accodamento?

Prima di esaminare il codice necessarie per configurare l'applicazione, prendere in considerazione i vantaggi dell'utilizzo di una coda in questo scenario anziché affidare a terminali POS parlare direttamente (sincrono) al sistema di gestione dell'inventario.

### <a name="temporal-decoupling"></a>Separazione temporale

Con il criterio di messaggistica asincrono, produttori e consumatori non è necessario essere online nello stesso momento. L'infrastruttura di messaggistica archivia i messaggi in modo affidabile fino a quando non la parte che è pronta per riceverle. Questo errore indica che i componenti dell'applicazione distribuita possono essere disconnessi, uno liberamente; ad esempio, per la manutenzione o a causa di un arresto anomalo del componente, senza influire l'intero sistema. Inoltre, l'applicazione che solo devono essere in linea a determinati orari del giorno. In questo scenario di vendita al dettaglio, ad esempio, il sistema di gestione dell'inventario solo potrebbe essere in linea dopo la fine della giornata lavorativa.

### <a name="load-leveling"></a>Caricare il livellamento

In molte applicazioni carico sistema varia nel tempo, mentre il tempo necessario per ogni unità di lavoro è in genere costante. Produttori messaggio intermediating e clienti con una coda indica che l'applicazione che (lavoro) solo effettuare il provisioning in un carico medio anziché un carico massimo di servizio. Antero-posteriore della coda verrà ingrandimento e del contratto del carico in arrivo. In questo modo direttamente denaro per quanto riguarda l'importo dell'infrastruttura necessaria per soddisfare il carico dell'applicazione.

![Immagine di code Bus di servizio 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Il bilanciamento del carico

Quando il carico aumenta, è possono aggiungere ulteriori processi di lavoro da leggere dalla coda di lavoro. Ogni messaggio viene elaborato solo da uno dei processi di lavoro. Inoltre, questo il pull bilanciamento del carico consente l'uso ottimale dei computer lavoro anche se il computer di lavoro differiscono per quanto riguarda la potenza di elaborazione di estraggono messaggi sua velocità massima. Questo modello è spesso detto il modello consumer concorrenti.

![Immagine di code Bus di servizio 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Aggancio separato

Utilizzo di Accodamento per intermedio tra consumatori e dei produttori messaggio offre un intrinseco aggancio separato tra i componenti. Poiché produttori e consumatori non siano a conoscenza a altro, è possibile aggiornare un consumer senza alcun effetto sul produttore. Inoltre, la topologia di messaggistica può evolversi senza influenzare endpoint esistenti. Verrà trattato più quando si parla pubblicazione/sottoscrizione.

## <a name="show-me-the-code"></a>Mostra il codice

La sezione seguente viene illustrato come utilizzare Bus di servizio per generare l'applicazione.

### <a name="sign-up-for-an-azure-account"></a>Iscriversi a un account Azure

È necessario un account Azure per iniziare a lavorare con Bus di servizio. Se non già presente, è possibile iscriversi per ottenere un account gratuito [qui](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Creare uno spazio dei nomi

Dopo avere creato una sottoscrizione, è possibile [creare un nuovo spazio dei nomi](service-bus-create-namespace-portal.md). Ogni spazio dei nomi funziona come contenitore per un set di entità Bus di servizio. Assegnare il nuovo spazio dei nomi di un nome univoco tutti gli account di servizio Bus. 

### <a name="install-the-nuget-package"></a>Installare il pacchetto NuGet

Per utilizzare lo spazio dei nomi Bus di servizio, un'applicazione deve fare riferimento a assembly Bus di servizio, in particolare Microsoft.ServiceBus.dll. È possibile trovare questo assembly come parte di Microsoft Azure SDK e il download è disponibile nella [pagina di download di Azure SDK](https://azure.microsoft.com/downloads/). Tuttavia, il [pacchetto del servizio Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) è il modo più semplice per ottenere l'API di Bus del servizio e per configurare l'applicazione con tutte le dipendenze Bus di servizio.

### <a name="create-the-queue"></a>Creare la coda

Operazioni di gestione per Bus di servizio di messaggistica entità (argomenti code e pubblicazione/sottoscrizione) vengono eseguite mediante la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Servizio Bus utilizza un modello di sicurezza in base [Condiviso accesso firma (SA)](service-bus-sas-overview.md) . La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) rappresenta un provider di token di sicurezza con metodi factory predefiniti restituzione alcuni provider di token conosciuti. Si userà il metodo [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) per contenere le credenziali SA. L'istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene quindi creata con l'indirizzo di base dello spazio dei nomi Bus di servizio e il provider di token.

La classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) fornisce metodi per creare, enumerare ed eliminare entità messaggistica. Il codice è illustrato di seguito viene illustrato l'istanza [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) viene creato e utilizzato per creare la coda **DataCollectionQueue** .

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Si noti che sono presenti overload del metodo [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) che consentono di proprietà della coda per l'ottimizzazione. Ad esempio, è possibile impostare time to live (TTL) quello predefinito per i messaggi inviati alla coda.

### <a name="send-messages-to-the-queue"></a>Inviare messaggi a coda

Per le operazioni in fase di esecuzione su entità Bus di servizio; ad esempio, inviare e ricevere messaggi, un'applicazione prima di tutto necessario creare un oggetto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) . Analogamente alla classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) , viene creata istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) dall'indirizzo di base dello spazio dei nomi del servizio e il provider di token.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

I messaggi inviati a e ricevuto dal servizio Bus code sono istanze della classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Questa classe è costituito da un insieme di proprietà standard (ad esempio [etichette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dizionario che viene utilizzato per contenere le proprietà delle applicazioni e corpo dei dati dell'applicazione non autorizzato. Un'applicazione può impostare il corpo passando qualsiasi oggetto serializzabile (nell'esempio seguente passa in un oggetto **SalesData** che rappresenta i dati delle vendite dal terminale POS), che verrà utilizzato [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) per serializzare l'oggetto. In alternativa, è possibile fornire un oggetto di [flusso](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) .

Il modo più semplice per inviare messaggi a una determinata coda, in questo caso **DataCollectionQueue**, consiste nell'utilizzare [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) per creare un oggetto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) direttamente dall'istanza [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) .

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Ricevere i messaggi dalla coda

Per ricevere i messaggi dalla coda, è possibile utilizzare un oggetto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) che vanno creati direttamente da [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) utilizzando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Ricevitori di messaggio possono lavorare in modi diversi: **ReceiveAndDelete** e **PeekLock**. [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) viene impostata quando viene creato il destinatario del messaggio, come un parametro alla chiamata [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) .


Quando si usa la modalità di **ReceiveAndDelete** , la ricezione è un'operazione su una singola finestra di dialogo; vale a dire quando Bus di servizio riceve la richiesta, contrassegna il messaggio come utilizzata e restituisce all'applicazione. Modalità di **ReceiveAndDelete** il modello più semplice e funziona meglio per gli scenari in cui l'applicazione tollerabile non elaborazione di un messaggio se si verificasse un errore. Per comprendere questo, valutare la possibilità di uno scenario in cui l'utente invia la richiesta di ricezione e quindi si blocca prima di elaborarlo. Poiché servizio Bus contrassegnato il messaggio come consumate, quando il riavvio dell'applicazione e viene avviato il consumo messaggi nuovamente, esso verrà perso il messaggio che è stato utilizzato prima l'arresto anomalo.

Nella modalità di **PeekLock** la ricezione diventa un'operazione di due fasi, che consente alle applicazioni che non è possibile tollerare mancanti i messaggi di supporto. Quando servizio Bus riceve la richiesta, trova il messaggio successivo per l'utilizzo, per evitare che altri utenti ricevendo blocca e quindi restituisce all'applicazione. Dopo l'applicazione termina elaborazione del messaggio o memorizza in modo affidabile per l'elaborazione futuri, completa la seconda fase del processo di ricezione chiamando [completata](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) nel messaggio ricevuto. Quando servizio Bus rileva [completa](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) chiamata, contrassegna il messaggio come utilizzata.

Sono possibili due altri risultati. Prima di tutto, se l'applicazione è in grado di elaborare il messaggio per qualche motivo, è possibile chiamare [abbandonare](https://msdn.microsoft.com/library/azure/hh181837.aspx) sul messaggio ricevuto (invece di [completamento](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). In questo modo Bus di servizio sbloccare il messaggio e renderlo disponibile per la ricezione di nuovo dal consumer stesso o da un altro consumer completamento. In secondo luogo, è presente un timeout associato al blocco e se l'applicazione non è possibile elaborare il messaggio prima del blocco timeout (ad esempio, se l'applicazione determina l'arresto anomalo), quindi Bus di servizio verrà sbloccare il messaggio e renderlo disponibile per la ricezione nuovamente (essenzialmente eseguire un'operazione [abbandonare](https://msdn.microsoft.com/library/azure/hh181837.aspx) per impostazione predefinita).

Si noti che se l'applicazione determina l'arresto anomalo dopo elabora il messaggio, ma prima di [completamento](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) è stato rilasciato richiesta, il messaggio verrà recapitato nuovamente all'applicazione quando si riavvia. Questa operazione è spesso definita *Almeno una volta* elaborazione. Ciò significa che ogni messaggio verrà elaborato almeno una volta, ma in alcuni casi potrebbe recapitato lo stesso messaggio nuovamente. Se lo scenario non è possibile tollerare elaborazione duplicati, è necessario altro codice nell'applicazione di rilevare i duplicati. Questo risultato può essere ottenuto in base alla proprietà [ID messaggio](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del messaggio. Il valore di questa proprietà rimane costante tra tentativi di recapito. Questa operazione è definita *Una sola volta* elaborazione.

Il codice è illustrato di seguito riceve ed elabora un messaggio usando la modalità di **PeekLock** , ovvero l'impostazione predefinita in modo esplicito se nessun valore [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) .

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
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

### <a name="use-the-queue-client"></a>Utilizzare il client di coda

Negli esempi precedenti in questa sezione creata oggetti [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) e [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) direttamente da [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) a inviare e ricevere messaggi dalla coda, rispettivamente. In alternativa consiste nell'utilizzare la classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) , che supporta invio e ricezione operazioni oltre a funzionalità più avanzate, ad esempio sessioni.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di code, vedere [creare applicazioni che utilizzano le sottoscrizioni e gli argomenti della Bus di servizio](service-bus-create-topics-subscriptions.md) per continuare a questa discussione funzionalità di pubblicazione/sottoscrizione di argomenti Bus di servizio e le sottoscrizioni.