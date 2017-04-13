<properties
    pageTitle="Azure associazioni di funzioni HTTP e webhook | Microsoft Azure"
    description="Informazioni su come utilizzare trigger HTTP e webhook e le associazioni nelle funzioni di Azure."
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
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Azure associazioni di funzioni HTTP e webhook

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene spiegato come configurare e codice trigger HTTP e webhook e associazioni di funzioni di Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>Function.JSON per le associazioni HTTP e webhook

Il file *function.json* fornisce le proprietà relative alla richiesta e risposta.

Proprietà per la richiesta HTTP:

- `name`: Nome variabile utilizzato nel codice della funzione per l'oggetto richiesta (o il corpo della richiesta nel caso delle funzioni Node).
- `type`: Deve essere impostata su *httpTrigger*.
- `direction`: Deve essere impostata su *in*. 
- `webHookType`: Per i trigger WebHook, valori validi sono *github*, *il margine di flessibilità*e *genericJson*. Per un trigger di HTTP che non è un WebHook, impostare questa proprietà su una stringa vuota. Per ulteriori informazioni su WebHooks, vedere la sezione [trigger WebHook](#webhook-triggers) seguente.
- `authLevel`: Non è applicabile ai trigger WebHook. Impostare su "funzione" per richiedere la chiave dell'API "anonima" per elenco a discesa l'API fondamentale o "amministratore" per richiedere la chiave dell'API master. Per ulteriori informazioni, vedere [tasti di API](#apikeys) sotto.

Proprietà per la risposta HTTP:

- `name`: Nome variabile utilizzato nel codice della funzione per l'oggetto di risposta.
- `type`: Deve essere impostata su *http*.
- `direction`: Deve essere impostata su *Indietro*. 
 
Esempio *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>Trigger WebHook

Un trigger WebHook è un trigger di HTTP che contiene le seguenti funzionalità progettate per WebHooks:

* Per i provider WebHook specifico (attualmente GitHub e margine di flessibilità sono supportati), il runtime funzioni convalida firma del provider.
* Per le funzioni Node, il runtime funzioni fornisce il corpo della richiesta anziché l'oggetto richiesta. Non è disponibile Gestione speciali per c# funzioni, poiché è possibile controllare quali viene fornito specificando il tipo di parametro. Se si specifica `HttpRequestMessage` viene visualizzato l'oggetto richiesta. Se si specifica un tipo di POCO, il runtime funzioni tenta di analizzare un oggetto JSON nel corpo della convocazione per popolare le proprietà dell'oggetto.
* Per attivare un WebHook funzione richiesta HTTP deve includere una chiave dell'API. Per i trigger non WebHook HTTP, questo requisito è facoltativo.

Per informazioni su come configurare un GitHub WebHook, vedere [GitHub Developer - creazione WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

## <a name="url-to-trigger-the-function"></a>URL per la funzione di attivazione

Per avviare una funzione, si invia una richiesta HTTP a un URL è una combinazione di funzione app URL e il nome della funzione:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>Tasti di API

Per impostazione predefinita, una chiave dell'API deve essere inclusa in una richiesta HTTP a una funzione HTTP o WebHook di attivazione. Il tasto può essere incluso in una variabile di stringa di query denominata `code`, o possono essere inclusi in un `x-functions-key` intestazione HTTP. Per le funzioni non WebHook, è possibile indicare che una chiave dell'API non è necessario mediante l'impostazione di `authLevel` proprietà su "anonimo" nel file *function.json* .

È possibile trovare valori di chiave dell'API nella cartella *D:\home\data\Functions\secrets* nel file system dell'app funzione.  La chiave master e un tasto funzione vengono impostate nel file *host.json* , come illustrato nell'esempio seguente. 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

Il tasto funzione da *host.json* può essere utilizzato per impostare un trigger qualsiasi funzione ma non è possibile impostare un trigger una funzione disabilitata. La chiave master può essere utilizzata per qualsiasi funzione di attivazione e attiverà una funzione anche se è disattivata. È possibile configurare una funzione per richiedere la chiave master mediante l'impostazione di `authLevel` proprietà a "amministratore". 

Se la cartella di *informazioni riservate* contiene un file JSON con lo stesso nome di una funzione di `key` proprietà del file di può essere utilizzata anche per attivare la funzione e questo tasto funziona solo con la funzione fa riferimento a. Ad esempio, la chiave dell'API di una funzione denominata `HttpTrigger` specificato nel *HttpTrigger.json* nella cartella *informazioni riservate* . Ecco un esempio:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Quando si sta configurando un trigger WebHook, non condividere la chiave master con il provider di WebHook. Utilizzare una chiave che funziona solo con la funzione che elabora i WebHook.  La chiave master può essere utilizzata per impostare un trigger qualsiasi funzione anche disabilitato funzioni.

## <a name="example-c-code-for-an-http-trigger-function"></a>Codice di esempio c# per una funzione di trigger HTTP 

Nell'esempio di codice consente di cercare un `name` parametro nella stringa di query o il corpo della richiesta HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>Codice di esempio F # per una funzione di trigger HTTP

Nell'esempio di codice consente di cercare un `name` parametro nella stringa di query o il corpo della richiesta HTTP.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Sarà necessario un `project.json` file che utilizza NuGet per fare riferimento il `FSharp.Interop.Dynamic` e `Dynamitey` assembly, come segue:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Questo verrà usata NuGet per recuperare le dipendenze e si farà riferimento dello script.

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>Esempio di codice node per una funzione di trigger HTTP 

In questo esempio consente di cercare un `name` parametro nella stringa di query o il corpo della richiesta HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>Codice di esempio c# per una funzione GitHub WebHook 

In questo esempio accede commenti problema GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>Codice di esempio F # per una funzione GitHub WebHook

In questo esempio accede commenti problema GitHub.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>Esempio di codice node per una funzione GitHub WebHook 

In questo esempio accede commenti problema GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
