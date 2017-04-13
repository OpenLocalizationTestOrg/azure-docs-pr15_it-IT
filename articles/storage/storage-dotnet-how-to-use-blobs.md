<properties
    pageTitle="Guida introduttiva a archiviazione Blob Azure (archiviazione degli oggetti) utilizzando .NET | Microsoft Azure"
    description="Archiviare dati non strutturati nel cloud con lo spazio di archiviazione Blob Azure (spazio di archiviazione oggetto)."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>Guida introduttiva a archiviazione Blob Azure con .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica

Archiviazione Blob Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archiviazione BLOB consentono di memorizzare qualsiasi tipo di testo o dati binari, ad esempio un documento, un file multimediale o un programma di installazione applicazioni. Archiviazione BLOB viene definita anche lo spazio di archiviazione di oggetto.

### <a name="about-this-tutorial"></a>Su questa esercitazione

In questa esercitazione viene illustrato come scrivere codice .NET per alcuni scenari comuni di utilizzo dell'archiviazione Blob Azure. Scenari includono durante il caricamento, l'elenco, il download e l'eliminazione BLOB.

**Durata stimata per completare:** 45 minuti

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Raccolta di Client di archiviazione Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure Configuration Manager per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Un [account di archiviazione Azure](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Ulteriori esempi

Per ulteriori esempi sull'utilizzo di archiviazione Blob, vedere [Introduzione a archiviazione Blob Azure in .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). È possibile scaricare l'applicazione di esempio ed eseguirlo o esplorare il codice in GitHub.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Aggiungere le dichiarazioni di nomi

Aggiungere quanto segue `using` le istruzioni nella parte superiore di `program.cs` file:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Creare il client di servizi Blob

La classe **CloudBlobClient** consente di recuperare contenitori ed BLOB archiviati in archiviazione Blob. Ecco un modo per creare il client di servizi:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

A questo punto si è pronti scrivere codice che legge i dati da e scrive dati in archiviazione Blob.

## <a name="create-a-container"></a>Creare un contenitore

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

In questo esempio viene illustrato come creare un contenitore se non esiste già:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Per impostazione predefinita, il nuovo contenitore è privato, il che significa che è necessario specificare il tasto di scelta di spazio di archiviazione per scaricare BLOB da questo contenitore. Se si desidera rendere disponibili i file all'interno del contenitore a tutti gli utenti, è possibile impostare il contenitore di tipo public utilizzando il codice seguente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Tutti gli utenti su Internet può vedere BLOB in un contenitore di pubblico, ma è possibile modificare o eliminarli solo se si dispone di una firma di accesso condiviso o il tasto di scelta account appropriato.

## <a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Archiviazione Blob Azure supporta BLOB di blocco e BLOB di pagine.  Nella maggior parte dei casi, blob blocco è il tipo consigliato da utilizzare.

Per caricare un file in un blob di blocco, ottenere un riferimento contenitore e usarlo per ottenere un riferimento di blob di blocco. Dopo avere inserito un riferimento di blob, è possibile caricare qualsiasi flusso di dati tramite il metodo **UploadFromStream** . Se non si è in precedenza esiste, o salvarlo in caso affermativo, questa operazione creerà il blob.

Nell'esempio seguente viene illustrato come caricare un blob in un contenitore e si presuppone che il contenitore è già stato creato.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, ottenere innanzitutto un riferimento contenitore. È quindi possibile utilizzare il metodo del contenitore **ListBlobs** per recuperare il BLOB e/o directory all'interno di essa. Per accedere a vasta gamma di metodi e proprietà per un restituito **IListBlobItem**, è necessario eseguirne il cast a un oggetto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** .  Se il tipo è sconosciuto, è possibile utilizzare una verifica del tipo per determinare quale eseguirne il cast a.  Il codice seguente viene illustrato come recuperare e URI di ogni elemento di output di `photos` contenitore:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

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

Come mostrato in precedenza, è possibile assegnare un nome BLOB con informazioni sul percorso nei nomi. In questo modo si crea una struttura di directory virtuale che è possibile organizzare e scorrere come si farebbe un tradizionale file system. Si noti che la struttura di directory è virtuale, solo le risorse disponibili in archiviazione Blob sono contenitori ed BLOB. Tuttavia, la raccolta di client lo spazio di archiviazione offre un oggetto **CloudBlobDirectory** per fare riferimento a una directory virtuale e semplificare il processo di utilizzo di BLOB organizzati in questo modo.

Ad esempio, considerare le seguenti BLOB di blocco in un contenitore denominato `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando si chiama **ListBlobs** sul contenitore 'foto' (come illustrato nell'esempio precedente), viene restituito un elenco gerarchico. Presentazione contiene oggetti **CloudBlobDirectory** e **CloudBlockBlob** , che rappresenta la directory e BLOB nel contenitore, rispettivamente. L'output risultante è simile alla:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Facoltativamente, è possibile impostare il parametro **UseFlatBlobListing** del metodo **ListBlobs** su **true**. In questo caso, ogni blob nel contenitore viene restituito come oggetto **CloudBlockBlob** . La chiamata a **ListBlobs** per restituire un semplice elenco è simile alla seguente:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e i risultati aspetto simile al seguente:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>Scaricare BLOB

Per scaricare BLOB, prima di tutto recuperare un riferimento blob e quindi il metodo **DownloadToStream** . Nell'esempio seguente viene utilizzato il metodo **DownloadToStream** per trasferire il contenuto di blob a un oggetto di flusso che è possibile mantenere quindi in un file locale.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

È anche possibile utilizzare il metodo **DownloadToStream** per scaricare il contenuto di un blob come stringa di testo.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminare BLOB

Per eliminare un blob, ottenere innanzitutto un riferimento di blob e quindi chiamare il metodo **Delete** su di esso.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Elenco BLOB di pagine in modo asincrono

Se si voce un numero elevato di BLOB o si desidera controllare il numero di risultati restituito in un'operazione di elenco, è possibile elencare BLOB nelle pagine dei risultati. In questo esempio viene illustrato come restituire i risultati in pagine in modo asincrono, in modo che non l'esecuzione viene bloccata in attesa per restituire un elevato numero di risultati.

In questo esempio mostra un blob piatto voce, ma è anche possibile eseguire un elenco gerarchico mediante l'impostazione di `useFlatBlobListing` parametro del metodo **ListBlobsSegmentedAsync** a `false`.

Poiché il metodo di esempio chiama un metodo asincrono, deve essere preceduto il `async` parola chiave e deve restituire un oggetto di **attività** . La parola chiave await specificata per il metodo **ListBlobsSegmentedAsync** sospende l'esecuzione del metodo esempio fino al completamento dell'attività di elenco.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
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

## <a name="writing-to-an-append-blob"></a>Scrivere un blob di Accodamento

Blob accodamento è un nuovo tipo di blob, introdotti con versione 5. x della libreria di client di archiviazione Azure per .NET. Blob Accodamento ottimizzato per le operazioni di accodamento, ad esempio la registrazione. Ad esempio un blob blocco blob accodamento è costituito da blocchi, ma quando si aggiunge un nuovo blocco a un blob di accodamento, è sempre aggiunti alla fine del blob. Non è possibile aggiornare o eliminare un blocco esistente in un blob di Accodamento. Il blocco di ID per un blob di accodamento non viene esposto come lo sono per un blob di blocco.

Ogni blocco in un blob accodamento può essere una dimensione diversa, fino a un massimo di 4 MB, e un blob di accodamento può includere un massimo di 50.000 blocchi. La dimensione massima di un blob di accodamento è leggermente più 195 GB (4 MB X 50.000 blocchi).

Nell'esempio seguente viene creato un nuovo blob di accodamento e aggiunge alcuni dati, la simulazione di un'operazione semplice registrazione.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Per ulteriori informazioni sulle differenze tra i tre tipi di BLOB, vedere [Understanding blocco BLOB, BLOB di pagine e accoda BLOB](https://msdn.microsoft.com/library/azure/ee691964.aspx) .

## <a name="managing-security-for-blobs"></a>Gestione della sicurezza per i BLOB

Per impostazione predefinita, lo spazio di archiviazione di Azure i dati rimangono sicura, limitare l'accesso per il proprietario dell'account, che è in possesso dei tasti di scelta account. Quando è necessario condividere dati blob nell'account di archiviazione, è importante per farlo senza compromettere la sicurezza dei tasti di scelta account. Inoltre, è possibile crittografare dati blob per assicurarsi che sia sicuro successo in rete e lo spazio di archiviazione di Azure.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Controllo dell'accesso ai dati blob

Per impostazione predefinita, i dati nel proprio account di archiviazione blob sono accessibili solo al proprietario dell'account di archiviazione. Autenticazione delle richieste di archivio Blob richiede il tasto di scelta account per impostazione predefinita. Tuttavia, si desidera rendere disponibili alcuni dati blob ad altri utenti. Sono disponibili due opzioni:

- **Accesso anonimo:** Per rendere un contenitore o il relativo BLOB pubblicamente disponibili per l'accesso anonimo. Per ulteriori informazioni, vedere [gestire anonimo accesso in lettura contenitori e BLOB](storage-manage-access-to-resources.md) .
- **Shared accesso firme:** È possibile fornire i clienti con una firma di accesso condiviso (SA), che fornisce l'accesso delegato a una risorsa nel proprio account di archiviazione, con le autorizzazioni specificate e in un intervallo di tempo specificato. Per ulteriori informazioni, vedere [Uso firme di Access condiviso (SA)](storage-dotnet-shared-access-signature-part-1.md) .

### <a name="encrypting-blob-data"></a>La crittografia dei dati blob

Spazio di archiviazione Azure supporta la crittografia dei dati blob nel client e sul server:

- **Crittografia lato client:** La raccolta di Client lo spazio di archiviazione per .NET supporta la crittografia dei dati nelle applicazioni client prima di caricare allo spazio di archiviazione di Azure e decrittografare i dati durante il download al client. La raccolta supporta anche l'integrazione con Azure chiave archivio per la gestione delle chiavi account lo spazio di archiviazione. Per ulteriori informazioni, vedere [La crittografia lato Client con .NET per lo spazio di archiviazione di Microsoft Azure](storage-client-side-encryption.md) . Vedere anche [Esercitazione: crittografare e decrittografare BLOB di archiviazione di Microsoft Azure utilizzando Azure chiave archivio](storage-encrypt-decrypt-blobs-key-vault.md).
- **La crittografia sul lato server**: archiviazione Azure ora supporta la crittografia sul lato server. Vedere [la crittografia del servizio di archiviazione Azure per dati inattivi (Preview)](storage-service-encryption.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di spazio di archiviazione Blob, seguire questi collegamenti per altre informazioni.

### <a name="microsoft-azure-storage-explorer"></a>Esplora archivi Microsoft Azure
- [Microsoft Azure archiviazione Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) app è gratuita, autonoma da Microsoft che consente di utilizzare visivamente i dati di Azure lo spazio di archiviazione in Windows, OS X e Linux.

### <a name="blob-storage-samples"></a>Esempi di spazio di archiviazione BLOB

- [Guida introduttiva a archiviazione Blob Azure in .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Riferimento di spazio di archiviazione BLOB

- [Raccolta di Client lo spazio di archiviazione per riferimento .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [Guida di riferimento all'API REST](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>Guide concettuale

- [Trasferire i dati con l'utilità di AzCopy](storage-use-azcopy.md)
- [Guida introduttiva a spazio di archiviazione File per .NET](storage-dotnet-how-to-use-files.md)
- [Utilizzo di archiviazione blob Azure con SDK WebJobs](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
