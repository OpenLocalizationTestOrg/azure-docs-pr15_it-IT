<properties 
    pageTitle="Formati di flusso di lavoro Media Encoder Premium e codec | Microsoft Azure" 
    description="In questo argomento fornisce una panoramica dei formati di formati di flusso di lavoro Premium codificatore multimediali e codec" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erik43" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako;anilmur"/>

#<a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formati di flusso di lavoro Media Encoder Premium e codec


>[AZURE.NOTE]Per eventuali domande codificatore premium, inviare tramite posta elettronica mepd per il sito Microsoft.com.
>
>Processore media del flusso di lavoro di Media Encoder Premium descritte in questo argomento non è disponibile in Cina. 

Il documento contiene un elenco di input e formati di file di output e codec supportati per la versione di anteprima pubblica di codificatore di **Flusso di lavoro Media Encoder Premium** .

[Media Encoder Premium Worflow Input formati e codec](#input_formats)

[Formati di Output Worflow Premium codificatore multimediali e codec](#output_formats)

**Flusso di lavoro Media Encoder Premium** supporta sottotitoli descritte in [questa](#closed_captioning) sezione. 


##<a id="input_formats"></a>Flusso di lavoro Media Encoder Premium Input formati e codec

La sezione seguente elenca i formati di file e codec supportati questo processore media come input.

###<a name="input-containerfile-formats"></a>Formati di File/contenitore di input

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Flussi di trasporto MPEG-2
- Flussi di programmi MPEG-2
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compresso 8 bit/10 bit)

###<a name="input-video-codecs"></a>Codec Video Input

- AVC 8 bit/10 bit, fino a 4:2:2, inclusi AVCIntra
- Avid DNxHD (in MXF)
- DVCPro/DVCProHD (in MXF)
- JPEG2000
- MPEG-2 (fino a 422 profilo e di alto livello, comprese le varianti, ad esempio XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1

###<a name="input-audio-codecs"></a>Codec Audio input

- AES (SMPTE 331 M e 302 M, AES3-2003)
- Dolby® E
- Dolby® digitale (AC3)
- AAC (AAC-LC, HE AAC e AAC-HEv2; fino a 5.1)
- MPEG Layer 2
- MP3 (1 MPEG Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Formati di Output del flusso di lavoro Premium codificatore elementi multimediali e codec

La sezione seguente elenca i formati codec e i file che sono supportati come output da questo processore media.

###<a name="output-containerfile-formats"></a>Formati di File/contenitore di output

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM e AS02)
- DPP (inclusi AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (non compresso 8 bit/10 bit)
- File formato di Smooth Streaming (PIFF 1.3)
- MPEG-TS 


###<a name="output-video-codecs"></a>Codec Video di output

- AVC (h. 264; 8 bit; fino a alto profilo livellare 5.2; 4 K Ultra HD. All'interno di AVC)
- Avid DNxHD (in MXF)
- DVCPro/DVCProHD (in MXF)
- MPEG-2 (fino a 422 profilo e di alto livello, comprese le varianti, ad esempio XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
- MPEG-1
- Windows Media Video/VC-1
- Creazione delle miniature JPEG

###<a name="output-audio-codecs"></a>Codec Audio di output

- AES (SMPTE 331 M e 302 M, AES3-2003)
- Dolby® digitale (AC3)
- Dolby® Digital Plus (E AC3) fino a 7.1
- AAC (AAC-LC, HE AAC e AAC-HEv2; fino a 5.1)
- MPEG Layer 2
- MP3 (1 MPEG Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Supporto per i sottotitoli

Via l'acquisizione, **Del flusso di lavoro di Media Encoder Premium** supporta:

1. File di controllo del codice sorgente
1. File TT SMPTE
1. CEA-608/CEA-708 – riportati come dati utente (messaggi caso di h. 264 scuola elementari flussi ATSC/53, SCTE20) o come Accessori dati nei file MXF/GXF
1. File STL da sinistra a destra

Nell'output, sono disponibili le opzioni seguenti:

1. CEA 608 alla CEA 708 traduzione
1. CEA 608/CEA 708 attraversano (incorporati nei messaggi caso dei flussi di scuola elementari h. 264 o trasferiti dati come Accessori in file MXF)
1. CONTROLLO DEL CODICE SORGENTE
1. Testo di tempo SMPTE (dall'origine CEA 608 per SMPTE RP2052; inclusa la creazione di file DFXP)
1. File di ordinare da sinistra a destra
1. Flussi DVB da sinistra a destra

Nota: non tutti i formati di output indicati sono supportati per il recapito tramite streaming in servizi multimediali di Windows Azure.

##<a name="known-issues"></a>Problemi noti

Se il video di input non contiene sottotitoli codificati, l'output bene ancora conterrà un file TTML vuoto. 


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
