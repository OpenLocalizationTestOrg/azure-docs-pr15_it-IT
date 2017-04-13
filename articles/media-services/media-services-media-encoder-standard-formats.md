<properties 
    pageTitle="Formati Standard codificatore di elementi multimediali e codec" 
    description="Questo argomento fornisce una panoramica dei formati Standard codificatore di elementi multimediali e codec." 
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
    ms.date="10/10/2016"
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-standard-formats-and-codecs"></a>Codec e i formati Standard di Media Encoder


Il documento contiene un elenco di più comuni importazione / esportazione i formati di file che è possibile usare con Media Encoder Standard.


##<a name="input-containerfile-formats"></a>Formati di File/contenitore di input

Formati di file (estensioni di file)|Supportati
---|---|---|---
FLV (con codec h. 264 e AAC) (FLV)          |Sì 
MXF (.mxf)                  |Sì 
GXF (.gxf)                  |Sì 
3GP MPEG2-PS, MPEG2-TS (.ts, PS, 3GP, .3gpp, mpg)   |Sì 
Video Windows Media (WMV) / ASF (con estensione wmv, ASF) |Sì 
AVI (non compresso 8 bit/10 bit) (con estensione avi)|Sì 
MP4 (MP4, M4A, m4v) / ISMV (.isma, .ismv)|Sì 
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (dvr-ms) |Sì 
Matroska/WebM (.mkv)        |Sì 
WAVE/WAV (wav) |Sì 
QuickTime (. mov) |Sì

>[AZURE.NOTE] Sopra è riportato un elenco delle estensioni di file più comuni. Media Encoder Standard supporta molti altri (ad esempio: m2ts, .mpeg2video, qt). Se si tenta di codificare un file e viene visualizzato un messaggio di errore informazioni sul formato non è supportato, fornire un feedback [qui](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).

###<a name="audio-formats-in-input-containers"></a>Formati audio nei contenitori di input 

Media Encoder Standard supporta esecuzione i seguenti formati audio nei contenitori di input:

- MXF, GXF e QuickTime di file che hanno tracce audio con interfoliazione stereo o 5.1 esempi

o

- File MXF, GXF e QuickTime in cui l'audio viene eseguita come tracce PCM separate, ma è possibile definire il mapping di canale (a stereo o 5.1) dai metadati file

Si noti che il supporto per mappatura esplicita fornito / dall'utente sarà fornito nel prossimo futuro.


##<a name="input-video-codecs"></a>Codec Video Input

Codec Video Input|Supportati
---|---|---|---
AVC 8 bit/10 bit, fino a 4:2:2, inclusi AVCIntra   |8 bit 4:2:0 e 4:2:2 
Avid DNxHD (in MXF)                                 |Sì 
DVCPro/DVCProHD (in MXF)                            |Sì 
Video digitale (DV) (nei file AVI)                   |Sì
2000 JPEG                                           |Sì 
MPEG-2 (fino a 422 profilo e di alto livello, comprese le varianti, ad esempio XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Fino a 422 profilo 
MPEG-1                                              |Sì 
VC-1/FORMATO WMV9                                           |Sì 
Canopus sede centrale/HQX                                      |No 
MPEG-4 parte 2                                       |Sì 
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sì 
YUV420 non compressi o mezzanine                   |Sì
Apple ProRes 422                                    |Sì
Apple ProRes 422 LT |Sì
Sede centrale di Apple ProRes 422 |Sì
Apple ProRes Proxy|Sì
Apple ProRes 4444 |Sì
Apple ProRes 4444 XQ |Sì



##<a name="input-audio-codecs"></a>Codec Audio input

Codec Audio input|Supportati
---|---|---|---
AAC (AAC-LC, HE AAC e AAC-HEv2; fino a 5.1)|Sì 
MPEG Layer 2|Sì 
MP3 (1 MPEG Audio Layer 3)|Sì 
Windows Media Audio|Sì 
WAV/PCM|Sì 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sì 
[Sono state](http://go.microsoft.com/fwlink/?LinkId=822667) |Sì 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sì 
AMR (adattata più velocità)|Sì
AES (SMPTE 331 M e 302 M, AES3-2003)        |No 
Dolby® E                                    |No 
Dolby® digitale (AC3)                        |No 
Digital Dolby® Plus (E AC3)                 |No 


##<a name="output-formats-and-codecs"></a>Formati di output e codec

La tabella seguente elenca i formati di codec e i file sono supportati per l'esportazione.


Formato di file|Codec video|Codec audio
---|---|---
MP4 <br/><br/>(inclusi i contenitori MP4 multi-velocità) |H. 264 (alta, principali e i profili di previsione)|AAC-LC, HE-AAC v1, HE-AAC v2 
SERVIZI TERMINAL MPEG2 |H. 264 (alta, principali e i profili di previsione)|AAC-LC, HE-AAC v1, HE-AAC v2 



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche

[Codifica contenuto su richiesta con i servizi multimediali di Azure](media-services-encode-asset.md)

[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)
