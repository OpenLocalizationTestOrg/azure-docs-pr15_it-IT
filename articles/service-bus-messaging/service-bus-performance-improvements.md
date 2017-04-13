<properties 
    pageTitle="Procedure consigliate per migliorare le prestazioni utilizzando Bus di servizio | Microsoft Azure"
    description="In questo articolo viene descritto come utilizzare Bus di servizio Azure per ottimizzare le prestazioni quando lo scambio di messaggi gestiti."
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
    ms.date="10/25/2016"
    ms.author="sethm" />

# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Procedure consigliate per miglioramenti delle prestazioni tramite messaggistica Bus di servizio

In questo argomento viene illustrato come utilizzare la messaggistica di Azure servizio Bus per ottimizzare le prestazioni quando lo scambio di messaggi gestiti. La prima parte di questo argomento vengono descritti i meccanismi diversi disponibili allo scopo di migliorare le prestazioni. Nella seconda parte vengono fornite indicazioni su come utilizzare Bus di servizio in modo da può offrire prestazioni ottimali in un determinato scenario.

In questo argomento, il termine "client" si riferisce a qualsiasi entità che accede a Bus di servizio. Un client può richiedere il ruolo di un mittente o un ricevitore. Il termine "mittente" viene usato per un cliente di coda o l'argomento Bus di servizio che invia messaggi a una coda Bus di servizio o l'argomento. Il termine "ricevitore" si riferisce a un client di coda o l'abbonamento Bus di servizio che riceve messaggi da una coda Bus di servizio o l'abbonamento.

Queste sezioni diversi concetti che consente di aumentare le prestazioni Bus di servizio.

## <a name="protocols"></a>Protocolli

Servizio Bus consente ai client di inviare e ricevere messaggi tramite tre protocolli

1. Avanzate Accodamento Protocol (AMQP)

2. Bus di servizio di messaggistica Protocol (SBMP)

3. HTTP

AMQP e SBMP sono più efficiente quanto mantengono la connessione al servizio Bus, purché factory messaggistica. Inoltre, implementata batch e prelettura. A meno che non sia esplicitamente indicato, tutto il contenuto di questo argomento si presuppone che l'utilizzo di AMQP o SBMP.

## <a name="reusing-factories-and-clients"></a>Riutilizzo di client e factory

Gli oggetti client Bus di servizio, ad esempio [QueueClient][] o [MessageSender][], vengono creati tramite un oggetto [MessagingFactory][] , che vengono forniti anche interni di gestione di connessioni. Non è necessario chiudere factory messaggistica o coda, l'argomento e i client di sottoscrizione dopo aver inviato un messaggio e quindi ricrearle quando si invia il messaggio successivo. Chiudere una factory messaggistica Elimina la connessione al servizio Bus di servizio e una nuova connessione quando ricreare il produttore. Stabilire una connessione è un'operazione costosa che è possibile evitare utilizzando nuovamente la stessa factory e oggetti client per più operazioni. È possibile utilizzare l'oggetto [QueueClient][] in modo sicuro per l'invio di messaggi da operazioni asincrone simultanee e più thread. 

## <a name="concurrent-operations"></a>Operazioni simultanee

Eseguire un'operazione (inviare, ricevere, eliminare, ecc) richiedono tempo. Questa volta include l'elaborazione dell'operazione dal servizio di assistenza Bus oltre la latenza della richiesta e la risposta. Per aumentare il numero di operazioni per tempo, è necessario eseguire le operazioni contemporaneamente. È possibile eseguire questa operazione in diversi modi:

-   **Operazioni asincrone**: il client Pianifica operazioni eseguendo le operazioni asincrone. La richiesta successiva viene avviata prima del completamento richiesta precedente. Di seguito è illustrato un esempio di un'operazione di invio asincrono:

    ```
    BrokeredMessage m1 = new BrokeredMessage(body);
    BrokeredMessage m2 = new BrokeredMessage(body);
    
    Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #1");
      });
    Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
      {
        Console.WriteLine("Sent message #2");
      });
    Task.WaitAll(send1, send2);
    Console.WriteLine("All messages sent");
    ```

    Questo è un esempio dell'operazione di ricezione un asincrono:
    
    ```
    Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
    
    Task.WaitAll(receive1, receive2);
    Console.WriteLine("All messages received");
    
    async void ProcessReceivedMessage(Task<BrokeredMessage> t)
    {
      BrokeredMessage m = t.Result;
      Console.WriteLine("{0} received", m.Label);
      await m.CompleteAsync();
      Console.WriteLine("{0} complete", m.Label);
    }
    ```

-   **Più factory**: tutti i client (mittenti oltre ricevitori) creato da factory stessa condividono una connessione TCP. La velocità massima dipende dal numero di operazioni che può essere eseguita per la connessione TCP. La velocità che è possibile ottenere con una singola factory notevolmente varia a seconda di andata e TCP e la dimensione dei messaggi. Per ottenere velocità maggiore, è necessario utilizzare più factory messaggistica.

## <a name="receive-mode"></a>Modalità di ricezione

Quando si crea un client coda o l'abbonamento, è possibile specificare la modalità di ricezione: *blocco di anteprima* o *ricevere ed eliminare*. Ricevere il valore predefinito è [PeekLock][]. Quando si opera in questa modalità, il client invia una richiesta per ricevere un messaggio da Bus di servizio. Dopo il client ha ricevuto il messaggio, viene inviata una richiesta per completare il messaggio.

Quando si imposta la modalità di ricezione su [ReceiveAndDelete][], entrambi i passaggi vengono combinati in un'unica richiesta. Consente di ridurre il numero complessivo di operazioni e migliorare la velocità di messaggio complessiva. L'aumento delle prestazioni include inclusi perdere i messaggi.

Servizio Bus non supporta le transazioni per le operazioni di ricezione ed eliminazione. Inoltre, semantica anteprima blocco è necessaria per qualsiasi scenari in cui il client intende rinviare o [inattiva](service-bus-dead-letter-queues.md) di un messaggio.

## <a name="client-side-batching"></a>Sul lato client in batch

Sul lato client in batch consente ai client coda o l'argomento ritardare l'invio di un messaggio per un determinato periodo di tempo. Se il client invia messaggi aggiuntivi durante questo periodo di tempo, trasmette i messaggi in un unico batch. Sul lato client batch comporta un client coda/abbonamento in batch più richieste di **completamento** in una singola richiesta. Il batch è disponibile solo per le operazioni di **INVIO** e **completamento** asincrone. Icona del operazioni vengono immediatamente inviate al servizio Bus di servizio. Il batch non verificarsi relativo all'anteprima o operazioni di ricezione né batch verifichi tra i client.

Se il batch supera la dimensione massima dei messaggi, l'ultimo messaggio viene rimosso dal lotto e il client invia immediatamente il batch. L'ultimo messaggio diventa il primo messaggio batch successivo. Per impostazione predefinita, un client utilizza un intervallo di batch di 20 ms. È possibile modificare l'intervallo di batch impostando la proprietà [BatchFlushInterval][] prima di creare factory messaggistica. Questa impostazione viene applicata a tutti i client che vengono creati da questa factory.

Per disattivare la divisione in batch, impostare la proprietà [BatchFlushInterval][] su **TimeSpan**. Per esempio:

```
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Batch non ha alcun effetto il numero di ore fatturabili operazioni di messaggistica ed è disponibile solo per il protocollo client Bus di servizio. Il protocollo HTTP non supporta il batch.

## <a name="batching-store-access"></a>Accesso a store batch

Per aumentare la velocità di una coda/argomento/sottoscrizione, Bus di servizio batch più messaggi quando scrive nell'archivio interno. Se è attivato in coda o un argomento, verrà batch scrivere messaggi nell'archivio. Se è attivato in una coda o l'abbonamento, sarà batch eliminazione dei messaggi dall'archivio. Se l'accesso a store in batch è abilitato per un'entità, Bus di servizio ritarda un'operazione di scrittura archivio relativo a tale entità da un massimo di 20 ms. Operazioni di archivio aggiuntivo che si verificano durante questo intervallo vengono aggiunte al batch. Accesso a store in batch viene applicata solo alle operazioni di **INVIO** e **completamento** ; ricevere operazioni non sono interessate. Accesso a store in batch è una proprietà per un'entità. Il batch si verifica per tutte le giuridiche che consentono l'accesso a store in batch.

Quando si crea una nuova coda, l'argomento o l'abbonamento, accesso a store in batch è attivata per impostazione predefinita. Per disattivare l'accesso a store in batch, impostare la proprietà [EnableBatchedOperations][] su **false** prima di creare l'entità. Per esempio:

```
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Accesso a store in batch non ha alcun effetto il numero di ore fatturabili operazioni di messaggistica ed è una proprietà di una coda, argomento o l'abbonamento. È indipendente dalla modalità di ricezione e il protocollo utilizzato tra un client e il servizio Bus di servizio.

## <a name="prefetching"></a>Prelettura

Prelettura consente al client coda o l'abbonamento caricare altri messaggi del servizio quando si esegue un'operazione di ricezione. Il client archivia i messaggi in una cache locale. Le dimensioni della cache dipende dalle proprietà [QueueClient.PrefetchCount][] o [SubscriptionClient.PrefetchCount][] . Ogni client che consente a prelettura mantiene la propria cache. Una cache non verrà condivise tra i client. Se il client inizia un'operazione di ricezione e la cache non contiene alcun dato, il servizio trasmette un batch di messaggi. Le dimensioni del batch uguale le dimensioni della cache o 256KB, minore. Se il client inizia un'operazione di ricezione e la cache contiene un messaggio, il messaggio viene accettato dalla cache.

Quando un messaggio è prelettura dei, il servizio blocca il messaggio prelette. In questo modo non è possibile ricevere il messaggio prelette da un ricevitore diverso. Se il destinatario non è possibile completare il messaggio prima della scadenza il blocco, il messaggio sarà disponibile per gli altri destinatari. Copia prelette del messaggio rimarrà nella cache. Il ricevitore che utilizza la copia nella cache scaduta riceverà un'eccezione quando si tenta di completare il messaggio. Per impostazione predefinita, il blocco di messaggi scade dopo 60 secondi. Questo valore può essere esteso a 5 minuti. Per evitare che il consumo di messaggi scaduti, la dimensione della cache deve essere sempre minore del numero di messaggi che possono essere utilizzati da un client all'interno dell'intervallo di timeout di blocco.

Quando si utilizza la scadenza di blocco predefinito di 60 secondi, un valore valido per [SubscriptionClient.PrefetchCount][] è 20 volte massimo di elaborazione tassi di tutti i destinatari della factory. Ad esempio una factory crea 3 ricevitori e ogni destinatario può elaborare fino a 10 messaggi al secondo. Il numero totale di prelettura non deve superare i 20\*3\*10 = 600. Per impostazione predefinita, [QueueClient.PrefetchCount][] è impostata su 0, il che significa che nessun messaggio aggiuntivo vengono recuperato dal servizio.

Prelettura messaggi aumenta la velocità complessiva per una sottoscrizione o coda perché riduce il numero complessivo di operazioni di messaggio o di round trip. Recupero del primo messaggio, tuttavia, richiederà più tempo (a causa la dimensione dei messaggi maggiore). Ricezione di messaggi prelette risultante sarà più veloce perché questi messaggi già scaricati dal client.

Proprietà time to live (TTL) di un messaggio viene controllata dal server in fase che il server invia il messaggio al client. Il client non controlla la proprietà del messaggio TTL quando si riceve il messaggio. Se, tuttavia, è possibile ricevere il messaggio anche se TTL il messaggio ha è già trascorsa mentre il messaggio è stato memorizzati nella cache dal client.

Prelettura non ha alcun effetto il numero di ore fatturabili operazioni messaggistica ed è disponibile solo per il protocollo client Bus di servizio. Il protocollo HTTP non supporta prelettura. Prelettura è disponibile per l'icona del e asincrono operazioni di ricezione.

## <a name="express-queues-and-topics"></a>Gli argomenti e code express

Entità Express abilitare alta velocità e ridotta scenari latenza. Con express entità, se un messaggio viene inviato a una coda o un argomento, il messaggio non immediatamente archiviato nell'archivio di SMS. Se, tuttavia, viene memorizzato nella cache. Se un messaggio rimarrà nella coda per più di alcuni secondi, automaticamente scrivere stabile lo spazio di archiviazione, pertanto protezione dalla perdita a causa di un'interruzione. Scrivere il messaggio in una cache di memoria aumenta la velocità e riduce la latenza, perché non è possibile accedere a archiviazione stabile al momento che dell'invio del messaggio. I messaggi che vengono utilizzati all'interno di alcuni secondi non vengono scritte nell'archivio messaggi. Nell'esempio seguente viene creato un argomento express.

```
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Se viene inviato un messaggio contenente informazioni importanti che non devono essere perse a un'entità express, il mittente possibile forzare Bus di servizio mantenere immediatamente il messaggio archiviazione stabile impostando la proprietà [ForcePersistence][] su **true**.

## <a name="use-of-partitioned-queues-or-topics"></a>Utilizzo di code partizionate o argomenti

Internamente, Bus di servizio utilizza il nodo stesso e la messaggistica Archivia per elaborare e archiviare tutti i messaggi per un'entità messaggistica (coda o argomento). Coda partizionata o un argomento, invece, è distribuito più nodi e messaggistica. Rendimento non solo una trasmissione superiore maggiore rispetto a code normali e argomenti argomenti e code partizionate, presentano anche una maggiore disponibilità. Per creare un'entità partizionata, impostare la proprietà [EnablePartitioning][] su **true**, come illustrato nell'esempio seguente. Per ulteriori informazioni sulle entità partizionata, vedere [partizioni messaggistica entità][].

```
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Utilizzo di più code

Se non è possibile utilizzare una coda partizionata o un argomento o non è possibile gestire il carico previsto per una singola coda partizionata o l'argomento, è necessario utilizzare più entità messaggistica. Quando si usano più entità, creare un client dedicato per ogni entità, invece di usare lo stesso client per tutte le entità.

## <a name="development--testing-features"></a>Sviluppo e le funzionalità di test

Servizio Bus è una funzionalità che viene utilizzato in particolare per lo sviluppo di cui **non deve essere utilizzato in configurazioni di produzione**.

[TopicDescription.EnableFilteringMessagesBeforePublishing](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing.aspx)
- Quando nuove regole o filtri vengono aggiunti all'argomento, è possibile utilizzare EnableFilteringMessagesBeforePublishing per verificare che la nuova espressione di filtro funziona come previsto.

## <a name="scenarios"></a>Scenari

Nelle sezioni seguenti descrivono tipici scenari di messaggistica e strutturare le impostazioni predefinite Bus di servizio. Velocità classificati come piccole (meno di 1 messaggio/secondo), moderato (messaggio/secondo 1 o maggiore ma minore di 100 messaggi/secondo) e alta (100 messaggi/secondo o versione successiva). Il numero di client è classificato come piccole moderato (5 o meno), (più di 5 ma minore o uguale a 20) e grandi (più di 20).

### <a name="high-throughput-queue"></a>Coda ad alta velocità

Obiettivo: Ottimizzare le prestazioni di una sola coda. Il numero di mittenti e destinatari è piccolo.

-   Utilizzare una coda partizionata per la disponibilità e migliorare le prestazioni.

-   Per aumentare la velocità di invio complessiva nella coda, utilizzare più factory messaggio per creare i mittenti. Per ogni mittente, utilizzare operazioni asincrone o più thread.

-   Per aumentare la velocità di ricezione globale dalla coda, utilizzare più factory messaggio per creare ricevitori.

-   Utilizzare operazioni asincrone per sfruttare il batch lato client.

-   Impostare l'intervallo di batch 50ms per ridurre il numero di trasmissioni del protocollo client Bus di servizio. Se si utilizzano più mittenti, aumentare l'intervallo batch a 100 ms.

-   Lasciare store in batch abilitato l'accesso. In questo modo aumenta la velocità con cui è possono scrivere i messaggi nella coda.

-   Impostare il numero di prelettura su 20 ore massimo di elaborazione tassi di tutti i ricevitori di una factory. Consente di ridurre il numero di trasmissioni del protocollo client Bus di servizio.

### <a name="multiple-high-throughput-queues"></a>Più code ad alta velocità

Obiettivo: Ottimizzare velocità effettiva del più code. La velocità di una singola coda è moderato o elevato.

Per ottenere produttività massima tra più code, usare le impostazioni indicate per ottimizzare le prestazioni di una sola coda. Inoltre, è possibile utilizzare diverse factory per creare i client di inviare o ricevano da code diverse.

### <a name="low-latency-queue"></a>Coda di bassa latenza

Obiettivo: Ridurre a icona latenza-to-end di coda o un argomento. Il numero di mittenti e destinatari è piccolo. La velocità della coda è la dimensione minima o moderato.

-   Utilizzare una coda partizionata per migliorare la disponibilità.

-   Disabilitare il batch lato client. Il client invia immediatamente un messaggio.

-   Disattivare l'accesso in batch store. Il servizio scrive immediatamente il messaggio all'archivio.

-   In un unico client, impostare il numero di prelettura su 20 volte la frequenza di elaborazione del ricevitore. Se più messaggi arrivano nella coda nello stesso momento, il protocollo client Bus di servizio trasmette tutte contemporaneamente. Quando il client riceve il messaggio successivo, il messaggio è già nella cache locale. La cache dovrebbe essere minimo.

-   Se mediante più client, impostare il numero totale di prelettura 0. In questo modo il client secondo può ricevere il secondo messaggio mentre il primo client è in corso l'elaborazione il primo messaggio.

### <a name="queue-with-a-large-number-of-senders"></a>Accodare con un numero elevato di mittenti attendibili

Obiettivo: Ottimizzare la velocità di trasmissione di una coda o un argomento con un numero elevato di mittenti attendibili. Ogni mittente invia messaggi con una frequenza moderato. Il numero di destinatari è piccolo.

Servizio Bus consente fino a 1000 connessioni simultanee a un'entità messaggistica (o 5000 utilizzando AMQP). Questo limite viene applicato a livello di spazio dei nomi e delle linee code/argomenti/abbonamenti, il limite di connessioni simultanee per spazio dei nomi. Per code, questo numero verrà condivise tra mittenti e destinatari. Se tutte le connessioni di 1000 sono necessari per i mittenti, è necessario sostituire la coda con un argomento e una singola sottoscrizione. Un argomento accetta fino a 1000 connessioni simultanee da mittenti, mentre la sottoscrizione accetta un aggiuntive 1000 connessioni contemporaneamente dai destinatari. Se sono necessari più di 1000 mittenti simultanei, i mittenti devono inviare messaggi al protocollo Bus di servizio tramite HTTP.

Per ottimizzare la velocità di trasmissione, eseguire le operazioni seguenti:

-   Utilizzare una coda partizionata per la disponibilità e migliorare le prestazioni.

-   Se ogni mittente si trova in un altro processo, utilizzare una singola factory per ogni processo.

-   Utilizzare operazioni asincrone per sfruttare il batch lato client.

-   Usare il valore predefinito batch intervallo di 20 ms per ridurre il numero di trasmissioni del protocollo client Bus di servizio.

-   Lasciare store in batch abilitato l'accesso. In questo modo aumenta la velocità con cui è possono scrivere i messaggi nella coda o argomento.

-   Impostare il numero di prelettura su 20 ore massimo di elaborazione tassi di tutti i ricevitori di una factory. Consente di ridurre il numero di trasmissioni del protocollo client Bus di servizio.

### <a name="queue-with-a-large-number-of-receivers"></a>Accodare con un numero elevato di destinatari

Obiettivo: Ottimizzare la velocità di ricezione di una coda o un abbonamento con un numero elevato di destinatari. Ogni destinatario riceve messaggi di un tasso moderato. Il numero di mittenti è piccolo.

Servizio Bus consente fino a 1000 connessioni simultanee a un'entità. Se una coda richiede più di 1000 ricevitori, è necessario sostituire la coda con un argomento e più abbonamenti. Ogni abbonamento supportino fino a 1000 connessioni contemporaneamente. In alternativa, ricevitori possono accedere alla coda mediante il protocollo HTTP.

Per ottimizzare la velocità di trasmissione, eseguire le operazioni seguenti:

-   Utilizzare una coda partizionata per la disponibilità e migliorare le prestazioni.

-   Se ogni destinatario si trova in un altro processo, utilizzare una singola factory per ogni processo.

-   Ricevitori consentono operazioni icona del o asincrone. Dato il tasso di moderato ricezione di un ricevitore singoli, sul lato client in batch di una richiesta di completamento non influisce sulla velocità ricevitore.

-   Lasciare store in batch abilitato l'accesso. Consente di ridurre il carico complessivo dell'entità. Riduce inoltre la velocità con cui è possono scrivere i messaggi nella coda o argomento.

-   Impostare il numero prefetch su un valore piccolo (ad esempio, PrefetchCount = 10). In questo modo ricevitori di un periodo di inattività mentre gli altri destinatari dispone di un numero elevato di messaggi memorizzati nella cache.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Argomento con un numero limitato di abbonamenti

Obiettivo: Ottimizzare le prestazioni di un argomento con un numero limitato di abbonamenti. Viene ricevuto un messaggio da abbonamenti per molti utenti, ovvero che la frequenza di ricezione combinato su tutte le sottoscrizioni supera la velocità di invio. Il numero di mittenti è piccolo. Il numero di destinatari per abbonamento è piccolo.

Per ottimizzare la velocità di trasmissione, eseguire le operazioni seguenti:

-   Utilizzare un argomento partizionato per la disponibilità e migliorare le prestazioni.

-   Per aumentare la velocità di invio complessiva nell'argomento, utilizzare più factory messaggio per creare i mittenti. Per ogni mittente, utilizzare operazioni asincrone o più thread.

-   Per aumentare la velocità di ricezione globale da una sottoscrizione, utilizzare più factory messaggio per creare ricevitori. Per ogni destinatario, utilizzare operazioni asincrone o più thread.

-   Utilizzare operazioni asincrone per sfruttare il batch lato client.

-   Usare il valore predefinito batch intervallo di 20 ms per ridurre il numero di trasmissioni del protocollo client Bus di servizio.

-   Lasciare store in batch abilitato l'accesso. In questo modo aumenta la velocità globale in cui è possono scrivere messaggi nell'argomento.

-   Impostare il numero di prelettura su 20 ore massimo di elaborazione tassi di tutti i ricevitori di una factory. Consente di ridurre il numero di trasmissioni del protocollo client Bus di servizio.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Argomento con un numero elevato di abbonamenti

Obiettivo: Ottimizzare le prestazioni di un argomento con un numero elevato di abbonamenti. Dagli abbonamenti molti, viene ricevuto un messaggio che indica la frequenza di ricezione combinato su tutte le sottoscrizioni maggiore la velocità di invio. Il numero di mittenti è piccolo. Il numero di destinatari per abbonamento è piccolo.

Argomenti con un numero elevato di abbonamenti in genere espongono bassa capacità produttiva complessiva se tutti i messaggi vengono inviati a tutte le sottoscrizioni. Ciò è dovuto al fatto che ogni messaggio ricevuto tutte le volte e tutti i messaggi contenuti in un argomento e tutte le sottoscrizioni vengono memorizzate nello stesso archivio. Si presuppone che il numero di mittenti e il numero di destinatari per abbonamento è piccoli. Servizio Bus supporta fino a 2.000 sottoscrizioni per argomento.

Per ottimizzare la velocità di trasmissione, eseguire le operazioni seguenti:

-   Utilizzare un argomento partizionato per la disponibilità e migliorare le prestazioni.

-   Utilizzare operazioni asincrone per sfruttare il batch lato client.

-   Usare il valore predefinito batch intervallo di 20 ms per ridurre il numero di trasmissioni del protocollo client Bus di servizio.

-   Lasciare store in batch abilitato l'accesso. In questo modo aumenta la velocità globale in cui è possono scrivere messaggi nell'argomento.

-   Impostare il numero di prelettura su 20 volte la velocità di ricezione previsto in secondi. Consente di ridurre il numero di trasmissioni del protocollo client Bus di servizio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'ottimizzazione delle prestazioni Bus di servizio, vedere [partizionati messaggistica entità][].

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [PeekLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [ReceiveAndDelete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx
  [BatchFlushInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.netmessagingtransportsettings.batchflushinterval.aspx
  [EnableBatchedOperations]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations.aspx
  [QueueClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.prefetchcount.aspx
  [SubscriptionClient.PrefetchCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.prefetchcount.aspx
  [ForcePersistence]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.forcepersistence.aspx
  [EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [Partizionata entità messaggistica]: service-bus-partitioning.md
  