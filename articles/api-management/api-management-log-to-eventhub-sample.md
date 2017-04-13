<properties
    pageTitle="Monitorare l'API con gestione API Azure, hub di eventi e Runscope"
    description="Applicazione di esempio che illustra i criteri di log a eventhub della connessione Azure API Management, Azure evento hub e Runscope per HTTP registrazione e monitoraggio"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Monitorare l'API con gestione API Azure, hub di eventi e Runscope

Il [servizio di gestione dell'API](api-management-key-concepts.md) fornisce numerose funzionalità per migliorare l'elaborazione di richieste HTTP inviate all'API HTTP. Tuttavia, l'esistenza delle richieste e le risposte sono temporanei. Viene inviata e fluisce attraverso il servizio di gestione di API per il back-end API. L'API elabora la richiesta e risposta attraversa tornare al consumer API. Il servizio di gestione dell'API mantiene alcune importanti statistiche sulle API per la visualizzazione nel dashboard del portale di Publisher, ma prima che i dettagli sono stati eliminati.

Utilizzando il [log di eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) [criteri](api-management-howto-policies.md) nel servizio di gestione delle API è possibile inviare i dettagli dalla richiesta e risposta a un [Hub evento Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Esistono diversi motivi il motivo per cui si desidera generare eventi dai messaggi HTTP inviati all'API. Alcuni esempi di audit trail delle aggiornamenti, analitica l'utilizzo, eccezione avvisi e le integrazioni parte 3 °.   

In questo articolo viene illustrato come acquisire l'intero messaggio di richiesta e risposta HTTP, inviare a un Hub di evento e quindi inoltrare i messaggi a un servizio che fornisce HTTP registrazione e monitoraggio dei servizi di terze.

## <a name="why-send-from-api-management-service"></a>Perché inviare dal servizio di gestione dell'API?
È possibile scrivere middleware HTTP che può essere inserito nel Framework API HTTP per acquisire risposte e inserimento delle loro nella registrazione e monitoraggio sistemi. Svantaggio di questo approccio è middleware HTTP deve essere integrato con l'API di back-end e deve corrispondere la piattaforma dell'API. Se sono presenti più API ciascuno di essi necessario distribuire middleware. Esistono spesso motivi perché back-end API non possono essere aggiornati.

Mediante il servizio di gestione di Azure API per l'integrazione con infrastruttura di registrazione fornisce una soluzione centralizzata e indipendente dalla piattaforma. È inoltre scalable in parte a causa delle funzionalità di [replica geografico](api-management-howto-deploy-multi-region.md) di Azure API Management.

## <a name="why-send-to-an-azure-event-hub"></a>Perché inviare a un Hub di evento Azure?
È un opportuno chiedere, informazioni sulla creazione di un criterio specifico di Azure evento hub? Sono disponibili molte origini diverse nel punto in cui si vuole accedere richieste personali. Perché non inviare solo le richieste direttamente alla destinazione finale?  Che è un'opzione. Tuttavia, quando si effettua richieste di accesso da un servizio di gestione dell'API, è necessario considerare come la registrazione messaggi impatto sulle prestazioni dell'API. Aumento graduale carico può essere gestito aumentando le istanze di componenti del sistema disponibili o sfruttando replica geografico. Tuttavia, brevi picchi di traffico possono causare richieste deve essere ritardata in modo significativo se le richieste di infrastruttura di registrazione iniziano a rallentare in condizioni di carico.

Gli hub di evento Azure è progettato per grandi volumi di ingresso di dati, con la capacità per gestire un numero superiore di eventi che il numero di richieste HTTP la maggior parte dei grafici di API. Hub evento funge da un tipo di buffer sofisticate tra il servizio di gestione dell'API e l'infrastruttura che verrà archiviate e i messaggi. In questo modo che le prestazioni di API non subiranno a causa dell'infrastruttura di registrazione.  

Una volta i dati trascorsa a un Hub di evento viene mantenuto e di attesa per gli utenti di evento Hub per la relativa elaborazione. Hub evento non è rilevante come verrà elaborata, basta cuore assicurandosi che sarà possibile recapitare il messaggio.     

Hub eventi hanno la possibilità di eventi di flusso a più gruppi di consumer. In questo modo eventi possano essere elaborati dal sistemi completamente diversi. In questo modo il supporto di molti scenari di integrazione senza immissione ritardi di addizione per l'elaborazione della richiesta API all'interno del servizio di gestione delle API di un solo evento deve essere generata.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Un criterio per inviare messaggi di applicazione/http
Un Hub evento accetta i dati dell'evento come una stringa semplice. Il contenuto della stringa è completamente dall'utente. Per poter creare un pacchetto di una richiesta HTTP e inviarlo a un evento hub è necessario formattare la stringa con informazioni sulla richiesta o una risposta. Se esiste un formato esistente in situazioni in questo modo, è possibile riutilizzare, quindi non è scrivere secondo le proprie analisi codice. Inizialmente considerate utilizzando il [Georgiano](http://www.softwareishard.com/blog/har-12-spec/) per l'invio di risposte. Tuttavia, questo formato è ottimizzato per l'archiviazione di una sequenza di richieste HTTP in formato JSON in base a. Il contenuto di un numero di elementi obbligatori aggiunti complessità non necessarie per lo scenario di passando il messaggio HTTP in rete.  

Un'opzione alternativa sono stati utilizzati i `application/http` tipo di elemento multimediale come descritto nella specifica HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Questo tipo di oggetto multimediale utilizza lo stesso formato esatto che viene utilizzato per effettivamente inviare messaggi HTTP in rete, ma l'intero messaggio può essere inserito nel corpo di un altro richiesta HTTP. In questo caso è sufficiente che verranno utilizzare il corpo come il messaggio da inviare a un hub di evento. Opportunamente, si verifica un parser che esiste nel [Client di Microsoft ASP.NET Web API 2.2](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) raccolte che possono analizzare questo formato e convertirla in nativo `HttpRequestMessage` e `HttpResponseMessage` oggetti.

Per poter creare il messaggio che è necessario sfruttare c# in base a [espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx) di gestione delle API di Azure. Ecco i criteri che invia un messaggio di richiesta HTTP a un hub evento Azure.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>Dichiarazione di criteri
Sono alcuni aspetti particolare che è opportuno menzionare su questa espressione criterio. Il criterio di log a eventhub ha un attributo denominato id logger fa riferimento al nome del logger creato all'interno del servizio di gestione delle API di. Dettagli su come installare un registratore Hub evento nel servizio di gestione delle API sono disponibili nel documento di [registrazione di eventi a un hub di evento Azure in Azure API Management](api-management-howto-log-event-hubs.md). Il secondo attributo è un parametro facoltativo che indica hub evento quale partizione per archiviare il messaggio. Hub evento utilizzare partizioni per abilitare scalabilty e richiede un minimo di due. Il recapito ordinato dei messaggi garantisce solo all'interno di una partizione. Se non si fornisce l'istruzione Hub di evento in quale partizione per inserire il messaggio, utilizza un algoritmo circolari per distribuire il carico. Tuttavia, che può causare alcune i messaggi da elaborare nell'ordine corretto.  

### <a name="partitions"></a>Partizioni
Per assicurarsi che i messaggi vengono inviati agli utenti in ordine e sfruttano le funzionalità di distribuzione carico delle partizioni, si è scelto di inviare messaggi di richiesta HTTP a una partizione e messaggi di risposta HTTP in una seconda partizione. In questo modo una distribuzione carico e possibile garantire che tutte le richieste verranno utilizzate in ordine e tutte le risposte verranno utilizzate in ordine. È possibile che una risposta che verrà utilizzata prima che la richiesta corrispondente, ma che non è un problema come abbiamo un meccanismo diverso per correlare le richieste per le risposte ed è possibile sapere che le richieste di precedono sempre le risposte.

### <a name="http-payloads"></a>Payload HTTP
Dopo la compilazione di `requestLine` è possibile controllare se il corpo della richiesta deve essere troncato. Parte decimale verrà troncato a 1024 solo nel corpo della richiesta. Questa operazione potrebbe aumentare, tuttavia singoli messaggi di evento Hub sono limitati a 256KB, in modo che è probabile che alcuni messaggi HTTP enti verranno non rientrano in un singolo messaggio. Quando si esegue la registrazione e analitica numerose informazioni può essere derivato da solo la riga richiesta HTTP e le intestazioni. Inoltre, molte richieste API solo restituiscono piccole dimensioni e pertanto la perdita di valore relativo alle informazioni da troncare grandi quantità relativamente bassi rispetto a quando la riduzione di trasferimento, elaborazione e i costi di spazio di archiviazione per mantenere tutto il contenuto del corpo. Un'ultima nota sull'elaborazione corpo è che è necessario passare `true` ad As<string>metodo () perché venga letto il contenuto del corpo, ma è stata inoltre desidera back-end API in grado di leggere il corpo. Passando true per questo metodo è causare il corpo nel buffer in modo che possa essere letto una seconda volta. Questo è importante sapere se è disponibile un'API che indica il caricamento di file grandi o utilizza polling lungo. In questi casi sarebbe migliore evitare la lettura del corpo del tutto.   

### <a name="http-headers"></a>Intestazioni HTTP
Intestazioni HTTP possono essere trasferite semplicemente su nel formato di messaggio in un formato di coppia chiave/valore semplice. Avendo scelto rimuovere un determinati campi riservati di sicurezza, per evitare perdite inutilmente informazioni sulle credenziali. Non viene in genere API chiavi e altre credenziali da essere utilizzati per scopi analitica. Se si desidera eseguire analisi l'utente e il prodotto specifico in uso, quindi è possibile ottenere dalla `context` oggetto e aggiungere queste informazioni al messaggio.     
### <a name="message-metadata"></a>Metadati di messaggio
Quando si creano il messaggio completo per inviare a hub evento, la prima riga non è in parte effettivamente la `application/http` messaggio. La prima riga è metadati aggiuntivi composta da se il messaggio è una richiesta o messaggio di risposta e un id di messaggio che viene utilizzato per creare una relazione tra le richieste di risposte. L'id del messaggio viene creato tramite un altro criterio che è simile alla seguente:

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

È possibile creato il messaggio di richiesta, che archiviata in una variabile fino a quando la risposta è stata restituita e quindi semplicemente inviata la richiesta e risposta come un singolo messaggio. Tuttavia, inviare la richiesta e risposta in modo indipendente e utilizzando un id messaggio per creare una relazione tra le due, si ottiene una maggiore flessibilità la dimensione dei messaggi, la possibilità di sfruttare i vantaggi delle partizioni più pur mantenendo messaggio ordine e la richiesta verrà visualizzato in nostro dashboard di registrazione in anticipo. È inoltre possibile che alcuni scenari in cui una risposta valida non è stata inviata all'hub di evento, probabilmente a causa di un errore di richiesta irreversibile nel servizio di gestione delle API, ma è ancora avrà un record della richiesta.

I criteri per inviare il messaggio di risposta HTTP è molto simile alla richiesta e pertanto la configurazione dei criteri completata è simile alla seguente:

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

Il `set-variable` criterio crea un valore che è possibile accedere da entrambe le `log-to-eventhub` criteri nel `<inbound>` sezione e la `<outbound>` sezione.  

## <a name="receiving-events-from-event-hubs"></a>Ricevere gli eventi da hub di evento
Gli eventi da Azure evento Hub vengono ricevuti tramite il [protocollo AMQP](http://www.amqp.org/). Il team di Microsoft servizio Bus state client raccolte disponibili per semplificare gli eventi di tempo. Esistono due diversi approcci supportati, uno è da *Consumer diretto* e l'altro usando la `EventProcessorHost` classe. Esempi di questi due approcci sono disponibili nella [Guida di programmazione di eventi hub](../event-hubs/event-hubs-programming-guide.md). È riportata una breve spiegazione delle differenze, `Direct Consumer` offre il controllo completo e la `EventProcessorHost` indica parte del lavoro impianti idraulici per ma consente di determinate ipotesi di come si elaborerà gli eventi.  

### <a name="eventprocessorhost"></a>EventProcessorHost
In questo esempio verrà utilizzato il `EventProcessorHost` per semplicità, tuttavia potrebbe non la soluzione ideale per questo scenario particolare. `EventProcessorHost`indica il lavoro per accertarsi che non è necessario preoccuparsi di thread problemi all'interno di una classe processore particolare evento. Tuttavia, in questo scenario, stiamo semplicemente la conversione del messaggio in un altro formato e passando lungo a un altro servizio di utilizzo di un metodo asincrono. Non è necessario per l'aggiornamento di stato condiviso e pertanto non rischiare di thread problemi. Per la maggior parte dei casi, `EventProcessorHost` rappresenta forse la scelta migliore e sia l'opzione più semplice.     

### <a name="ieventprocessor"></a>IEventProcessor
Il concetto centrale quando si usa `EventProcessorHost` consiste nel creare un'implementazione del `IEventProcessor` interfaccia che contiene il metodo `ProcessEventAsync`. Le caratteristiche essenziali di tale metodo sono illustrata di seguito:

  asincrono {IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) attività

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Un elenco di oggetti EventData vengono passati al metodo e si scorrere l'elenco. Byte di ogni metodo sono analizzati predefiniti in un oggetto HttpMessage e tale oggetto viene passato a un'istanza di IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
Il `HttpMessage` istanza contiene tre tipi di dati:

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

Il `HttpMessage` istanza contiene un `MessageId` GUID che consente di connettersi alla richiesta HTTP alla risposta HTTP corrispondente e un valore boolean che indica se l'oggetto contiene un'istanza di un HttpRequestMessage e HttpResponseMessage. Utilizzando incorporato in classi HTTP da `System.Net.Http`, non sarà possibile sfruttare le `application/http` analisi incluso in `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Il `HttpMessage` istanza viene inoltrato all'implementazione di `IHttpMessageProcessor` che è un'interfaccia creata per separare la ricezione e interpretazione dell'evento da Azure evento Hub effettivamente l'elaborazione di esso.


## <a name="forwarding-the-http-message"></a>Inoltrare il messaggio HTTP
Per questo esempio, ho deciso che sarebbe interessante push richiesta HTTP tramite a [Runscope](http://www.runscope.com). Runscope è un servizio basato sul cloud specializzata in HTTP debug, la registrazione e monitoraggio. Devono avere un livello gratuito, in modo facile provare e consente di visualizzare le richieste HTTP in che scorre in tempo reale tramite il servizio di gestione delle API.

Il `IHttpMessageProcessor` implementazione è simile alla seguente,

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

Non sarà possibile usufruire di una [raccolta di client esistente per Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) che semplifica la push `HttpRequestMessage` e `HttpResponseMessage` istanze nel proprio servizio. Per accedere all'API Runscope è necessario un account e un tasto di API. Istruzioni per ottenere una chiave dell'API sono disponibili in questo screencast di [Creazione di applicazioni all'API Runscope di accesso](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) .

## <a name="complete-sample"></a>Esempio completo
Il [codice sorgente](https://github.com/darrelmiller/ApimEventProcessor) e il test per l'esempio siano presenti Github. È necessario un [Servizio di gestione dell'API](api-management-get-started.md), [Un Hub evento connesso](api-management-howto-log-event-hubs.md)e un [Account di archiviazione](../storage/storage-create-storage-account.md) per eseguire l'esempio per se stessi.   

Il campione è solo una semplice applicazione Console è in attesa per gli eventi provenienti da Hub di evento, convertirli in una `HttpRequestMessage` e `HttpResponseMessage` oggetti e quindi inoltra all'API Runscope.

Nell'immagine animata seguente, è possibile visualizzare una richiesta a un'API nel portale per sviluppatori, applicazione Console che mostra il messaggio ricevuto, elaborati e inoltro delle chiamate e quindi la richiesta e risposta viene visualizzato nel controllo il traffico Runscope.

![Dimostrazione della richiesta inoltrato a Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Riepilogo
Servizio di gestione di API Azure fornisce un posto ideale per acquisire il traffico HTTP in viaggio da e verso l'API. Azure hub di evento è una soluzione scalabilità, costo per il traffico di acquisizione e reinserirla nella sistemi di elaborazione secondaria per l'accesso, monitoraggio e altri sofisticate analitica. Connessione a sistemi come Runscope un semplice come alcune decine righe di codice di monitoraggio del traffico 3 ° di terze parti.

## <a name="next-steps"></a>Passaggi successivi
-   Ulteriori informazioni su Azure evento hub
    -   [Guida introduttiva di Azure evento hub](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Ricevere messaggi con EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Hub evento programmazione manuale](../event-hubs/event-hubs-programming-guide.md)
-   Altre informazioni sull'integrazione di gestione delle API e hub di evento
    -   [Registrazione di eventi a un hub di evento Azure in Azure API Management](api-management-howto-log-event-hubs.md)
    -   [Riferimento a un'entità logger](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [riferimento log a eventhub criteri](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    