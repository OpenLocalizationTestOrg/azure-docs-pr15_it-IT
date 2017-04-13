<properties
    pageTitle="Rilevare nominale ed emozioni con Azure Media Analitica | Microsoft Azure"
    description="In questo argomento viene illustrato come rilevare facce ed emozioni con Azure Media Analitica."
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
    ms.date="09/26/2016"   
    ms.author="milanga;juliako;"/>
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>Rilevare nominale ed emozioni con Analitica Media Azure

##<a name="overview"></a>Panoramica

Processore media **Azure Media nominale rilevamento** (programma minimo) consente di contare, tenere traccia dei movimenti del e valutare anche la partecipazione del pubblico e reazione tramite le espressioni del volto. Questo servizio contiene due caratteristiche: 

- **Rilevamento nominale**

    Rilevamento nominale Trova e tiene traccia delle risorse umane facce all'interno di un video. Più facce possono essere rilevate e successivamente tenere traccia durante lo spostamento, con i metadati di ora e luogo restituiti in un file JSON. Durante la verifica, tenterà di assegnare un ID coerente per la stessa faccia mentre la persona è gli spostamenti sullo schermo, anche se sono nascosto o lasciare brevemente la cornice.

    >[AZURE.NOTE]Questo services non vengono eseguite riconoscimento viso. Persona che lascia il frame o diventa nascosto per troppo a lungo sarà assegnato un nuovo ID quando restituiscono.

- **Rilevamento emozioni**
    
    Rilevamento emozioni è un componente facoltativo del processore Media rilevamento nominale che restituisce analisi più attributi affettivi dalle facce rilevate, inclusi "Happiness", sadness, paura, anger e così via. 

**Azure Media nominale rilevamento** Pannello di gestione è attualmente disponibile in anteprima.

In questo argomento vengono fornite informazioni dettagliate sulle **Azure Media nominale rilevamento** e viene illustrato come utilizzare con Media Services SDK per .NET.

##<a name="face-detector-input-files"></a>File di input viene smile

File video. Attualmente sono supportati i formati seguenti: MP4, MOV e WMV.

##<a name="face-detector-output-files"></a>Usato per il file di output di rilevamento

L'API di rilevamento e la gestione di nominale fornisce ad alta precisione nominale posizione rilevamento e verifica in grado di rilevare facce umane fino a 64 in un video. Facce frontale forniscono i risultati migliori, mentre si facce laterali e piccole facce (minore o uguale a 24 x 24 pixel) potrebbero non essere accurate.

Le facce rilevate e tener traccia vengono restituite con le coordinate (a sinistra, superiore, larghezza e altezza) che indica la posizione di facce nell'immagine in pixel, come un numero di ID nominale che indica la registrazione della persona. Numeri di ID nominale sono soggetti a reimpostare circostanze quando nominale frontale viene persa o sovrapposti nella cornice che alcune persone Guida assegnate più ID.

###<a id="output_elements"></a>Elementi del file JSON di output

Per il rilevamento nominale e tenerne traccia operazione, il risultato di output contiene i metadati da facce all'interno del file nel formato JSON specificato.

Il rilevamento nominale e verifica JSON include gli attributi seguenti:

Elemento|Descrizione
---|---
Versione|Questa opzione fa riferimento alla versione dell'API di Video.
Scala cronologica|"Segni di graduazione" al secondo del video.
Scarto|Verrà visualizzata la differenza di orario per data e ora. Nella versione 1.0 di API di Video, è sempre uguale 0. In futuro scenari che è supportato, questo valore può variare.
Frequenza fotogrammi|Frame al secondo del video.
Frammenti|I metadati sono suddivise diversi segmenti definiti frammenti. Ogni frammento contiene un inizio, durata, numero di intervallo ed eventi.
Inizio|Ora di inizio del primo evento 'segni di graduazione'.
Durata|La lunghezza del frammento in "segni di graduazione".
Intervallo|Intervallo di ogni voce dell'evento all'interno del frammento in "segni di graduazione".
Eventi|Ogni evento contiene le facce rilevare e analizzare le all'interno di tale intervallo di tempo. Si tratta di una matrice di matrice di eventi. Matrice esterna rappresenta un intervallo di tempo. La matrice interna è costituita da 0 o altri eventi che si sono verificati in tale momento. Un [] parentesi vuota indica che sono state rilevate senza facce.
ID| ID della faccia tracciata. Questo numero può variare inavvertitamente se una faccia assume la forma non rilevata. Una determinata persona deve avere lo stesso ID in tutto il video complessivo, ma ciò non può essere garantito a causa di limitazioni dell'algoritmo di rilevamento (occlusione e così via)
X, Y|In alto a sinistra X e y pari nominale rettangolo di selezione in una scala normalizzata di 0,0 a 1.0. <br/>-X e Y coordinate sono relative carta trasferibile dalla maglietta sempre, in modo che se si dispone di un verticale video (o capovolto, nel caso di iOS), è necessario trasporre le coordinate di conseguenza.
Larghezza, altezza|La larghezza e altezza della faccia rettangolo di selezione in una scala normalizzata di 0,0 a 1.0.
facesDetected|La presente alla fine dei risultati JSON e riepiloga il numero di facce nell'algoritmo vengono rilevato durante il video. Perché gli ID possono essere reimpostati inavvertitamente se una faccia diventa rilevata (ad esempio nominale si spegne dello schermo, ha un aspetto simile al computer), questo numero non può sempre uguale al numero true di facce nel video.

Rilevamento nominale utilizza tecniche di frammentazione (nel punto in cui i metadati possono essere suddiviso in blocchi basate sul tempo ed è possibile scaricare solo gli elementi necessari) e segmentazione (nel punto in cui gli eventi sono suddivise nel caso in cui ricevono troppo grande). Alcuni calcoli semplici consentono di trasformare i dati. Se, ad esempio, un evento avviata 6300 (segni di graduazione), con una scala cronologica di 2997 (segni di graduazione/sec) e frequenza fotogrammi di 29,97 (frame/sec), quindi:

- Inizio/scala cronologica 2.1 secondi
- Secondi x (frequenza fotogrammi/scala cronologica) = 63 frame

Di seguito è un semplice esempio di estrazione JSON in un per formato cornice per il rilevamento nominale e verifica:
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>Smile input rilevamento e generare l'output di esempio

###<a name="input-video"></a>Video di ingresso

[Video di ingresso](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configurazione delle attività (predefinito)

Quando si crea un'attività con **Azure Media nominale rilevamento**, è necessario specificare una configurazione preimpostata. Il seguente di configurazione predefinito è solo per il rilevamento nominale.

    {"version":"1.0"}

###<a name="json-output"></a>JSON output

Nell'esempio seguente di output JSON troncato.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>Rilevamento di emozioni di input e output esempio


###<a name="input-video"></a>Video di ingresso

[Video di ingresso](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>Configurazione delle attività (predefinito)

Quando si crea un'attività con **Azure Media nominale rilevamento**, è necessario specificare una configurazione preimpostata. Il seguente predefinito di configurazione specifica per creare JSON basata sul rilevamento emozioni.
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>Descrizione degli attributi

Nome attributo|Descrizione
---|---
Modalità|Facce: Smile solo rilevamento <br/>AggregateEmotion: Valori restituiti emozioni Media per tutte le facce in cornice.
AggregateEmotionWindowMs|Utilizzare la modalità AggregateEmotion selezionata. Specifica la lunghezza del video utilizzata per produrre ogni risultato aggregato in millisecondi.
AggregateEmotionIntervalMs|Utilizzare la modalità AggregateEmotion selezionata. Specifica con quale frequenza per produrre risultati aggregati.

####<a name="aggregate-defaults"></a>Impostazioni predefinite di aggregazione

Di seguito sono consigliati valori per le impostazioni della finestra e intervallo di aggregazione. AggregateEmotionWindowMs deve contenere più di AggregateEmotionIntervalMs.

   |Impostazioni predefinite (s)|Max(s)|Min(s)
---|---|---|---
AggregateEmotionWindowMs|0,5|2|0,25
AggregateEmotionIntervalMs|0,5|1|0,25

###<a name="json-output"></a>JSON output

JSON output per aggregare emozioni (troncate):
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>Limitazioni

- Formati video supportati includono MP4, MOV e WMV.
- L'intervallo di dimensioni nominale rilevabile è 24 24 a 2048 x 2048 pixel. Le facce fuori l'intervallo non vengano rilevate.
- Per ogni video, il numero massimo di facce restituito è 64.
- Alcune facce potrebbero non essere rilevate a causa di problemi tecnici legati alla; ad esempio grandi angoli nominale (testa posa) e occlusione di grandi dimensioni. Facce frontale e vicino anteriore necessario ottenere risultati ottimali.


## <a name="sample-code"></a>Codice di esempio

Il programma che segue mostra come:

1. Creazione di un bene e caricare un file di supporto nel asset.
1. Crea un processo con un'attività di rilevamento nominale in base a un file di configurazione che contiene il predefinito json seguenti. 
                    
        {
            "version": "1.0"
        }

1. Scaricare i file di output JSON. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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

##<a name="related-links"></a>Collegamenti correlati

[Panoramica di Analitica dei servizi Media Azure](media-services-analytics-overview.md)

[Demo di elementi multimediali Analitica Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)