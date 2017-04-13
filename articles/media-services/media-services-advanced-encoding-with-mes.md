<properties 
    pageTitle="Avanzate codifica con Media Encoder Standard" 
    description="In questo argomento viene illustrato come eseguire la codifica avanzate personalizzando Media Encoder Standard predefiniti di attività. L'argomento viene illustrato come utilizzare Media Services .NET SDK per creare un'attività di codifica e processo. Viene inoltre illustrato fornire predefiniti personalizzati al processo di codifica." 
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
    ms.date="09/25/2016"   
    ms.author="juliako"/>


#<a name="advanced-encoding-with-media-encoder-standard"></a>Avanzate codifica con Media Encoder Standard

##<a name="overview"></a>Panoramica

In questo argomento viene illustrato come eseguire le operazioni di codifica avanzate con Media Encoder Standard. L'argomento viene [illustrato come utilizzare .NET per creare un'attività di codifica e un processo che consente di eseguire questa operazione](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet). Viene inoltre illustrato fornire predefiniti personalizzati per l'attività di codifica. Per descrizione degli elementi che vengono utilizzati da predefiniti, vedere [questo documento](https://msdn.microsoft.com/library/mt269962.aspx). 

Vengono illustrati i predefiniti personalizzati eseguono le attività di codifica seguenti:

- [Generare anteprime](media-services-custom-mes-presets-with-dotnet.md#thumbnails)
- [Tagliare un video (ritaglio)](media-services-custom-mes-presets-with-dotnet.md#trim_video)
- [Creare una sovrapposizione](media-services-custom-mes-presets-with-dotnet.md#overlay)
- [Inserire un brano invisibile quando input è privo di audio](media-services-custom-mes-presets-with-dotnet.md#silent_audio)
- [Disabilitare deinterlacciamento automatico](media-services-custom-mes-presets-with-dotnet.md#deinterlacing)
- [Impostazioni predefinite solo audio](media-services-custom-mes-presets-with-dotnet.md#audio_only)
- [Concatena due o più file video](media-services-custom-mes-presets-with-dotnet.md#concatenate)
- [Video di ritaglio con Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#crop)
- [Inserire una traccia video quando input non ha video](media-services-custom-mes-presets-with-dotnet.md#no_video)
- [Ruotare un video](media-services-custom-mes-presets-with-dotnet.md#rotate_video)

##<a id="encoding_with_dotnet"></a>Codifica con i servizi di supporto SDK .NET

Nell'esempio seguente viene utilizzata Media Services .NET SDK per eseguire le operazioni seguenti:

- Creare un processo di codifica.
- È possibile ottenere un riferimento al codificatore Media Encoder Standard.
- Caricare il codice XML personalizzato o JSON predefinito. È possibile salvare il file XML o JSON (ad esempio, [XML](media-services-custom-mes-presets-with-dotnet.md#xml) o [JSON](media-services-custom-mes-presets-with-dotnet.md#json) in un file, utilizzare il codice seguente per caricare il file.

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
- Aggiungere un'attività di codifica al processo. 
- Specificare la risorsa input da codificare.
- Creare una risorsa di output che contiene la risorsa codificata.
- Aggiungere un gestore eventi per controllare lo stato di avanzamento del processo.
- Inviare il processo.
    
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace CustomizeMESPresests
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
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    // Get an uploaded asset.
                    var asset = _context.Assets.FirstOrDefault();
        
                    // Encode and generate the output using custom presets.
                    EncodeToAdaptiveBitrateMP4Set(asset);
        
                    Console.ReadLine();
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
                
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText("CustomPreset_JSON.json");
                
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
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
        
                static public IAsset UploadMediaFilesFromFolder(string folderPath)
                {
                    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
        
                    foreach (var af in asset.AssetFiles)
                    {
                        // The following code assumes 
                        // you have an input folder with one MP4 and one overlay image file.
                        if (af.Name.Contains(".mp4"))
                            af.IsPrimary = true;
                        else
                            af.IsPrimary = false;
        
                        af.Update();
                    }
        
                    return asset;
                }
        
        
                static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Load the XML (or JSON) from the local file.
                    string configuration = File.ReadAllText(customPresetFileName);
        
                    // Create a task
                    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input assets to be encoded.
                    // This asset contains a source file and an overlay file.
                    task.InputAssets.Add(assetSource);
        
                    // Add an output asset to contain the results of the job. 
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
        
            }
        }


##<a name="support-for-relative-sizes"></a>Supporto per le dimensioni relative

Durante la generazione anteprime di essa, non è necessario specificare sempre output larghezza e altezza in pixel. È possibile specificarle le percentuali, nell'intervallo [1%,..., 100%].

###<a name="json-preset"></a>JSON preimpostato 
    
    "Width": "100%",
    "Height": "100%"

###<a name="xml-preset"></a>XML predefinito
    
    <Width>100%</Width>
    <Height>100%</Height>
    
##<a id="thumbnails"></a>Generare anteprime

In questa sezione viene illustrato come personalizzare un predefinito che genera anteprime. Informazioni nel quale si desidera codificare il file e le informazioni necessarie per generare le anteprime delle preimpostato definito di seguito. È possibile eseguire una del SEM predefiniti documentazione [qui](https://msdn.microsoft.com/library/mt269960.aspx) e aggiungere il codice che genera anteprime.  

>[AZURE.NOTE]L'impostazione di **SceneChangeDetection** di seguito possono preimpostato essere impostata su true se si esegue la codifica a un singolo velocità video. Se si codifica a un video con più velocità e impostare **SceneChangeDetection** su true, il codificatore viene restituito un errore.  


Per informazioni sullo schema, consultare [questo](https://msdn.microsoft.com/library/mt269962.aspx) argomento.

Assicurarsi di esaminare la sezione [Considerazioni](media-services-custom-mes-presets-with-dotnet.md#considerations) .

###<a id="json"></a>JSON preimpostato


    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
       
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a id="xml"></a>XML predefinito


    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

###<a name="considerations"></a>Considerazioni

Le considerazioni seguenti:

- L'uso di data e ora esplicita per Inizio/passaggio/intervallo si presuppone che l'origine di input lungo almeno 1 minuto.
- Elementi jpg o Png/BmpImage hanno inizio, passaggio e gli attributi della stringa di intervallo, questi possono essere interpretati come:

    - Aggiungere una cornice numero qualora siano integer non negativi, ad esempio "avviare": "120"
    - Rispetto alla durata di origine se espresso come suffisso %, ad esempio "avviare": "15%", o
    - Timestamp se espresso come hh... formato, ad esempio "avviare": "00: 01:00"

    È possibile combinare e corrispondono, il formato desiderati.
    
    Inoltre, inizio supporta anche una Macro speciale: {procedure}, che si verifica il frame "interessante" prima della nota contenuto: (passaggio e intervallo vengono ignorati quando inizio è impostato su {consigliato})
    
    - Le impostazioni predefinite: Avviare: {migliore}
- Formato di output deve essere fornito in modo esplicito per ogni formato immagine: Jpg, Png/BmpFormat. Quando si presenta, SEM corrisponde a JpgVideo a JpgFormat e così via. FormatoOutput introduce una nuova Macro specifico di immagine codec: {Index}, quali che è necessario presentano (una volta e una sola volta) per i formati immagine.

##<a id="trim_video"></a>Tagliare un video (ritaglio)

In questa sezione parla sulla modifica delle impostazioni predefinite codificatore per ritagliare o tagliare il video di ingresso nel punto in cui l'input è un file mezzanine cosiddetta o su richiesta. Il codificatore può anche essere utilizzato per ritagliare o tagliare una risorsa, acquisizione o archiviata da un flusso live: i dettagli sono disponibili in [questo blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Per tagliare i video, è possibile eseguire una del SEM predefiniti documentazione [qui](https://msdn.microsoft.com/library/mt269960.aspx) e modificare l'elemento **origini** (come illustrato di seguito). Il valore di ora di inizio deve corrispondere timestamp assoluto del video di input. Ad esempio, se il primo fotogramma del video input contiene un timestamp di 12:00:10.000, ora di inizio deve essere almeno 12:00:10.000 e versioni successive. Nell'esempio seguente, si presuppone che il video di ingresso presenta un timestamp iniziale pari a zero. **Origini** deve essere inserito all'inizio del preimpostato. 
 
###<a id="json"></a>JSON preimpostato
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1000,
              "MaxBitrate": 1000,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 650,
              "MaxBitrate": 650,
              "BufferWindow": "00:00:05",
              "Width": 640,
              "Height": 360,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 400,
              "MaxBitrate": 400,
              "BufferWindow": "00:00:05",
              "Width": 320,
              "Height": 180,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    } 

###<a name="xml-preset"></a>XML predefinito
    
Per tagliare i video, è possibile eseguire una del SEM predefiniti documentazione [qui](https://msdn.microsoft.com/library/mt269960.aspx) e modificare l'elemento **origini** (come illustrato di seguito).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

##<a id="overlay"></a>Creare una sovrapposizione

Media Encoder Standard consente di sovrapposizione un'immagine in un video esistente. Attualmente sono supportati i formati seguenti: png, jpg, gif, bmp e. Preimpostato definito di seguito è un esempio di base di una sovrapposizione video.

Oltre la definizione di un file preimpostato, è necessario comunicare servizi multimediali i file del bene è immagine sovrapposta e il file è il video di origine su cui si desidera sovrapposto l'immagine. File video deve essere file **principale** . 

Nell'esempio di .NET sopra definisce due funzioni: **UploadMediaFilesFromFolder** ed **EncodeWithOverlay**. La funzione UploadMediaFilesFromFolder il caricamento di file da una cartella (ad esempio, BigBuckBunny.mp4 e Image001.png) e imposta il file mp4 come file principale del bene. La funzione **EncodeWithOverlay** utilizza il file preimpostato personalizzato che è stato passato alla funzione (ad esempio predefinito che segue) per creare l'attività di codifica. 

>[AZURE.NOTE]Limitazioni corrente:
>
>L'impostazione di opacità sovrapposizione non è supportata.
>
>I file video di origine e il file di immagine sovrapposta devono essere bene stesso e il file video deve essere impostato come file principale in questa risorsa.

###<a name="json-preset"></a>JSON preimpostato
    
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


###<a name="xml-preset"></a>XML predefinito
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


##<a id="silent_audio"></a>Inserire un brano invisibile quando input è privo di audio

Per impostazione predefinita, se si invia un input al codificatore che contiene solo video e audio non bene output contiene file che contengono dati solo video. Alcuni lettori potrebbero non riuscire a gestire tali flussi di output. Per forzare il codificatore per aggiungere un brano invisibile per l'output in questo scenario, è possibile utilizzare questa impostazione.

Per forzare il codificatore per generare una risorsa che contiene un brano invisibile quando input è privo di audio, specificare il valore di "InsertSilenceIfNoAudio".

È possibile eseguire una del SEM predefiniti documentazione [qui](https://msdn.microsoft.com/library/mt269960.aspx)e apportare le modifiche seguenti:

###<a name="json-preset"></a>JSON preimpostato

    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

###<a name="xml-preset"></a>XML predefinito

    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

##<a id="deinterlacing"></a>Disabilitare deinterlacciamento automatico

I clienti non è necessario fare se desiderano contenuto interlacciato essere automaticamente deselezionare interlacciato. Quando l'automatico deinterlacciamento è attivato (impostazione predefinita) il SEM non il rilevamento automatico di fotogrammi interlacciati e solo interlaces deselezionare frame contrassegnati come interlacciato.

È possibile disattivare l'automatico deinterlacciamento. Questa opzione non è consigliabile.

###<a name="json-preset"></a>JSON preimpostato
    
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

###<a name="xml-preset"></a>XML predefinito
    
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


##<a id="audio_only"></a>Impostazioni predefinite solo audio

In questa sezione viene due impostazioni predefinite di SEM solo audio: Audio AAC e AAC buona qualità Audio.

###<a name="aac-audio"></a>Audio AAC 

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

###<a name="aac-good-quality-audio"></a>AAC qualità Audio

    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="concatenate"></a>Concatena due o più file video

Nell'esempio seguente viene illustrato come è possibile generare un predefinito per concatenare due o più file video. Lo scenario più comune è quando si desidera aggiungere un'intestazione o una pagina di riepilogo al video principale. La destinazione è quando i file video da modificare insieme condividono proprietà (risoluzione video, frequenza, conteggio traccia audio e così via). È necessario prestare attenzione a non per combinare video di tassi di frame diverso o con numero di tracce audio diverso.

###<a name="requirements-and-considerations"></a>Requisiti e le informazioni

- Video inpui devono contenere solo una traccia audio.
- Video inpui devono avere la stessa frequenza.
- È necessario caricare i video in distinte beni e impostare i video come file principale in ogni risorsa.
- È necessario conoscere la durata del video.
- Preimpostato esempi riportati di seguito si presuppone che tutti i video inpui iniziano con un timestamp pari a zero. È necessario modificare i valori di ora di inizio se i video dispone timestamp iniziale diverso, come avviene in genere con archivi live.
- Preimpostato JSON rende riferimenti espliciti ai valori idargomento dei beni inpui.
- Presupponendo che l'impostazione predefinita JSON è stato salvato in un file locale, ad esempio "C:\supportFiles\preset.json". Inoltre, presuppone che le attività siano state create caricando due file video e che si conoscono i valori di idargomento risultanti.
- Il frammento di codice e JSON preimpostato viene illustrato un esempio di concatenare due file video. È possibile estendere a più di due video in base a:

    1. Attività di chiamata. InputAssets.Add() più volte per aggiungere altri video, nell'ordine.
    2. Effettua corrispondente modifica all'elemento "Origini" in JSON, aggiungendo altre voci, nello stesso ordine. 


###<a name="net-code"></a>Codice .NET

    
    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();
    
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
    
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");
    
    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);
    
    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    
    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

###<a name="json-preset"></a>JSON preimpostato

Aggiornare il predefinito con ID delle attività che si desidera concatenate e con intervallo di tempo appropriato per ogni video personalizzato.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

##<a id="crop"></a>Video di ritaglio con Media Encoder Standard

Vedere l'argomento [video Ritaglia con Media Encoder Standard](media-services-crop-video.md) .

##<a id="no_video"></a>Inserire una traccia video quando input non ha video

Per impostazione predefinita, se si invia un input al codificatore che contiene solo audio e non video, le risorse di output contiene file che contengono dati solo audio. Alcuni lettori inclusa Azure Media Player (vedere [questo](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) potrebbe non riuscire a gestire tali flussi. Per forzare il codificatore per aggiungere una traccia di video in bianco e nero per l'output in questo scenario, è possibile utilizzare questa impostazione. 

>[AZURE.NOTE]Forzare il codificatore per inserire una traccia video di output aumenta la dimensione dell'output di risorse, e quindi il costo sostenuto per l'attività di codifica. È necessario eseguire test per verificare che questo aumento disponga solo un impatto minimi per le spese mensili.

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Inserimento di video solo la velocità di bit più bassi

Si supponga che si utilizza una velocità più codifica preimpostate come ["H264 velocità più 720p"](https://msdn.microsoft.com/library/mt269960.aspx) codificare l'intero catalogo input per lo streaming, che contiene una combinazione di file video e file in formato solo audio. In questo scenario, quando l'input non ha video, può essere necessario forzare il codificatore per inserire solo la velocità più bassi, anziché inserire video ogni velocità bit output una traccia di video in bianco e nero. A tale scopo, è necessario specificare il contrassegno "InsertBlackIfNoVideoBottomLayerOnly".

È possibile eseguire una del SEM predefiniti documentazione [qui](https://msdn.microsoft.com/library/mt269960.aspx)e apportare le modifiche seguenti:

#### <a name="json-preset"></a>JSON preimpostato

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML predefinito

    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideoBottomLayerOnly</Condition>

### <a name="inserting-video-at-all-output-bitrates"></a>Inserimento di video in qualsiasi output bitrate

Si supponga che si utilizza una velocità più codifica preimpostate come ["H264 velocità più 720p](https://msdn.microsoft.com/library/mt269960.aspx) codificare l'intero catalogo input per lo streaming, che contiene una combinazione di file video e file in formato solo audio. In questo scenario, quando l'input non ha video, può essere necessario forzare il codificatore per inserire una traccia di video in bianco e nero tutto bitrate output. In questo modo l'output attività sono tutti omogeneo in base al numero di tracce video e tracce audio. A tale scopo, è necessario specificare il contrassegno "InsertBlackIfNoVideo".

È possibile eseguire una del SEM predefiniti documentazione [qui](https://msdn.microsoft.com/library/mt269960.aspx)e apportare le modifiche seguenti:

#### <a name="json-preset"></a>JSON preimpostato

    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML predefinito
    
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <StretchMode>AutoSize</StretchMode>
    <Condition>InsertBlackIfNoVideo</Condition>

##<a id="rotate_video"></a>Ruotare un video

[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) supporta rotazione dagli angoli di 0/90, 180/270. Il comportamento predefinito è "Auto", in cui il tentativo di rilevare i metadati di rotazione nel file video in arrivo e compensa essa. Includere il seguente elemento **origini** a uno delle impostazioni predefinite definito [di seguito](http://msdn.microsoft.com/library/azure/mt269960.aspx):

### <a name="json-preset"></a>JSON preimpostato

    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...
### <a name="xml-preset"></a>XML predefinito

    <Sources>
        <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Vedere anche [in questo](https://msdn.microsoft.com/library/azure/mt269962.aspx#PreserveResolutionAfterRotation) argomento per ulteriori informazioni su come il codificatore interpreta le impostazioni di altezza e larghezza nell'impostazione predefinita, quando viene attivata indennizzo di rotazione.

È possibile usare il valore "0" per indicare il codificatore di ignorare i metadati di rotazione, se presenti, in video di input. 


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche 

[Panoramica codifica servizi multimediali di Microsoft](media-services-encode-asset.md)
