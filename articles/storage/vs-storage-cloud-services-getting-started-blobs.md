<properties
    pageTitle="Guida introduttiva a blob lo spazio di archiviazione e Visual Studio connessi servizi (servizi cloud) | Microsoft Azure"
    description="Come iniziare a utilizzare archiviazione Blob Azure in un progetto di servizio cloud in Visual Studio dopo la connessione a un account di archiviazione utilizzando Visual Studio connessi servizi"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Guida introduttiva a archiviazione Blob Azure e Visual Studio connessi servizi (cloud services progetti)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

In questo articolo viene descritto come iniziare a utilizzare archiviazione Blob Azure dopo aver creato o si desidera fare riferimento a un account di archiviazione Azure utilizzando la finestra di dialogo di Visual Studio **Aggiungere servizi connessi** in un progetto di Visual Studio cloud services. Ecco come accedere e creare contenitori blob e come eseguire attività comuni come durante il caricamento, voce e il download BLOB. Gli esempi sono scritti c\# e utilizzare la [Libreria Client di Microsoft Azure lo spazio di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Archiviazione Blob Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati che accessibili da qualsiasi punto nel mondo tramite HTTP o HTTPS. Un blob singolo può essere di qualsiasi dimensione. BLOB possono essere elementi quali immagini, file audio e video, dati non elaborati e file di documento.

Come file live nelle cartelle, BLOB di spazio di archiviazione live nei contenitori. Dopo aver creato un file di archiviazione, verrà creato uno o più contenitori nello spazio di archiviazione. In archiviazione denominata "Raccoglitore", ad esempio, è possibile creare contenitori nello spazio di memorizzazione denominata "immagini" per archiviare le immagini e un'altra denominata "audio" per memorizzare file audio. Dopo aver creato i contenitori, è possibile caricare file blob singoli ad essi.

- Per ulteriori informazioni su come modificare a livello di programmazione BLOB, vedere [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md).
- Per informazioni generali sull'archiviazione di Azure, vedere [la documentazione di spazio di archiviazione](https://azure.microsoft.com/documentation/services/storage/).
- Per informazioni generali sui servizi Cloud Windows Azure, vedere [la documentazione di servizi Cloud](https://azure.microsoft.com/documentation/services/cloud-services/).
- Per ulteriori informazioni sulla programmazione di applicazioni ASP.NET, vedere [ASP.NET](http://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Contenitori di blob Access nel codice

Per accedere a livello di programmazione BLOB di progetti di servizi cloud, è necessario aggiungere i seguenti elementi, se non sono già presenti.

1. Aggiungere le seguenti dichiarazioni di spazio dei nomi di codice nella parte superiore di qualsiasi file c# in cui si desidera accedere a livello di programmazione archiviazione Azure.

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. È possibile ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice riportato di seguito per ottenere il la stringa di connessione di spazio di archiviazione e informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. È possibile ottenere un oggetto **CloudBlobClient** per fare riferimento a un contenitore esistente nell'account di archiviazione.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. È possibile ottenere un oggetto **CloudBlobContainer** per fare riferimento a un contenitore di blob specifico.

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Utilizzare tutto il codice riportato nella procedura precedente che precede il codice riportato nelle sezioni seguenti.

## <a name="create-a-container-in-code"></a>Creare un contenitore nel codice

> [AZURE.NOTE] Alcune API che eseguono chiamate fuori allo spazio di archiviazione di Azure in ASP.NET sono asincrone. Per ulteriori informazioni, vedere [asincrono programmazione con asincrono e Await](http://msdn.microsoft.com/library/hh191443.aspx) . Il codice nell'esempio seguente presuppone che si siano utilizzando i metodi di programmazione asincrono.

Per creare un contenitore nell'account di archiviazione, è sufficiente è aggiungere una chiamata a **CreateIfNotExistsAsync** come nel codice seguente:

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


Per rendere disponibile per tutti i file all'interno del contenitore, è possibile impostare il contenitore sia pubblico tramite il codice riportato di seguito.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


Tutti gli utenti su Internet può vedere BLOB in un contenitore di pubblico, ma è possibile modificare o eliminare solo se si dispone della chiave di accesso appropriato.

## <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Spazio di archiviazione Azure supporta blocco BLOB e BLOB di pagine. Nella maggior parte dei casi, blob blocco è il tipo consigliato da utilizzare.

Per caricare un file in un blob di blocco, ottenere un riferimento contenitore e usarlo per ottenere un riferimento di blob di blocco. Dopo avere inserito un riferimento blob, è possibile caricare qualsiasi flusso di dati tramite il metodo **UploadFromStream** . Questa operazione crea il blob se non si è stati creati in precedenza o sovrascrive se esiste. Nell'esempio seguente viene illustrato come caricare un blob in un contenitore e si presuppone che il contenitore è già stato creato.

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, ottenere innanzitutto un riferimento contenitore. È quindi possibile utilizzare il metodo del contenitore **ListBlobs** per recuperare il BLOB e/o directory all'interno di essa. Per accedere a vasta gamma di metodi e proprietà per un restituito **IListBlobItem**, è necessario eseguirne il cast a un oggetto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** . Se il tipo è sconosciuto, è possibile utilizzare una verifica del tipo per determinare quale eseguirne il cast a. Il codice seguente viene illustrato come recuperare e URI di ogni elemento nel contenitore di **foto** di output:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Come mostrato nell'esempio precedente, il concetto di directory all'interno di contenitori, il servizio blob. Si tratta in modo che è possibile organizzare i BLOB in una struttura più mi piace cartella. Ad esempio, considerare le seguenti BLOB blocco in un contenitore denominato **foto**:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando si chiama **ListBlobs** sul contenitore (come illustrato nell'esempio precedente), l'insieme restituito contiene oggetti **CloudBlobDirectory** e **CloudBlockBlob** che rappresenta la directory e BLOB contenuti al livello superiore. Ecco l'output risultante:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Facoltativamente, è possibile impostare il parametro **UseFlatBlobListing** del metodo **ListBlobs** su **true**. In questo modo ogni blob viene restituito come un **CloudBlockBlob**, indipendentemente dalla directory. Ecco la chiamata a **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

ed ecco i risultati:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Per ulteriori informazioni, vedere [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx).

## <a name="download-blobs"></a>Scaricare BLOB

Per scaricare BLOB, prima di tutto recuperare un riferimento blob e quindi il metodo **DownloadToStream** . Nell'esempio seguente viene utilizzato il metodo **DownloadToStream** per trasferire il contenuto di blob a un oggetto di flusso che è possibile mantenere quindi in un file locale.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

È anche possibile utilizzare il metodo **DownloadToStream** per scaricare il contenuto di un blob come stringa di testo.

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminare BLOB

Per eliminare un blob, chiamare il metodo **eliminare** innanzitutto ottenere un riferimento blob.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Elenco BLOB di pagine in modo asincrono

Se si voce un numero elevato di BLOB o si desidera controllare il numero di risultati restituito in un'operazione di elenco, è possibile elencare BLOB nelle pagine dei risultati. In questo esempio viene illustrato come restituire i risultati in pagine in modo asincrono, in modo che non l'esecuzione viene bloccata durante l'attesa per restituire un elevato numero di risultati.

In questo esempio mostra un blob flat voce, ma è anche possibile eseguire un elenco gerarchico impostando il parametro **useFlatBlobListing** del metodo **ListBlobsSegmentedAsync** su **false**.

Poiché il metodo di esempio chiama un metodo asincrono, deve essere preceduto con la parola chiave **asincrono** e deve restituire un oggetto di **attività** . La parola chiave await specificata per il metodo **ListBlobsSegmentedAsync** sospende l'esecuzione del metodo esempio fino al completamento dell'attività di elenco.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
