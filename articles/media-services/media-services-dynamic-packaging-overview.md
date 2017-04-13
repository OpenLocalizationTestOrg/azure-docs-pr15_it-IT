<properties
    pageTitle="Panoramica di imballaggio dinamico | Microsoft Azure"
    description="Argomento offre e panoramica di imballaggio dinamico."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>Salvataggio di un pacchetto dinamico

##<a name="overview"></a>Panoramica

Formati di protezione del contenuto a una serie di tecnologie client e servizi multimediali di Microsoft Azure può essere utilizzati per recapitare i formati di file molti origine media, i flussi di formati multimediali (ad esempio, iOS, XBOX, Silverlight, Windows 8). Questi client comprendere protocolli diversi, ad esempio iOS richiede un formato V4 HTTP Live Streaming (HLS) e Silverlight e Xbox richiedono Smooth Streaming. Se si dispone di un insieme di velocità adattata (multi-velocità) MP4 (supporto di Base ISO 14496-12) file o una serie di file Smooth Streaming di velocità adattata che si desidera utilizzare per i client che supportano trattino MPEG, HLS o Smooth Streaming, è necessario sfruttare imballaggio dinamico servizi multimediali.

Con imballaggio dinamico è sufficiente consiste nel creare una risorsa che contiene un insieme di file Smooth Streaming velocità adattata o adattata velocità MP4. Quindi, in base al formato specificato nella richiesta manifesto o frammento di Streaming via richiesta server garantisce ricevere il flusso del protocollo scelto. Di conseguenza, è sufficiente archiviare e pagare per i file nel formato di archiviazione e la compilazione e fornire la risposta appropriata in base a richieste da un client di servizi Media.

Il diagramma seguente illustra la codifica tradizionale e flusso di lavoro statico imballaggio.

![Codifica statico](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Il diagramma seguente illustra il flusso di lavoro dinamico imballaggio.

![Codifica dinamico](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Per sfruttare imballaggio dinamico, è prima necessario ottenere almeno un'unità di trasmissione su richiesta per l'endpoint flusso dal quale si prevede di recapito del contenuto. Per ulteriori informazioni, vedere [come servizi multimediali di scala](media-services-portal-manage-streaming-endpoints.md).

##<a name="common-scenario"></a>Scenari comuni

1. Caricare un file di input (denominato file mezzanine). Ad esempio, h. 264, MP4 o WMV (per l'elenco di formati supportati, vedere [Formati supportati da Media Encoder Standard](media-services-media-encoder-standard-formats.md).

1. Codificare il file mezzanine ai set di velocità adattata h. 264 MP4.

1. Pubblicare la risorsa che contiene la velocità adattata MP4 impostare creando Locator su richiesta.

1. Creare gli URL di trasmissione per accedere e flusso del contenuto.


##<a name="preparing-assets-for-dynamic-streaming"></a>Preparazione risorse per lo streaming dinamico

Per preparare le risorse per lo streaming dinamico sono disponibili due opzioni:

1. [Caricare un file master](media-services-dotnet-upload-files.md).
2. [Utilizzare il codificatore Media Encoder Standard per produrre set di velocità adattata h. 264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Flusso del contenuto](media-services-deliver-content-overview.md).


##<a id="unsupported_formats"></a>Formati non supportati da imballaggio dinamico

I seguenti formati di file di origine non supportati da imballaggio dinamico.

- File di Dolby digital mp4.
- Dolby digital smussate i file.

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
