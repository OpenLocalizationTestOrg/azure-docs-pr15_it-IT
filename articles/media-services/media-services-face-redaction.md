<properties
    pageTitle="Smile redazione con analitica media Azure | Microsoft Azure"
    description="In questo argomento viene illustrato come redigi facce con analitica media Azure."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/12/2016"   
    ms.author="juliako;"/>
 
#<a name="face-redaction-with-azure-media-analytics"></a>Adattamento nominale con analitica media Azure

##<a name="overview"></a>Panoramica

**Azure Media Redactor** è un processore di elementi multimediali di [Azure Media Analitica](media-services-analytics-overview.md) (programma minimo) che offre redazione nominale scalable nel cloud. Nominale redazione consente di modificare il video per sfocatura facce di persone selezionati. È consigliabile utilizzare il servizio di redazione nominale in scenari di sicurezza ed elementi multimediali di news pubblici. Alcuni minuti di materiale che contiene più facce possono richiedere ore redigi manualmente, ma con questo servizio il processo di redazione nominale richiederà pochi semplici passaggi. Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

In questo argomento vengono fornite informazioni dettagliate sulle **Azure Media Redactor** e viene illustrato come utilizzare con Media Services SDK per .NET.

**Azure Media Redactor** Pannello di gestione è attualmente disponibile in anteprima.

## <a name="face-redaction-modes"></a>Modalità di redazione nominale

Adattamento viso funziona rilevamento facce in ogni fotogramma del video e il controllo dell'oggetto nominale entrambi avanti e indietro nel tempo, in modo che la stessa persona può sfocata da anche altri angoli. Il processo di adattamento automatico è molto complesso e non sempre produrre 100% di output desiderato, per questo motivo Analitica Media fornisce si bastano un paio di modi per modificare l'output finale.

Oltre una modalità completamente automatica è disponibile un flusso di lavoro in due passaggi che consente la selezione/deserializzare-selection di facce trovate tramite un elenco di ID. Inoltre, per rendere non autorizzato per regolazioni cornice del Pannello di gestione utilizza un file di metadati nel formato JSON. Questo flusso di lavoro viene suddivisa in modalità **Analizza** e **Redact** . È possibile combinare due modalità in un unico passaggio che esegue entrambe le attività in un processo. Questa modalità è denominata **combinato**.

###<a name="combined-mode"></a>Modalità combinate

Questo produrranno un mp4 adattamento automaticamente senza qualsiasi input manuale.

Finestra di condivisione|Nome del file|Note
---|---|---
Risorse di input|foo. bar|Video in formato WMV, MOV o MP4
Configurazione di input|Configurazione del processo predefinito|{'versione':'1.0 ', 'opzioni': {'modalità': 'combinati'}}
Risorse di output|foo_redacted.mp4|Video con sfocatura applicato

####<a name="input-example"></a>Esempio di input:

[guardare questo video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

####<a name="output-example"></a>Esempio di output:

[guardare questo video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

###<a name="analyze-mode"></a>Modalità di analisi

Il passaggio del flusso di lavoro in due passaggi **analizzare** ha un input video e genera un file JSON dei percorsi di nominale e immagini jpg di ogni faccia rilevato.

Finestra di condivisione|Nome del file|Note
---|---|----
Risorse di input|foo. bar|Video in formato WMV, MPV o MP4
Configurazione di input|Configurazione del processo predefinito|{'versione':'1.0 ', 'opzioni': {'modalità': 'analisi'}}
Risorse di output|foo_annotations.JSON|Dati di annotazione delle posizioni nominale nel formato JSON. Possono essere modificato dall'utente per modificare la Sfocatura riquadri. Vedere esempio riportato di seguito.
Risorse di output|foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg]|In formato jpg ritagliate di ogni rilevato nominale, in cui il numero indica labelId della faccia

####<a name="output-example"></a>Esempio di output:

    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… troncati


###<a name="redact-mode"></a>Redigi modalità

Il secondo passaggio del flusso di lavoro ha un numero maggiore di input che deve essere combinati in una singola risorsa.

Questo include un elenco di ID di sfocatura, del video originale e le annotazioni JSON. Questa modalità utilizza le annotazioni per applicare la sfocatura nel video di input.

L'output di analisi non include il video originale. Il video deve essere caricata nel asset input per l'attività di modalità Redact e selezionato come file principale.

Finestra di condivisione|Nome del file|Note
---|---|---
Risorse di input|foo. bar|Video in formato WMV, MPV o MP4. Stesso video come nel passaggio 1.
Risorse di input|foo_annotations.JSON|file di metadati annotazioni dalla fase 1, con modifiche facoltative.
Risorse di input|foo_IDList.txt (facoltativo)|Elenco di nominale ID a redigi separati da facoltativa nuova riga. Lasciare vuoto questo sfocatura tutte le facce.
Configurazione di input|Configurazione del processo predefinito|{'versione':'1.0 ', 'opzioni': {'modalità': 'redigi'}}
Risorse di output|foo_redacted.mp4|Video con sfocatura applicati in base a annotazioni

####<a name="example-output"></a>Output di esempio

Questo è l'output di un IDList con un ID selezionato.

[guardare questo video](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

##<a name="attribute-descriptions"></a>Descrizione degli attributi

Nel Pannello di gestione di redazione consente ad alta precisione nominale posizione rilevare e verifica che in grado di rilevare facce umane fino a 64 in una cornice del video. Facce frontale forniscono i risultati migliori, mentre si facce laterali e piccole facce (minore o uguale a 24 x 24 pixel) sono difficili.

Le facce rilevate e tener traccia vengono restituite con le coordinate che indica la posizione di facce, come immagine di un numero di ID che indica la registrazione della persona. Numeri di ID nominale sono soggetti a reimpostare circostanze quando nominale frontale viene persa o sovrapposti nella cornice che alcune persone Guida assegnate più ID.

Per una spiegazione dettagliata per gli attributi, vedere l'argomento [nominale rilevare ed emozioni con Azure Media Analitica](media-services-face-and-emotion-detection.md) .

## <a name="sample-code"></a>Codice di esempio

Il programma che segue mostra come:

1. Creazione di un bene e caricare un file di supporto nel asset.
1. Creare un processo con un'attività di redazione nominale in base a un file di configurazione che contiene il predefinito json seguenti. 
                    
        {'version':'1.0', 'options': {'mode':'combined'}}

1. Scaricare i file di output JSON. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceRedaction
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
        
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
        
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
        
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
        
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


##<a name="next-step"></a>Passaggio successivo

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Collegamenti correlati

[Panoramica di Analitica dei servizi Media Azure](media-services-analytics-overview.md)

[Demo di elementi multimediali Analitica Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
