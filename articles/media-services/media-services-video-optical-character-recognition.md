<properties
    pageTitle="Consente di convertire il testo contenuto nel file video in testo digitale Azure Media Analitica | Microsoft Azure"
    description="Azure Media Analitica OCR (riconoscimento ottico dei caratteri) consente di convertire il contenuto di testo in file video in testo digitale modificabile, che supportano le ricerche.  In questo modo è possibile automatizzare l'estrazione dei metadati significativo da segnale video dei file multimediali."
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
    ms.author="juliako"/>
 
#<a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Consente di convertire il testo contenuto nel file video in testo digitale Analitica Media Azure 

##<a name="overview"></a>Panoramica

Se è necessario estrarre il contenuto di testo da file video e generare un testo digitale modificabile, che supportano le ricerche, è necessario utilizzare Azure Media Analitica OCR (riconoscimento ottico dei caratteri). Questo processore Media Azure rileva contenuto di testo si file video e genera file di testo per l'uso. OCR consente di automatizzare l'estrazione dei metadati significativo da segnale video dei file multimediali.

Se utilizzato in combinazione con un motore di ricerca, è possibile indicizzare i file multimediali dal testo facilmente e migliorare l'individuazione del contenuto. Questo è estremamente utile nel video altamente testo, ad esempio una registrazione video o l'acquisizione schermo di una presentazione. Processore Azure OCR Media è ottimizzato per testo digitale.

Processore di elementi multimediali **Azure Media OCR** è attualmente in anteprima.

In questo argomento vengono fornite informazioni dettagliate sulle **Azure Media OCR** e viene illustrato come utilizzare con Media Services SDK per .NET. Per ulteriori informazioni ed esempi, vedere [questo blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

##<a name="ocr-input-files"></a>File di input OCR

File video. Attualmente sono supportati i formati seguenti: MP4, MOV e WMV.

##<a name="task-configuration"></a>Configurazione delle attività 

Configurazione delle attività (predefinito). Quando si crea un'attività con **OCR Media Azure**, è necessario specificare una configurazione preimpostata usando JSON o XML. 

###<a name="attribute-descriptions"></a>Descrizione degli attributi

Nome attributo|Descrizione
---|---
Lingua|(facoltativo) vengono illustrati la lingua del testo da cercare. Una delle operazioni seguenti: rilevamento automatico (impostazione predefinita), arabo, due lingue, ChineseTraditional, CECA danese, olandese, inglese, finlandese, francese, tedesco, greco, ungherese, italiano, giapponese, coreano, norvegese, polacco, portoghese, Romeno, russo, SerbianCyrillic, SerbianLatin, slovacco, spagnolo, svedese, turco.
TextOrientation|(facoltativo) descrive l'orientamento del testo da cercare.  "A sinistra" significa che fa parte superiore di tutte le lettere verso sinistra.  Testo predefinito (simile a quello che si trova in un libro) può essere chiamato "I" orientato.  Una delle operazioni seguenti: rilevamento automatico (impostazione predefinita), verso l'alto, verso destra, freccia giù, freccia sinistra.
TimeInterval|(facoltativo) descrive il tasso di esempio.  Il valore predefinito è ogni secondo 1/2.<br/>Formato JSON-hh. Servizio di archiviazione sicura (impostazione predefinita 00:00:00.500)<br/>Formato XML – XSD W3C durata primitiva (impostazione predefinita PT0.5)
DetectRegions|(facoltativo) Matrice di oggetti DetectRegion specificando le aree nella cornice del video in cui si desidera rilevare testo.<br/>Un oggetto DetectRegion è costituito da valori quattro integer seguenti:<br/>A sinistra: pixel dal margine sinistro<br/>Inizio-pixel dal margine superiore<br/>Larghezza: la larghezza dell'area in pixel<br/>Altezza: altezza dell'area in pixel

####<a name="json-preset-example"></a>Esempio preimpostato JSON
        
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

####<a name="xml-preset-example"></a>File XML di esempio predefinite

    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

##<a name="ocr-output-files"></a>File di output OCR

L'output del processore media OCR è un file JSON.

###<a name="elements-of-the-output-json-file"></a>Elementi del file JSON di output

L'output OCR Video fornisce i dati segmentato tempo utilizzando i caratteri trovato nel video.  È possibile utilizzare gli attributi, ad esempio lingua o orientamento hiamata aggiuntivo su esattamente le parole che si è interessati durante l'analisi. 

L'output contiene gli attributi seguenti:

Elemento|Descrizione
---|---
Scala cronologica|"segni di graduazione" al secondo del video
Scarto|ora offset della data e ora. Nella versione 1.0 di API di Video, è sempre uguale 0.
Frequenza fotogrammi|Frame al secondo del video
Larghezza|larghezza del video in pixel
altezza|altezza del video in pixel
Frammenti|Matrice di blocchi basate sul tempo del video in cui sono suddivisione in blocchi i metadati
inizio|ora di inizio del frammento in "segni di graduazione"
durata|lunghezza del frammento in "segni di graduazione"
intervallo|intervallo di ogni evento all'interno del frammento specificato
eventi|in forma di matrice che contiene aree
area geografica|oggetto che rappresenta rilevato parole o frasi
lingua|lingua del testo rilevato in un'area
orientamento|orientamento del testo rilevato in un'area
righe|Matrice di righe di testo rilevato in un'area
testo|il testo

###<a name="json-output-example"></a>Esempio di output JSON

Nell'esempio seguente di output contiene video informazioni generali e frammenti video diversi. In ogni frammento video contiene ogni area che viene rilevata dal Pannello di gestione OCR con la lingua e l'orientamento testo. L'area contiene anche ogni riga di word in quest'area con testo della linea, la posizione della riga e tutte le informazioni su word (il contenuto di word, posizione e confidenza) nella riga corrente. Di seguito è illustrato un esempio e inserire alcuni commenti in linea.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Codice di esempio

Il programma che segue mostra come:

1. Creazione di un bene e caricare un file di supporto nel asset.
1. Crea un processo con un file di configurazione/preimpostato OCR.
1. Scaricare i file di output JSON. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace OCR
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
        
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
        
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
        
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
        
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
