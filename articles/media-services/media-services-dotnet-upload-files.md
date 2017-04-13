<properties 
    pageTitle="Caricare file in un account di servizi multimediali utilizzando .NET | Microsoft Azure" 
    description="Informazioni su come ottenere contenuti multimediali in servizi multimediali mediante la creazione e caricamento di risorse." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>Caricare file in un account di servizi multimediali utilizzando .NET

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTO](media-services-rest-upload-files.md)
 - [Portale](media-services-portal-upload-files.md)

In servizi multimediali, è possibile caricare (o acquisizione) i file digitali in una risorsa. Entità **Asset** possono contenere video, audio, immagini, raccolte anteprime, testo tracce e sottotitoli codificati file (e i metadati relativi a questi file.)  Una volta i file vengono caricati, il contenuto è archiviato in modo sicuro nel cloud per l'ulteriore elaborazione e trasmissione.

I file del bene denominati **File di risorse**. L'istanza **AssetFile** e sul file multimediale effettivo sono due oggetti distinti. L'istanza di AssetFile contenente metadati relativi a file multimediale, mentre il file multimediale contiene il contenuto multimediale effettivo.

>[AZURE.NOTE]Considerazioni quando si sceglie il nome di un file di risorse:
>
>- Servizi multimediali di utilizzerà il valore della proprietà IAssetFile.Name durante la creazione di URL per il contenuto di flusso (ad esempio http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Per questo motivo, non è consentita la codifica percentuale. Il valore della proprietà **Name** non può contenere i seguenti [caratteri percentuale codifica-riservati](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Inoltre, può essere presente solo uno '. "per l'estensione del file.
>
>- La lunghezza del nome non deve essere maggiore di 260 caratteri.

Quando si creano risorse, è possibile specificare le seguenti opzioni di crittografia. 

- **Nessuno** - crittografia non viene utilizzata. Si tratta del valore predefinito. Si noti che, quando si utilizza questa opzione il contenuto non protetta durante il transito o inattivi in archiviazione.
Se si prevede di eseguire un MP4 tramite download graduale, usare questa opzione. 
- **CommonEncryption** - usare questa opzione se si sta caricando contenuto già crittografato e protetto con PlayReady DRM (ad esempio Smooth Streaming protetto con PlayReady DRM) o la crittografia comuni.
- **EnvelopeEncrypted** : usare questa opzione se si sta caricando HLS crittografate con AES. Si noti che i file di essere stati codificati e crittografati da trasformare Manager.
- **StorageEncrypted** - crittografa il contenuto Cancella localmente utilizzando la crittografia AES-256 bit e quindi caricamento per lo spazio di archiviazione di Azure in cui è archiviato crittografato inattivi. Risorse è protetti con crittografia di archiviazione sono automaticamente in formato non crittografati e inseriti in un file system crittografato prima di codifica e facoltativamente nuovamente crittografati prima di caricare nuovamente come una nuova risorsa di output. Il caso di utilizzo principale per la crittografia di spazio di archiviazione è quando si desidera proteggere i file di supporto di input di alta qualità con crittografia inattivi su disco.

    Servizi multimediali di consente la crittografia su disco lo spazio di archiviazione per le risorse non sopra transito come Digital Rights Manager (DRM).

    Se la risorsa è lo spazio di archiviazione crittografato, è necessario configurare i criteri di recapito bene. Per ulteriori informazioni vedere [configurazione bene recapito criteri](media-services-dotnet-configure-asset-delivery-policy.md).

Se si specifica per le risorse siano crittografati con un'opzione **CommonEncrypted** o un'opzione **EnvelopeEncypted** , sarà necessario associare un **ContentKey**le risorse. Per ulteriori informazioni, vedere [come creare un ContentKey](media-services-dotnet-create-contentkey.md). 

Se si specifica per le risorse siano crittografati con un'opzione di **StorageEncrypted** , Media Services SDK per .NET creerà un **StorateEncrypted** **ContentKey** per le risorse.


In questo argomento viene illustrato come utilizzare Media Services .NET SDK, nonché le estensioni di file multimediali servizi .NET SDK per caricare i file in una risorsa di servizi di supporto.

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Caricare un singolo file con Media Services .NET SDK 

Il codice di esempio seguente viene utilizzata .NET SDK per eseguire le operazioni seguenti: 

- Crea una risorsa vuota.
- Crea un'istanza di AssetFile che si desidera associare all'attività.
- Crea un'istanza di AccessPolicy che consente di definire le autorizzazioni e la durata di accesso per la risorsa.
- Crea un'istanza di Locator che fornisce l'accesso per la risorsa.
- Consente di caricare un file multimediale in servizi di supporto. 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>Caricare più file con Media Services .NET SDK 

Il codice seguente viene illustrato come creare una risorsa e caricare più file.

Il codice esegue le operazioni seguenti:
    
-   Crea una risorsa vuota utilizzando il metodo CreateEmptyAsset definito nel passaggio precedente.
    
-   Crea un'istanza di **AccessPolicy** che consente di definire le autorizzazioni e la durata di accesso per la risorsa.
    
-   Crea un'istanza di **Locator** che fornisce l'accesso per la risorsa.
    
-   Crea un'istanza di **BlobTransferClient** . Questo tipo rappresenta un client che opera su BLOB Azure. In questo esempio abbiamo utilizzare il client per controllare lo stato di caricamento. 
    
-   Enumerati i file nella directory specificata e crea un'istanza di **AssetFile** per ogni file.
    
-   Carica i file in servizi multimediali di utilizzando il metodo **UploadAsync** . 
    
>[AZURE.NOTE] Utilizzare il metodo UploadAsync per garantire che non devono bloccare le chiamate e i file vengono caricati in parallelo.
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



Durante il caricamento di un numero elevato di risorse, tenere presente quanto segue.

- Creare un nuovo oggetto **CloudMediaContext** per thread. La classe **CloudMediaContext** non è affidabili.
 
- Aumentare NumberOfConcurrentTransfers rispetto al valore predefinito di 2 su un valore superiore ad esempio 5. Impostazione di questa proprietà viene applicata a tutte le istanze di **CloudMediaContext**. 
 
- Mantenere ParallelTransferThreadCount il valore predefinito di 10.
 
##<a id="ingest_in_bulk"></a>Il caricamento di risorse in blocco tramite Media Services .NET SDK 

Caricamento di file di risorse di grandi dimensioni può essere una bottiglia durante la creazione di risorse. Il caricamento di risorse in grandi quantitativi o "Il caricamento in blocco", comporta la creazione di risorse del processo di caricamento di separazione. Per utilizzare un blocco il caricamento approccio, creare un manifesto (IngestManifest) che descrive il bene e i file associati. Per caricare i file associati al contenitore di blob del manifesto quindi usare il metodo di caricamento di propria scelta. Servizi multimediali di Microsoft Azure consente di controllare il contenitore blob associato il manifesto. Una volta al contenitore di blob viene caricato un file, servizi multimediali di Microsoft Azure completare la creazione di risorse in base alla configurazione di un bene nel manifesto (IngestManifestAsset).


Per creare una nuova chiamata IngestManifest il metodo Create esposto dall'insieme di IngestManifests il CloudMediaContext. Questo metodo si crea un nuovo IngestManifest con il nome del manifesto specificati.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Creare le risorse che saranno associate IngestManifest in blocco. Configurare le opzioni di crittografia desiderata sulla bene per il caricamento in blocco.

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Un IngestManifestAsset associa una risorsa in un blocco IngestManifest per il caricamento in blocco. Inoltre, associa AssetFiles che costituiranno ogni risorsa. Per creare un IngestManifestAsset, utilizzare il metodo Create nel contesto del server.

Nell'esempio seguente viene aggiunta IngestManifestAssets nuovo due che associare le due attività create in precedenza per la maggior parte acquisizione manifesto. Inoltre, ogni IngestManifestAsset associa un insieme di file che verrà caricato per ogni risorsa durante il caricamento in blocco.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
È possibile utilizzare qualsiasi applicazione client ad alta velocità in grado di caricare i file di risorse al contenitore di spazio di archiviazione blob URI fornito dalla proprietà **IIngestManifest.BlobStorageUriForUpload** della IngestManifest. Un servizio di upload degni di nota ad alta velocità è [Aspera su richiesta per applicazione Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). È inoltre possibile scrivere codice per caricare i file di risorse, come illustrato nell'esempio seguente.
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

Il codice per caricare i file di risorse per l'esempio utilizzati in questo argomento viene illustrato nell'esempio seguente.
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

È possibile determinare l'avanzamento per tutte le risorse associate a un **IngestManifest** polling proprietà statistiche del **IngestManifest**il caricamento di massa. Per aggiornare le informazioni sull'avanzamento, è necessario utilizzare un nuovo **CloudMediaContext** ogni volta che si sondaggio proprietà statistiche.

Nell'esempio seguente viene polling un IngestManifest dal relativo **Id**.
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>Caricare i file usando le estensioni SDK .NET 

Nell'esempio seguente viene illustrato come caricare un singolo file mediante le estensioni SDK .NET. In questo caso in cui viene utilizzato il metodo **CreateFromFile** , ma la versione asincrona è anche disponibile (**CreateFromFileAsync**). Il metodo **CreateFromFile** consente di specificare il nome del file, opzioni di crittografia e un callback per segnalare l'avanzamento di caricamento del file.


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

Nell'esempio seguente chiama funzione UploadFile e specifica la crittografia di spazio di archiviazione come opzione di creazione di risorse.  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>Passaggio successivo

Ora che è stato caricato un bene per servizi multimediali, passare all'argomento [come ottenere un processore di elementi multimediali][] .

[Come ottenere un processore di elementi multimediali]: media-services-get-media-processor.md
 
