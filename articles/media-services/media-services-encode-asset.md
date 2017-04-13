<properties 
    pageTitle="Panoramica e il confronto di Azure su Encoder media richiesta | Microsoft Azure" 
    description="Questo argomento fornisce una panoramica e il confronto di Azure su Encoder media richiesta." 
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
    ms.author="juliako"/>

#<a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Panoramica e il confronto di Azure su Encoder media richiesta

##<a name="encoding-overview"></a>Panoramica di codifica

Servizi multimediali di Azure fornisce più opzioni per la codifica di file multimediali nel cloud.

Quando si avvia con i servizi di supporto, è importante conoscere la differenza tra i formati codec e file.
Codec sono software che implementa algoritmi compressione/decompressione mentre in formati di file sono contenitori di video compresso.

Servizi multimediali di fornisce imballaggio dinamico che consente di distribuire il contenuto di velocità adattata MP4 o Smooth Streaming codificato in streaming formati supportati da servizi multimediali (trattino MPEG, HLS, Smooth Streaming, unità 10K) senza che sia necessario creare un nuovo pacchetto in questi formati di flusso.

Per sfruttare [imballaggio dinamico](media-services-dynamic-packaging-overview.md), è necessario eseguire le operazioni seguenti:

- Codificare il file mezzanine (origine) in un set di velocità adattata MP4 file o velocità adattata Smooth Streaming (codifica passaggi illustrati più avanti in questa esercitazione).
- È possibile ottenere almeno un'unità di trasmissione su richiesta per l'endpoint flusso dal quale si prevede di recapito del contenuto. Per ulteriori informazioni, vedere [come riservato unità scala Streaming su richiesta](media-services-portal-manage-streaming-endpoints.md).

Servizi multimediali di supporta le operazioni seguenti nel Encoder richiesta descritti in questo articolo:

- [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
- [Flusso di lavoro Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

In questo articolo offre una breve panoramica su richiesta Encoder media e vengono forniti collegamenti ad articoli che forniscono informazioni più dettagliate. L'argomento vengono forniti anche confronto codificatore di.

Si noti che per impostazione predefinita ogni account servizi multimediali può avere una sola attività di codifica attiva alla volta. È possibile prenotare unità codifica che consentono di avere più attività codifica contemporaneamente in esecuzione, uno per ogni unità riservato codifica che si acquista. Per informazioni, vedere [proporzioni codifica unità](media-services-scale-media-processing-overview.md).

##<a name="media-encoder-standard"></a>Media Encoder Standard

###<a name="how-to-use"></a>Come usare

[Come codificare con Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

###<a name="formats"></a>Formati

[Formati e codec](media-services-media-encoder-standard-formats.md)

###<a name="presets"></a>Impostazioni predefinite

Media Encoder Standard viene configurato utilizzando uno del codificatore predefiniti descritti [di seguito](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadati di input e di output

I metadati di input Encoder descritto [di seguito](http://msdn.microsoft.com/library/azure/dn783120.aspx).

I metadati di output Encoder descritto [di seguito](http://msdn.microsoft.com/library/azure/dn783217.aspx).

###<a name="generate-thumbnails"></a>Generare anteprime

Per informazioni, vedere [come generare anteprime utilizza Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#thumbnails).

###<a name="trim-videos-clipping"></a>Tagliare video (ritaglio)

Per informazioni, vedere [come Taglia video tramite Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#trim_video).

###<a name="create-overlays"></a>Creare sovrapposizioni

Per informazioni, vedere [come creare sovrapposizioni utilizza Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md#overlay).

###<a name="see-also"></a>Vedere anche

[Blog servizi multimediali](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##<a name="media-encoder-premium-workflow"></a>Flusso di lavoro Media Encoder Premium

###<a name="overview"></a>Panoramica

[Introduzione a Premium codifica in Media Azure servizi](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

###<a name="how-to-use"></a>Come usare

Flusso di lavoro Media Encoder Premium viene configurato tramite flussi di lavoro complessi. File di flusso di lavoro è possibile creare e aggiornati tramite lo strumento di [Progettazione flussi di lavoro](media-services-workflow-designer.md) .

[Come usare Premium codifica in Media Azure servizi](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

###<a name="known-issues"></a>Problemi noti

Se il video di input non contiene sottotitoli codificati, l'output bene ancora conterrà un file TTML vuoto. 


##<a id="compare_encoders"></a>Confronto Encoder

###<a id="billing"></a>Contatore utilizzato da ogni codificatore di fatturazione

Nome processore elementi multimediali|Prezzi applicabile|Note
---|---|---
**Media Encoder Standard** |CODIFICATORE|Codifica attività verrà addebitata in base alle dimensioni di output della risorsa della GB, la frequenza specificata [qui][1], nella colonna CODIFICATORE.
**Flusso di lavoro Media Encoder Premium** |CODIFICATORE PREMIUM|Codifica attività verrà addebitata in base alle dimensioni di output della risorsa della GB, la frequenza specificata [qui][1], nella colonna CODIFICATORE PREMIUM.


In questa sezione consente di confrontare le funzionalità di codifica di **Media Encoder Standard** e **Del flusso di lavoro di Media Encoder Premium**.


###<a name="input-containerfile-formats"></a>Formati di File/contenitore di input

Formati di File/contenitore di input|Media Encoder Standard|Flusso di lavoro Media Encoder Premium
---|---|---
Adobe® Flash® F4V           |Sì|Sì
MXF/SMPTE 377M              |Sì|Sì
GXF                         |Sì|Sì
Flussi di trasporto MPEG-2    |Sì|Sì
Flussi di programmi MPEG-2      |Sì|Sì
MPEG-4/MP4                  |Sì|Sì
Windows Media/ASF           |Sì|Sì
AVI (non compresso 8 bit/10 bit)|Sì|Sì
3GPP/3GPP2                  |Sì|No
File formato di Smooth Streaming (PIFF 1.3)|Sì|No
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sì|No
Matroska/WebM               |Sì|No
QuickTime (. mov) |Sì|No

###<a name="input-video-codecs"></a>Codec Video Input

Codec Video Input|Media Encoder Standard|Flusso di lavoro Media Encoder Premium
---|---|---
AVC 8 bit/10 bit, fino a 4:2:2, inclusi AVCIntra   |8 bit 4:2:0 e 4:2:2|Sì
Avid DNxHD (in MXF)                                 |Sì|Sì
DVCPro/DVCProHD (in MXF)                            |Sì|Sì
JPEG2000                                            |Sì|Sì
MPEG-2 (fino a 422 profilo e di alto livello, comprese le varianti, ad esempio XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|Fino a 422 profilo|Sì
MPEG-1                                              |Sì|Sì
Windows Media Video/VC-1                            |Sì|Sì
Canopus sede centrale/HQX                                      |No|No
MPEG-4 parte 2                                       |Sì|No
[Theora](https://en.wikipedia.org/wiki/Theora)      |Sì|No
Apple ProRes 422    |Sì|No
Apple ProRes 422 LT |Sì|No
Sede centrale di Apple ProRes 422 |Sì|No
Apple ProRes Proxy|Sì|No
Apple ProRes 4444 |Sì|No
Apple ProRes 4444 XQ |Sì|No

###<a name="input-audio-codecs"></a>Codec Audio input

Codec Audio input|Media Encoder Standard|Flusso di lavoro Media Encoder Premium
---|---|---
AES (SMPTE 331 M e 302 M, AES3-2003)        |No|Sì
Dolby® E                                    |No|Sì
Dolby® digitale (AC3)                        |No|Sì
Digital Dolby® Plus (E AC3)                 |No|Sì
AAC (AAC-LC, HE AAC e AAC-HEv2; fino a 5.1)|Sì|Sì
MPEG Layer 2|Sì|Sì
MP3 (1 MPEG Audio Layer 3)|Sì|Sì
Windows Media Audio|Sì|Sì
WAV/PCM|Sì|Sì
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sì|No
[Sono state](https://en.wikipedia.org/wiki/Opus_(audio_format)) |Sì|No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sì|No


###<a name="output-containerfile-formats"></a>Formati di File/contenitore di output

Formati di File/contenitore di output|Media Encoder Standard|Flusso di lavoro Media Encoder Premium
---|---|---
Adobe® Flash® F4V|No|Sì
MXF (OP1a, XDCAM e AS02)|No|Sì
DPP (inclusi AS11)|No|Sì
GXF|No|Sì
MPEG-4/MP4|Sì|Sì
MPEG-TS|Sì|Sì
Windows Media/ASF|No|Sì
AVI (non compresso 8 bit/10 bit)|No|Sì
File formato di Smooth Streaming (PIFF 1.3)|No|Sì

###<a name="output-video-codecs"></a>Codec Video di output

Codec Video di output|Media Encoder Standard|Flusso di lavoro Media Encoder Premium
---|---|---
AVC (h. 264; 8 bit; fino a alto profilo livellare 5.2; 4 K Ultra HD. All'interno di AVC)|Solo 8 bit 4:2:0|Sì
Avid DNxHD (in MXF)|No|Sì
MPEG-2 (fino a 422 profilo e di alto livello, comprese le varianti, ad esempio XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)|No|Sì
MPEG-1|No|Sì
Windows Media Video/VC-1|No|Sì
Creazione delle miniature JPEG|No|Sì

###<a name="output-audio-codecs"></a>Codec Audio di output

Codec Audio di output|Media Encoder Standard|Flusso di lavoro Media Encoder Premium
---|---|---
AES (SMPTE 331 M e 302 M, AES3-2003)|No|Sì
Dolby® digitale (AC3)|No|Sì
Dolby® Digital Plus (E AC3) fino a 7.1|No|Sì
AAC (AAC-LC, HE AAC e AAC-HEv2; fino a 5.1)|Sì|Sì
MPEG Layer 2|No|Sì
MP3 (1 MPEG Audio Layer 3)|No|Sì
Windows Media Audio|No|Sì


##<a name="error-codes"></a>Codici di errore  

Nella tabella seguente sono elencati i codici di errore che potrebbero essere restituiti nel caso in cui si è verificato un errore durante l'esecuzione di attività codifica.  Per ottenere dettagli sull'errore nel codice .NET, utilizzare la classe [ErrorDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) . Per ottenere dettagli sull'errore nel codice resto, utilizzare l'API REST [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) .

ErrorDetail.Code|Le possibili cause dell'errore
-----|-----------------------
Sconosciuto| Errore sconosciuto durante l'esecuzione dell'attività
ErrorDownloadingInputAssetMalformedContent|Categoria di errore che copra gli errori di download input risorse, ad esempio nomi di file non valido, zero file di lunghezza, non corretti formati e così via.
ErrorDownloadingInputAssetServiceFailure|Categoria di errore che descrive i problemi sul lato del servizio - gli errori di rete o lo spazio di archiviazione di esempio durante il download.
ErrorParsingConfiguration|Categoria di errore in cui attività <see cref="MediaTask.PrivateData"/> (configurazione) non è valida, ad esempio la configurazione non predefinito di un sistema valido o contiene XML non valido.
ErrorExecutingTaskMalformedContent|Categoria di errore durante l'esecuzione di attività in cui problemi all'interno dei file multimediali input causano errori.
ErrorExecutingTaskUnsupportedFormat|Categoria degli errori nel punto in cui il processore media non è possibile elaborare i file disponibili - formato media non è supportata o non corrisponde alla configurazione. Ad esempio si tenta di produrre un output solo audio da un'attività che contiene solo video
ErrorProcessingTask|Categoria di altri errori di processore media rileva durante l'elaborazione dell'attività non correlate al contenuto.
ErrorUploadingOutputAsset|Categoria di errore durante il caricamento di risorse output
ErrorCancelingTask|Categoria degli errori per coprire errori durante il tentativo di annullare l'operazione
TransientError|Categoria degli errori per coprire problemi temporanei (ad esempio. problemi temporanei di rete con lo spazio di archiviazione di Azure)


Per ottenere assistenza dal team di **Servizi multimediali** , aprire un [ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-articles"></a>Articoli correlati

- [Eseguire operazioni di codifica avanzate personalizzando Media Encoder Standard predefiniti](media-services-custom-mes-presets-with-dotnet.md)
- [Le quote e limitazioni](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
