<properties
    pageTitle="Azure trigger di funzioni e le associazioni | Microsoft Azure"
    description="Informazioni su come utilizzare trigger e le associazioni nelle funzioni di Azure."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funzioni, funzioni, elaborazione dell'evento, webhooks, calcolo dinamico, architettura senza server"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-developer-reference"></a>Azure funzioni trigger e le associazioni di riferimento per sviluppatori


In questo argomento vengono fornite informazioni generali trigger e associazioni. Include alcune funzionalità avanzate di associazione e sintassi supportata da tutti i tipi di associazione.  

Se si sta cercando informazioni dettagliate sul configurazione e codifica un tipo specifico di trigger o associazione, può essere necessario fare clic su uno dei trigger o associazioni elencate invece di seguito:

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)] 

Gli articoli seguenti presuppongono che aver letto il [riferimento per sviluppatori di Azure funzioni](functions-reference.md)e gli articoli di riferimento per sviluppatori [c#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md)o [Node](functions-reference-node.md) .



## <a name="overview"></a>Panoramica

Trigger sono le risposte evento utilizzate per attivare il codice personalizzato. Consentono di rispondere agli eventi in tutta la piattaforma Azure o in locale. Le associazioni rappresentano i metadati necessari per connettere il codice trigger desiderato o input associato o dati di output.

Per farsi un'idea delle associazioni diversi che è possibile integrare con l'app di Azure funzione, fare riferimento alla tabella seguente.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]  

Per comprendere meglio attiva e le associazioni in generale, si supponga di esecuzione del codice a processo rilasciato un nuovo elemento in una coda di archiviazione Azure. Funzioni di Azure fornisce un trigger di Azure coda per il supporto. Sarà necessario, le informazioni seguenti per controllare la coda:
 
- Account di archiviazione in cui è presente nella coda.
- Il nome della coda.
- Un nome di variabile utilizzerà il codice per fare riferimento al nuovo elemento è stato eliminato nella coda.  
 
Un trigger di coda associazione contiene queste informazioni per una funzione di Azure. Il file *function.json* per ogni funzione contiene tutte le associazioni correlate.  Ecco un esempio *function.json* contenente una coda di attivare l'associazione. 

    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        }
      ],
      "disabled": false
    }

Il codice può inviare diversi tipi di output a seconda di come viene elaborato il nuovo elemento coda. È consigliabile, ad esempio, scrivere un nuovo record a una tabella di archiviazione Azure.  A tale scopo, è possibile impostare un'associazione di output in una tabella di archiviazione Azure. Ecco un esempio *function.json* che include lo spazio di archiviazione di associazione di output della tabella che potrebbe essere utilizzata con un trigger di coda. 


    {
      "bindings": [
        {
          "name": "myNewUserQueueItem",
          "type": "queueTrigger",
          "direction": "in",
          "queueName": "queue-newusers",
          "connection": "MY_STORAGE_ACCT_APP_SETTING"
        },
        {
          "type": "table",
          "name": "myNewUserTableBinding",
          "tableName": "newUserTable",
          "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
          "direction": "out"
        }
      ],
      "disabled": false
    }


La funzione c# seguente risponde a un nuovo elemento da eliminare nella coda e scrive una nuova voce utente in una tabella di archiviazione Azure.

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;

    public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                    TraceWriter log)
    {
        // In this example the queue item is a JSON string representing an order that contains the name, 
        // address and mobile number of the new customer.
        dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);
    
        await myNewUserTableBinding.AddAsync(
            new Person() { 
                PartitionKey = "Test", 
                RowKey = Guid.NewGuid().ToString(), 
                Name = order.name,
                Address = order.address,
                MobileNumber = order.mobileNumber }
            );
    }
    
    public class Person
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Name { get; set; }
        public string Address { get; set; }
        public string MobileNumber { get; set; }
    }

Per ulteriori esempi di codice e informazioni più specifiche relative ai tipi di archiviazione Azure supportate, vedere [funzioni di Azure trigger e le associazioni per lo spazio di archiviazione di Azure](functions-bindings-storage.md).


Per usare le funzionalità di associazione più avanzate nel portale di Azure, fare clic sull'opzione di **editor avanzato** nella scheda **integrazione** della funzione. Editor avanzato consente di modificare *function.json* direttamente nel portale.

## <a name="dynamic-parameter-binding"></a>Associazione di parametro dinamico 

Invece di una configurazione statica impostazione per le proprietà di associazione di output, è possibile configurare le impostazioni per l'associazione in modo dinamico ai dati che fanno parte di associazione di input del trigger. Valutare la possibilità di uno scenario in cui nuovi ordini vengono elaborati utilizzando una coda di archiviazione Azure. Ogni nuovo elemento coda è una stringa JSON contenente almeno le proprietà seguenti:

    {
      name : "Customer Name",
      address : "Customer's Address".
      mobileNumber : "Customer's mobile number."
    }

È consigliabile inviare un messaggio SMS con il proprio account Twilio come un aggiornamento che è stato ricevuto l'ordine al cliente.  È possibile configurare il `body` e `to` campo del Twilio output associazione in modo dinamico da associare il `name` e `mobileNumber` che erano parte dell'input come indicato di seguito.

    {
      "name": "myNewOrderItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newOrders",
      "connection": "orders_STORAGE"
    },
    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "{mobileNumber}",
      "from": "+XXXYYYZZZZ",
      "body": "Thank you {name}, your order was received",
      "direction": "out"
    },
 
A questo punto è solo il codice della funzione deve inizializzare il parametro di output come indicato di seguito. Durante l'esecuzione le proprietà di output saranno associate ai dati di input desiderati.

C#

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message variable.
    message = new SMSMessage();

    // When using dynamic parameter binding no need to set this in code.
    // message.body = msg;
    // message.to = myNewOrderItem.mobileNumber

Node

    context.bindings.message = {
        // When using dynamic parameter binding no need to set this in code.
        //body : msg,
        //to : myNewOrderItem.mobileNumber
    };




## <a name="random-guids"></a>GUID casuale

Funzioni di Azure fornisce una sintassi per generare GUID casuali con le associazioni. La sintassi di associazione seguente verrà scritto output in un nuovo BLOB con un nome univoco in un contenitore di archiviazione Azure: 

    {
      "type": "blob",
      "name": "blobOutput",
      "direction": "out",
      "path": "my-output-container/{rand-guid}"
    }



## <a name="returning-a-single-output"></a>Restituisce un unico output

Nei casi in cui il codice di funzione restituisce un unico output, è possibile utilizzare un'associazione di output denominata `$return` per mantenere la firma di una funzione più naturale nel codice. Può essere utilizzato solo con le lingue che supportano un valore restituito (c#, Node, F #). L'associazione sarà simile alla seguente associazione di output blob che viene utilizzata con un trigger di coda.

    {
      "bindings": [
        {
          "type": "queueTrigger",
          "name": "input",
          "direction": "in",
          "queueName": "test-input-node"
        },
        {
          "type": "blob",
          "name": "$return",
          "direction": "out",
          "path": "test-output-node/{id}"
        }
      ]
    }


Il codice c# seguente viene restituito l'output più naturale senza utilizzare un `out` parametro nella firma della funzione.


    public static string Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }

    // Async example
    public static Task<string> Run(WorkItem input, TraceWriter log)
    {
        string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
        log.Info($"C# script processed queue message. Item={json}");
        return json;
    }


Questo approccio stesso è illustrato di seguito con Node.

    module.exports = function (context, input) {
        var json = JSON.stringify(input);
        context.log('Node.js script processed queue message', json);
        context.done(null, json);
    }

F # esempio fornita di seguito.

    let Run(input: WorkItem, log: TraceWriter) =
        let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
        log.Info(sprintf "F# script processed queue message '%s'" json)
        json

Anche utilizzabile con più parametri di output se si designa un unico output con `$return`.


## <a name="route-support"></a>Supporto per la distribuzione

Per impostazione predefinita quando si crea una funzione per un trigger HTTP o WebHook, la funzione è utilizzabili con una route della maschera:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

È possibile personalizzare questo percorso facoltativa `route` proprietà trigger HTTP input di associazione. Ad esempio, il file *function.json* seguente definisce un `route` proprietà per un trigger HTTP:

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }

Con questa configurazione, la funzione ora è destinata con la seguente route anziché route originale.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

In questo modo il codice della funzione supportare due parametri dell'indirizzo, `category` e `id`. È possibile utilizzare qualsiasi [Vincolo della Route API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) con i parametri. Il codice di funzione c# seguente vengono utilizzate le entrambi i parametri.

    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }

Ecco il codice di funzione node usare gli stessi parametri di route.

    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;
    
        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }
    
        context.done();
    } 

Per impostazione predefinita, tutti i cicli di funzione sono preceduti *api*. È anche possibile personalizzare o rimuovere il prefisso utilizzando il `http.routePrefix` proprietà nel file *host.json* . Nell'esempio seguente rimuove il prefisso della route *api* utilizzando una stringa vuota per il prefisso nel file *host.json* .

    {
      "http": {
        "routePrefix": ""
      }
    }

Per informazioni dettagliate su come aggiornare il file *host.json* per la funzione, vedere, [su come aggiornare app funzione file](functions-reference.md#fileupdate). 

Aggiungendo questa configurazione, la funzione è destinata con la seguente route:

    http://<yourapp>.azurewebsites.net/products/electronics/357

Per informazioni su altre proprietà che è possibile configurare nel file *host.json* , vedere [informazioni di riferimento host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).





## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le risorse seguenti:

* [Una funzione](functions-test-a-function.md)
* [Ridimensionare una funzione](functions-scale.md)
