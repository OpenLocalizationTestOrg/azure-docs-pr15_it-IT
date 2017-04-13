<properties 
    pageTitle="Avanzate codifica con flusso di lavoro Media Encoder Premium | Microsoft Azure" 
    description="Informazioni su come codificare con flusso di lavoro Media Encoder Premium. Esempi di codice scritto in c# e utilizzano Media Services SDK per .NET." 
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

#<a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Avanzate codifica con flusso di lavoro Media Encoder Premium

>[AZURE.NOTE] Processore media del flusso di lavoro di Media Encoder Premium descritte in questo argomento non è disponibile in Cina.

Per eventuali domande codificatore premium, inviare tramite posta elettronica mepd per il sito Microsoft.com.

##<a name="overview"></a>Panoramica

Servizi multimediali di Microsoft Azure sono stati introdotti processore media **Del flusso di lavoro di Media Encoder Premium** . Questo anticipo processore offerte codifica funzionalità per i flussi di lavoro su richiesta premium. 

Gli argomenti seguenti strutturare i dettagli relativi al **Flusso di lavoro Media Encoder Premium**: 

- [Formati supportati dal flusso di lavoro di Media Encoder Premium](media-services-premium-workflow-encoder-formats.md) : vengono illustrati i formati di file e codec supportati dal **Flusso di lavoro Media Encoder Premium**.

- La sezione [confronto Encoder](media-services-encode-asset.md#compare_encoders) confronta le funzionalità di codifica del **Flusso di lavoro Media Encoder Premium** e **Media Encoder Standard**.

In questo argomento viene illustrato come codificare con **Flusso di lavoro Media Encoder Premium** utilizzando .NET.

Attività di codifica per il **Flusso di lavoro Media Encoder Premium** richiedono un file di configurazione separato, denominato file del flusso di lavoro. Questi file hanno l'estensione .workflow e vengono creati con lo strumento di [Progettazione flussi di lavoro](media-services-workflow-designer.md) .

##<a name="encode"></a>Codificare

Attività di codifica per il **Flusso di lavoro Media Encoder Premium** richiedono un file di configurazione separato, denominato file del flusso di lavoro. Questi file hanno l'estensione .workflow e vengono creati con lo strumento di [Progettazione flussi di lavoro](media-services-workflow-designer.md) .


È inoltre possibile visualizzare l'impostazione predefinita i file del flusso di lavoro [di seguito](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La cartella contiene anche la descrizione di questi file.

I file del flusso di lavoro devono essere caricate al proprio account di servizi multimediali di un bene e deve essere passato questa risorsa per l'attività di codifica.

Nell'esempio seguente viene illustrato come codificare con **Flusso di lavoro Media Encoder Premium**. 

Vengono eseguite le seguenti operazioni: 
 
1. Creazione di un bene e caricare un file di flusso di lavoro. 
2. Creazione di un bene e caricare un file di origine.
3. È possibile ottenere il processore media "Media Encoder Premium del flusso di lavoro".
4. Creare un processo e un'attività. 

    Nella maggior parte dei casi, la stringa di configurazione per l'attività è vuota (come nell'esempio seguente). Esistono alcuni scenari avanzati (che richiedono a impostare le proprietà di runtime in modo dinamico) in questo caso è necessario fornire una stringa XML per l'attività di codifica. Esempi di scenari sono: creazione di una sovrapposizione, elementi multimediali in parallelo o sequenziale panorama, sottotitoli.
5. Aggiungere le attività di input all'attività.
    
    un. 1 ° – bene del flusso di lavoro.

    b. 2 °-risorsa video.
    
    **Nota**: la risorsa del flusso di lavoro deve essere aggiunta all'attività prima di risorse multimediali. Stringa di configurazione per questa operazione deve essere vuota. 

6. Inviare il processo di codifica.

Di seguito è un esempio completo. Per informazioni su come configurare allo sviluppo .NET servizi multimediali, vedere [Servizi multimediali di sviluppo con .NET](media-services-dotnet-how-to-use.md).


    using System; 
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace MediaEncoderPremiumWorkflowSample
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
    
            private static readonly string _supportFiles =
                Path.GetFullPath(@"../..\Media");
    
            private static readonly string _workflowFilePath =
                Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
            
            private static readonly string _singleMP4InputFilePath =
                Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
    
            }
    
            static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
            {
                var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
    
                var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                    AccessPermissions.Write | AccessPermissions.List);
    
                var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
    
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading {0}", assetFile.Name);
    
                locator.Delete();
                accessPolicy.Delete();
    
                return asset;
            }
    
            static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                    processor,
                    "",
                    TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(workflow);
                task.InputAssets.Add(video); // we add one asset
                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new
                        EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish. 
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                progressJobTask.Wait();
    
                // If job state is Error the event handling 
                // method for job progress should log errors.  Here we check 
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    throw new Exception("\nExiting method due to job error.");
                }
    
                return job.OutputMediaAssets[0];
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
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
            static private void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
    
                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
    
                Console.Write(builder.ToString());
            }
    
    
            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
    
                return processor;
            }
        }
    }


Per eventuali domande codificatore premium, inviare tramite posta elettronica mepd per il sito Microsoft.com.

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
