<properties
    pageTitle="Azure trigger di funzioni e le associazioni per lo spazio di archiviazione di Azure | Microsoft Azure"
    description="Informazioni su come utilizzare trigger di archiviazione Azure e associazioni nelle funzioni di Azure."
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
    ms.author="chrande"/>

# <a name="azure-functions-triggers-and-bindings-for-azure-storage"></a>Azure trigger di funzioni e le associazioni per lo spazio di archiviazione di Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

In questo articolo viene illustrato come configurare e codice archiviazione Azure trigger e associazioni di funzioni di Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="storagequeuetrigger"></a>Azure trigger di coda di spazio di archiviazione

#### <a name="functionjson-for-storage-queue-trigger"></a>Function.JSON per trigger coda lo spazio di archiviazione

Il file *function.json* specifica le proprietà seguenti.

- `name`: Il nome della variabile utilizzato nel codice di funzione coda o nel messaggio di coda. 
- `queueName`: Il nome della coda al sondaggio. Per le regole di denominazione coda, vedere [denominazione code e i metadati](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Il nome di un'impostazione di app che contiene una stringa di connessione di spazio di archiviazione. Se si omette `connection` vuoto, il trigger sarà compatibili con la stringa di connessione di spazio di archiviazione predefinito per app funzione, che corrisponde all'impostazione app AzureWebJobsStorage.
- `type`: Deve essere impostata su *queueTrigger*.
- `direction`: Deve essere impostata su *in*. 

Esempio *function.json* per un trigger di coda lo spazio di archiviazione:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

#### <a name="queue-trigger-supported-types"></a>Tipi di trigger coda supportati

Il messaggio di coda può essere deserializzato a uno dei seguenti tipi:

* Oggetto (da JSON)
* Stringa
* Matrice di byte 
* `CloudQueueMessage`(C#) 

#### <a name="queue-trigger-metadata"></a>Coda trigger metadati

È possibile ottenere metadati coda nella funzione utilizzando i nomi di variabile:

* expirationTime
* insertionTime
* nextVisibleTime
* ID
* popReceipt
* dequeueCount
* queueTrigger (un altro modo per recuperare il testo del messaggio coda come stringa)

In questo esempio di codice c# recupera e registri accodare dei metadati:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### <a name="handling-poison-queue-messages"></a>Gestione dei messaggi coda danneggiato

Messaggi il cui contenuto genera una funzione non viene eseguita sono denominati *messaggi danneggiati*. Quando la funzione non riesce, il messaggio di coda non viene eliminato e infine acquisito nuovamente causa il ciclo da ripetere. SDK possibile interrompere automaticamente il ciclo dopo un numero limitato di iterazioni oppure è possibile eseguire manualmente.

SDK chiama una funzione fino a 5 ore per l'elaborazione di un messaggio di coda. Se il quinto tentativo non riesce, il messaggio viene spostato a una coda danneggiata.

Coda danneggiata, denominata *{originalqueuename}*-danneggiato. È possibile scrivere è necessaria una funzione per elaborare i messaggi dalla coda danneggiata registrazione loro o inviando il file una notifica che attenzione manuale. 

Se si vuole gestire manualmente i messaggi danneggiati, è possibile ottenere il numero di volte in cui un messaggio è stato selezionato per l'elaborazione selezionando `dequeueCount`.

## <a id="storagequeueoutput"></a>Associazione di output Azure coda di spazio di archiviazione

#### <a name="functionjson-for-storage-queue-output-binding"></a>associazione di output Function.JSON per coda lo spazio di archiviazione

Il file *function.json* specifica le proprietà seguenti.

- `name`: Il nome della variabile utilizzato nel codice di funzione coda o nel messaggio di coda. 
- `queueName`: Il nome della coda. Per le regole di denominazione coda, vedere [denominazione code e i metadati](https://msdn.microsoft.com/library/dd179349.aspx).
- `connection`: Il nome di un'impostazione di app che contiene una stringa di connessione di spazio di archiviazione. Se si omette `connection` vuoto, il trigger sarà compatibili con la stringa di connessione di spazio di archiviazione predefinito per app funzione, che corrisponde all'impostazione app AzureWebJobsStorage.
- `type`: Deve essere impostata su *coda*.
- `direction`: Deve essere impostata su *Indietro*. 

Esempio *function.json* per una coda di spazio di archiviazione di output associazione che utilizza un trigger di coda e scrive un messaggio di coda:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="queue-output-binding-supported-types"></a>Tipi di associazione di output supportati coda

Il `queue` associazione può serializzare i tipi seguenti a un messaggio di coda:

* Oggetto (`out T` in c# crea un messaggio con un oggetto null se il parametro è null quando la funzione termina)
* Stringa (`out string` in c# Crea messaggio della coda se il valore del parametro non null quando la funzione termina)
* Matrice di byte (`out byte[]` in c# works come stringa) 
* `out CloudQueueMessage`(C#, funzionamento stringa) 

In c#, è anche possibile associare a `ICollector<T>` o `IAsyncCollector<T>` in `T` corrisponde a uno dei tipi supportati.

#### <a name="queue-output-binding-code-examples"></a>Esempi di codice coda output associazione

In questo esempio di codice c# scrive un messaggio di coda unico output per ogni messaggio coda di input.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

In questo esempio di codice c# scrive più messaggi utilizzando `ICollector<T>` (utilizzare `IAsyncCollector<T>` in una funzione asincrono):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

## <a id="storageblobtrigger"></a>Trigger di blob Azure lo spazio di archiviazione

#### <a name="functionjson-for-storage-blob-trigger"></a>Function.JSON per trigger di spazio di archiviazione blob

Il file *function.json* specifica le proprietà seguenti.

- `name`: Il nome della variabile utilizzato nel codice di funzione il blob. 
- `path`: Un percorso che specifica il contenitore per monitorare e facoltativamente un modello di nome blob.
- `connection`: Il nome di un'impostazione di app che contiene una stringa di connessione di spazio di archiviazione. Se si omette `connection` vuoto, il trigger sarà compatibili con la stringa di connessione di spazio di archiviazione predefinito per app funzione, che corrisponde all'impostazione app AzureWebJobsStorage.
- `type`: Deve essere impostata su *blobTrigger*.
- `direction`: Deve essere impostata su *in*.

Esempio *function.json* per un trigger di spazio di archiviazione blob che controlla le blob che vengono aggiunti al contenitore gli elementi di lavoro esempi:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

#### <a name="blob-trigger-supported-types"></a>Tipi di trigger BLOB supportati

Il blob può essere deserializzato a uno dei seguenti tipi di funzioni nodo o c#:

* Oggetto (da JSON)
* Stringa

Nelle funzioni c# è anche possibile associare a uno dei tipi seguenti:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Altri tipi di deserializzato da [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

#### <a name="blob-trigger-c-code-example"></a>BLOB trigger c# esempio

In questo esempio di codice c# registra il contenuto di ogni blob che viene aggiunto al contenitore monitorato.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

#### <a name="blob-trigger-name-patterns"></a>Modelli di nomi di trigger BLOB

È possibile specificare un modello di nome blob nel `path` proprietà. Per esempio:

```json
"path": "input/original-{name}",
```

Questo percorso otterrebbe blob denominato *Blob1.txt originale* nel contenitore di *input* e il valore della `name` variabile nel codice di funzione sarebbe `Blob1`.

Un altro esempio:

```json
"path": "input/{blobname}.{blobextension}",
```

Questo percorso otterrebbe anche un blob denominate *Blob1.txt originale*e il valore di `blobname` e `blobextension` le variabili di codice della funzione sarebbe *Blob1 originale* e *txt*.

È possibile limitare i tipi di blob che comportano la funzione specificando un motivo con un valore fisso per l'estensione di file. Se si imposta la `path` *a/esempi / {nome} PNG*, solo *PNG* BLOB nel contenitore di *esempi* attiverà la funzione.

Se è necessario specificare un modello di nome i nomi di blob che dispone di parentesi graffe nel nome, fare doppio le parentesi graffe. Ad esempio, se si desidera trovare BLOB nel contenitore di *immagini* i cui nomi alla seguente:

        {20140101}-soundfile.mp3

Utilizzare questa opzione per la `path` proprietà:

        images/{{20140101}}-{name}

Nell'esempio, il `name` valore variabile sarebbe *soundfile.mp3*. 

#### <a name="blob-receipts"></a>Conferme di BLOB

Il runtime funzioni Azure garantisce che non offre la funzione trigger blob venga chiamata più volte per lo stessa blob nuovo o aggiornato. A tale scopo, mantenendo *blob conferme* per determinare se è stata elaborata una versione blob specificato.

Conferme di BLOB sono archiviate in un contenitore denominato *host di webjobs azure* nell'account di archiviazione Azure specificato dalla stringa di connessione AzureWebJobsStorage. Una conferma di blob contiene le informazioni seguenti:

* La funzione che è stata chiamata per blob ("*{nome di funzione app}*. Funzioni. *{nome di funzione}*", ad esempio:"functionsf74b96f7. Functions.CopyBlob")
* Il nome del contenitore
* Il tipo di blob ("BlockBlob" o "PageBlob")
* Il nome blob
* ETag (un identificatore di versione blob, ad esempio: "0x8D1DC6E70A277EF")

Se si vuole forzare rielaborare di un blob, è possibile eliminare manualmente la conferma blob per tale blob dal contenitore *host di webjobs azure* .

#### <a name="handling-poison-blobs"></a>Gestione BLOB danneggiato

Quando una funzione di trigger blob non riesce, SDK denominandolo nuovamente, nel caso in cui l'errore è stato causato da un errore temporaneo. Se l'errore è causato dal contenuto del blob, la funzione non riesce ogni volta che tenta di elaborare il blob. Per impostazione predefinita, SDK chiama una funzione fino a 5 ore per un determinato blob. Se il quinto tentativo non riesce, SDK aggiunge un messaggio a una coda denominata *veleno di blobtrigger webjobs*.

Il messaggio di coda BLOB danneggiato è un oggetto JSON che contiene le proprietà seguenti:

* FunctionId (nel formato *{nome di funzione app}*. Funzioni. *{nome di funzione}*)
* BlobType ("BlockBlob" o "PageBlob")
* Nomecontenitore
* BlobName
* ETag (un identificatore di versione blob, ad esempio: "0x8D1DC6E70A277EF")

#### <a name="blob-polling-for-large-containers"></a>BLOB polling per i contenitori di grandi dimensioni

Se il contenitore blob che esegue il monitoraggio trigger contiene più di 10.000 BLOB, i file in attesa di BLOB nuove o modificate le analisi di runtime funzioni registro. Questo processo non è in tempo reale; una funzione potrebbe non essere attivata finché alcuni minuti o più dopo aver creato il blob. Inoltre, [lo spazio di archiviazione registri vengono creati nel "sforzi"](https://msdn.microsoft.com/library/azure/hh343262.aspx) base; non ci sono garanzie che tutti gli eventi da acquisire. In alcuni casi potrebbero perse registri. Se le limitazioni di velocità e l'affidabilità di trigger blob per i contenitori di grandi dimensioni non sono accettabili per l'applicazione, il metodo consigliato consiste nel creare un messaggio di coda quando si crea il blob e utilizza un trigger di coda anziché un trigger blob per elaborare il blob.
 
## <a id="storageblobbindings"></a>Archivio blob Azure lo spazio di archiviazione di input e output associazioni

#### <a name="functionjson-for-a-storage-blob-input-or-output-binding"></a>Function.JSON per un blob di spazio di archiviazione di input o associazione di output

Il file *function.json* specifica le proprietà seguenti.

- `name`: Il nome della variabile utilizzato nel codice di funzione il blob. 
- `path`: Un percorso che specifica il contenitore per leggere il blob da o scrivere il blob per e facoltativamente un modello di nome blob.
- `connection`: Il nome di un'impostazione di app che contiene una stringa di connessione di spazio di archiviazione. Se si omette `connection` vuoto, l'associazione sarà compatibili con la stringa di connessione di spazio di archiviazione predefinito per app funzione, che corrisponde all'impostazione app AzureWebJobsStorage.
- `type`: Deve essere impostata su *blob*.
- `direction`: Impostare in *Avanti* o *Indietro*. 

Esempio *function.json* per un'archiviazione blob input o associazione, utilizzando un trigger di coda per copiare un blob di output:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="blob-input-and-output-supported-types"></a>BLOB di input e di output supportati

Il `blob` associazione può serializzare o deserializzare i tipi seguenti nelle funzioni Node o c#:

* Oggetto (`out T` in c# per blob di output: crea un blob come oggetto null se il valore del parametro è null quando la funzione termina)
* Stringa (`out string` in c# per blob di output: crea un blob solo se il parametro stringa non null quando la funzione restituisce)

In c# funzioni, è anche possibile associare ai tipi seguenti:

* `TextReader`(solo input)
* `TextWriter`(solo output)
* `Stream`
* `CloudBlobStream`(solo output)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### <a name="blob-output-c-code-example"></a>BLOB output c# esempio

Questo esempio di codice c# copia un blob il cui nome è ricevuto un messaggio di coda.

```csharp
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

## <a id="storagetablesbindings"></a>Azure lo spazio di archiviazione tabelle di input e associazioni di output

#### <a name="functionjson-for-storage-tables"></a>Function.JSON per le tabelle di spazio di archiviazione

*Function.json* specifica le proprietà seguenti.

- `name`: Il nome della variabile utilizzato nel codice della funzione per l'associazione di tabella. 
- `tableName`: Il nome della tabella.
- `partitionKey`e `rowKey` : utilizzati per una singola entità in una funzione nodo c# o di leggere o scrivere una singola entità in una funzione di nodo.
- `take`: Il numero massimo di righe da leggere per tabella di input in una funzione di nodo.
- `filter`: Espressione di filtro OData per tabella di input in una funzione di nodo.
- `connection`: Il nome di un'impostazione di app che contiene una stringa di connessione di spazio di archiviazione. Se si omette `connection` vuoto, l'associazione sarà compatibili con la stringa di connessione di spazio di archiviazione predefinito per app funzione, che corrisponde all'impostazione app AzureWebJobsStorage.
- `type`: Deve essere impostata su *tabella*.
- `direction`: Impostare in *Avanti* o *Indietro*. 

L' esempio seguente *function.json* utilizza un trigger di coda per leggere una riga nella tabella. Il JSON fornisce un valore di chiave hardcoded partizione e specifica che il tasto riga proviene dal messaggio coda.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="storage-tables-input-and-output-supported-types"></a>Tipi supportati per le tabelle di archiviazione di input e output

Il `table` associazione può serializzare o deserializzare gli oggetti in funzioni Node o c#. Gli oggetti avrà le proprietà RowKey e PartitionKey. 

In c# funzioni, è anche possibile associare ai tipi seguenti:

* `T`dove `T` implementa`ITableEntity`
* `IQueryable<T>`(solo input)
* `ICollector<T>`(solo output)
* `IAsyncCollector<T>`(solo output)

#### <a name="storage-tables-binding-scenarios"></a>Spazio di archiviazione tabelle scenari di associazione

Associazione tabella supporta gli scenari seguenti:

* Leggere una singola riga in una funzione nodo c# o.

    Impostare `partitionKey` e `rowKey`. Il `filter` e `take` proprietà non vengono utilizzate in questo scenario.

* Per saperne di più righe in una funzione c#.

    Il runtime funzioni fornisce un `IQueryable<T>` oggetto associato alla tabella. Tipo di `T` necessario derivano da `TableEntity` o implementare `ITableEntity`. Il `partitionKey`, `rowKey`, `filter`, e `take` proprietà non vengono utilizzate in questo scenario. è possibile utilizzare il `IQueryable` oggetto di eseguire qualsiasi applicazione di filtri necessari. 

* Per saperne di più righe in una funzione di nodo.

    Impostare il `filter` e `take` proprietà. Non imposta `partitionKey` o `rowKey`.

* Scrivere una o più righe in una funzione c#.

    Il runtime funzioni fornisce un `ICollector<T>` o `IAsyncCollector<T>` associato alla tabella, in cui `T` specifica lo schema di entità che si desidera aggiungere. In genere, digitare `T` deriva da `TableEntity` o `ITableEntity`, ma non è necessario. Il `partitionKey`, `rowKey`, `filter`, e `take` proprietà non vengono utilizzate in questo scenario.

#### <a name="storage-tables-example-read-a-single-table-entity-in-c-or-node"></a>Esempio di spazio di archiviazione tabelle: un'entità singola tabella nodo c# o di lettura

Nell'esempio di codice c# seguente funziona con il file *function.json* precedente mostrato in precedenza per leggere un'entità singola tabella. Il messaggio di coda è il valore di chiave di riga e tabella entità viene letto in un tipo definito nel file *run.csx* . Il tipo di include `PartitionKey` e `RowKey` proprietà e non deriva da `TableEntity`. 

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Il codice riportato di seguito F # funziona anche con il file *function.json* precedente da leggere un'entità singola tabella.

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

Nell'esempio seguente nodo funziona anche con il file *function.json* precedente da leggere un'entità singola tabella.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### <a name="storage-tables-example-read-multiple-table-entities-in-c"></a>Esempio di spazio di archiviazione tabelle: per saperne di più entità tabella c# 

I seguenti *function.json* e c# codice entità di lettura di esempio per una chiave di partizione specificato nel messaggio della coda di.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Il codice c# aggiunge un riferimento a Azure archiviazione SDK in modo che il tipo di entità possibile deriva da `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### <a name="storage-tables-example-create-table-entities-in-c"></a>Esempio di spazio di archiviazione tabelle: creare entità della tabella in C# 

Nell'esempio seguente *function.json* e *run.csx* viene illustrato come scrivere entità della tabella in c#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### <a name="storage-tables-example-create-table-entities-in-f"></a>Esempio di spazio di archiviazione tabelle: creare entità della tabella in F#

Nell'esempio seguente *function.json* e *run.fsx* viene illustrato come scrivere entità della tabella in F #.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

#### <a name="storage-tables-example-create-a-table-entity-in-node"></a>Esempio di spazio di archiviazione tabelle: creare una tabella di entità nel nodo

Nell'esempio seguente *function.json* e *run.csx* viene illustrato come scrivere una tabella di entità nel nodo.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
