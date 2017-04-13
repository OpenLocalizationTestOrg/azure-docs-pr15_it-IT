<properties 
    pageTitle="Suddiviso code e argomenti | Microsoft Azure"
    description="In questo articolo viene descritto come suddividere code Bus di servizio e argomenti usando più Broker messaggio."
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
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>Gli argomenti e code partizionate

Azure Bus di servizio impiega più Broker messaggio per elaborare i messaggi e più archivi di messaggistica per archiviare i messaggi. Coda convenzionale o un argomento è gestita dal gestore singolo messaggio e archiviato in un archivio di SMS. Servizio Bus consente inoltre code o argomenti per essere suddiviso in più Broker messaggio e archivi messaggistica. Questo errore indica che la velocità effettiva del coda partizionata o argomento non è limitata dalle prestazioni di un gestore singolo messaggio o di messaggistica. Inoltre, temporaneamente fuori di un archivio di messaggistica non esegue il rendering coda partizionata o un argomento non è disponibile. Gli argomenti e code partizionate possono contenere tutte Bus di servizio le funzioni avanzate, ad esempio il supporto per le transazioni e sessioni.

Per ulteriori informazioni sul servizio Bus interni, vedere l'argomento [architettura Bus di servizio][] .

## <a name="how-it-works"></a>Come funziona

Ogni coda partizionata o l'argomento è costituito da più frammenti. Ogni frammento viene archiviato in un archivio di messaggistica diverso e gestita da un gestore di messaggio diverso. Quando un messaggio viene inviato a una coda partizionata o argomento, Bus di servizio assegna il messaggio a uno dei frammenti. La selezione viene eseguita in modo casuale mediante Bus di servizio o una chiave di partizione che è possibile specificare il mittente.

Quando si desidera ricevere un messaggio da una coda partizionata o da una sottoscrizione di un argomento partizionata query servizio Bus tutti i frammenti per i messaggi, quindi restituisce il primo messaggio ottenuto da uno degli archivi SMS al destinatario. Cache servizio Bus gli altri messaggi e li restituisce quando riceve aggiuntive riceveranno le richieste. Il client di ricezione non è presente la suddivisione; il comportamento con i clienti di una coda partizionata o l'argomento (ad esempio, leggere, completare, rinviare la gestione, messaggi non recapitabili, prelettura) è uguale a quello di un'entità normale.

Non esiste costi aggiuntivi quando inviare un messaggio o la ricezione di un messaggio da una coda partizionata o un argomento.

## <a name="enable-partitioning"></a>Abilitare partizione

Per utilizzare code partizionate e argomenti con Bus di servizio Azure, utilizzare Azure SDK 2.2 o versione successiva oppure specificare `api-version=2013-10` l'HTTP richiede.

È possibile creare code Bus di servizio e gli argomenti della dimensioni 1, 2, 3, 4 o 5 GB (il valore predefinito è 1 GB). Con partizioni abilitato, Bus di servizio crea 16 partizioni per ogni GB specificato. Pertanto, se si crea una coda a 5 GB dimensioni, con 16 partizioni le dimensioni massime coda diventano (5 \* 16) = 80 GB. È possibile visualizzare la dimensione massima del partizionata coda o argomento controllando in entrata nel [portale di Azure][].

Esistono diversi modi per creare una coda partizionata o un argomento. Quando si crea la coda o l'argomento dall'applicazione in uso, è possibile abilitare partizione per la coda o l'argomento impostando la proprietà [QueueDescription.EnablePartitioning][] o [TopicDescription.EnablePartitioning][] rispettivamente su **true**. Queste proprietà devono essere impostate al momento della coda o argomento viene creato. Non è possibile modificare queste proprietà in un coda esistente o un argomento. Per esempio:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

In alternativa, è possibile creare una coda partizionata o un argomento in Visual Studio o nel [portale di Azure][]. Quando si crea una nuova coda o l'argomento nel portale, impostare l'opzione **Abilita partizioni** in e **l'Impostazioni generali** della coda o argomento nella finestra **Impostazioni** su **true**. In Visual Studio, fare clic sulla casella di controllo **Abilita partizione** nella finestra di dialogo **Nuova coda** o **Nuovo argomento** .

## <a name="use-of-partition-keys"></a>Utilizzo di chiavi partizione

Quando un messaggio in coda in una coda partizionata o un argomento, Bus di servizio verifica la presenza di una chiave di partizione. Se viene trovato, seleziona il frammento in base a tale tasto. Se non viene trovato una chiave di partizione, seleziona il frammento in base a un algoritmo interno.

### <a name="using-a-partition-key"></a>Utilizzo di una chiave di partizione

Alcuni scenari, ad esempio sessioni o le transazioni, richiedono i messaggi archiviati in un frammento specifico. Tutti questi scenari richiedono l'utilizzo di una chiave di partizione. Tutti i messaggi che utilizzano la stessa chiave partizione vengono assegnati a stesso frammento. Se il frammento è temporaneamente non disponibile, Bus di servizio viene restituito un errore.

A seconda lo scenario messaggio diverso vengono utilizzate come chiave di partizione:

**ID sessione**: se un messaggio è impostata la proprietà [BrokeredMessage.SessionId][] , quindi Bus di servizio utilizza questa proprietà come chiave di partizione. In questo modo, tutti i messaggi che appartiene alla stessa sessione vengono gestiti dalla stessa gestore di messaggio. In questo modo Bus di servizio garantire messaggio ordinamento e la coerenza degli stati sessione.

**PartitionKey**: se un messaggio con la proprietà [BrokeredMessage.PartitionKey][] ma non la proprietà [BrokeredMessage.SessionId][] impostare quindi Bus di servizio utilizza la proprietà [PartitionKey][] come chiave di partizione. Se il messaggio ha [ID sessione][] e l'insieme di proprietà [PartitionKey][] , entrambe le proprietà devono essere identiche. Se la proprietà [PartitionKey][] è impostata su un valore diverso rispetto alla proprietà [ID sessione][] , Bus di servizio restituisce un'eccezione **InvalidOperationException** . La proprietà [PartitionKey][] deve essere utilizzata se un mittente invia messaggi di transazione presente non sessione. Chiave di partizione assicura che tutti i messaggi inviati all'interno di una transazione vengono gestiti dal gestore di messaggistica stesso.

**ID messaggio**: se la coda o l'argomento [QueueDescription.RequiresDuplicateDetection][] proprietà è impostata su **true** e non sono impostate proprietà [BrokeredMessage.SessionId][] o [BrokeredMessage.PartitionKey][] , quindi la proprietà [BrokeredMessage.MessageId][] funge da chiave partizione. Si noti che le raccolte di Microsoft .NET e AMQP assegna automaticamente un ID messaggio se l'applicazione di invio dei messaggi non. In questo caso, tutte le copie dello stesso messaggio vengono gestite dal stesso gestore di messaggio. In questo modo Bus di servizio rilevare ed eliminare messaggi duplicati. Se la proprietà [QueueDescription.RequiresDuplicateDetection][] non è impostata su **true**, Bus di servizio non prendere in considerazione la proprietà [ID messaggio][] come chiave di partizione.

### <a name="not-using-a-partition-key"></a>Non si usa una chiave di partizione

In assenza di una chiave di partizione, Bus di servizio distribuisce i messaggi in circolare a tutti i frammenti di coda partizionata o argomento. Se il frammento scelto non è disponibile, Bus di servizio assegna il messaggio a un altro frammento. In questo modo, l'operazione di invio ha avuto esito positivo nonostante l'indisponibilità temporanea di un archivio di SMS.

Per assegnare Bus di servizio abbastanza tempo per accodare il messaggio in un frammento diversi, il valore di [MessagingFactorySettings.OperationTimeout][] specificato dal client che invia il messaggio deve essere maggiore dopo 15 secondi. È consigliabile impostare la proprietà [OperationTimeout][] il valore predefinito di 60 secondi.

Si noti che una chiave di partizione "Blocca" un messaggio da un frammento specifico. Se l'archivio messaggistica che contiene il frammento è disponibile, Bus di servizio viene restituito un errore. In assenza di una chiave di partizione, Bus di servizio possibile scegliere un frammento diversi e l'operazione ha avuto esito positivo. Si consiglia di conseguenza, non fornisce una chiave di partizione a meno che non è necessario.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Argomenti avanzati: utilizzare le transazioni con entità partizionata

I messaggi che vengono inviati come parte di una transazione è necessario specificare una chiave di partizione. Questa operazione può essere una delle seguenti proprietà: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][]o [BrokeredMessage.MessageId][]. Tutti i messaggi inviati come parte della stessa transazione specificare la stessa chiave di partizione. Se si tenta di inviare un messaggio senza una chiave di partizione all'interno di una transazione, Bus di servizio restituisce un'eccezione **InvalidOperationException** . Se si tenta di inviare messaggi più all'interno della stessa transazione che dispongono di chiavi partizione diversi, servizio Bus restituisce un'eccezione **InvalidOperationException** . Per esempio:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Se le proprietà come una chiave di partizione sono impostate, Bus di servizio blocca il messaggio a un frammento specifico. Questo problema si verifica se non viene utilizzata una transazione. È consigliabile non specificare una chiave di partizione se non è necessario.

## <a name="using-sessions-with-partitioned-entities"></a>Utilizzo di sessioni con entità partizionata

Per inviare un messaggio di transazione a una coda o sessione presente argomento, il messaggio è necessario impostare la proprietà [BrokeredMessage.SessionId][] . Se la proprietà [BrokeredMessage.PartitionKey][] inoltre specificata, deve essere identica alla proprietà [ID sessione][] . Se sono diversi, servizio Bus restituisce un'eccezione **InvalidOperationException** .

Diversamente da quanto succede regolare code (non suddivisi) o argomenti, non è possibile utilizzare una sola transazione per inviare messaggi più sessioni diverse. Se si è tentato, Bus di servizio restituisce un'eccezione **InvalidOperationException **. Per esempio:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Inoltro automatico dei messaggi con entità partizionata

Azure Bus di servizio supporta l'inoltro automatico dei messaggi da, a o tra entità partizionata. Per abilitare l'inoltro automatico dei messaggi, impostare la proprietà [QueueDescription.ForwardTo][] nella coda di origine o l'abbonamento. Se il messaggio specifica una chiave di partizione ([ID sessione][], [PartitionKey][]o [ID messaggio][]), la chiave di partizione viene usata per l'entità di destinazione.

## <a name="considerations-and-guidelines"></a>Considerazioni e indicazioni

- **Caratteristiche di coerenza alta**: se l'entità utilizza caratteristiche quali sessioni, duplicati o il controllo esplicito delle partizioni chiave, quindi le operazioni di messaggistica vengono sempre inviate frammenti. Se uno qualsiasi dei frammenti esperienza traffico elevato o archivio sottostante è danneggiato, queste operazioni esito negativo e disponibilità verrà ridotte. In generale, la coerenza è molto superiore a entità non suddiviso; solo un sottoinsieme del traffico si sta verificando problemi, anziché tutto il traffico.
- **Gestione**: operazioni, ad esempio creazione, aggiornamento ed eliminazione devono essere eseguite in tutti i frammenti dell'entità. Se i frammenti sono danneggiato potrebbe causare errori per queste operazioni. Per l'operazione di ottenere informazioni come messaggio conta devono essere aggregate da tutti i frammenti. Se i frammenti sono danneggiato, lo stato di disponibilità entità viene indicato come limitata.
- **Scenari di messaggio volume ridotto**: per tali scenari, soprattutto quando si usa il protocollo HTTP, potrebbe essere necessario eseguire più operazioni di ricezione per ottenere tutti i messaggi. Per le richieste di ricezione, front-end esegue una ricezione su tutti i frammenti e le cache tutte le risposte ricevute. Sarebbe vantaggi offerti dalla memorizzazione nella cache e ricevere una richiesta di ricezione successiva nella stessa connessione latenza sarà inferiore. Tuttavia, se si dispongono di più connessioni o utilizzare il protocollo HTTP, che stabilisce una nuova connessione per ogni richiesta. Di conseguenza, non ci sono garanzie che da terra sullo stesso nodo. Se tutti i messaggi esistenti sono bloccati e memorizzati in un altro front-end, l'operazione di ricezione restituisce **null**. Scadono alla fine dei messaggi e si possono ricevere nuovamente. È consigliabile mantenere attivi HTTP.
- **Sfoglia/leggere i messaggi**: PeekBatch non restituisce il numero di messaggi specificato nella [proprietà MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Esistono due motivi comuni per l'oggetto. Un motivo è che le dimensioni dell'insieme di messaggi aggregata superano la dimensione massima di 256KB. Un altro motivo è che se la coda o l'argomento include la [proprietà EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) impostato su **true**, una partizione che non sia sufficiente messaggi per completare il numero richiesto di messaggi. In generale, se un'applicazione desidera ricevere un numero specifico di messaggi, è necessario chiamare [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) più volte fino a quando non ottiene il numero di messaggi o non sono presenti più messaggi da leggere. Per ulteriori informazioni, inclusi esempi di codice, vedere [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) o [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Ultima aggiunta di caratteristiche

- Aggiungere o rimuovere regola è ora supportata con entità partizionata. Diverso da entità non suddiviso, queste operazioni non sono supportate in transazioni. 
- AMQP è ora supportato per inviare e ricevere messaggi e da un'entità partizionata.
- AMQP è ora supportato per le operazioni seguenti: [Batch inviare](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Batch ricevere](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [ricezione dal numero di sequenza](https://msdn.microsoft.com/library/azure/hh330765.aspx), [leggere](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Rinnovare blocco](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Messaggio programmazione](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Annullare messaggio pianificato](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Aggiungi regola](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Rimuovere regola](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Blocco rinnovo della sessione](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Lo stato della sessione di Set di](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Sessione Get](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Leggere i messaggi di sessione](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)e [Consente di enumerare le sessioni](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitazioni relative all'entità partizionata

Attualmente Bus di servizio sono presenti le seguenti limitazioni relative code partizionate e argomenti:

-   Gli argomenti e code partizionate non supporta invio di messaggi che appartengono a diverse sessioni in una sola transazione.
-   Servizio Bus consente attualmente fino a 100 code partizionate o argomenti per ogni spazio dei nomi. Ogni coda partizionata o l'argomento viene contato verso la quota di 10.000 entità per spazio dei nomi (si applica al livello Premium).

## <a name="next-steps"></a>Passaggi successivi

Vedere la sezione del [supporto AMQP 1.0 per servizio Bus suddiviso code e argomenti][] per ulteriori informazioni su come partizione entità messaggistica. 

  [Architettura di Bus di servizio]: service-bus-architecture.md
  [Portale di Azure]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [ID sessione]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [ID messaggio]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [Supporto di AMQP 1.0 per servizio Bus suddiviso code e argomenti]: service-bus-partitioned-queues-and-topics-amqp-overview.md
