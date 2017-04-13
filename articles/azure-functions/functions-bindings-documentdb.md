<properties
    pageTitle="Azure associazioni di funzioni DocumentDB | Microsoft Azure"
    description="Informazioni su come utilizzare le associazioni di Azure DocumentDB nelle funzioni di Azure."
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

# <a name="azure-functions-documentdb-bindings"></a>Associazioni di funzioni DocumentDB Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene descritto come configurare e associazioni di Azure DocumentDB codice nelle funzioni di Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Associazione di input DocumentDB Azure

Associazioni di input è possono caricare un documento da una raccolta di DocumentDB e passare direttamente all'associazione. È possibile determinare l'id documento in base al trigger che richiamato la funzione. In una funzione c#, le modifiche apportate al record vengono inviate automaticamente tornare alla raccolta al termine della funzione correttamente.

#### <a name="functionjson-for-documentdb-input-binding"></a>Function.JSON per l'associazione di input DocumentDB

Il file *function.json* fornisce le proprietà seguenti:

- `name`: Nome variabile utilizzato nel codice della funzione per il documento.
- `type`: deve essere impostata su "documentdb".
- `databaseName`: Il database contenente il documento.
- `collectionName`: La raccolta contenente il documento.
- `id`: L'Id del documento da recuperare. Questa proprietà supporta associazioni simile a "{queueTrigger}", che verrà utilizzato il valore di stringa del messaggio coda come documento ID.
- `connection`: Questa stringa deve essere un set di impostazione dell'applicazione all'endpoint per l'account DocumentDB. Se si sceglie l'account dalla scheda integrazione, verrà creata una nuova impostazione App dell'utente con un nome che assume la forma seguente yourAccount_DOCUMENTDB. Se è necessario creare manualmente l'impostazione di App, la stringa di connessione deve essere nel seguente formato, AccountEndpoint =<Endpoint for your account>; AccountKey =<Your primary access key>;.
- ' direzione: deve essere impostata su *"in"*.

Esempio *function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>Esempio di codice input DocumentDB Azure per un trigger di coda c#
 
Usa function.json riportato sopra, associazione di input DocumentDB verrà recuperare il documento con l'id che corrisponde alla stringa di messaggio di coda e passare al parametro "documento". Se il documento non è stato trovato il parametro 'document' sarà null. Il documento viene aggiornato con il valore di testo nuovo quindi al termine della funzione.
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>Esempio di codice input DocumentDB Azure per un trigger di coda F #

Usa function.json riportato sopra, associazione di input DocumentDB verrà recuperare il documento con l'id che corrisponde alla stringa di messaggio di coda e passare al parametro "documento". Se il documento non è stato trovato il parametro 'document' sarà null. Il documento viene aggiornato con il valore di testo nuovo quindi al termine della funzione.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

È necessario un `project.json` file che utilizza NuGet per specificare il `FSharp.Interop.Dynamic` e `Dynamitey` pacchetti come le dipendenze dei pacchetti, come il seguente:

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

Questo verrà usata NuGet per recuperare le dipendenze e si farà riferimento dello script.

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Esempio di codice input DocumentDB Azure per un trigger di coda Node
 
Usa function.json riportato sopra, associazione di input DocumentDB verrà recuperare il documento con l'id che corrisponde alla stringa di messaggio di coda e passarlo alla `documentIn` associazione di proprietà. Le funzioni Node, documenti aggiornati non vengono inviati tornare alla raccolta. Tuttavia, è possibile passare associazione di input direttamente in un output DocumentDB associazione denominato `documentOut` per supportare gli aggiornamenti. In questo esempio aggiorna la proprietà text del documento di input e imposta come documento di destinazione.
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Associazioni di output DocumentDB Azure

Le funzioni possono scrivere JSON associazione di output di documenti a un database di Azure DocumentDB utilizzando il **Documento DocumentDB Azure** . Per ulteriori informazioni su Azure DocumentDB rivedere l' [Introduzione alle DocumentDB](../documentdb/documentdb-introduction.md) e l' [esercitazione Introduzione](../documentdb/documentdb-get-started.md).

#### <a name="functionjson-for-documentdb-output-binding"></a>associazione di output Function.JSON per DocumentDB

Il file function.json fornisce le proprietà seguenti:

- `name`: Nome variabile utilizzato nel codice della funzione per il nuovo documento.
- `type`: deve essere impostata su *"documentdb"*.
- `databaseName`: Il database contenente la raccolta in cui verrà creato il nuovo documento.
- `collectionName`: La raccolta in cui verrà creato il nuovo documento.
- `createIfNotExists`: È un valore boolean per indicare se la raccolta verrà creata se non esiste. Il valore predefinito è *false*. Il motivo per questo è le novità le raccolte vengono create con la velocità di effettiva riservato, sono previsti prezzi implicazioni. Per ulteriori informazioni, visitare i [prezzi pagina](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: Questa stringa deve essere che un' **Impostazione dell'applicazione** impostate sul punto finale per l'account DocumentDB. Se si sceglie l'account dalla scheda **integrazione** , una nuova impostazione App verrà creata automaticamente con un nome che include i seguenti `yourAccount_DOCUMENTDB`. Se è necessario creare manualmente l'impostazione di App, la stringa di connessione deve essere nel seguente formato, `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: deve essere impostata su *"Indietro"*. 
 
Esempio function.json:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Azure DocumentDB output esempio per un trigger di coda Node

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

Documento di output:

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>Azure DocumentDB output esempio per un trigger di coda F #

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>Azure DocumentDB output esempio per un trigger di coda c#


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Azure DocumentDB codice esempio impostazione nome file di output

Se si desidera impostare il nome del documento nella funzione, è sufficiente impostare il `id` valore.  Se ad esempio JSON contenuto per un dipendente viene interrotta nella coda simile al seguente:

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

È possibile utilizzare il codice c# seguente in una funzione di trigger coda: 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

O il codice F # equivalente:

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Output di esempio:

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
