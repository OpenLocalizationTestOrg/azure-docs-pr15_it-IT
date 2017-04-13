<properties
    pageTitle="Guida introduttiva a blob lo spazio di archiviazione e Visual Studio connessi servizi (ASP.NET 5) | Microsoft Azure"
    description="Come iniziare a utilizzare archiviazione Blob Azure in un progetto di Visual Studio ASP.NET 5 dopo aver creato un account di archiviazione utilizzando Visual Studio connessi servizi"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Guida introduttiva a Blob Azure lo spazio di archiviazione e Visual Studio connessi servizi (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>Panoramica

In questo articolo viene descritto come iniziare a utilizzare archiviazione Blob Azure in Visual Studio dopo aver creato o si desidera fare riferimento a un account di archiviazione Azure in un progetto ASP.NET 5 utilizzando la finestra di dialogo di Visual Studio aggiungere servizi connessi.

Archiviazione Blob Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati che accessibili da qualsiasi punto nel mondo tramite HTTP o HTTPS. Un blob singolo può essere di qualsiasi dimensione. BLOB possono essere elementi quali immagini, file audio e video, dati non elaborati e file di documento. In questo articolo viene descritto come iniziare a utilizzare archiviazione blob dopo aver creato un account di archiviazione Azure utilizzando la finestra di dialogo di Visual Studio **Aggiungere servizi connessi** in un progetto ASP.NET 5.

Come file live nelle cartelle, BLOB di spazio di archiviazione live nei contenitori. Dopo aver creato un file di archiviazione, verrà creato uno o più contenitori nello spazio di archiviazione. In archiviazione denominata "Raccoglitore", ad esempio, è possibile creare contenitori nello spazio di memorizzazione denominata "immagini" per archiviare le immagini e un'altra denominata "audio" per memorizzare file audio. Dopo aver creato i contenitori, è possibile caricare file blob singoli ad essi. Per ulteriori informazioni su come modificare a livello di programmazione BLOB, vedere [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md) .

##<a name="access-blob-containers-in-code"></a>Contenitori di blob Access nel codice

Per accedere a livello di programmazione BLOB nei progetti ASP.NET 5, è necessario aggiungere i seguenti elementi, se non sono già presenti.

1. Aggiungere le seguenti dichiarazioni di spazio dei nomi di codice nella parte superiore di qualsiasi file c# in cui si desidera accedere a livello di archiviazione Azure.

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. È possibile ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice riportato di seguito per ottenere il la stringa di connessione di spazio di archiviazione e informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Nota:** Utilizzare tutto il codice sopra davanti al codice nelle sezioni seguenti.


3. Utilizzare un oggetto **CloudBlobClient** per ottenere un riferimento **CloudBlobContainer** a un contenitore esistente nell'account di archiviazione.

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>Creare un contenitore nel codice

È anche possibile utilizzare **CloudBlobClient** per creare un contenitore nell'account di archiviazione. È sufficiente consiste nell'aggiungere una chiamata a **CreateIfNotExistsAsync** come nel codice seguente:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**Nota:** API che eseguono chiamate allo spazio di archiviazione Azure ASP.NET 5 sono asincrone. Per ulteriori informazioni, vedere [asincrono programmazione con asincrono e Await](http://msdn.microsoft.com/library/hh191443.aspx) . Il codice seguente presuppone vengono utilizzati i metodi di programmazione asincrono.

Per rendere disponibile per tutti i file all'interno del contenitore, è possibile impostare il contenitore sia pubblico tramite il codice riportato di seguito.

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>Caricare un blob in un contenitore

Per caricare un file di archivio blob in un contenitore, ottenere un riferimento contenitore e usarlo per ottenere un riferimento blob. Dopo aver ottenuto un riferimento blob, è possibile caricare qualsiasi flusso di dati tramite il metodo **UploadFromStreamAsync** . Questa operazione crea il blob se non è già presente, o sovrascrive se esiste. Nell'esempio seguente viene illustrato come caricare un blob in un contenitore e si presuppone che il contenitore è già stato creato.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore
Per elencare i BLOB in un contenitore, ottenere innanzitutto un riferimento contenitore. Chiamare il metodo del contenitore **ListBlobsSegmentedAsync** per recuperare il BLOB e/o directory all'interno di essa. Per accedere a vasta gamma di metodi e proprietà per un restituito **IListBlobItem**, è necessario eseguirne il cast a un oggetto **CloudBlockBlob**, **CloudPageBlob**o **CloudBlobDirectory** . Se non si conosce il tipo di blob, è possibile utilizzare una verifica del tipo per determinare quale eseguirne il cast a. Il codice seguente viene illustrato come recuperare e generare l'output URI di ogni elemento in un contenitore.

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

Esistono altri modi per visualizzare un elenco del contenuto di un contenitore di blob. Per ulteriori informazioni, vedere [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) .

##<a name="download-a-blob"></a>Scaricare un blob
Per scaricare un blob, ottenere innanzitutto un riferimento a quello e quindi il metodo **DownloadToStreamAsync** . Nell'esempio seguente viene utilizzato il metodo **DownloadToStreamAsync** per trasferire il contenuto di blob a un oggetto di flusso che è possibile salvarla come file locale.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

Ci sono altri modi per salvare BLOB come file. Per ulteriori informazioni, vedere [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md#download-blobs) .

##<a name="delete-a-blob"></a>Eliminare un blob
Per eliminare un blob, ottenere innanzitutto un riferimento a quello e quindi chiamare il metodo **DeleteAsync** .

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
