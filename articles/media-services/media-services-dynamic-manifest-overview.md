<properties 
    pageTitle="Filtri e manifesti dinamici | Microsoft Azure" 
    description="In questo argomento viene descritto come creare filtri in modo che il client può utilizzare a sezioni specifiche del flusso di un flusso. Servizi multimediali di crea manifesti dinamici per archiviare questo flusso selettiva." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>Filtri e manifesti dinamici

A partire dalla versione 2.11, servizi multimediali consente di definire filtri per le risorse. Questi filtri vengono regole lato server per consentono agli utenti di scegliere di eseguire operazioni come: riproduzione solo una sezione di un video (anziché la riproduzione del video intero), oppure specificare solo un sottoinsieme di rendering audio e video in grado di gestire dispositivi del cliente (invece di tutti i rendering associate bene). Il filtraggio dei beni viene archiviato tramite s **Manifesto dinamico**creato richiesta del cliente per eseguire il flusso di un video in base a filtri specificati.

Negli argomenti vengono illustrati gli scenari comuni in cui usare filtri sarebbe molto utile per i clienti e i collegamenti ad argomenti che illustrano come creare filtri a livello di programmazione (attualmente, è possibile creare filtri con le API REST solo).

##<a name="overview"></a>Panoramica

Durante l'esecuzione del contenuto per i clienti (streaming eventi live o punti) l'obiettivo è fornire un video di alta qualità a vari dispositivi in condizioni di rete diversa. Per ottenere questo obiettivo procedere come indicato di seguito:

- codificare il flusso di flusso video multi-velocità ([velocità adattata](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (si farà delle condizioni di qualità e di rete) e 
- utilizzare servizi multimediali [Imballaggio dinamico](media-services-dynamic-packaging-overview.md) per dinamicamente assemblare nuovamente il flusso in diversi protocolli (si farà streaming su diversi dispositivi). Servizi multimediali di supporta il recapito della velocità di adattamento seguenti streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso). 

###<a name="manifest-files"></a>File manifesto 

Quando si codifica di un bene per il flusso di velocità adattata, viene creato un file **manifesto** (playlist) (il file è basato su XML o testo). Il file **manifesto** include ad esempio di flusso dei metadati: tenere traccia di tipo (audio, video o testo), tenere traccia di nome, l'ora di inizio e fine, velocità (qualità), rilevamento delle lingue, finestra di presentazione (finestra scorrevole di a durata fissa), i codec video (ID). Inoltre, richiede al lettore per recuperare il frammento successivo fornendo informazioni successivi riproducibili video frammenti disponibili e il relativo percorso. Frammenti (o segmenti) sono "blocchi" effettivi di contenuto video.


Ecco un esempio di un file manifesto: 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>Manifesti dinamici

Esistono [scenari](media-services-dynamic-manifest-overview.md#scenarios) quando il client richiede una maggiore flessibilità rispetto a quello descritto nel file manifesto del bene predefinito. Per esempio:

- Dispositivo specifico: recapitare solo e/o rendering specificato specificati tracce lingue supportate dal dispositivo che viene utilizzato per la riproduzione del contenuto ("rendering delle immagini filtro"). 
- Ridurre il manifesto per visualizzare un clip secondario di un evento live ("clip secondario filtro").
- Tagliare l'inizio di un video ("tagliare un video").
- Regolare la finestra di presentazione (DVR) per fornire una lunghezza limitata della finestra DVR nel lettore ("finestra presentazione regolazione").
 
Per ottenere questo flessibilità, servizi multimediali di offre **Manifesti dinamica** in base a [filtri](media-services-dynamic-manifest-overview.md#filters).  Dopo aver definito i filtri, i clienti possono utilizzarli per trasmettere una specifica rendering delle immagini o clip secondari del video. Specificano i filtri nell'URL di trasmissione. Filtri possono essere applicati al velocità adattata streaming protocolli supportati da [Imballaggio dinamico](media-services-dynamic-packaging-overview.md): HLS, MPEG-tratto, Smooth Streaming e unità 10K. Per esempio:

MPEG trattino URL con filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL di trasmissione smussate con filtro

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Per ulteriori informazioni su come distribuire il contenuto e creare gli URL di trasmissione, vedere [Panoramica sul contenuto di recapito](media-services-deliver-content-overview.md).


>[AZURE.NOTE]Si noti che manifesti dinamico non vengono modificati bene e manifesto predefinito per tale risorsa. Il client può scegliere di richiedere un flusso con o senza filtri. 


###<a id="filters"></a>Filtri 

Esistono due tipi di risorse filtri: 

- Filtri globali (possono essere applicate a qualsiasi bene l'account di servizi multimediali di Windows Azure, hanno una durata dell'account) e 
- Filtri locali (può essere applicata solo a una risorsa a cui il filtro è stato associato al momento della creazione, hanno un ciclo di vita del bene). 

Tipi di filtro locale e globale hanno le stesse proprietà. La differenza tra i due principale è quali scenari quali tipi di un filtro più adatto. Filtri globali sono in genere adatti per i profili di dispositivo (filtro di rendering delle immagini) in locali filtri possono essere utilizzati per tagliare una risorsa specifica.


##<a id="scenarios"></a>Scenari comuni 

Come è stato detto prima, durante l'esecuzione del contenuto per i clienti (streaming eventi live o punti) l'obiettivo è fornire un video di alta qualità a vari dispositivi in condizioni di rete diversa. Inoltre, i potrebbe avere altri requisiti che includono le risorse di filtro e l'utilizzo di s **Manifesto dinamico**. Nelle sezioni seguenti vengono assegnare una breve panoramica dei diversi scenari di filtri.

- Specificare solo un sottoinsieme di rendering audio e video in grado di gestire alcuni dispositivi (invece di tutti i rendering associate bene). 
- Riproduzione solo una sezione di un video (anziché la riproduzione del video intero).
- Modificare DVR presentazione finestra.

##<a name="rendition-filtering"></a>Filtro di rendering delle immagini 

È possibile scegliere di codificare la risorsa più profili di codifica (previsto h. 264, h. 264 alto, AACL, AACH, Dolby Digital Plus) e più bitrate di qualità. Tuttavia, non tutti i dispositivi client supporterà profili e bitrate tutte le risorse. Dispositivi Android meno recenti, ad esempio, supporta solo h. 264 previsto + AACL. Inviare bitrate più elevati a un dispositivo che non è possibile visualizzare i vantaggi, rifiuti calcolo della larghezza di banda e dispositivo. Ad esempio dispositivo deve decodificare tutte le informazioni, solo per ridimensionarlo verso il basso per la visualizzazione.

Con manifesto dinamico, è possibile creare profili dispositivo ad esempio il cellulare, console HD/SD e così via e includere le tracce e le caratteristiche che si desidera far parte di ogni profilo.

 
![Esempio di filtro di rendering delle immagini][renditions2]

Nell'esempio seguente, un codificatore utilizzato per codificare una risorsa mezzanine in sette rendering video ISO MP4s (da 180p a 1080p). Risorse codificata possono essere dinamicamente riunite in uno dei seguenti protocolli di trasmissione: HLS, uniforme, MPEG trattino e unità 10K.  Nella parte superiore del diagramma, viene visualizzato il manifesto HLS per la risorsa senza filtri (contiene tutti i sette rendering).  In basso a sinistra, viene visualizzato il manifesto HLS a cui è stato applicato un filtro denominato "ott". Il filtro "ott" specifica per rimuovere tutti bitrate sotto 1 Mbps, che ha i livelli di qualità due inferiore vengono rimossi nella risposta.  In basso a destra, viene visualizzato il manifesto HLS a cui è stato applicato un filtro denominato "mobile". Specifica il filtro "mobile" per rimuovere rendering nel punto in cui la risoluzione è maggiore di 720p, che ha due rendering 1080p vengono rimosse.

![Filtro di rendering delle immagini][renditions1]

##<a name="removing-language-tracks"></a>Rimuovere le tracce di lingua

Le risorse possono includere più lingue per l'audio, ad esempio inglese, spagnolo, francese, e così via. In genere, i responsabili di Windows Media Player SDK predefinito selezione traccia audio e tracce audio disponibile per selezione dell'utente. È difficile sviluppare tali SDK di Windows Media Player, richiede l'esecuzione di diverse implementazioni tra Framework di Windows Media player specifico di dispositivo. Inoltre, alcune piattaforme API di Windows Media Player sono limitate e non include funzionalità di selezione audio nel punto in cui gli utenti non è possibile selezionare o modificare la traccia audio predefinita. Con i filtri di risorse, è possibile controllare il comportamento mediante la creazione di filtri che includono solo lingue per l'audio desiderati.

![Tracce lingue filtro][language_filter]


##<a name="trimming-start-of-an-asset"></a>Limitazione per motivi di inizio di un bene 

Nella maggior parte degli eventi live streaming, operatori eseguono alcuni test prima dell'evento effettivo. Ad esempio, è possibile includere un computer slate … prima dell'inizio dell'evento: "Verrà avviato il programma momentaneamente". Se il programma è archiviazione, il test e dati ardesia inoltre archiviare e saranno incluso nella presentazione. Tuttavia, queste informazioni non devono essere visualizzate ai client. Con manifesto dinamico, è possibile creare un filtro di ora di inizio e rimuovere i dati non desiderati dal manifesto.

![Limitazione per motivi di inizio][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>Creazione di clip secondari (visualizzazioni) da un archivio live

Numero di eventi live è lunghe in esecuzione e archivio live può includere più eventi. Dopo l'evento live estremità broadcaster potrebbe essere necessario suddividere nell'archivio live in avvio di programmi logico e interrompere le sequenze. Quindi pubblicare separatamente queste applicazioni virtuale senza post elaborazione nell'archivio live e non si sta creando attività separata (che non verrà visualizzato vantaggi dei frammenti memorizzati nella cache esistenti in CDN). Esempi di tali programmi virtuale (clip secondari) sono i trimestri un calcio o partita di pallacanestro, punti nel baseball o singoli eventi di un pomeriggio del programma relativi alle Olimpiadi.

Con manifesto dinamico, è possibile creare filtri utilizzando l'ora di inizio/fine e creare visualizzazioni virtuali nella parte superiore dell'archivio live. 

![Filtro secondaria][subclip_filter]

Risorse filtrate:

![Sci][skiing]

##<a name="adjusting-presentation-window-dvr"></a>Regolare la finestra di presentazione (DVR)

In servizi multimediali di Windows Azure sono attualmente archivio circolare dove è possibile configurare la durata tra 5 minuti - 25 ore. Filtro manifesto può essere usato per creare una finestra DVR rolling nella parte superiore dell'archivio, senza eliminare i file multimediali. Sono disponibili molti scenari in cui broadcaster da fornire una finestra DVR limitata passare con il bordo live e allo stesso tempo mantenere una finestra di archiviazione più grande. Un chi li trasmette consiglia di utilizzare i dati dalla finestra DVR per evidenziare i clip o he\she necessario fornire finestre DVR diverse per diversi dispositivi. Ad esempio, la maggior parte dei dispositivi mobili non gestire grande windows DVR (è possibile avere una finestra DVR 2 minuti per dispositivi mobili e 1 ora per i client desktop).

![Finestra DVR][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>Regolazione LiveBackoff (live posizione)

Manifesto può essere utilizzato per rimuovere il bordo di un programma live live alcuni secondi. In questo modo broadcaster guardare la presentazione in corrispondenza del punto di pubblicazione anteprima e creare annuncio punti di inserimento prima che i visualizzatori per ricevere il flusso (in genere sottoposta-off da 30 secondi). Broadcaster quindi inserire questi annunci a loro Framework client che vengano ricevuti ed elaborare le informazioni prima l'opportunità di annunci pubblicitari.

Oltre al supporto annuncio LiveBackoff utilizzabili per la regolazione posizione live download nel client in modo che quando i client il trascinamento e raggiunto il bordo live possano continuare ad accedere frammenti dal server invece di errori HTTP 404 o 412.

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>Combinare più regole in un singolo filtro

È possibile combinare più regole filtro in un singolo filtro. Ad esempio è possibile definire una regola di intervallo per rimuovere slate da un archivio live e permettono di filtrare bitrate disponibili. Per le regole di filtro più il risultato finale è la composizione (solo intersezione) di queste regole.

![regole di più][multiple-rules]

##<a name="create-filters-programmatically"></a>Creare filtri a livello di programmazione

L'argomento seguente illustra come entità servizi multimediali correlate ai filtri. Viene inoltre come creare filtri.  

[Creare filtri con le API REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinare più filtri (filtro composizione)

È anche possibile combinare più filtri in un singolo URL. 

Lo scenario seguente illustra il motivo per cui è possibile combinare filtri:

1. È necessario filtrare la qualità video per dispositivi mobili, ad esempio iPAD o Android (per limitare la qualità video). Per rimuovere le qualità indesiderate, è necessario creare un filtro globale è adatto per i profili di dispositivo. Come detto in precedenza, filtri globali possono essere utilizzati per tutte le attività con lo stesso account di servizi di supporto senza associazione altre. 
2. È possibile tagliare l'ora di inizio e fine di un bene. A tale scopo, è necessario creare un filtro locale, impostare l'ora di inizio/fine. 
3. Si desidera combinare entrambi i filtri (senza combinazione che è necessario aggiungere il filtro qualità per il filtro di limitazione per motivi che renderà difficile l'utilizzo di filtro).

Per combinare i filtri, è necessario impostare i nomi dei filtri all'elenco di manifesto/riproduzione URL con delimitato da punto e virgola. Si supponga di che avere un filtro denominato *MyMobileDevice* che la qualità di filtri e si dispone di un'altra denominata *MyStartTime* per impostare un'ora di inizio specifico. È possibile combinarle alla seguente:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

È possibile combinare un massimo di 3 filtri. 

Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.


##<a name="know-issues-and-limitations"></a>Fonti di informazioni limitazioni e problemi

- Manifesto dinamico funziona in GOP limiti (fotogrammi chiave) pertanto pulizia con maggiore precisione GOP. 
- È possibile utilizzare stesso nome di filtro per i filtri locali e globali. Si noti che filtro locale hanno la precedenza e sostituiranno filtri globali.
- Se si aggiorna un filtro, può richiedere fino a 2 minuti per l'endpoint flusso aggiornare le regole. Se il contenuto è stato fornito con alcuni filtri e memorizzati nella cache di proxy e CDN cache, l'aggiornamento di questi filtri possa causare errori di Windows Media player. È consigliabile per cancellare la cache dopo l'aggiornamento del filtro. Se questa opzione non è possibile, utilizzare un filtro diverso.


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Vedere anche

[La distribuzione di contenuti per Cenni preliminari sui clienti](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 