<properties 
    pageTitle="Codificare un bene con Media Encoder Standard utilizzando .NET | Microsoft Azure" 
    description="In questo argomento viene illustrato come utilizzare .NET per la codifica di un bene mediante Media Encoder Strandard." 
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
    ms.date="09/19/2016"
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificare un bene con Media Encoder Standard utilizzando .NET

Processi di codifica sono una delle operazioni di trasformazione più comune in servizi di supporto. Creare processi di codifica per convertire i file multimediali da una codifica a un'altra. Quando si codifica, è possibile utilizzare servizi multimediali incorporati Media Encoder. È inoltre possibile utilizzare un codificatore fornito da un partner di servizi multimediali; Encoder di terze parti sono disponibili tramite Azure Marketplace. 

In questo argomento viene illustrato come utilizzare .NET codificare propri beni con Media Encoder Standard (SEM). Media Encoder Standard viene configurato utilizzando uno del codificatore predefiniti descritti [di seguito](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

È consigliabile codificare sempre i file mezzanine in una velocità adattata MP4 impostare e quindi si converte il set per il formato desiderato grazie [Imballaggio dinamico](media-services-dynamic-packaging-overview.md). Per sfruttare imballaggio dinamico, è prima necessario ottenere almeno un'unità di trasmissione su richiesta per l'endpoint flusso dal quale si prevede di recapito del contenuto. Per ulteriori informazioni, vedere [come servizi multimediali di scala](media-services-portal-manage-streaming-endpoints.md).

Se le risorse di output sono lo spazio di archiviazione crittografato, è necessario configurare i criteri di recapito bene. Per ulteriori informazioni vedere [configurazione bene recapito criteri](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]SEM produce un file di output con un nome che contiene i primi 32 caratteri del nome del file di input. Il nome si basa su quello specificato nel file preimpostato. Ad esempio "nomefile": "_ {base} {Index} {estensione}". {Base} viene sostituito dai primi 32 caratteri del nome del file di input.

###<a name="mes-formats"></a>Formati di SEM

[Formati e codec](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>SEM predefiniti

Media Encoder Standard viene configurato utilizzando uno del codificatore predefiniti descritti [di seguito](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadati di input e di output

Quando si codifica un input (o più attività) utilizzando SEM, si riceve una risorsa di output nel successo codificare di completamento dell'attività. Le risorse di output contengono video, audio, le miniature, manifesto e così via in base a preimpostato codifica che utilizzare.

Le risorse di output contiene anche un file con metadati sulla bene input. Il nome del file XML di metadati è nel formato seguente: < asset_id > _metadata.xml (ad esempio 41114ad3-eb5e - 4c 57 8d 92-5354e2b7d4a4_metadata.xml), dove < asset_id > è il valore di idargomento dell'asset input. Lo schema dei metadati input XML è descritta [di seguito](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Le risorse di output contiene anche un file con metadati sulla bene output. Il nome del file XML di metadati è nel formato seguente: < source_file_name > _manifest.xml (ad esempio BigBuckBunny_manifest.xml). Lo schema dei metadati output XML è descritta [di seguito](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Se si desidera esaminare uno dei due file di metadati, è possibile creare un indicatore di posizione SA e scaricare il file nel computer locale. È possibile trovare un esempio su come creare un indicatore di posizione SA e scaricare un file mediante le estensioni di file multimediali servizi .NET SDK.

##<a name="download-sample"></a>Download di esempio

Accedere ed eseguire un campione da [qui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##<a name="example"></a>Esempio

Nell'esempio seguente viene utilizzata Media Services .NET SDK per eseguire le operazioni seguenti:

- Creare un processo di codifica.
- È possibile ottenere un riferimento al codificatore Media Encoder Standard.
- Specificare l'utilizzo di "H264 velocità più 720p" preimpostato. È possibile visualizzare tutte le impostazioni predefinite [di seguito](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). È anche possibile esaminare lo schema a cui devono essere conformi questi predefiniti argomento [qui](https://msdn.microsoft.com/library/mt269962.aspx) .
- Aggiungere una singola attività codifica al processo. 
- Specificare la risorsa input da codificare.
- Creare una risorsa di output che conterrà la risorsa codificata.
- Aggiungere un gestore eventi per controllare lo stato di avanzamento del processo.
- Inviare il processo.
        
        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        

            // Create a task with the encoding details, using a string preset.
            // In this case "H264 Multiple Bitrate 720p" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Multiple Bitrate 720p",
                TaskOptions.None);
        
            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
        
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
            return job.OutputMediaAssets[0];
        }
        
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
            }
        }
        
        
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
            return processor;
        }


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche 

[Come generare anteprima Media Encoder Standard con .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Panoramica di codifica servizi di supporto](media-services-encode-asset.md)
