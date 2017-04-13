<properties 
    pageTitle="Utilizzo di archiviazione blob Azure con SDK WebJobs" 
    description="Informazioni su come usare archiviazione blob Azure con SDK WebJobs. Avviare un processo quando viene visualizzata un nuovo blob in un contenitore e gestire 'blob danneggiato'." 
    services="app-service\web, storage" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>Utilizzo di archiviazione blob Azure con SDK WebJobs

## <a name="overview"></a>Panoramica

Questa guida include esempi di codice c# che mostrano come attivare un processo quando blob Azure viene creato o aggiornato. Gli esempi di codice utilizzano [SDK WebJobs](websites-dotnet-webjobs-sdk.md) versione 1. x.

Per esempi di codice che illustrano come creare BLOB, vedere [come utilizzare l'archiviazione di Azure coda con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
        
Presuppone che si sa [come creare un progetto WebJob in Visual Studio con stringhe di connessione che fanno riferimento al proprio account di archiviazione](websites-dotnet-webjobs-sdk-get-started.md) o a [più account di archiviazione](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

## <a id="trigger"></a>Procedura di attivazione di una funzione quando un blob viene creato o aggiornato

In questa sezione viene illustrato come utilizzare il `BlobTrigger` attributo. 

> [AZURE.NOTE] SDK WebJobs analizza i file di log per controllare le nuove o modificate BLOB. Questo processo non è in tempo reale; una funzione potrebbe non essere attivata finché alcuni minuti o più dopo aver creato il blob. Inoltre, [lo spazio di archiviazione registri vengono creati nel "sforzi"](https://msdn.microsoft.com/library/azure/hh343262.aspx) base; non ci sono garanzie che tutti gli eventi da acquisire. In alcuni casi potrebbero perse registri. Se le limitazioni di velocità e l'affidabilità di trigger blob non sono accettabili per l'applicazione, il metodo consigliato consiste nel creare un messaggio di coda quando creare il blob e utilizzare l'attributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) anziché il `BlobTrigger` attributo sulla funzione che elabora il blob.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Singolo segnaposto per il nome di blob con estensione  

Nell'esempio seguente viene copiata BLOB di testo che vengono visualizzati nel contenitore di *input* al contenitore di *output* :

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Costruttore dell'attributo accetta un parametro stringa che specifica il nome del contenitore e un segnaposto per il nome blob. In questo esempio, se è stato creato un blob denominato *Blob1.txt* nel contenitore di *input* , la funzione crea un blob denominato *Blob1.txt* nel contenitore di *output* . 

È possibile specificare un modello di nome con il segnaposto di nome blob, come illustrato nell'esempio seguente:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Questo codice copia solo blob che hanno nomi che iniziano con "originale-". Ad esempio *Blob1.txt originale* nel contenitore di *input* viene copiata alla *Copia Blob1.txt* nel contenitore di *output* .

Se è necessario specificare un modello di nome i nomi di blob che dispone di parentesi graffe nel nome, fare doppio le parentesi graffe. Ad esempio, se si desidera trovare BLOB nel contenitore di *immagini* i cui nomi alla seguente:

        {20140101}-soundfile.mp3

Questa scheda per il modello:

        images/{{20140101}}-{name}

Nell'esempio, il valore di *nome* segnaposto sarebbe *soundfile.mp3*. 

### <a name="separate-blob-name-and-extension-placeholders"></a>Segnaposto nome e l'estensione blob separato

Nell'esempio seguente viene modificato l'estensione del file come copia blob che vengono visualizzati nel contenitore di *input* al contenitore di *output* . Il codice registra l'estensione del blob *input* e imposta l'estensione del blob *output* *txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a id="types"></a>Tipi di che è possibile associare a BLOB

È possibile utilizzare il `BlobTrigger` attributo sui tipi seguenti:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Altri tipi di deserializzato da [ICloudBlobStreamBinder](#icbsb) 

Se si desidera utilizzare direttamente con l'account di archiviazione Azure, è possibile aggiungere anche un `CloudStorageAccount` parametro alla firma del metodo.

Per esempi, vedere il [codice di associazione blob repository sdk di webjobs azure in GitHub.com](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs).

## <a id="string"></a>Ottenere testo blob contenuto utilizzando l'associazione a stringa

Se si prevede BLOB di testo, `BlobTrigger` possono essere applicate a una `string` parametro. Nell'esempio seguente associa un blob di testo a un `string` parametro denominato `logMessage`. La funzione utilizza il parametro per scrivere il contenuto del blob al dashboard di WebJobs SDK. 
 
        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>Guida di contenuto del blob serializzato utilizzando ICloudBlobStreamBinder

Nell'esempio seguente viene utilizzata una classe implementata `ICloudBlobStreamBinder` per abilitare la `BlobTrigger` attributo per associare un blob per la `WebImage` tipo.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK", 
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

Il `WebImage` codice di associazione è fornito in un `WebImageBinder` classe che deriva da `ICloudBlobStreamBinder`.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input, 
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>Ottenere il percorso blob per blob attivazione

Per ottenere il nome del contenitore e blob del blob che ha generato la funzione, includere un `blobTrigger` parametro nella firma della funzione di stringa.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a>In che modo gestire BLOB danneggiato

Quando un `BlobTrigger` funzione non riesce, SDK denominandolo nuovamente, nel caso in cui l'errore è stato causato da un errore temporaneo. Se l'errore è causato dal contenuto del blob, la funzione non riesce ogni volta che tenta di elaborare il blob. Per impostazione predefinita, SDK chiama una funzione fino a 5 ore per un determinato blob. Se il quinto tentativo non riesce, SDK aggiunge un messaggio a una coda denominata *veleno di blobtrigger webjobs*.

Il numero massimo di tentativi è configurabile. La stessa impostazione di [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) viene usata per la gestione di blob danneggiato e gestione dei messaggi coda danneggiato. 

Il messaggio di coda BLOB danneggiato è un oggetto JSON che contiene le proprietà seguenti:

* FunctionId (in formato *{WebJob nome}*. Funzioni. *{Nome di funzione}*, ad esempio: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* Nomecontenitore
* BlobName
* ETag (un identificatore di versione blob, ad esempio: "0x8D1DC6E70A277EF")

Nell'esempio seguente, la `CopyBlob` è presente codice che causa il blocco ogni chiamata. Dopo il SDK denominandolo per il numero massimo di tentativi, viene creato un messaggio nella coda blob danneggiato e tale messaggio viene elaborato dalla `LogPoisonBlob` funzione. 

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }
        
        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK deserializza automaticamente il messaggio JSON. Ecco la `PoisonBlobMessage` classe: 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a>Algoritmo di polling BLOB

SDK WebJobs analizza tutti i contenitori specificati da `BlobTrigger` attributi all'avvio dell'applicazione. In un account di archiviazione di grandi dimensioni questa operazione può richiedere un po' di tempo in modo che può essere un po' di tempo prima che vengono trovati nuovi BLOB e `BlobTrigger` le funzioni vengono eseguite.

Per rilevare BLOB nuove o modificate dopo l'avvio dell'applicazione, SDK letta periodicamente i registri di spazio di archiviazione blob. I registri blob vengono memorizzati e ottenere fisica scritte solo 10 minuti o e quindi in modo che sia significativo ritardo dopo un blob viene creato o aggiornato prima corrispondente `BlobTrigger` funzione esegue. 

Si verifica un'eccezione per i BLOB creati usando il `Blob` attributo. Quando SDK WebJobs crea un nuovo blob, passa il nuovo blob immediatamente a qualsiasi corrispondenti `BlobTrigger` funzioni. Pertanto se si dispone di una catena di blob ingressi e uscite, SDK può elaborare loro in modo efficiente. Ma se si desidera eseguire il blob funzioni di elaborazione per BLOB creato o aggiornato in altro modo bassa latenza, è consigliabile usare `QueueTrigger` anziché `BlobTrigger`.

### <a id="receipts"></a>Conferme di BLOB

SDK WebJobs garantisce che nessun `BlobTrigger` funzione ottiene chiamata più volte per lo stessa blob nuovo o aggiornato. A tale scopo, mantenendo *blob conferme* per determinare se è stata elaborata una versione blob specificato.

Conferme di BLOB sono archiviate in un contenitore denominato *host di webjobs azure* nell'account di archiviazione Azure specificato dalla stringa di connessione AzureWebJobsStorage. Una conferma di blob contiene le informazioni seguenti:

* La funzione che è stata chiamata per blob ("*{WebJob nome}*. Funzioni. *{Nome di funzione}*", ad esempio:"WebJob1.Functions.CopyBlob")
* Il nome del contenitore
* Il tipo di blob ("BlockBlob" o "PageBlob")
* Il nome blob
* ETag (un identificatore di versione blob, ad esempio: "0x8D1DC6E70A277EF")

Se si vuole forzare rielaborare di un blob, è possibile eliminare manualmente la conferma blob per tale blob dal contenitore *host di webjobs azure* .

## <a id="queues"></a>Argomenti correlati rientranti l'articolo code

Per informazioni su come gestire l'elaborazione di blob attivate tramite un messaggio di coda o per WebJobs SDK scenari non specifici di blob elaborazione, vedere [come utilizzare l'archiviazione di Azure coda con SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Argomenti correlati descritti in questo articolo sono i seguenti:

* Funzioni asincrone
* Più istanze
* Spegnimento
* Utilizzare gli attributi WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice.
* Impostare i valori per WebJobs SDK parametri di costruzione nel codice
* Configurare `MaxDequeueCount` per la gestione di blob danneggiato.
* Impostare un trigger manualmente una funzione
* Scrivere i registri

## <a id="nextsteps"></a>Passaggi successivi

Questa guida è forniti esempi di codice che illustrano come gestire scenari comuni per l'utilizzo di BLOB Azure. Per ulteriori informazioni sull'utilizzo di Azure WebJobs e WebJobs SDK, vedere [Azure WebJobs consigliati risorse](http://go.microsoft.com/fwlink/?linkid=390226).
 
