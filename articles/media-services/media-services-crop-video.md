<properties
    pageTitle="Come ritagliare video | Microsoft Azure"
    description="In questo articolo viene illustrato come ritagliare video con Media Encoder Standard."
    services="media-services"
    documentationCenter=""
    authors="anilmur"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"  
    ms.author="anilmur;juliako;"/>

# <a name="crop-videos-with-media-encoder-standard"></a>Video di ritaglio con Media Encoder Standard

È possibile usare Media Encoder Standard (SEM) per ritagliare l'input video. Ritaglio è il processo di selezione di una finestra rettangolare all'interno della cornice video e codifica solo i pixel all'interno di tale finestra. Nel diagramma seguente consente di illustrare il processo.

![Ritagliare un video](./media/media-services-crop-video/media-services-crop-video01.png)

Si supponga di che disporre come input un video che ha una risoluzione di 1920x1080 pixel (proporzioni 16:9), ma nere (elemento chiave caselle) alla sinistra e a destra, in modo che solo una finestra di 4:3 o 1440x1080 pixel contiene video attivo. È possibile utilizzare SEM per ritagliare o modificare le barre nere e codificare l'area geografica 1440x1080.

Ritaglio in SEM è una fase pre-elaborazione, in modo che i parametri di ritaglio nel preimpostato codifica applicano al video input originale. Codifica è una fase successiva e le impostazioni di larghezza/altezza si applicano per la *pre-elaborazione* video e non per il video originale. Quando si progetta il predefinito è necessario eseguire le operazioni seguenti: (a) selezionare i parametri di ritaglio in base a video input originale e (b) il video ritagliato in base alle impostazioni di codifica. Se non corrispondono alle impostazioni per il video ritagliata di codifica, l'output non saranno come previsto.

Il [seguente](media-services-advanced-encoding-with-mes.md#encoding_with_dotnet) argomento viene illustrato come creare un processo di codifica con SEM e come specificare un predefinito per l'attività di codifica personalizzato. 

## <a name="creating-a-custom-preset"></a>Creazione di un predefinito personalizzato

Nell'esempio illustrato nella figura:

1. Input originale è 1920x1080
1. È necessario essere ritagliata per l'output di 1440x1080, che vengono centrati nella cornice di input
1. Questo errore indica un offset X pari (1920-1440) / 2 = 240 e Y offset pari a zero
1. La larghezza e l'altezza del rettangolo di ritaglio sono 1440 e 1080, rispettivamente
1. Nella fase codifica fai consiste nel generare tre livelli, sono riportate le risoluzioni 1440x1080, 960 x 720 e 480 x 360, rispettivamente

###<a name="json-preset"></a>JSON preimpostato


    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


##<a name="restrictions-on-cropping"></a>Restrizioni sul ritaglio

La caratteristica di ritaglio deve essere manuale. È necessario caricare il video di input in uno strumento di modifica appropriato che consente di selezionare cornici di interesse, posizionare il cursore per determinare l'offset per il rettangolo di ritaglio, per determinare il codifica predefinito che ottimizzato per quel particolare e così via video e. Questa caratteristica non deve attivare elementi quali: rilevamento automatico e la rimozione dei bordi nero formato lettera/formato pillarbox di immissione dati video.

Vincoli seguenti si applicano alla caratteristica di ritaglio. Se non si è soddisfatti, la codifica attività può non riuscire o produrre un output non previsto.

1. Le coordinate e le dimensioni del rettangolo di ritaglio devono corrispondere all'interno del video di input
1. Come detto in precedenza, la larghezza e altezza nelle impostazioni di codifica deve corrispondere al video ritagliato
1. Ritaglio si applica a video acquisiti in modalità orizzontale (ad esempio, non è applicabile al video registrati con uno smartphone contenute in verticale o orientamento verticale)
1. Risultati ottimali con video graduale acquisita con pixel quadrati

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Passaggio successivo
 
Vedere Azure servizi multimediali di percorsi formativi per informazioni sulle caratteristiche offerte da AMS.  

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
