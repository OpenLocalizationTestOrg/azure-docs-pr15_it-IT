<properties 
    pageTitle="Scaricare i file multimediali" 
    description="Informazioni su come scaricare risorse del computer. Esempi di codice scritto in c# e utilizzano Media Services SDK per .NET." 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>

#<a name="how-to-deliver-an-asset-by-download"></a>Procedura: inviare un bene per il Download

In questo argomento vengono illustrate le opzioni per l'esecuzione di risorse multimediali caricati ai servizi di supporto. È possibile distribuire il contenuto dei servizi di supporto in numerosi scenari di applicazioni. È possibile scaricare risorse multimediali o accedervi mediante un indicatore di posizione. È possibile inviare contenuti multimediali a un'altra applicazione o a un altro provider. Per migliorare le prestazioni e scalabilità, è possibile anche distribuire contenuto tramite una rete di recapito contenuti (CDN).

In questo esempio viene illustrato come scaricare risorse multimediali dalla pagina Servizi multimediali nel computer locale. Il codice di una query processi associati all'account di servizi di supporto per l'ID di processo e si accede insieme **OutputMediaAssets** (ovvero l'insieme di uno o più risorse multimediali di output risultante dall'esecuzione di un processo). In questo esempio viene illustrato come scaricare risorse multimediali di output da un processo, ma è possibile applicare lo stesso approccio per scaricare altre risorse.

    
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 
    
        // Get a reference to the job. 
        IJob job = GetJob(jobId);
    
        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
    
        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };
    
        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;
    
            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
    
            Console.WriteLine("File download path:  " + localDownloadPath);
    
            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
    
            outputFile.DownloadProgressChanged -= DownloadProgress;
        }
    
        Task.WaitAll(downloadTasks.ToArray());
    
        return outputAsset;
    }
    
    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

   
##<a name="see-also"></a>Vedere anche 

[Distribuzione di contenuti di flusso](media-services-deliver-streaming-content.md)

