<properties
    pageTitle="Azure trigger funzioni Bus di servizio e le associazioni | Microsoft Azure"
    description="Informazioni su come utilizzare trigger Bus di servizio Azure e associazioni nelle funzioni di Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="funzioni di Azure, funzioni, elaborazione di eventi, calcolo dinamico, architettura senza server"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure trigger funzioni Bus di servizio e le associazioni per code e argomenti

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene illustrato come configurare e codice Bus di servizio Azure trigger e associazioni di funzioni di Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>Trigger coda o l'argomento Bus di servizio

#### <a name="functionjson"></a>Function.JSON

Il file *function.json* specifica le proprietà seguenti.

- `name`: Il nome della variabile utilizzato nel codice di funzione coda o argomento o nel messaggio coda o l'argomento. 
- `queueName`: Per coda attivare solo il nome della coda al sondaggio.
- `topicName`: Per l'argomento di attivare solo il nome dell'argomento al sondaggio.
- `subscriptionName`: Per l'argomento di attivare solo il nome dell'abbonamento.
- `connection`: Il nome di un'impostazione di app che contiene una stringa di connessione Bus di servizio. La stringa di connessione deve essere per uno spazio dei nomi, Bus di servizio non è limitato a una coda specifica o un argomento. Se la stringa di connessione non dispone di gestione di diritti, impostare il `accessRights` proprietà. Se si omette `connection` vuoto, trigger o associazione sarà compatibili con la stringa di connessione predefinita Bus di servizio per app funzione, che corrisponde all'impostazione app AzureWebJobsServiceBus.
- `accessRights`: Specifica i diritti di accesso disponibili per la stringa di connessione. Valore predefinito è `manage`. Impostare su `listen` se si sta utilizzando una stringa di connessione che non consente di gestire le autorizzazioni. In caso contrario funzioni runtime può provare e non riesce a eseguire le operazioni che richiedono la gestione di diritti.
- `type`: Deve essere impostata su *serviceBusTrigger*.
- `direction`: Deve essere impostata su *in*. 

Esempio *function.json* per un trigger di coda Bus di servizio:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>Esempio di codice c# che consente di elaborare un messaggio di coda Bus di servizio

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>Esempio di codice F # che consente di elaborare un messaggio di coda Bus di servizio

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Esempio di codice Node che consente di elaborare un messaggio di coda Bus di servizio

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>Tipi di supportati

Il messaggio di coda Bus di servizio può essere deserializzato a uno dei seguenti tipi:

* Oggetto (da JSON)
* stringa
* Matrice di byte 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>Comportamento PeekLock

Il runtime funzioni riceve un messaggio in `PeekLock` modalità e le chiamate perse `Complete` messaggio se la funzione viene completata correttamente o chiamate `Abandon` in caso contrario. Se la funzione viene eseguita per più di `PeekLock` timeout, il blocco verrà automaticamente rinnovato.

#### <a id="sbpoison"></a>Gestione dei messaggi

Servizio Bus indica il proprio messaggio danneggiato gestione che non può essere controllato o configurato in configurazione funzioni Azure o codice. 

#### <a id="sbsinglethread"></a>Thread singolo

Per impostazione predefinita le funzioni runtime elabora contemporaneamente più messaggi coda. Per indirizzare il runtime per elaborare solo un argomento messaggio o un singola coda alla volta, impostare `serviceBus.maxConcurrrentCalls` su 1 nel file *host.json* . Per informazioni sul file *host.json* , vedere [Struttura delle cartelle](functions-reference.md#folder-structure) in articolo di riferimento per sviluppatori e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) nella pagina wiki di archivio WebJobs.Script.

## <a id="sboutput"></a>Associazione di output coda Bus di servizio o l'argomento

#### <a name="functionjson"></a>Function.JSON

Il file *function.json* specifica le proprietà seguenti.

- `name`: Il nome della variabile utilizzato nel codice di funzione coda o messaggio della coda. 
- `queueName`: Per coda attivare solo il nome della coda al sondaggio.
- `topicName`: Per l'argomento di attivare solo il nome dell'argomento al sondaggio.
- `subscriptionName`: Per l'argomento di attivare solo il nome dell'abbonamento.
- `connection`: Impostare un trigger identica a quella di Bus di servizio.
- `accessRights`: Specifica i diritti di accesso disponibili per la stringa di connessione. Valore predefinito è `manage`. Impostare su `send` se si sta utilizzando una stringa di connessione che non consente di gestire le autorizzazioni. In caso contrario funzioni runtime può provare e non riesce a eseguire le operazioni che richiedono gestione di diritti, ad esempio la creazione di code.
- `type`: Deve essere impostata su *serviceBus*.
- `direction`: Deve essere impostata su *Indietro*. 

Esempio *function.json* per l'uso di un trigger timer per scrivere messaggi di coda Bus di servizio:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>Tipi di supportati

Funzioni di Azure è possono creare un messaggio di coda Bus di servizio da uno qualsiasi dei tipi seguenti.

* Oggetto (sempre creato un messaggio JSON, il messaggio con un oggetto null se il valore è null quando la funzione termina)
* stringa (Crea un messaggio se il valore è non null quando la funzione termina)
* Matrice di byte (funziona come stringa) 
* `BrokeredMessage`(C#, funzionamento stringa)

Per la creazione di più messaggi in una funzione c#, è possibile utilizzare `ICollector<T>` o `IAsyncCollector<T>`. Viene creato un messaggio quando si chiama la `Add` metodo.

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>Esempi di codice c# che creano messaggi coda Bus di servizio

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>Esempio di codice F # che crea un messaggio di coda Bus di servizio

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>Esempio di codice Node che crea un messaggio di coda Bus di servizio

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
