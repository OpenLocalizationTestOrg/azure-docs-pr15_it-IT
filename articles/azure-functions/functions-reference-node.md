<properties
    pageTitle="Riferimenti per sviluppatori di funzioni NodeJS Azure | Microsoft Azure"
    description="Informazioni su come sviluppare funzioni Azure utilizzando NodeJS."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funzioni, funzioni, elaborazione dell'evento, webhooks, calcolo dinamico, architettura senza server"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Azure NodeJS funzioni di riferimento per sviluppatori

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [Script F #](../articles/azure-functions/functions-reference-fsharp.md)
- [Node](../articles/azure-functions/functions-reference-node.md)

L'esperienza di nodo/JavaScript per le funzioni di Azure consente di esportare una funzione che verrà inviata facilmente un `context` oggetto per la comunicazione con il runtime e per la ricezione e invio di dati tramite le associazioni.

In questo articolo si presuppone che il [riferimento per sviluppatori di Azure funzioni](functions-reference.md)già letti.

## <a name="exporting-a-function"></a>Esportazione di una funzione

Tutte le funzioni JavaScript necessario esportare un unico `function` tramite `module.exports` per il runtime trovare la funzione ed eseguirlo. Questa funzione è necessario includere sempre un `context` oggetto.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Associazioni di `direction === "in"` vengono passati come argomenti della funzione, ovvero è possibile utilizzare [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) gestire in modo dinamico nuovi input (ad esempio tramite `arguments.length` per scorrere tutti gli input). Questa funzionalità è utile se è presente un trigger con alcun input aggiuntivi, come è possibile accedere ai dati di trigger prevedibili senza fare riferimento il `context` oggetto.

Gli argomenti sono sempre passati alla funzione nell'ordine che non siano presenti nei *function.json*, anche se si non specificato nell'istruzione esportazioni. Ad esempio, se si dispone di `function(context, a, b)` e modificarla in `function(context, a)`, è comunque possibile ottenere il valore di `b` nel codice di funzione facendo riferimento a `arguments[3]`.

Tutte le associazioni, indipendentemente dalla direzione, vengono anche passate lungo il `context` oggetto (vedere sopra). 

## <a name="context-object"></a>oggetto di contesto

Il runtime utilizza un `context` oggetto per passare i dati da e verso la funzione e consentono di comunicare con il runtime.

L'oggetto di contesto è sempre il primo parametro a una funzione e deve sempre essere incluso perché contiene ad esempio i metodi `context.done` e `context.log` che devono utilizzare correttamente il runtime. È possibile assegnare un nome oggetto qualsiasi elemento desiderato (ad esempio `ctx` o `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>Context.Bindings

Il `context.bindings` oggetto raccoglie tutti i dati di input e di output. I dati viene aggiunta al `context.bindings` oggetto tramite il `name` proprietà dell'associazione. Ad esempio, data la definizione di associazione seguente *function.json*, è possibile accedere il contenuto della coda tramite `context.bindings.myInput`. 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

Il `context.done` funzione indica il runtime che termine in esecuzione. È importante per chiamare al termine con la funzione. in caso contrario, viene comunque mai saprà che la funzione è stata completata. 

Il `context.done` funzione consente di passare nuovamente un errore definito dall'utente per il runtime, come un elenco delle proprietà delle proprietà che sovrascriverà le proprietà nel `context.bindings` oggetto.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>Context.log(Message)

Il `context.log` metodo consente di generare l'output di istruzioni di registrazione correlate insieme ai fini della registrazione. Se si usa `console.log`, i messaggi verranno mostrati solo per la registrazione a livello processo, che non siano utili.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

Il `context.log` metodo supporta lo stesso formato parametro che supporta il nodo [util.format metodo](https://nodejs.org/api/util.html#util_util_format_format) . Pertanto, ad esempio codice come il seguente:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

può essere scritta come segue:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>Trigger HTTP: context.req e context.res

Nel caso di trigger HTTP, perché è ad esempio un modello comune usare `req` e `res` per gli oggetti di richiesta e risposta HTTP, deciso rendere più semplice accedere a quelle dell'oggetto di contesto, anziché forzare l'uso la versione completa `context.bindings.name` motivo.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>Nodo versione e gestione dei pacchetti

La versione di nodo è bloccata in `5.9.1`. Abbiamo stiamo esaminando aggiunta del supporto per più versioni e impostarla come configurabile.

È possibile includere pacchetti nella funzione per caricare un file di *package.json* alla cartella della funzione nel file system dell'app funzione. Per file caricare istruzioni, vedere la sezione **sull'aggiornamento di file app funzione** dell' [argomento di riferimento per sviluppatori di funzioni di Azure](functions-reference.md#fileupdate). 

È inoltre possibile utilizzare `npm install` nell'interfaccia riga di comando dell'app funzione servizi (Kudu):

1. Passare a: `https://<function_app_name>.scm.azurewebsites.net`.

2. Fare clic su **Console di Debug > CMD**.

3. Passare a `D:\home\site\wwwroot\<function_name>`.

4. Eseguire `npm install`.

Dopo avere installati i pacchetti necessari, l'importazione alla funzione nei modi comune (ad esempio tramite `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>Variabili di ambiente

Per ottenere una variabile di ambiente o un'app impostazione valore, utilizzare `process.env`, come illustrato nell'esempio seguente:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>Supporto di macchina/CoffeeScript

Non è ancora alcun supporto diretto per automatico compilazione macchina/CoffeeScript tramite il runtime, in modo che in questo caso tutti devono essere gestite all'esterno di runtime in fase di distribuzione. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le risorse seguenti:

* [Riferimenti per sviluppatori di funzioni Azure](functions-reference.md)
* [Riferimenti per sviluppatori di Azure funzioni c#](functions-reference-csharp.md)
* [Riferimenti per sviluppatori di Azure funzioni F #](functions-reference-fsharp.md)
* [Associazioni e azure trigger di funzioni](functions-triggers-bindings.md)
