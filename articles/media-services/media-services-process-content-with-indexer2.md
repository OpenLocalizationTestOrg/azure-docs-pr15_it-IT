<properties
    pageTitle="L'indicizzazione di file multimediali con l'anteprima dell'indicizzatore 2 Media Azure | Microsoft Azure"
    description="Azure Media indicizzatore consente di rendere il contenuto dei file multimediali che supportano le ricerche e per generare una trascrizione full-text per sottotitoli codificati e parole chiave. In questo argomento viene illustrato come utilizzare l'anteprima 2 indicizzatore Media."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>


# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>L'indicizzazione di file multimediali con l'anteprima dell'indicizzatore 2 Media Azure

##<a name="overview"></a>Panoramica

Processore di elementi multimediali di **Azure Media indicizzatore 2 anteprima** (programma minimo) consente di verificare che supportano le ricerche di contenuti e i file multimediali, come generare sottotitoli tracce didascalie. Rispetto alla versione precedente di [Azure Media indicizzatore](media-services-index-content.md), **Azure Media indicizzatore 2 anteprima** esegue l'indicizzazione più veloce e offre più ampio al supporto delle lingue. Lingue supportate includono inglese, spagnolo, francese, tedesco, italiano, cinese, portoghese e arabo.

**Anteprima di Azure Media indicizzatore 2** Pannello di gestione è attualmente in anteprima.

In questo argomento viene illustrato come creare i processi di indicizzazione con **Azure Media indicizzatore 2 Preview**.

>[AZURE.NOTE]Le considerazioni seguenti:
>
>Indicizzatore 2 non è supportato in Cina, Azure e Azure per enti pubblici.
>
>L'anteprima è limitata ai ~ 10 minuti di elaborazione, ma è disponibile per tutti i clienti.
>
>Durante l'indicizzazione del contenuto, assicurarsi di usare i file multimediali che hanno molto chiara sintesi e riconoscimento vocale (senza musica di sottofondo, rumore, effetti o fruscio microfono). Alcuni esempi di contenuto appropriato sono: registrato riunioni, lezioni o presentazioni. Il contenuto seguente potrebbe non essere utilizzato per indicizzare: filmati, programmi TV, tutti gli elementi presenti con audio misto e gli effetti audio, in modo non corretto registrato contenuto con rumore (fruscio).


In questo argomento vengono fornite informazioni dettagliate su **Azure Media indicizzatore 2 Preview** e viene illustrato come utilizzare con Media Services SDK per .NET

##<a name="input-and-output-files"></a>File di input e di output

###<a name="input-files"></a>File di input

File audio o video

###<a name="output-files"></a>File di output

Un processo di indicizzazione può generare sottotitoli codificati file nei formati seguenti:  

- **Sami di Inari**
- **TTML**
- **WebVTT**

Chiusa didascalia (CC) file nei formati seguenti possono essere utilizzati per rendere accessibile agli utenti con disability udito file audio e video.

##<a name="task-configuration-preset"></a>Configurazione delle attività (predefinito)

Quando si crea un'attività di indicizzazione con **Azure Media indicizzatore 2 Preview**, è necessario specificare una configurazione preimpostata.

JSON seguenti imposta parametri disponibili.

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>Lingue supportate  

Azure Media indicizzatore 2 Preview supporta sintesi e riconoscimento vocale di testo per le lingue seguenti (quando si specifica il nome della lingua nella configurazione dell'attività, il codice di 4 caratteri tra parentesi quadre come illustrato di seguito):

- Inglese [EnUs]
- Spagnolo [EsEs]
- Cinese [ZhCn]
- Francese [FrFr]
- Tedesco [DeDe]
- Italiano [ItIt]
- Portoghese [PtBr]
- Arabo (Egitto) [ArEg]


## <a name="sample-code"></a>Codice di esempio

Il programma che segue mostra come:

1. Creazione di un bene e caricare un file di supporto nel asset.
1. Crea un processo con un'attività di indicizzazione in base a un file di configurazione che contiene il predefinito json seguenti.
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. Scaricare i file di output. 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                static void Main(string[] args)
                {
        
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
        
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
        
                    // Launch the job.
                    job.Submit();
        
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        
                    progressJobTask.Wait();
        
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
        
                    return job.OutputMediaAssets[0];
                }
        
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
        
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
        
                    return asset;
                }
        
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
        
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
        
                    return processor;
                }
        
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
        
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
            }
        }


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>Collegamenti correlati

[Panoramica di Analitica dei servizi Media Azure](media-services-analytics-overview.md)

[Demo di elementi multimediali Analitica Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)