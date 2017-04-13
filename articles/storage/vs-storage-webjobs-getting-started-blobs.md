<properties
    pageTitle="Guida introduttiva a blob lo spazio di archiviazione e Visual Studio connessi servizi (WebJob progetti) | Microsoft Azure"
    description="Informazioni su come iniziare a usare archiviazione Blob in un progetto WebJob dopo la connessione a un'archiviazione Azure mediante Visual Studio connessi servizi."
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Guida introduttiva a Blob Azure lo spazio di archiviazione e Visual Studio connessi servizi (WebJob progetti)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

Questo articolo fornisce esempi di codice c# che mostrano come attivare un processo quando blob Azure viene creato o aggiornato. Gli esempi di codice utilizzano la versione di [SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) 1. x. Quando si aggiunge un account di archiviazione a un progetto WebJob utilizzando la finestra di dialogo di Visual Studio **Aggiungere servizi connessi** , è installato il pacchetto di Azure archiviazione NuGet appropriato, i riferimenti .NET appropriati vengono aggiunti al progetto e stringhe di connessione per l'account di archiviazione vengono aggiornate nel file app.



## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Procedura di attivazione di una funzione quando un blob viene creato o aggiornato

In questa sezione viene illustrato come utilizzare l'attributo **BlobTrigger** .

 **Nota:** SDK WebJobs analizza i file di log per controllare le nuove o modificate BLOB. Questo processo è particolarmente lento; una funzione potrebbe non essere attivata finché alcuni minuti o più dopo aver creato il blob.  Se l'applicazione deve elaborare BLOB immediatamente, il metodo consigliato consiste nel creare un messaggio di coda quando si crea il blob e utilizzare l'attributo [QueueTrigger](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) anziché l'attributo **BlobTrigger** sulla funzione che elabora il blob.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Tipi di che è possibile associare a BLOB

È possibile utilizzare l'attributo **BlobTrigger** sui tipi seguenti:

* **stringa**
* **TextReader**
* **Flusso**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Altri tipi di deserializzato da [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Se si desidera utilizzare direttamente con l'account di archiviazione Azure, è anche possibile aggiungere un parametro **CloudStorageAccount** alla firma del metodo.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Ottenere testo blob contenuto utilizzando l'associazione a stringa

Se si prevede BLOB di testo, **BlobTrigger** possono essere applicate a un parametro **stringa** . Nell'esempio seguente associa un blob di testo a un parametro **stringa** denominato **logMessage**. La funzione utilizza il parametro per scrivere il contenuto del blob al dashboard di WebJobs SDK.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Guida di contenuto del blob serializzato utilizzando ICloudBlobStreamBinder

Nell'esempio seguente viene utilizzata una classe implementata **ICloudBlobStreamBinder** per abilitare l'attributo **BlobTrigger** associare un blob al tipo di **WebImage** .

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

Il codice di associazione **WebImage** viene fornito in una classe **WebImageBinder** che deriva da **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>In che modo gestire BLOB danneggiato

Quando una funzione **BlobTrigger** non riesce, SDK denominandolo nuovamente, nel caso in cui l'errore è stato causato da un errore temporaneo. Se l'errore è causato dal contenuto del blob, la funzione non riesce ogni volta che tenta di elaborare il blob. Per impostazione predefinita, SDK chiama una funzione fino a 5 ore per un determinato blob. Se il quinto tentativo non riesce, SDK aggiunge un messaggio a una coda denominata *veleno di blobtrigger webjobs*.

Il numero massimo di tentativi è configurabile. La stessa impostazione di [MaxDequeueCount](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) viene usata per la gestione di blob danneggiato e gestione dei messaggi coda danneggiato.

Il messaggio di coda BLOB danneggiato è un oggetto JSON che contiene le proprietà seguenti:

* FunctionId (in formato *{WebJob nome}*. Funzioni. *{Nome di funzione}*, ad esempio: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" o "PageBlob")
* Nomecontenitore
* BlobName
* ETag (un identificatore di versione blob, ad esempio: "0x8D1DC6E70A277EF")

Nell'esempio seguente, la funzione **CopyBlob** ha codice che causa il blocco ogni chiamata. Dopo il SDK chiama per il numero massimo di tentativi, viene creato un messaggio nella coda blob danneggiato e tale messaggio viene elaborato dalla funzione **LogPoisonBlob** .

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

SDK deserializza automaticamente il messaggio JSON. Ecco la classe **PoisonBlobMessage** :

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Algoritmo di polling BLOB

SDK WebJobs analizza tutti i contenitori specificati dagli attributi **BlobTrigger** all'avvio dell'applicazione. In un account di archiviazione di grandi dimensioni questa operazione può richiedere un po' di tempo in modo che può essere un po' di tempo prima che vengono trovati nuovi BLOB e le funzioni **BlobTrigger** vengono eseguite.

Per rilevare BLOB nuove o modificate dopo l'avvio dell'applicazione, SDK letta periodicamente i registri di spazio di archiviazione blob. I log blob vengono memorizzati e ottenere fisica scritte solo 10 minuti o, in modo che può essere significativo ritardo dopo un blob viene creato o aggiornato prima corrispondente funzione **BlobTrigger** viene eseguito.

Si verifica un'eccezione per i BLOB creati tramite l'attributo **Blob** . Quando SDK WebJobs crea un nuovo blob, passa il nuovo blob immediatamente a tutte le funzioni **BlobTrigger** corrispondente. Pertanto se si dispone di una catena di blob ingressi e uscite, SDK può elaborare loro in modo efficiente. Ma se si vuole che esegue il blob funzioni per BLOB creato o aggiornato in altro modo di elaborazione bassa latenza, si consiglia di utilizzare **QueueTrigger** anziché **BlobTrigger**.

### <a name="blob-receipts"></a>Conferme di BLOB

SDK WebJobs garantisce che non offre la funzione **BlobTrigger** venga chiamata più volte per lo stessa blob nuovo o aggiornato. A tale scopo, mantenendo *blob conferme* per determinare se è stata elaborata una versione blob specificato.

Conferme di BLOB sono archiviate in un contenitore denominato *host di webjobs azure* nell'account di archiviazione Azure specificato dalla stringa di connessione AzureWebJobsStorage. Una conferma di blob contiene le informazioni seguenti:

* La funzione che è stata chiamata per blob ("*{WebJob nome}*. Funzioni. *{Nome di funzione}*", ad esempio:"WebJob1.Functions.CopyBlob")
* Il nome del contenitore
* Il tipo di blob ("BlockBlob" o "PageBlob")
* Il nome blob
* ETag (un identificatore di versione blob, ad esempio: "0x8D1DC6E70A277EF")

Se si vuole forzare rielaborare di un blob, è possibile eliminare manualmente la conferma blob per tale blob dal contenitore *host di webjobs azure* .

## <a name="related-topics-covered-by-the-queues-article"></a>Argomenti correlati rientranti l'articolo code

Per informazioni su come gestire l'elaborazione di blob attivate tramite un messaggio di coda o per WebJobs SDK scenari non specifici di blob elaborazione, vedere [come utilizzare l'archiviazione di Azure coda con SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Argomenti correlati descritti in questo articolo sono i seguenti:

* Funzioni asincrone
* Più istanze
* Spegnimento
* Utilizzare gli attributi WebJobs SDK nel corpo di una funzione
* Impostare le stringhe di connessione SDK nel codice.
* Impostare i valori per WebJobs SDK parametri di costruzione nel codice
* Configurare **MaxDequeueCount** per la gestione di blob danneggiato.
* Impostare un trigger manualmente una funzione
* Scrivere i registri

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è forniti esempi di codice che illustrano come gestire scenari comuni per l'utilizzo di BLOB Azure. Per ulteriori informazioni sull'utilizzo di Azure WebJobs e WebJobs SDK, vedere [WebJobs Azure documentazione risorse](http://go.microsoft.com/fwlink/?linkid=390226).
