<properties 
    pageTitle="Creazione di filtri con i servizi di supporto Azure SDK .NET" 
    description="In questo argomento viene descritto come creare filtri in modo che il client può utilizzare a sezioni specifiche del flusso di un flusso. Servizi multimediali di crea manifesti dinamici per ottenere questo flusso selettiva." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="07/18/2016"
    ms.author="juliako;cenkdin"/>


#<a name="creating-filters-with-azure-media-services-net-sdk"></a>Creazione di filtri con i servizi di supporto Azure SDK .NET

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [RESTO](media-services-rest-dynamic-manifest.md)

A partire dalla versione 2.11, servizi multimediali consente di definire filtri per le risorse. Questi filtri vengono regole lato server per consentono agli utenti di scegliere di eseguire operazioni come: riproduzione solo una sezione di un video (anziché la riproduzione del video intero), oppure specificare solo un sottoinsieme di rendering audio e video in grado di gestire dispositivi del cliente (invece di tutti i rendering associate bene). Il filtraggio dei beni viene ottenuto tramite s **Manifesto dinamico**creato richiesta del cliente per eseguire il flusso di un video in base a filtri specificati.

Per informazioni più dettagliate relativi ai filtri e manifesto dinamico, vedere [Panoramica manifesti dinamico](media-services-dynamic-manifest-overview.md).

In questo argomento viene illustrato come utilizzare Media Services .NET SDK per creare, aggiornare ed eliminare i filtri. 


Nota che se si aggiorna un filtro, potrebbe richiedere fino a 2 minuti per lo streaming endpoint per aggiornare le regole. Se il contenuto è stato served usando questo filtro e memorizzati nella cache di proxy e CDN cache, aggiornare il filtro può causare errori di Windows Media player. È consigliabile per cancellare la cache dopo l'aggiornamento del filtro. Se questa opzione non è possibile, utilizzare un filtro diverso. 

##<a name="types-used-to-create-filters"></a>Tipi di utilizzati per creare filtri

Quando si creano filtri, vengono utilizzati i tipi seguenti: 

- **IStreamingFilter**.  Questo tipo si basa sul seguenti API REST [filtro](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**. Questo tipo è basato su seguenti API REST [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**. Questo tipo è basato su seguenti API REST [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** e **IFilterTrackPropertyCondition**. Questi tipi sono in base alle seguenti API REST [FilterTrackSelect e FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##<a name="createupdatereaddelete-global-filters"></a>Creazione o aggiornamento/lettura/eliminazione filtri globali

Il codice seguente viene illustrato come utilizzare .NET per creare, aggiornare, leggere ed eliminare i filtri di risorse.
    
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
                
    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
    
    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);
    
    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
    
    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();
    
    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


##<a name="createupdatereaddelete-asset-filters"></a>Filtri di creazione/aggiornamento/lettura/eliminazione bene

Il codice seguente viene illustrato come utilizzare .NET per creare, aggiornare, leggere ed eliminare i filtri di risorse.

    
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
    
        
    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());
    
    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);
    
    filter.Update();
    
    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);
    
    // Delete
    filterUpdated.Delete();
    



##<a name="build-streaming-urls-that-use-filters"></a>Creare gli URL che utilizzano i filtri di flusso

Per informazioni su come pubblicare e in più assicurano le risorse, vedere [L'esecuzione di contenuto Cenni preliminari sui clienti](media-services-deliver-content-overview.md).


Negli esempi seguenti viene illustrato come aggiungere filtri per l'URL di trasmissione.

**MPEG TRATTINO** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming V4 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming V3 (HLS)**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**UNITÀ 10K**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Vedere anche 

[Panoramica di manifesti dinamico](media-services-dynamic-manifest-overview.md)
 

