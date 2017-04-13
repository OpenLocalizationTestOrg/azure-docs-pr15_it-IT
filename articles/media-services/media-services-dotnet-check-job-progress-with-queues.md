<properties 
    pageTitle="Consente di eseguire il monitoraggio delle notifiche del processo di servizi multimediali con .NET lo spazio di archiviazione di Azure coda | Microsoft Azure" 
    description="Informazioni su come utilizzare lo spazio di archiviazione di Azure coda per eseguire il monitoraggio delle notifiche del processo di servizi di supporto. Nell'esempio di codice scritto in c# e utilizza Media Services SDK per .NET." 
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
    ms.date="08/19/2016"   
    ms.author="juliako"/>

# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Utilizzare lo spazio di archiviazione di Azure coda per eseguire il monitoraggio delle notifiche del processo di servizi multimediali con .NET

Quando si eseguono processi, spesso necessario un modo per tenere traccia dello stato di processo. È possibile controllare lo stato di avanzamento usando lo spazio di archiviazione di Azure coda per eseguire il monitoraggio delle notifiche del processo di servizi multimediali, come descritto in questo argomento, o che definisce un gestore eventi StateChanged (come descritto in [questo](media-services-check-job-progress.md) argomento.  

## <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications"></a>Utilizzare lo spazio di archiviazione di Azure coda per eseguire il monitoraggio delle notifiche del processo di servizi multimediali

Servizi multimediali di Microsoft Azure è in grado di recapito dei messaggi di notifica per l' [archiviazione di Azure coda](../storage-dotnet-how-to-use-queues.md#what-is) durante l'elaborazione di processi di elementi multimediali. In questo argomento viene illustrato come ottenere questi messaggi di notifica dallo spazio di archiviazione coda.

Messaggi recapitati allo spazio di archiviazione coda sono accessibili da un punto qualsiasi nel mondo. Coda di Azure messaggistica architettura è affidabile e scalabilità. Polling dello spazio di archiviazione di coda preferibile altri metodi. 

Uno scenario comune per ascoltare le notifiche tramite servizi multimediali è sviluppo di un sistema di gestione dei contenuti che è necessario eseguire alcune attività aggiuntive dopo un processo di codifica completa (ad esempio trigger successivo passaggio in un flusso di lavoro o pubblicare il contenuto). 

###<a name="considerations"></a>Considerazioni

Quando si sviluppano applicazioni servizi multimediali che usano coda di archiviazione Azure, tenere presente quanto segue.

- Il servizio di accodamento non fornisce una garanzia di first in first out (FIFO) il recapito ordinato. Per ulteriori informazioni, vedere [code Azure e Azure servizio Bus code confrontate e Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Azure code di spazio di archiviazione non è un servizio di push; è necessario polling della coda. 
- È consentito qualsiasi numero di code. Per ulteriori informazioni, vedere [API REST di coda del servizio](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- Azure code di spazio di archiviazione sono alcuni limiti e specifiche sono descritti nell'articolo seguente: [code Azure e Azure servizio Bus code confrontate e Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###<a name="code-example"></a>Esempio di codice

Il codice riportato in questa sezione vengono eseguite le operazioni seguenti:

1. Definisce la classe **EncodingJobMessage** che esegue il mapping nel formato di messaggio di notifica. Il codice deserializza messaggi ricevuti dalla coda in oggetti del tipo **EncodingJobMessage** .
1. Carica le informazioni sull'account di servizi di supporto e lo spazio di archiviazione del file app. Queste informazioni vengono utilizzate per creare gli oggetti **CloudMediaContext** e **CloudQueue** .
1. Crea coda che riceve messaggi di notifica sul processo di codifica.
1. Consente di creare il punto finale di notifica che siano associato alla coda.
1. Allega il punto finale di notifica al processo e invia il processo di codifica. È possibile impostare più punti finali di notifica allegati a un processo.
1. In questo esempio abbiamo interessa solo negli stati finali di elaborazione del processo, in modo che **NotificationJobState.FinalStatesOnly** è passare al metodo **AddNew** . 
        
        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Se si passa NotificationJobState.All, dovrebbe ottenere tutte le notifiche di modifica stato: in coda -> pianificato -> elaborazione -> completato. Tuttavia, come indicato in precedenza, il servizio di code di spazio di archiviazione di Azure non garantisce il recapito ordinato. È possibile utilizzare la proprietà Timestamp (definita sul tipo di EncodingJobMessage nell'esempio seguente) ai messaggi di ordine. È possibile che si ottengano i messaggi di notifica duplicati. Utilizzare la proprietà ETag (definita sul tipo di EncodingJobMessage) per controllare i duplicati. È inoltre possibile che alcune notifiche di modifica stato verranno ignorati. 
1. Aspettare il processo ottenere lo stato termine selezionando coda ogni 10 secondi. Elimina i messaggi dopo che sono stati elaborati.
1. Elimina coda e il punto finale di notifica.

>[AZURE.NOTE]Il modo consigliato per lo stato del processo è in attesa per i messaggi di notifica, come illustrato nell'esempio seguente.
>
>In alternativa, è possibile verificare sullo stato del processo utilizzando la proprietà **IJob.State** .  Un messaggio di notifica sul completamento del processo può arrivare prima che lo stato di **IJob** è impostato su **completato**. La proprietà **IJob.State** riflette lo stato accurato con una certa lentezza.

    
    using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Web;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Runtime.Serialization.Json;
    
    namespace JobNotification
    {
        public class EncodingJobMessage
        {
            // MessageVersion is used for version control. 
            public String MessageVersion { get; set; }
        
            // Type of the event. Valid values are 
            // JobStateChange and NotificationEndpointRegistration.
            public String EventType { get; set; }
        
            // ETag is used to help the customer detect if 
            // the message is a duplicate of another message previously sent.
            public String ETag { get; set; }
        
            // Time of occurrence of the event.
            public String TimeStamp { get; set; }
    
            // Collection of values specific to the event.
    
            // For the JobStateChange event the values are:
            //     JobId - Id of the Job that triggered the notification.
            //     NewState- The new state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
            //     OldState- The old state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
    
            // For the NotificationEndpointRegistration event the values are:
            //     NotificationEndpointId- Id of the NotificationEndpoint 
            //          that triggered the notification.
            //     State- The state of the Endpoint. 
            //          Valid values are: Registered and Unregistered.
    
            public IDictionary<string, object> Properties { get; set; }
        }
    
        class Program
        {
            private static CloudMediaContext _context = null;
            private static CloudQueue _queue = null;
            private static INotificationEndPoint _notificationEndPoint = null;
    
            private static readonly string _singleInputMp4Path =
                Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
    
            static void Main(string[] args)
            {
                // Get the values from app.config file.
                string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
                string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
                string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
    
    
                string endPointAddress = Guid.NewGuid().ToString();
    
                // Create the context. 
                _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
    
                // Create the queue that will be receiving the notification messages.
                _queue = CreateQueue(storageConnectionString, endPointAddress);
    
                // Create the notification point that is mapped to the queue.
                _notificationEndPoint = 
                        _context.NotificationEndPoints.Create(
                        Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
    
    
                if (_notificationEndPoint != null)
                {
                    IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                    WaitForJobToReachedFinishedState(job.Id);
                }
    
                // Clean up.
                _queue.Delete();      
                _notificationEndPoint.Delete();
           }
    
    
            static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
            {
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
    
                // Create the queue client
                CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
                // Retrieve a reference to a queue
                CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
    
                // Create the queue if it doesn't already exist
                queue.CreateIfNotExists();
    
                return queue;
            }
     
    
            public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
    
                //Create an encrypted asset and upload the mp4. 
                IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
                    inputMediaFilePath);
    
                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
                // Create a task with the conversion details, using a configuration file. 
                ITask task = job.Tasks.AddNew("My encoding Task",
                    processor,
                    "H264 Multiple Bitrate 720p",
                    Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
    
                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);
    
                // Add a notification point to the job. You can add multiple notification points.  
                job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
                    _notificationEndPoint);
    
                job.Submit();
    
                return job;
            }
    
            public static void WaitForJobToReachedFinishedState(string jobId)
            {
                int expectedState = (int)JobState.Finished;
                int timeOutInSeconds = 600;
    
                bool jobReachedExpectedState = false;
                DateTime startTime = DateTime.Now;
                int jobState = -1;
    
                while (!jobReachedExpectedState)
                {
                    // Specify how often you want to get messages from the queue.
                    Thread.Sleep(TimeSpan.FromSeconds(10));
    
                    foreach (var message in _queue.GetMessages(10))
                    {
                        using (Stream stream = new MemoryStream(message.AsBytes))
                        {
                            DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                            settings.UseSimpleDictionaryFormat = true;
                            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                            EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
    
                            Console.WriteLine();
    
                            // Display the message information.
                            Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                            Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                            Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                            Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                            foreach (var property in encodingJobMsg.Properties)
                            {
                                Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                            }
    
                            // We are only interested in messages 
                            // where EventType is "JobStateChange".
                            if (encodingJobMsg.EventType == "JobStateChange")
                            {
                                string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                                if (JobId == jobId)
                                {
                                    string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                    string newJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "NewState").FirstOrDefault().Value;
    
                                    JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                    JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
    
                                    if (newJobState == (JobState)expectedState)
                                    {
                                        Console.WriteLine("job with Id: {0} reached expected state: {1}", 
                                            jobId, newJobState);
                                        jobReachedExpectedState = true;
                                        break;
                                    }
                                }
                            }
                        }
                        // Delete the message after we've read it.
                        _queue.DeleteMessage(message);
                    }
    
                    // Wait until timeout
                    TimeSpan timeDiff = DateTime.Now - startTime;
                    bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                    if (timedOut)
                    {
                        Console.WriteLine(@"Timeout for checking job notification messages, 
                                            latest found state ='{0}', wait time = {1} secs",
                            jobState,
                            timeDiff.TotalSeconds);
    
                        throw new TimeoutException();
                    }
                }
            }
       
            static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
            {
                var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
                    assetCreationOptions);
    
                var fileName = Path.GetFileName(singleFilePath);
    
                var assetFile = asset.AssetFiles.Create(fileName);
    
                Console.WriteLine("Created assetFile {0}", assetFile.Name);
                Console.WriteLine("Upload {0}", assetFile.Name);
    
                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading of {0}", assetFile.Name);
    
                return asset;
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

L'esempio precedente prodotto l'output seguente. Si valori variano.
    
    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4
    
    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35
    
    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
    State: Finished
    

## <a name="next-step"></a>Passaggio successivo

Servizi multimediali di revisione percorsi formativi

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
