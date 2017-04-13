<properties
    pageTitle="Riferimenti per sviluppatori di funzioni Azure | Microsoft Azure"
    description="Imparare a sviluppare funzioni Azure utilizzando c#."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funzioni, funzioni, elaborazione dell'evento, webhooks, calcolo dinamico, architettura senza server"/>

<tags
    ms.service="functions"
    ms.devlang="dotnet"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-c-developer-reference"></a>Riferimenti per sviluppatori di Azure funzioni c#

> [AZURE.SELECTOR]
- [C# script](../articles/azure-functions/functions-reference-csharp.md)
- [Script F #](../articles/azure-functions/functions-reference-fsharp.md)
- [Node](../articles/azure-functions/functions-reference-node.md)
 
L'esperienza c# per le funzioni di Azure si basa su Azure WebJobs SDK. Dati fluisce la funzione c# tramite gli argomenti del metodo. Nomi degli argomenti vengono specificati in `function.json`, e non esistono nomi predefiniti per l'accesso a elementi quali i token di funzione logger e annullamento.

In questo articolo si presuppone che il [riferimento per sviluppatori di Azure funzioni](functions-reference.md)già letti.

## <a name="how-csx-works"></a>Come funziona il .csx

Il `.csx` formato consente di scrivere meno "standard" e di focalizzare l'attenzione su come scrivere una c# funzione. Funzioni di Azure, è sufficiente includere tutti i riferimenti di assembly e spazi dei nomi è necessario impostare superiore, come di consueto e anziché disposizione tutti gli elementi in uno spazio dei nomi e una classe, è possibile definire solo il `Run` metodo. Se è necessario includere tutte le classi, ad esempio per definire gli oggetti POCO, è possibile includere una classe all'interno dello stesso file.

## <a name="binding-to-arguments"></a>Associazione di argomenti

Le associazioni diversi sono associate a una funzione c# tramite il `name` proprietà nella configurazione *function.json* . Ogni associazione include tipi supportati che descritte per associazione; ad esempio, un trigger blob supportino una stringa, un POCO o diversi altri tipi. È possibile utilizzare il tipo più adatta alle proprie esigenze. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>Registrazione

Per ottenere l'output per i registri di trasmissione in c#, è possibile includere un `TraceWriter` digitato argomento. È consigliabile denominare quest `log`. È consigliabile evitare di `Console.Write` nelle funzioni di Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Asincrono

Per rendere una funzione asincrono, utilizzare la `async` parole chiave e restituire un `Task` oggetto.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Token di annullamento

In alcuni casi, potrebbe essere operazioni sensibili alla chiusura. Mentre è sempre consigliabile scrivere codice che è possibile gestire un arresto anomalo, nei casi in cui si desidera gestire spegnimento richiede, è possibile definire un [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) digitato argomento.  A `CancellationToken` saranno forniti deve essere attivato se un arresto host. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importazione di spazi dei nomi

Se è necessario importare gli spazi dei nomi, è possibile eseguire in modo come sempre, con la `using` clausola.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Gli spazi dei nomi seguenti vengono importati automaticamente e pertanto sono facoltativi:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Riferimento ad assembly esterni

Per gli assembly framework, aggiungere riferimenti utilizzando il `#r "AssemblyName"` direttiva.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
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
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Se è necessario fare riferimento a un assembly privato, è possibile caricare il file assembly in un `bin` cartella relativa alla funzione di riferimento che utilizzando il file (ad esempio nome  `#r "MyAssembly.dll"`). Per informazioni su come caricare file nella cartella di funzione, vedere la sezione seguente nella gestione dei pacchetti.

## <a name="package-management"></a>Gestione dei pacchetti

Per utilizzare pacchetti NuGet in una funzione c#, caricare un file di *project.json* per la cartella della funzione nel file system dell'app funzione. Ecco un esempio *project.json* config aggiunge un riferimento a Microsoft.ProjectOxford.Face versione 1.1.0:

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

È supportata solo la 4.6 di .NET Framework, dunque, verificare che il file *project.json* specifica `net46` come illustrato di seguito.

Quando si carica un file *project.json* , il runtime Ottiene i pacchetti e aggiunge automaticamente i riferimenti agli assembly pacchetto. Non è necessario aggiungere `#r "AssemblyName"` direttive. È sufficiente aggiungere necessari `using` istruzioni al file di *run.csx* per utilizzare i tipi definiti in pacchetti NuGet.


### <a name="how-to-upload-a-projectjson-file"></a>Come caricare un file project.json

1. Inizio assicurandosi che l'app di funzione è in esecuzione, che è possibile effettuare aprendo la funzione nel portale di Azure. 

    Questo anche consente di accedere ai registri di trasmissione in cui verranno visualizzati output di installazione di pacchetto. 

2. Per caricare un file project.json, utilizzare uno dei metodi descritti nella sezione **come aggiornare i file app funzione** dell' [argomento di riferimento per sviluppatori di funzioni di Azure](functions-reference.md#fileupdate). 

3. Dopo che il file *project.json* viene caricato, viene visualizzato output come nell'esempio seguente la funzione di streaming log:

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

Per ottenere una variabile di ambiente o un'app impostazione valore, utilizzare `System.Environment.GetEnvironmentVariable`, come illustrato nell'esempio seguente:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Riutilizzo di codice .csx

È possibile utilizzare le classi e i metodi definiti in altri file *.csx* nel file *run.csx* . A tale scopo, utilizzare `#load` direttive nel file *run.csx* , come illustrato nell'esempio seguente.

Esempio *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Esempio *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

È possibile usare un percorso relativo con la `#load` direttiva:

* `#load "mylogger.csx"`Carica un file memorizzato nella cartella di funzione.

* `#load "loadedfiles\mylogger.csx"`Carica un file memorizzato in una cartella nella cartella di funzione.

* `#load "..\shared\mylogger.csx"`Carica un file memorizzato in una cartella allo stesso livello della cartella di funzione, vale a dire direttamente in *wwwroot*.
 
Il `#load` direttiva funziona solo con i file *.csx* (c# script) e non con file con *estensione cs* . 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere le risorse seguenti:

* [Riferimenti per sviluppatori di funzioni Azure](functions-reference.md)
* [Riferimenti per sviluppatori di Azure funzioni F #](functions-reference-fsharp.md)
* [Azure NodeJS funzioni di riferimento per sviluppatori](functions-reference-node.md)
* [Associazioni e azure trigger di funzioni](functions-triggers-bindings.md)

