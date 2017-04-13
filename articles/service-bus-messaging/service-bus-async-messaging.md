<properties 
    pageTitle="Messaggistica asincrono Bus di servizio | Microsoft Azure"
    description="Descrizione del servizio Bus asincrono messaggistica."
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

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Modelli di messaggistica asincroni e disponibilità

Messaggistica asincrono può essere implementata in vari modi diversi. Con code, argomenti e le sottoscrizioni, Bus di servizio Azure supporta asincronia tramite un archivio e meccanismo di inoltro. Normale funzionamento (icona del), inviare messaggi a code e gli argomenti e ricevere messaggi da code e abbonamenti. Scrivere le applicazioni dipendono da queste entità sempre la disponibilità. Quando lo stato di entità assume l'aspetto, a causa di una varietà di circostanze, è necessario un modo per fornire un'entità funzionalità ridotte in grado di soddisfare la maggior parte delle esigenze.

Modelli di messaggistica asincroni in genere utilizzato per consentire un numero di scenari di comunicazione. È possibile creare applicazioni in cui client possono inviare i messaggi ai servizi, anche se il servizio non è in esecuzione. Per le applicazioni di tale esperienza consente di burst delle comunicazioni, una coda livellare il carico fornendo una posizione per le comunicazioni di buffer. Infine, è possibile ottenere un bilanciamento del carico semplice ma efficace per distribuire i messaggi in più computer.

Per mantenere la disponibilità di una di queste entità, valutare la possibilità di un numero di diversi modi in cui queste entità possono essere visualizzati non è disponibile per un sistema di messaggistica permanente. In generale, è possibile vedere l'entità non sono più disponibili per le applicazioni che creiamo in diversi modi seguenti:

- Impossibile inviare messaggi.

- Impossibile ricevere messaggi.

- Non è possibile gestire le entità (creare, recuperare, aggiornare o eliminare entità).

- Impossibile contattare il servizio.

Per ciascuno di questi errori, guasti diversi esistono che consentono a un'applicazione di continuare a svolgere un'attività livello alcune funzionalità ridotte. Ad esempio, un sistema che è possibile inviare messaggi ma non ricevere continueranno a ricevere gli ordini dei clienti, ma non è possibile elaborare gli ordini. In questo argomento vengono illustrati i potenziali problemi che possono verificarsi e come possono essere attenuati questi problemi. Servizio Bus ha introdotto un numero di riduzioni disponibili che è necessario acconsentire esplicitamente e in questo argomento viene anche le regole che regolano l'utilizzo di tali riduzioni consenso esplicito.

## <a name="reliability-in-service-bus"></a>Affidabilità di Bus di servizio

Esistono diversi modi per gestire i problemi di messaggio ed entità e sono disponibili linee guida all'uso appropriato del tali riduzioni. Per comprendere le linee guida, è necessario conoscere cosa può avere esito negativo in Bus di servizio. A causa della progettazione di sistemi di Azure, tutti questi problemi cambiano di frequente. Un alto livello, le diverse cause indisponibilità appaiono come segue:

-   Limitazione da un sistema esterno da cui dipende Bus di servizio. La limitazione si verifica da interazioni con le risorse di archiviazione e di calcolo.

-   Problema per un sistema da cui dipende Bus di servizio. Ad esempio una parte specificata di spazio di archiviazione può verificarsi problemi.

-   Errore del servizio Bus sul singolo sottosistema. In questo caso, un nodo di calcolo accessibile in uno stato incoerente e riavvia, causando tutte le entità che serve bilanciare gli altri nodi. Ciò può causare un breve periodo di elaborazione dei messaggi lenta.

-   Errore di servizio Bus all'interno di un Data Center di Azure. Verrà visualizzata una "irreversibile" durante il quale il sistema non è raggiungibile per numero di minuti o alcune ore.

> [AZURE.NOTE] L' termine **dello spazio di archiviazione** può indicare lo spazio di archiviazione di Azure e SQL Azure.

Servizio Bus contiene un numero di riduzioni disponibili per questi problemi. Nelle sezioni seguenti ogni problema e le relative soluzioni di attenuazione.

### <a name="throttling"></a>Limitazione

Con Bus di servizio, limitazione consente la gestione di frequenza messaggio collaborazione. Ogni singolo nodo Bus di servizio include molte entità. Ognuna di queste entità effettua richieste sul sistema in termini di CPU, memoria, lo spazio di archiviazione e altri aspetti. Quando uno di questi aspetti rileva l'uso che supera le soglie definite, Bus di servizio possibile rifiutare una richiesta specificata. Il chiamante riceve un [ServerBusyException][] e tentativi dopo 10 secondi.

Come attenuazione, il codice di leggere l'errore e interrompere qualsiasi tentativi del messaggio per almeno 10 secondi. Poiché l'errore può verificarsi in parti dell'applicazione cliente, è probabile che ogni parte in modo indipendente consente di eseguire la logica di ripetizione. Il codice può ridurre la probabilità che viene limitata abilitando partizione nella coda o argomento.

### <a name="issue-for-an-azure-dependency"></a>Problema per una relazione Azure

Altri componenti all'interno di Azure in alcuni casi possono essere problemi del servizio. Ad esempio, quando viene aggiornato un sistema che utilizza Bus di servizio, tale sistema temporaneamente può verificarsi funzionalità ridotte. Per risolvere questi tipi di problemi, Bus di servizio regolarmente esamina e viene riduzioni disponibili. Ripercussioni di queste soluzioni di attenuazione vengono visualizzate. Ad esempio, per gestire problemi temporanei con lo spazio di archiviazione, Bus di servizio implementato un sistema che consente di eseguire operazioni Invia messaggio lavorare in modo coerente. A causa della natura di attenuazione, un messaggio inviato può richiedere fino a 15 minuti vengano visualizzate nella coda interessata o sottoscrizione e pronto per un'operazione di ricezione. In generale, maggior parte delle entità non verificherà il problema. Tuttavia, dato il numero di entità nel servizio Bus all'interno di Azure, questa attenuazione è utile per un sottoinsieme dei clienti Bus di servizio.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Errore di servizio Bus su un singolo sottosistema

Per tutte le applicazioni circostanze possono causare un componente interno del servizio Bus non sarà più coerente. Quando servizio Bus rileva questo, raccoglie dati dall'applicazione per facilitare la diagnosi Dov'è. Una volta raccolti i dati, il riavvio dell'applicazione durante un tentativo di fare in modo coerente. Questo processo si verifica abbastanza rapidamente e i risultati in un'entità non siano visibili agli non essere disponibile per alcuni minuti, anche se tipico verso il basso volte sono molto più brevi.

In questi casi, l'applicazione client genera un'eccezione [TimeoutException][] o [MessagingException][] . Servizio Bus contiene attenuazione per risolvere il problema in forma di riconnessione automatica. Quando il messaggio non è stato recapitato il periodo di tentativi è esaurito, è possibile esplorare con altre caratteristiche, ad esempio [accoppiate gli spazi dei nomi][]. Accoppiate presentano altre avvertenze descritti in questo articolo.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Errore del servizio Bus all'interno di un Data Center di Azure

Probabile causa di un errore in un Data Center Azure è una distribuzione di aggiornamento non riuscita di Bus di servizio o un sistema di dipendente. Come è maturato la piattaforma, è ridotta la probabilità di questo tipo di errore. Un Data Center errore può verificarsi anche per motivi che includono le seguenti:

-   Impianti elettrici interruzione (alimentatore e generare power scomparsa).
-   Connettività (interruzione internet tra il client e Azure).

In entrambi i casi, emergenza naturale o altri eventi generano il problema. Per risolvere il problema e assicurarsi che sia ancora possibile inviare messaggi, è possibile utilizzare [gli spazi dei nomi accoppiate][] per attivare i messaggi da inviare in un'altra posizione, mentre la posizione principale viene integro nuovamente. Per ulteriori informazioni, vedere [procedure consigliate per le applicazioni isolanti contro interruzioni Bus di servizio e guasti][].

## <a name="paired-namespaces"></a>Spazi dei nomi accoppiate

La caratteristica di [spazi dei nomi accoppiate][] supporta scenari in cui un'entità Bus di servizio o distribuzione all'interno di un data center non è più disponibile. Mentre si verifica questo evento raramente, sistemi distribuiti devono ancora essere pronti a gestire peggiore dei casi. Perché alcuni elementi da cui dipende Bus di servizio si verificano un problema a breve termine in genere, si verifica questo evento. Per mantenere la disponibilità delle applicazioni durante un periodo di inattività, gli utenti Bus di servizio possono usare due spazi dei nomi separati preferenza in centri dati separato, pubblicare le relative entità messaggistica. Il resto di questa sezione viene utilizzata la terminologia seguente:

-   Spazio dei nomi primario: spazio dei nomi a cui l'applicazione interagisce, per inviare e ricevere operazioni.

-   Spazio dei nomi secondario: spazio dei nomi che funge da una copia di backup per lo spazio dei nomi principale. La logica dell'applicazione non interagisce con questo spazio dei nomi.

-   Intervallo failover: la quantità di tempo per accettare normale errori prima che l'applicazione passa dello spazio dei nomi principali per lo spazio dei nomi secondario.

Accoppiati spazi dei nomi supporto *invio della disponibilità*. Inviare la disponibilità mantenendo la possibilità di inviare messaggi. Per utilizzare invia disponibilità, l'applicazione deve soddisfare i requisiti seguenti:

1.  I messaggi vengono ricevuti solo dello spazio dei nomi principali.

2.  I messaggi inviati a una determinata coda o argomento potrebbe arrivare nell'ordine corretto.

3.  Se l'applicazione utilizza sessioni, i messaggi all'interno di una sessione di essere recapitati nell'ordine corretto. Si tratta di un'interruzione di normale funzionamento delle sessioni. Questo significa che l'applicazione utilizzi sessioni ai messaggi del gruppo in modo logico. Lo stato della sessione viene gestito solo sullo spazio dei nomi di principale.

4.  I messaggi all'interno di una sessione di essere recapitati nell'ordine corretto. Si tratta di un'interruzione di normale funzionamento delle sessioni. Questo significa che l'applicazione utilizzi sessioni ai messaggi del gruppo in modo logico.

5.  Lo stato della sessione viene gestito solo sullo spazio dei nomi di principale.

6.  Coda primaria può in linea e iniziare ad accettare messaggi prima coda secondaria recapita tutti i messaggi nella coda principale.

Nelle sezioni che seguono l'API, come l'API sono implementate e viene visualizzato il codice di esempio che utilizza la caratteristica. Notare che ci sono implicazioni fatturazione associate a questa caratteristica.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync API

La caratteristica di spazi dei nomi accoppiate include il metodo [PairNamespaceAsync][] sulla classe [Microsoft.ServiceBus.Messaging.MessagingFactory][] :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Dopo avere completato l'attività, la combinazione di spazio dei nomi inoltre completo e pronto per agire per qualsiasi [MessageReceiver][], [QueueClient][]o [TopicClient][] creati con l'istanza [MessagingFactory][] . [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] è la classe base per i diversi tipi di associazione disponibili con un oggetto [MessagingFactory][] . L'unica classe derivata è al momento, uno denominato [SendAvailabilityPairedNamespaceOptions][], che implementa i requisiti di disponibilità Invia. [SendAvailabilityPairedNamespaceOptions][] dispone di un set di costruttori che sono tra loro. Esaminando il costruttore con la maggior parte dei parametri, è possibile comprendere il comportamento di altri costruttori.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Questi parametri significato sono il seguente:

-   *secondaryNamespaceManager*: un'istanza di [NamespaceManager][] inizializzata per lo spazio dei nomi secondario che il metodo [PairNamespaceAsync][] consente di impostare lo spazio dei nomi secondario. Il responsabile dello spazio dei nomi viene utilizzato per ottenere l'elenco di code dello spazio dei nomi e per accertarsi che esistano code backlog necessari. Se non esistono tali code, vengono create. [NamespaceManager][] richiede la possibilità di creare un token con la richiesta di **Gestione** .

-   *messagingFactory*: l'istanza di [MessagingFactory][] per lo spazio dei nomi secondario. L'oggetto [MessagingFactory][] viene utilizzato per scambiare, se la proprietà [EnableSyphon][] è impostata su **true**, i messaggi provenienti da code backlog.

-   *backlogQueueCount*: il numero di code backlog per creare. Questo valore deve essere inferiore a 1. Quando si inviano messaggi al backlog, viene effettuata una di queste code in modo casuale scelta. Se si imposta il valore su 1, sola coda possa mai essere utilizzata. Quando si verifica questo evento e coda di un backlog genera errori, il client non sarà possibile provare a una coda di backlog diversi e potrebbe non riuscire a inviare il messaggio. È consigliabile impostare questo valore su alcune più grande valore e predefinito il valore 10. È possibile modificare a un valore superiore o inferiore a seconda di come quantità di dati che l'applicazione invia al giorno. Ogni coda backlog può contenere fino a 5 GB di messaggi.

-   *failoverInterval*: la quantità di tempo durante il quale si desidera accettare gli errori di spazio dei nomi primario prima di passare qualsiasi singola entità per lo spazio dei nomi secondario. Failover venga eseguito su base entità da entità. Entità in un unico spazio dei nomi attivi spesso in diversi nodi contenuti in Bus di servizio. Un errore in un'entità non implica un errore in un altro. È possibile impostare questo valore per [System.TimeSpan.Zero][] per il controllo a secondario immediatamente dopo l'errore prima e non temporanei. Gli errori che attivano il timer failover sono qualsiasi [MessagingException][] in cui la proprietà [IsTransient][] è FALSO o un [TimeoutException][]. Altre eccezioni, ad esempio [UnauthorizedAccessException][] non provocano failover, in quanto indicano che il client non è configurato correttamente. Un [ServerBusyException][] non causano failover perché è attendere 10 secondi, quindi inviare nuovamente il messaggio.

-   *enableSyphon*: indica che questa particolare associazione deve anche syphon i messaggi provenienti dallo spazio dei nomi secondario al spazio dei nomi primario. In generale, applicazioni che inviano messaggi devono impostare questo valore su **false**; le applicazioni di ricevano messaggi necessario impostare questo valore su **true**. Il motivo è che spesso, ci sono meno ricevitori messaggio più i mittenti dei messaggi. A seconda del numero di destinatari, è possibile scegliere di gestire i compiti syphon un'istanza singola applicazione. Con molti destinatari implicazioni fatturazione per ogni coda backlog.

Per utilizzare il codice, creare un'istanza di [MessagingFactory][] principale, secondario di un'istanza di [MessagingFactory][] , un'istanza di [NamespaceManager][] secondaria e un'istanza di [SendAvailabilityPairedNamespaceOptions][] . La chiamata possa essere costituiti semplicemente le operazioni seguenti:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Dopo avere completato l'attività restituita dal metodo [PairNamespaceAsync][] , tutte le impostazioni di configurazione e pronto per l'utilizzo. Prima che venga restituito l'attività, si potrebbe non ha completato tutte le operazioni di sfondo necessarie per l'associazione per funzionano in modo corretto. Di conseguenza, non deve iniziare l'invio di messaggi fino a quando non viene restituito. Se si sono verificati eventuali errori, ad esempio le credenziali errate o errore durante la creazione di code backlog queste eccezioni vengono generate dopo il completamento dell'attività. Restituisce l'attività, verificare che code sono state trovate o create esaminare la proprietà [BacklogQueueCount][] sull'istanza [SendAvailabilityPairedNamespaceOptions][] . Per il codice precedente, l'operazione viene visualizzata come segue:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di messaggistica asincrono nel servizio Bus, è possibile leggere ulteriori dettagli su [accoppiate gli spazi dei nomi][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Procedure consigliate per isolanti applicazioni rispetto a interruzioni Bus di servizio e di emergenza]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [spazi dei nomi accoppiate]: service-bus-paired-namespaces.md