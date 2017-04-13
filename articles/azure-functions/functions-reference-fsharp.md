<properties
    pageTitle="Riferimenti per sviluppatori di Azure funzioni F # | Microsoft Azure"
    description="Informazioni su come sviluppare funzioni Azure tramite F #."
    services="functions"
    documentationCenter="fsharp"
    authors="sylvanc"
    manager="jbronsk"
    editor=""
    tags=""
    keywords="Azure funzioni, funzioni, eventi di elaborazione, webhooks, calcolo dinamico, senza server architettura, F#"/>

<tags
    ms.service="functions"
    ms.devlang="fsharp"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/09/2016"
    ms.author="syclebsc"/>

# <a name="azure-functions-f-developer-reference"></a>Guida di riferimento per sviluppatori F # funzioni Azure

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [Script F #](../articles/azure-functions/functions-reference-fsharp.md)
- [Node](../articles/azure-functions/functions-reference-node.md)

F # per le funzioni di Azure è una soluzione per l'esecuzione facilmente frammenti di codice o "funzioni," nel cloud. La funzione F # tramite gli argomenti della funzione flusso dei dati. Nomi degli argomenti vengono specificati in `function.json`, e non esistono nomi predefiniti per l'accesso a elementi quali i token di funzione logger e annullamento.

In questo articolo si presuppone che il [riferimento per sviluppatori di Azure funzioni](functions-reference.md)già letti.

## <a name="how-fsx-works"></a>Come funziona il fsx

Un `.fsx` file è uno script F #. Può considerato come un progetto F # è contenuto in un singolo file. Il file contiene sia il codice per il programma (in questo caso, la funzione di Azure) e le istruzioni per la gestione di dipendenze.

Quando si utilizza un `.fsx` per una funzione di Azure, generalmente richiesti sono automaticamente inclusi automaticamente, consentendo di focalizzare l'attenzione sul codice funzione anziché "standard".

## <a name="binding-to-arguments"></a>Associazione di argomenti

Ogni associazione supporta alcuni set di argomenti, come illustrato in dettaglio nelle [funzioni Azure trigger e riferimento per sviluppatori associazioni](functions-triggers-bindings.md). Ad esempio, una delle associazioni argomento che supporta un trigger blob è un POCO, può essere espressa tramite un record F #. Per esempio:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

La funzione di Azure F # richiederà uno o più argomenti. Quando si parla argomenti di funzioni di Azure, si parla argomenti di _input_ e gli argomenti di _output_ . Un argomento di input è esattamente parole simili: input alla funzione Azure F #. Un argomento di _output_ è modificabili dati o un `byref<>` argomento che funge da un modo per passare dati tornare _Indietro_ della funzione.

Nell'esempio precedente, `blob` è un argomento di input e `output` è un argomento di output. Si noti che è stato usato `byref<>` per `output` (non è necessario aggiungere il `[<Out>]` annotazione). Utilizzando una `byref<>` tipo consente di modificare il record o l'oggetto dell'argomento si riferisce alla funzione.

Quando un record F # viene utilizzato come un tipo di input, la definizione di record deve essere contrassegnata con `[<CLIMutable>]` per consentire il framework Azure funzioni impostare i campi in modo appropriato prima di passare al record alla funzione. Dietro le quinte `[<CLIMutable>]` genera metodi di impostazione per le proprietà di record. Per esempio:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Una classe F # può inoltre essere utilizzata per entrambi in e gli argomenti. Per una classe, in genere necessario proprietà Get e set. Per esempio:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registrazione

Per ottenere l'output per la [trasmissione dei log](../app-service-web/web-sites-streaming-logs-and-console.md) in F #, la funzione dovrebbe eseguire un argomento di tipo `TraceWriter`. Per coerenza, si consiglia di questo argomento è denominato `log`. Per esempio:

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Asincrono

Il `async` flusso di lavoro può essere utilizzato, ma il risultato deve restituire un `Task`. Questa operazione può essere eseguita con `Async.StartAsTask`, ad esempio:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token di annullamento

Se la funzione deve gestire arresto correttamente, è possibile assegnarle un [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) argomento. Questa operazione può essere combinata con `async`, ad esempio:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importazione di spazi dei nomi

È possibile aprire gli spazi dei nomi nel modo consueto:

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Vengono aperti automaticamente gli spazi dei nomi seguenti:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Riferimento ad assembly esterni

Analogamente, assembly framework aggiunti i riferimenti con la `#r "AssemblyName"` direttiva.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

Assembly riportati di seguito vengono aggiunti automaticamente dalle funzioni Azure ambiente host:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Inoltre, sono speciali seguenti assembly le maiuscole/minuscole e possono fare riferimento simplename (ad esempio `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se è necessario fare riferimento a un assembly privato, è possibile caricare il file assembly in un `bin` cartella relativa alla funzione di riferimento che utilizzando il file (ad esempio nome  `#r "MyAssembly.dll"`). Per informazioni su come caricare file nella cartella di funzione, vedere la sezione seguente nella gestione dei pacchetti.

## <a name="editor-prelude"></a>Editor preludio

Un editor che supporta i servizi del compilatore F # non saranno tenere in considerazione la spazi dei nomi e assembly contenente funzioni di Azure. Di conseguenza, può essere utile includere preludio che consente di trovare assembly che si utilizza l'editor e per aprire in modo esplicito gli spazi dei nomi. Per esempio:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

Quando le funzioni di Azure viene eseguito il codice, l'elaborazione di origine con `COMPILED` definiti in modo preludio editor verrà ignorato.

## <a name="package-management"></a>Gestione dei pacchetti

Per utilizzare pacchetti NuGet in una funzione F #, aggiungere un `project.json` file per la cartella della funzione nel file system dell'app funzione. Ecco un esempio `project.json` file che si aggiunge un riferimento di pacchetto NuGet a `Microsoft.ProjectOxford.Face` versione 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

È supportata solo la 4.6 di .NET Framework, dunque, verificare che il `project.json` file specifica `net46` come illustrato di seguito.

Quando si carica un `project.json` file, il runtime Ottiene i pacchetti e aggiunge automaticamente i riferimenti agli assembly pacchetto. Non è necessario aggiungere `#r "AssemblyName"` direttive. È sufficiente aggiungere necessari `open` istruzioni per il `.fsx` file.

Si consiglia di collocare automaticamente i riferimenti assembly in preludio l'editor, per migliorare l'interazione dell'editor con servizi di compilare F #.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Come aggiungere un `project.json` file alla funzione Azure

1. Inizio assicurandosi che l'app di funzione è in esecuzione, che è possibile effettuare aprendo la funzione nel portale di Azure. Questo anche consente di accedere ai registri di trasmissione in cui verranno visualizzati output di installazione di pacchetto.

2. Per caricare un `project.json` file, utilizzare uno dei metodi descritti in [come aggiornare funzione app file](functions-reference.md#fileupdate). Se si utilizza [La distribuzione continua per le funzioni di Azure](functions-continuous-deployment.md), è possibile aggiungere un `project.json` file per la gestione temporanea diramazione per sperimentare prima di aggiungere la diramazione di distribuzione.

3. Dopo la `project.json` file viene aggiunto, si vedrà output simile al seguente nella funzione del flusso log:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variabili di ambiente

Per ottenere una variabile di ambiente o un'app impostazione valore, utilizzare `System.Environment.GetEnvironmentVariable`, ad esempio:

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Riutilizzo di codice fsx

È possibile utilizzare codice da altri `.fsx` file mediante un `#load` direttiva. Per esempio:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

Percorsi vengono fornite per la `#load` sono direttiva al percorso del `.fsx` file.

* `#load "logger.fsx"`Carica un file memorizzato nella cartella di funzione.

* `#load "package\logger.fsx"`Carica un file memorizzato nel `package` della cartella di funzione.

* `#load "..\shared\mylogger.fsx"`Carica un file memorizzato nel `shared` cartella allo stesso livello della cartella di funzione, vale a dire direttamente in `wwwroot`.

Il `#load` direttiva funziona solo con `.fsx` file (F # script) e non con `.fs` file.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le risorse seguenti:

* [Guida di F #](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/index)
* [Riferimenti per sviluppatori di funzioni Azure](functions-reference.md)
* [Riferimenti per sviluppatori di Azure funzioni c#](functions-reference-csharp.md)
* [Azure NodeJS funzioni di riferimento per sviluppatori](functions-reference-node.md)
* [Associazioni e azure trigger di funzioni](functions-triggers-bindings.md)
* [Funzioni di Azure test](functions-test-a-function.md)
* [Funzioni di Azure proporzioni dei caratteri](functions-scale.md)
