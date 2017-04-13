<properties
    pageTitle="Rilevare movimenti con Azure Media Analitica | Microsoft Azure"
    description="Processore media rilevazione movimento di Azure Media (programma minimo) consente di identificare in modo efficiente sezioni di interesse all'interno di un video in caso contrario lungo e se."
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>Rilevare movimenti con Analitica Media Azure

##<a name="overview"></a>Panoramica

Processore media **Rilevazione movimento di Azure Media** (programma minimo) consente di identificare in modo efficiente sezioni di interesse all'interno di un video in caso contrario lungo e se. Rilevamento di attività può essere usato su ripresa statico fotocamera per identificare le sezioni del video in cui si verifica l'animazione. Viene generato un file JSON che contiene i metadati con data e ora e il riquadro area geografica in cui si è verificato l'evento.

Questa tecnologia rivolto feed video di sicurezza, in grado di classificare animazione in falsi, ad esempio ombreggiature e modifiche di illuminazione ed eventi importanti. In questo modo è possibile generare gli avvisi di sicurezza da fotocamera feed senza da posta indesiderata con gli eventi irrilevanti infiniti, la possibilità di estrarre allontanato di interesse da video controllo molto lungo.

**Rilevazione movimento Media Azure** Pannello di gestione è attualmente disponibile in anteprima.

In questo argomento vengono fornite informazioni dettagliate sulle **Rilevazione movimento Media Azure** e viene illustrato come utilizzare con Media Services SDK per .NET


##<a name="motion-detector-input-files"></a>File di input viene animazione

File video. Attualmente sono supportati i formati seguenti: MP4, MOV e WMV.

##<a name="task-configuration-preset"></a>Configurazione delle attività (predefinito)

Quando si crea un'attività con **Rilevazione movimento Media Azure**, è necessario specificare una configurazione preimpostata. 

###<a name="parameters"></a>Parametri

È possibile utilizzare i seguenti:

Nome|Opzioni|Descrizione|Impostazione predefinita
---|---|---|---
sensitivityLevel|Stringa: 'basso', 'medium', 'high'|Imposta il livello di riservatezza quali movimenti viene segnalato. Regolare seguente per modificare la quantità di falsi.|'medium'
frameSamplingValue|Numero intero positivo|Imposta la frequenza con cui viene eseguito algoritmo. ogni frame è uguale a 1, 2 indica che ogni frame 2a e così via.|1
detectLightChange|Booleano: "true", "false"|Imposta se light le modifiche vengono segnalate nei risultati|"False"
mergeTimeThreshold|X-ora: Hh<br/>Esempio: 00:00:03|Specifica l'intervallo di tempo tra gli eventi di animazione in cui verranno combinati e segnalati come 1 a 2 eventi.|00:00:00
detectionZones|Matrice di zone:<br/>-Area rilevamento è una matrice di 3 o più punti<br/>-Punto si trova una x e y coordinate da 0 a 1.|Descrive l'elenco delle aree di rilevamento poligonale da utilizzare.<br/>Risultati verranno visualizzati con le aree come ID, mentre il primo diventa 'id': 0|Area singola che copre l'intero frame.

###<a name="json-example"></a>Esempio JSON

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>File di output rilevamento di animazione

Un processo di rilevamento animazione restituirà un file JSON del bene output che descrive gli avvisi di animazione e le categorie, all'interno del video. Il file conterrà informazioni la durata dell'animazione rilevato nel video e l'ora.

L'API di rilevamento animazione fornisce indicatori dopo che sono disponibili gli oggetti in movimento in uno sfondo fisso video (ad esempio controllo video). Rilevazione movimento è in grado di ridurre falsi allarmi, ad esempio illuminazione e ombreggiatura sulle modifiche apportate. Limitazioni corrente degli algoritmi includono video visione notte, oggetti semitrasparenti e oggetti di piccole dimensioni.

###<a id="output_elements"></a>Elementi del file JSON di output

>[AZURE.NOTE]Nella versione più recente, il formato di Output JSON è stato modificato e può rappresentare una modifica per alcuni clienti.

Nella tabella seguente vengono descritti gli elementi del file JSON di output.

Elemento|Descrizione
---|---
Versione|Questa opzione fa riferimento alla versione dell'API di Video. La versione corrente è 2.
Scala cronologica|"Segni di graduazione" al secondo del video.
Scarto|Orario per timestamp in "segni di graduazione". Nella versione 1.0 di API di Video, è sempre uguale 0. In futuro scenari che è supportato, questo valore può variare.
Frequenza fotogrammi|Frame al secondo del video.
Larghezza, altezza|Fa riferimento alla larghezza e altezza del video in pixel.
Inizio|Timestamp di inizio in "segni di graduazione".
Durata|La lunghezza dell'evento, in "segni di graduazione".
Intervallo|Intervallo di ogni voce dell'evento, in "segni di graduazione".
Eventi|Ogni frammento di evento contiene il movimento rilevato all'interno di tale intervallo di tempo.
Tipo|Nella versione corrente, è sempre "2" per animazione generico. In questo modo etichetta Video API della flessibilità necessaria per la classificazione di animazione in futuro le versioni.
RegionID|Come illustrato sopra, questo viene mantenuto 0 in questa versione. L'etichetta flessibilità Video API di trovare animazione in diverse aree geografiche nelle versioni future.
Aree|Fa riferimento all'area del video nel punto in cui è rilevante animazione. <br/><br/>-"id" rappresenta l'area regione: in questa versione è disponibile un solo, ID 0. <br/>-"tipo" rappresenta la forma dell'area è rilevante per animazione. "Rettangolo" e "poligono" sono attualmente supportati.<br/> Se è stato specificato "rettangolo", l'area geografica ha dimensioni x, Y, larghezza e altezza. Le coordinate X e Y rappresentano le coordinate XY angolo superiore sinistro dell'area di una scala normalizzata da 0,0 a 1.0. La larghezza e altezza rappresentano le dimensioni dell'area di una scala normalizzata da 0,0 a 1.0. Nella versione corrente, X, Y, larghezza e altezza sono sempre fissi 0, 0 e 1, 1. <br/>Se è stato specificato "poligono", l'area geografica ha dimensioni in punti. <br/>
Frammenti|I metadati sono suddivise diversi segmenti definiti frammenti. Ogni frammento contiene un inizio, durata, numero di intervallo ed eventi. Frammento con gli eventi non significa che è non stato rilevato alcun movimento durante l'ora di inizio e durata.
Parentesi quadre]|Ogni parentesi rappresenta un intervallo nell'evento. Svuotare tra parentesi quadre per tale intervallo indica che è non stato rilevato alcun movimento.
percorsi|La nuova voce nell'elenco eventi elenca il percorso in cui si è verificato l'animazione. Si tratta più specifico le aree di rilevamento.

Ecco un esempio di output JSON

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>Limitazioni

- Formati video supportati includono MP4, MOV e WMV.
- Rilevamento ottimizzato per i video di sfondo fisso. Algoritmo di è incentrata su riduzione dei falsi allarmi, ad esempio le modifiche di illuminazione e ombreggiature.
- Alcuni animazione potrebbe non essere rilevato a causa di problemi tecnici legati alla; ad esempio video visione notte, semitrasparente, oggetti e oggetti di piccole dimensioni.


## <a name="sample-code"></a>Codice di esempio

Il programma che segue mostra come:

1. Creazione di un bene e caricare un file di supporto nel asset.
1. Crea un processo con un'attività di rilevamento movimento video in base a un file di configurazione che contiene il predefinito json seguenti. 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. Scaricare i file di output JSON. 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Azure rilevazione movimento servizi multimediali blog](https://azure.microsoft.com/blog/motion-detector-update/)

[Panoramica di Analitica dei servizi Media Azure](media-services-analytics-overview.md)

[Demo di elementi multimediali Analitica Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
