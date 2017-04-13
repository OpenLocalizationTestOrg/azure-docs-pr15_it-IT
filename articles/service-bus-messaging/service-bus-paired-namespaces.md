<properties 
    pageTitle="Servizio Bus accoppiati spazi dei nomi | Microsoft Azure"
    description="Dettagli sull'implementazione accoppiate dello spazio dei nomi e costo"
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

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Accoppiati dettagli sull'implementazione di spazio dei nomi e le implicazioni dei costi

Il metodo [PairNamespaceAsync][] , mediante un'istanza [SendAvailabilityPairedNamespaceOptions][] esegue attività visibile per proprio conto. Poiché costi considerazioni quando si utilizza la caratteristica, è utile comprendere le attività in modo che si prevede che il comportamento quando si verifica. L'API svolge il seguente comportamento automatico per conto dell'utente:

-   Creazione di code backlog.
-   Creazione di un oggetto [MessageSender][] che parla a code o argomenti.
-   Quando un'entità messaggistica non è disponibile, invia messaggi all'entità effettuare il ping durante un tentativo di rilevare quando tale entità nuovamente disponibile.
-   Se lo si desidera crea di un insieme di "message pump" che spostare i messaggi provenienti da code backlog in code principale.
-   Coordinate chiusura/che le istanze di [MessagingFactory][] primario e secondario.

Un alto livello, la caratteristica funziona nel modo seguente: quando l'entità principale è integro, si verifica alcuna modifica comportamento. Quando la durata [FailoverInterval][] trascorre ed entità primaria vede non riuscita Invia dopo un non temporanei [MessagingException][] o un [TimeoutException][]verifica quanto segue:

1.  Inviare le operazioni di entità primaria sono disabilitate e il sistema esegue il ping dell'entità primaria fino a quando non possono essere recapitati ping.

2.  Una coda di backlog casuale sia selezionata.

3.  Oggetti [BrokeredMessage][] vengono inviati a coda di backlog scelto.

1.  Se non riesce a coda di backlog scelta di un'operazione di invio, tale coda verrà spostato dalla rotazione e una nuova coda sia selezionata. Informazioni su tutti i mittenti l'istanza di [MessagingFactory][] dell'errore.

Figura riportata di seguito illustrano la sequenza. Prima di tutto il mittente invia messaggi.

![Spazi dei nomi accoppiate][0]

In caso di errore per inviare a coda primaria, il mittente inizia a inviare messaggi a una coda di backlog scelto in modo casuale. Contemporaneamente, inizia un'attività ping.

![Spazi dei nomi accoppiate][1]

A questo punto i messaggi sono ancora nella coda secondaria e non sono stati recapitati a coda principale. Una volta coda primaria integro nuovamente, almeno un processo deve essere in esecuzione la syphon. Il syphon offre i messaggi da tutte le code backlog diversi per le entità di destinazione corretta (code e argomenti).

![Spazi dei nomi accoppiate][2]

Il resto di questo argomento illustra i dettagli specifici del funzionano di queste parti.

## <a name="creation-of-backlog-queues"></a>Creazione di code backlog

L'oggetto [SendAvailabilityPairedNamespaceOptions][] passato al metodo [PairNamespaceAsync][] indica il numero di code backlog che si desidera utilizzare. Ogni coda backlog viene quindi creata con le proprietà seguenti in modo esplicito set (tutti gli altri valori sono impostate su quelle predefinite di [QueueDescription][] ):

| Percorso                                   | [spazio dei nomi primario] / x servicebus trasferimento / [indice] dove [index] è un valore in [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | int. MaxValue                                                                                         |
| DefaultMessageTimeToLive               | MaxValue                                                                                    |
| AutoDeleteOnIdle                       | MaxValue                                                                                    |
| LockDuration                           | minuto                                                                                             |
| EnableDeadLetteringOnMessageExpiration | vero                                                                                                 |
| EnableBatchedOperations                | vero                                                                                                 |

Ad esempio, coda di backlog prima creati per spazio dei nomi **contoso** viene denominato `contoso/x-servicebus-transfer/0`.

Quando si creano le code, il codice prima verifica l'esistenza di tale coda. Se coda non esiste, viene creata nella coda. Il codice non pulizia code backlog "aggiuntive". In particolare, se l'applicazione con spazio dei nomi primario **contoso** richiede cinque backlog code ma una coda di backlog con il percorso `contoso/x-servicebus-transfer/7` è presente, tale coda backlog aggiuntive è ancora presenta ma non viene utilizzata. Il sistema consente in modo esplicito code backlog aggiuntive esiste non utilizzati. Come proprietario dello spazio dei nomi è responsabile per la pulizia qualsiasi code backlog inutilizzato/indesiderato. Il motivo per cui questa decisione è che Bus di servizio non è possibile sapere quali scopi esistono per tutte le code nello spazio dei nomi. Inoltre, se una coda con il nome specificato è presente ma non soddisfa presunto [QueueDescription][], quindi i motivi sono personalizzati per modificare il comportamento predefinito. Nessun garanzie sono stata progettate per apportare modifiche a code backlog dal codice. Assicurarsi di verificare accuratamente le modifiche.

## <a name="custom-messagesender"></a>MessageSender personalizzato

Quando si invia, tutti i messaggi attraversano un oggetto [MessageSender][] interno che a funzionare normalmente quando tutto funziona e reindirizza a code backlog quando cose "interruzione". Dopo aver ricevuto un errore non temporanei, viene avviato un timer. Dopo un periodo di [TimeSpan][] costituito il valore della proprietà [FailoverInterval][] durante il quale non riesce verranno inviati messaggi, si esegue il failover. A questo punto, per ogni entità si verificano le seguenti operazioni:

- Un'attività ping viene eseguita ogni [PingPrimaryInterval][] per verificare se l'entità è disponibile. Dopo questa operazione ha avuto esito positivo, tutto il codice client che utilizza l'entità immediatamente inizia a inviare nuovi messaggi per lo spazio dei nomi principale.

- Successive richieste per inviare la stessa entità da altri mittenti determinerà l' [BrokeredMessage][] inviato da modificare occupare in coda di backlog. La modifica rimuove alcune proprietà dall'oggetto [BrokeredMessage][] e archiviati in un' posizione. Le proprietà seguenti vengono deselezionate e aggiunti in un nuovo alias, consentendo Bus di servizio e SDK elaborare i messaggi in modo uniforme:

| Nome proprietà precedente       | Nuovo nome della proprietà |
|-------------------------|-------------------|
| ID sessione               | x-ms-ID sessione    |
| TimeToLive              | x-ms-timetolive   |
| ScheduledEnqueueTimeUtc | percorso di ms x         |

Il percorso di destinazione originale viene archiviato anche all'interno del messaggio come una proprietà denominata x-ms-percorso. Questo modello consente ai messaggi per molte entità di coesistere in una coda di backlog singola. Le proprietà vengono convertite nuovamente dalla syphon.

L'oggetto [MessageSender][] personalizzato può verificarsi problemi quando i messaggi si avvicina al limite di 256 KB e failover è impegnato. L'oggetto [MessageSender][] personalizzato archivia i messaggi per tutti gli argomenti e le code insieme nelle code backlog. Questo oggetto viene utilizzata una combinazione di messaggi provenienti da molti colori all'interno di code backlog. Per gestire il bilanciamento del carico tra molti client che non conoscono reciprocamente, SDK sceglie in modo casuale una coda di backlog per ogni [QueueClient][] o [TopicClient][] creare nel codice.

## <a name="pings"></a>Ping

Un messaggio ping è un [BrokeredMessage][] vuoto con la relativa proprietà [ContentType][] impostata su un valore [TimeToLive][] di 1 secondo e l'applicazione/vnd.ms-servicebus-ping. Questo ping è una caratteristica speciale in Bus di servizio: il server non è mai recapita un ping quando un chiamante richiede un [BrokeredMessage][]. In questo modo, è mai necessario salvare informazioni su come ricevere e ignorare questi messaggi. Quando sono considerate non è disponibile, verrà eseguito il ping ogni entità (coda univoca o argomento) per ogni istanza [MessagingFactory][] al client. Per impostazione predefinita, si verifica questo evento una volta al minuto. Messaggi ping sono considerati normali messaggi Bus di servizio e causano in base alle tariffe per i messaggi e della larghezza di banda. Come i client rilevano che il sistema è disponibile, interrompere i messaggi.

## <a name="the-syphon"></a>Il syphon

Almeno un programma eseguibile nell'applicazione deve essere attivamente in esecuzione la syphon. il syphon esegue un valore long sondaggio ricevere che dura 15 minuti. Quando tutte le entità sono disponibili e si dispone di 10 code backlog, l'applicazione che ospita la syphon chiama l'operazione di ricezione 40 ore orarie, 960 ore al giorno in ore 28800 30 giorni. Quando il syphon si sposta messaggi attivamente dal backlog in coda principale, ogni messaggio si verifica in base alle tariffe seguenti (in base alle tariffe standard per la dimensione dei messaggi e della larghezza di banda si applicano tutte le fasi):

1.  Inviare al backlog.

2.  Ricevere dal backlog.

3.  Inviare a primario.

4.  Ricevere da primario.

## <a name="closefault-behavior"></a>Chiudi/guasto comportamento

All'interno di un'applicazione che ospita syphon, una volta gli errori di [MessagingFactory][] primari o secondari o chiusura senza partner anche da errori/chiusa e il syphon rileva questo stato, atti syphon. Se altri [MessagingFactory][] non viene chiusa entro 5 secondi, il syphon verrà fault ancora aperta [MessagingFactory][].

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione dettagliata della messaggistica asincrono Bus di servizio, vedere [modelli di messaggistica asincroni e disponibilità][] . 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Modelli di messaggistica asincroni e disponibilità]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
