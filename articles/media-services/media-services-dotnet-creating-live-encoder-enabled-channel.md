<properties 
    pageTitle="Come eseguire il flusso live tramite servizi multimediali di Windows Azure per creare flussi di velocità multi con .NET | Microsoft Azure" 
    description="In questa esercitazione sono illustrati i passaggi della creazione di un canale che riceve un flusso di live singola velocità e codifica flusso multi-velocità utilizzando .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Come eseguire il flusso live tramite servizi multimediali di Windows Azure per creare flussi di velocità multi con .NET

> [AZURE.SELECTOR]
- [Portale](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [API REST](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/?WT.mc_id=A261C142F).

##<a name="overview"></a>Panoramica

In questa esercitazione sono illustrati i passaggi della creazione di un **canale** che riceve un flusso di live singola velocità e codifica con più velocità flusso.

Per ulteriori informazioni relative ai canali abilitati per la codifica live, vedere [multimediali tramite servizi multimediali di Windows Azure per creare flussi di velocità con più attivi](media-services-manage-live-encoder-enabled-channels.md).


##<a name="common-live-streaming-scenario"></a>Scenario di trasmissione Live comune

La procedura seguente descritte le attività per la creazione di applicazioni comuni di trasmissione live.

>[AZURE.NOTE] Attualmente, la durata massima consentita consigliata di un evento live è 8 ore. Se è necessario eseguire un canale per periodi di tempo più, contattare amslived per il sito Microsoft.com.

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che può trasmettere un flusso di velocità singola in uno dei seguenti protocolli: RTP, Smooth Streaming o RTP (MPEG-TS). Per ulteriori informazioni, vedere [supporto RTP servizi multimediali di Azure e Live Encoder](http://go.microsoft.com/fwlink/?LinkId=532824).

Questo passaggio può anche essere eseguito dopo aver creato il canale.

1. Creare e avviare un canale.

1. Recuperare il canale acquisizione URL.

L'URL di caricamento viene utilizzato dal codificatore di live per inviare il flusso per il canale.

1. Recuperare l'URL di anteprima di canale.

Utilizzare questo URL per verificare che il canale correttamente riceva flusso live.

2. Creare una risorsa.
3. Se si desidera per la risorsa in modo dinamico crittografati durante la riproduzione, eseguire le operazioni seguenti:
1. Creare una chiave del contenuto.
1. Configurare i criteri di autorizzazione della chiave del contenuto.
1. Configurare i criteri di recapito bene (utilizzato da imballaggio dinamico e dinamico crittografia).
3. Creare un programma e specificare le risorse che è stato creato.
1. Pubblicare la risorsa associata al programma mediante la creazione di un indicatore di posizione su richiesta.

Assicurarsi di essere presente almeno un flusso unità riservati per l'endpoint flusso da cui si desidera il contenuto di flusso.

1. Avviare il programma quando si è pronti avviare il flusso e l'archiviazione.
2. Facoltativamente, il codificatore live può essere segnalato per avviare un annuncio pubblicitario. L'annuncio viene inserito nel flusso di output.
1. Interrompere il programma ogni volta che si desidera interrompere la trasmissione e archiviazione l'evento.
1. Eliminare il programma e se lo si desidera eliminare la risorsa.

## <a name="what-youll-learn"></a>Si apprenderanno

In questo argomento viene illustrato come eseguire diverse operazioni su canali e applicazioni che utilizzano Media Services .NET SDK. Poiché molte operazioni sono lunga API di .NET gestire lunga vengono utilizzate operazioni.

L'argomento viene illustrato come eseguire le operazioni seguenti:

1. Creare e avviare un canale. API di esecuzione prolungata vengono utilizzate.
1. Ottenere i canali acquisizione endpoint (input). Occorre prevedere l'endpoint al codificatore in grado di inviare un flusso di live velocità singola.
1. È possibile ottenere l'endpoint di anteprima. L'endpoint viene utilizzato per visualizzare un'anteprima del flusso.
1. Creare una risorsa che verrà utilizzata per archiviare il contenuto. I criteri di recapito bene devono essere configurati anche, come illustrato nell'esempio seguente.
1. Creare un programma e specificare le risorse che è stata creata in precedenza. Avviare il programma. API di esecuzione prolungata vengono utilizzate.
1. Creare un indicatore di posizione per la risorsa, in modo che il contenuto viene pubblicato e può essere alterato per i clienti.
1. Visualizzare e nascondere Tablet. Avviare e arrestare annunci. API di esecuzione prolungata vengono utilizzate.
1. Pulire il canale e tutte le risorse associate.


##<a name="prerequisites"></a>Prerequisiti

Sono necessari per completare l'esercitazione.

- Per completare questa esercitazione, è necessario un account Azure.

Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/?WT.mc_id=A261C142F). È possibile ottenere crediti che possono essere utilizzati per provare a utilizzare i servizi di Azure a pagamento. Anche dopo il crediti vengono utilizzati, è possibile mantenere l'account e utilizzo delle caratteristiche, ad esempio la caratteristica Web Apps nel servizio di Azure App e servizi Azure gratuiti.
- Un account di servizi di supporto. Per creare un account di servizi multimediali, vedere [Creazione di Account](media-services-portal-create-account.md).
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o versioni successive.
- È necessario utilizzare Media Services .NET SDK versione 3.2.0.0 o versione successiva.
- Una webcam e un codificatore in grado di inviare un flusso di live velocità singola.

##<a name="considerations"></a>Considerazioni

- Attualmente, la durata massima consentita consigliata di un evento live è 8 ore. Se è necessario eseguire un canale per periodi di tempo più, contattare amslived per il sito Microsoft.com.
- Assicurarsi di essere presente almeno un flusso unità riservati per l'endpoint flusso da cui si desidera il contenuto di flusso.

##<a name="download-sample"></a>Download di esempio

Accedere ed eseguire un campione da [qui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##<a name="set-up-for-development-with-media-services-sdk-for-net"></a>Configurare per lo sviluppo con Media Services SDK per .NET

1. Creare un'applicazione console mediante Visual Studio.
1. Aggiungere l'applicazione console utilizzando pacchetto NuGet servizi multimediali Media Services SDK per .NET.

##<a name="connect-to-media-services"></a>Connettersi ai servizi di supporto
Come si consiglia di utilizzare un file app. per archiviare la chiave account e nome servizi multimediali.

>[AZURE.NOTE]Per trovare il nome e la chiave, accedere al portale di Azure e selezionare il proprio account. Sul lato destro viene visualizzata la finestra di impostazioni. Nella finestra Impostazioni selezionare chiavi. Fare clic sull'icona accanto a ogni casella di testo valore copiato negli Appunti di sistema.

Aggiungere la sezione appSettings al file App e impostare i valori per la chiave account e nome account servizi multimediali.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##<a name="code-example"></a>Esempio di codice

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
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
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##<a name="next-step"></a>Passaggio successivo

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Non che si stava cercando?

Se questo argomento non contiene risultati desiderati, non è un elemento o in un altro modo non soddisfa le proprie esigenze, specificare un indirizzo di commenti e suggerimenti utilizzando il thread Disqus riportata di seguito.
