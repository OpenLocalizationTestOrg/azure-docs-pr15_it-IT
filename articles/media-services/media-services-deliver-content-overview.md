<properties
    pageTitle="La distribuzione di contenuti per i clienti | Microsoft Azure"
    description="In questo argomento fornisce una panoramica di approfondite l'esecuzione di contenuto con servizi multimediali di Windows Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
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


# <a name="deliver-content-to-customers"></a>Distribuire il contenuto ai clienti
Durante l'esecuzione del contenuto di flusso o punti per i clienti, l'obiettivo è fornire video di alta qualità a vari dispositivi in condizioni di rete diversa.

A tale scopo, è possibile:

- Codificare il flusso di un flusso di video multi-velocità (velocità adattata). Verrà occuparsi di condizioni di qualità e di rete.
- Utilizzare servizi multimediali di Microsoft Azure [imballaggio dinamico](media-services-dynamic-packaging-overview.md) per dinamicamente assemblare nuovamente il flusso in protocolli diversi. Verrà occuparsi di streaming su dispositivi diversi. Servizi multimediali di supporta il recapito della velocità di adattamento seguenti streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG-tratto e unità 10K (per solo licenziatari a questo punto Adobe/accesso).

In questo articolo viene fornita una panoramica dei concetti importanti contenuti.

Per verificare se problemi noti, vedere [problemi noti](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Salvataggio di un pacchetto dinamico

Con imballaggio dinamico che fornisce servizi multimediali, è possibile distribuire il contenuto di velocità adattata MP4 o Smooth Streaming codificato in streaming formati supportati da servizi multimediali (MPEG-tratto HLS, Smooth Streaming, unità 10K) senza la necessità di creare un nuovo pacchetto in questi formati di flusso. È consigliabile l'esecuzione di contenuto con imballaggio dinamico.

Per sfruttare imballaggio dinamico, è necessario eseguire le operazioni seguenti:

- Codificare il file mezzanine (origine) in un set di file Smooth Streaming velocità adattata o MP4 adattata velocità.
- È possibile ottenere almeno un'unità di trasmissione su richiesta per l'endpoint flusso da cui si intende distribuire il contenuto. Per ulteriori informazioni, vedere [come scala flusso unità riservate su richiesta](media-services-portal-manage-streaming-endpoints.md).

Con dinamico imballaggio, archiviare e pagare per i file nel formato di memorizzazione singola. Servizi multimediali di verrà creare e gestire la risposta appropriata in base alle richieste.

Oltre a fornire l'accesso alle funzionalità di imballaggio dinamiche, flusso unità riservate su richiesta offrono capacità di uscita dedicato che può essere acquistata con incrementi di 200 MB/s. Per impostazione predefinita, streaming su richiesta è configurato in un modello di istanza condivisa per il server risorse (ad esempio, la capacità di calcolo o in uscita) vengono condivisi con tutti gli altri utenti. È possibile migliorare una velocità di trasmissione su richiesta acquistando unità riservata flusso su richiesta.

Per ulteriori informazioni, vedere [salvataggio di un pacchetto dinamico](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtri e manifesti dinamici

È possibile definire filtri per le risorse con i servizi di supporto. Questi filtri vengono regole lato server che consentono ai clienti di eseguire operazioni come riprodurre una sezione specifica di un video o specificare un sottoinsieme di rendering audio e video in grado di gestire dispositivi del cliente (invece di tutti i rendering associate bene). Questo filtro viene ottenuto tramite *manifesti dinamici* creati quando il cliente richiede il flusso di un video in base a uno o più filtri specificati.

Per ulteriori informazioni, vedere [filtri e manifesti dinamici](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a>Indicatori

Per fornire all'utente con un URL che può essere utilizzato per flusso o il download del contenuto, è innanzitutto necessario pubblicare le risorse mediante la creazione di un indicatore di posizione. Un indicatore di posizione fornisce un punto di ingresso per accedere ai file contenuti in una risorsa. Servizi multimediali sono supportati due tipi di indicatori:

- Indicatori di OnDemandOrigin. Questi vengono utilizzati per il flusso multimediale (ad esempio MPEG-tratto, HLS o Smooth Streaming) o gradualmente scaricare i file.
-  Indicatori di URL di accesso condiviso della firma (SA). Questi vengono utilizzati per scaricare i file multimediali nel computer locale.

Un *criterio di accesso* viene utilizzato per definire le autorizzazioni (ad esempio, lettura, scrittura ed elenco) e la durata per cui un client abbia accesso per una particolare attività. Si noti che l'autorizzazione di elenco (AccessPermissions.List) non deve essere utilizzato per la creazione di un indicatore di posizione OrDemandOrigin.

Indicatori le date di scadenza. Portale di Azure imposta una data di scadenza 100 anni in futuro per Locator.

>[AZURE.NOTE] Se il portale di Azure sono usate per creare indicatori prima di marzo 2015, tali indicatori impostati in modo che scadano dopo due anni.

Per aggiornare la data di scadenza su un indicatore di posizione, usare [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) o API di [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) . Nota Quando si aggiorna la data di scadenza di un indicatore di posizione SA, l'URL viene modificato.

Indicatori di posizione non sono progettati per gestire il controllo di accesso per utente. È possibile assegnare autorizzazioni di accesso diverse per i singoli utenti tramite le soluzioni di Digital Rights Management (DRM). Per ulteriori informazioni, vedere [Supporto di protezione](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando si crea un indicatore di posizione, potrebbero esserci un ritardo di 30 secondi a causa di memorizzazione richiesta e processi propagazione in archiviazione Azure.


## <a name="adaptive-streaming"></a>Adattata streaming

Tecnologie di velocità adattata consentono alle applicazioni di lettore di video determinare le condizioni di rete e quindi selezionare da bitrate diverse. Quando si riducono comunicazioni di rete, il client può selezionare una velocità inferiore in modo che la riproduzione è possibile continuare a bassa qualità video. Come migliorare le condizioni di rete, il client passare a una velocità maggiore con migliore qualità video. Servizi multimediali di Azure supporta le seguenti tecnologie di velocità adattata: HTTP Live Streaming (HLS), Smooth Streaming, MPEG-tratto e unità 10K.

Per offrire agli utenti con l'URL di flusso, è necessario creare un indicatore di posizione OnDemandOrigin. La creazione di locator consente il percorso di base per la risorsa che contiene il contenuto che si desidera eseguire il flusso. Tuttavia, per poter eseguire il flusso di contenuto, è necessario modificare ulteriormente il percorso. Per creare un URL completo al file manifesto streaming, è necessario concatenare valore path del locator e manifesto (filename.ism) nome file. **Aggiungere/manifesto** e un formato appropriato, se necessario, il percorso locator.

>[AZURE.NOTE]È anche possibile trasmettere il contenuto in una connessione SSL. A tale scopo, assicurarsi che l'URL di trasmissione iniziare con HTTPS.

È possibile trasmettere solo tramite SSL, se è stato creato l'endpoint di trasmissione in cui i contenuti dopo 10 settembre 2014. Se l'URL di trasmissione si basano sui punti finali flussi creati dopo 10 settembre 2014, l'URL contiene "streaming.mediaservices.windows.net". URL di trasmissione che contengono "origin.mediaservices.windows.net" (il formato precedente) non supportano SSL. Se l'URL è nel vecchio formato e si desidera trasmettere su SSL, creare un nuovo endpoint di trasmissione. Utilizzare gli URL in base a nuovo endpoint di flusso per lo streaming del contenuto su SSL.


## <a name="streaming-url-formats"></a>Formati di URL di flusso

### <a name="mpeg-dash-format"></a>Formato MPEG-tratto

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=mpd-Time-CSF)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato V4 Apple HTTP Live Streaming (HLS)

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato V3 Apple HTTP Live Streaming (HLS)

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-AAPL-V3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato Apple HTTP Live Streaming (HLS) con solo audio filtro

Per impostazione predefinita, le tracce solo audio sono incluse nel HLS manifesto. Questa operazione è necessaria certificazione Apple Store per reti cellulari. In questo caso, se un client non dispone di larghezza di banda sufficiente o è connesso tramite una connessione 2G, riproduzione passa alla solo audio. In questo modo le streaming del contenuto senza buffer, ma non viene visualizzato alcun video. In alcuni casi, il buffer player potrebbe essere preferito solo audio. Se si desidera rimuovere la traccia solo audio, aggiungere **solo audio = false** all'URL.

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-AAPL-v3,audio-only=false)

Per ulteriori informazioni, vedere [caratteristiche aggiuntive di output di supporto per la composizione dinamica manifesto e HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).


### <a name="smooth-streaming-format"></a>Formato Smooth Streaming

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Esempio:

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Manifesto Streaming 2.0 smussata (manifesto legacy)

Per impostazione predefinita, Smooth Streaming manifesto formato contiene tag ripetizione (r-tag). Tuttavia, alcuni lettori non supportano il tag r. I clienti con i lettori possono utilizzare un formato che disabilita il contrassegno r:

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>Unità 10K (per solo licenziatari a questo punto Adobe/Access)

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>Download graduale

Tramite download graduale, è possibile iniziare la riproduzione multimediale prima di tutto il file è stato scaricato. È possibile scaricare gradualmente ISM * (ismv, isma, ismt o ismc) file.

Per scaricare gradualmente contenuto, utilizzare il tipo di OnDemandOrigin di locator. Nell'esempio seguente mostra l'URL è in base al tipo di OnDemandOrigin di locator:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

È necessario decrittografare tutte le risorse crittografate lo spazio di archiviazione che si desidera eseguire il flusso dal servizio di origine per il download graduale.

## <a name="download"></a>Download

Per scaricare il contenuto a un dispositivo client, è necessario creare un indicatore di posizione SA. Locator SA consente di accedere al contenitore di archiviazione Azure in cui si trova il file. Per creare l'URL di download, è necessario incorporare il nome del file tra l'host e firma SA.

Nell'esempio seguente mostra l'URL che si basa su locator SA:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

Le considerazioni seguenti:

- È necessario decrittografare tutte le risorse crittografate lo spazio di archiviazione che si desidera eseguire il flusso dal servizio di origine per il download graduale.
- Non viene eseguito il download non è stata completata entro 12 ore.

## <a name="streaming-endpoints"></a>Punti finali flussi

Un endpoint flusso rappresenta un servizio di trasmissione in grado di fornire contenuto direttamente a un'applicazione di Windows Media player client o a una rete di distribuzione di contenuti (CDN) per la distribuzione. Il flusso in uscita da un servizio endpoint flusso può essere un flusso in diretta o una risorsa di punti nel proprio account di servizi multimediali. È anche possibile controllare la capacità del servizio di trasmissione endpoint per gestire l'aumento della larghezza di banda esigenze regolando streaming unità riservata. È consigliabile allocare almeno un'unità riservata per le applicazioni in un ambiente di produzione. Per ulteriori informazioni, vedere [come ridimensionare un servizio di supporto](media-services-portal-manage-streaming-endpoints.md).

## <a name="known-issues"></a>Problemi noti

### <a name="changes-to-smooth-streaming-manifest-version"></a>Le modifiche apportate ai Smooth Streaming manifesto versione

Prima del rilascio di servizio 2016 luglio - quando beni prodotti da Media Encoder Standard Media Encoder Premium flusso di lavoro o la Media Encoder di Azure precedenti sono stati trasmessi utilizzando imballaggio dinamico - Smooth Streaming manifesto restituito da adottare versione 2.0. Nella versione 2.0, le durate frammento non utilizzano tag cosiddetta Ripeti ("r"). Per esempio:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Nella versione servizio luglio 2016, manifesto Smooth Streaming generato conforme alla versione 2.2, con durate frammento utilizzando tag di ripetizione. Per esempio:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alcuni dei client Smooth Streaming legacy potrebbe non supporta i contrassegni ripetizioni e non verrà caricato il manifesto. Per evitare questo problema, è possibile utilizzare il parametro di formato manifesto legacy **(formato = v20 fmp4)** o aggiornare il client per la versione più recente, che supporta i tag di ripetizione. Per ulteriori informazioni, vedere [smussate Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Argomenti correlati

[Aggiornare Locator servizi multimediali dopo l'operazione chiavi per l'archiviazione](media-services-roll-storage-access-keys.md)
