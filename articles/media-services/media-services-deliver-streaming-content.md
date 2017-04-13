<properties 
    pageTitle="Pubblicare contenuto servizi multimediali di Windows Azure attraverso .NET" 
    description="Informazioni su come creare un indicatore di posizione utilizzato per generare un URL di trasmissione. Esempi di codice scritto in c# e utilizzano Media Services SDK per .NET." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="publish-azure-media-services-content-using-net"></a>Pubblicare contenuto servizi multimediali di Windows Azure attraverso .NET
 
> [AZURE.SELECTOR]
- [RESTO](media-services-rest-deliver-streaming-content.md)
- [.NET](media-services-deliver-streaming-content.md)
- [Portale](media-services-portal-publish.md)

##<a name="overview"></a>Panoramica

È possibile trasmettere una velocità adattata MP4 impostare creando un indicatore di posizione flusso su richiesta e la creazione di un URL di trasmissione. [Codifica di un bene](media-services-encode-asset.md) viene spiegato come codificare in una velocità adattata che impostare MP4. 

>[AZURE.NOTE]Se il contenuto è crittografato, configurare i criteri di recapito bene, come descritto in [questo](media-services-dotnet-configure-asset-delivery-policy.md) argomento, prima di creare un indicatore di posizione. 

Utilizzare un flusso locator su richiesta per creare URL che puntano a file MP4 che possono essere scaricati gradualmente.  

In questo argomento viene illustrato come creare un flusso locator per pubblicare le risorse e creare un passaggio uniforme, MPEG trattino e HLS streaming URL su richiesta. Viene inoltre attivo per creare il download graduale URL. 
     
##<a name="create-an-ondemand-streaming-locator"></a>Creare un flusso locator su richiesta

Per creare su richiesta streaming locator e di ottenere l'URL è necessario eseguire le operazioni seguenti:

   1. Se il contenuto è crittografato, definire un criterio di accesso.
   2. Creare un flusso locator su richiesta.
   3. Se si prevede di flusso, è possibile ottenere il file di manifesto flusso (ISM) del bene. 
        
    Se si prevede di scaricare gradualmente, è possibile ottenere i nomi dei file MP4 del bene.  
   4. Creare gli URL per il file manifesto o file MP4. 
   

###<a name="use-media-services-net-sdk"></a>Usare i servizi di supporto SDK .NET 

Creare gli URL di trasmissione 

    private static void BuildStreamingURLs(IAsset asset)
    {
    
        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();
        
        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }

Il codice di output:
    
    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
    

>[AZURE.NOTE]È anche possibile trasmettere il contenuto in una connessione SSL. A tale scopo, assicurarsi che l'URL di trasmissione iniziare con HTTPS. 

Creare gli URL di download graduale 

    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);
    
        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));
    
        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();
    
        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
                
        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }

Il codice di output:
    
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
    
    . . . 

###<a name="use-media-services-net-sdk-extensions"></a>Utilizzare le estensioni di file multimediali servizi .NET SDK

Il codice seguente chiama metodi di estensioni .NET SDK che creare un indicatore di posizione e generano Smooth Streaming, HLS e MPEG-tratto URL per il flusso adattata.

    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));
    
    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();
    
    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche

[Scaricare risorse](media-services-deliver-asset-download.md)
[configurare i criteri di recapito bene](media-services-dotnet-configure-asset-delivery-policy.md)
