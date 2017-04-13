<properties 
    pageTitle="Concetti di servizi multimediali Azure | Microsoft Azure" 
    description="In questo argomento fornisce una panoramica dei concetti di servizi multimediali di Azure" 
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

#<a name="azure-media-services-concepts"></a>Concetti di servizi multimediali Azure 

In questo argomento fornisce una panoramica dei concetti servizi multimediali più importanti.

##<a id="assets"></a>Attività e lo spazio di archiviazione

###<a name="assets"></a>Risorse

Un [bene](https://msdn.microsoft.com/library/azure/hh974277.aspx) contiene file digitali (inclusi video, audio, immagini, raccolte anteprime, didascalie e sottotitoli codificati file) e i metadati relativi a questi file. Dopo i file digitali vengono caricati in una risorsa, potrebbero essere utilizzate nei servizi multimediali di codifica e il flusso di flussi di lavoro.

Una risorsa sia associata a un contenitore di blob nell'account di archiviazione di Azure e i file del bene vengono memorizzati come BLOB in tale contenitore.

Prima di decidere quali contenuti multimediali per caricare e archiviare in una risorsa, le considerazioni seguenti:

- Una risorsa deve contenere solo una singola univoca istanza di contenuti multimediali. Ad esempio una singola modifica di un filmato, annunci pubblicitari o episodio di TV.
- Una risorsa non può contenere più rendering o modifica di un file audiovisivo. Un esempio di un uso improprio di un bene tentano di memorizzare più episodio TV, annunci pubblicitari o più angolazioni da una singola produzione all'interno di un bene. Archiviazione più rendering o modifica di un file audiovisivo di un bene può causare problemi di invio codifica processi, streaming e proteggere il recapito di risorse in un punto successivo del flusso di lavoro.  

###<a name="asset-file"></a>File di risorse 
Un [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) rappresenta un file audio o video effettivo archiviata in un contenitore di blob. Un file di risorse è sempre associato a una risorsa e una risorsa può contenere uno o più file. L'attività codificatore di servizi di supporto non riesce se un oggetto di file di risorse non è associato a un file digitale in un contenitore di blob.

L'istanza **AssetFile** e sul file multimediale effettivo sono due oggetti distinti. L'istanza di AssetFile contenente metadati relativi a file multimediale, mentre il file multimediale contiene il contenuto multimediale effettivo.

Non tentare di modificare il contenuto della contenitori blob generati da servizi multimediali senza utilizzare API del servizio di supporto.

###<a name="asset-encryption-options"></a>Opzioni di crittografia bene

A seconda del tipo di contenuto che si desidera caricare, archiviare e fornire servizi multimediali sono disponibili varie opzioni di crittografia che è possibile scegliere tra.

**Nessuno** Crittografia non viene utilizzata. Si tratta del valore predefinito. Si noti che, quando si utilizza questa opzione il contenuto non protetta durante il transito o inattivi in archiviazione.

Se si prevede di eseguire un MP4 utilizzando il download graduale, utilizzare questa opzione per caricare il contenuto.

**StorageEncrypted** : utilizzare questa opzione per crittografare il contenuto Cancella localmente utilizzando la crittografia AES 256 bit e quindi caricarlo su Azure lo spazio di archiviazione in cui è archiviato crittografati inattivi. Risorse è protetti con crittografia di archiviazione sono automaticamente in formato non crittografati e inseriti in un file system crittografato prima di codifica e facoltativamente nuovamente crittografati prima di caricare nuovamente come una nuova risorsa di output. Il caso di utilizzo principale per la crittografia di spazio di archiviazione è quando si desidera proteggere i file di supporto di input di alta qualità con crittografia inattivi su disco. 

Per offrire una risorsa crittografato di spazio di archiviazione, è necessario configurare i criteri di recapito del bene in modo che servizi multimediali di sa come si desidera distribuire il contenuto. Prima che la risorsa può essere alterata, server di streaming rimuove la crittografia di spazio di archiviazione e flusso il contenuto utilizzando i criteri di consegna specificata (ad esempio AES, PlayReady o senza crittografia). 

**CommonEncryptionProtected** - usare questa opzione se si desidera crittografare (o caricare già crittografati) contenuto per la crittografia comuni o PlayReady DRM (ad esempio Smooth Streaming è protetto con PlayReady DRM).

**EnvelopeEncryptionProtected** : usare questa opzione se si desidera proteggere (o caricare già protetti) HTTP Live Streaming (HLS) crittografate con la crittografia AES Advanced Standard (). Si noti che se si sta caricando HLS già crittografate con AES, è necessario sono stato crittografato da trasformare Manager.

###<a name="access-policy"></a>Criterio di accesso 

Un [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) definisce autorizzazioni (ad esempio lettura, scrittura ed elenco) e la durata dell'accesso a una risorsa. Passare in genere un oggetto AccessPolicy a un indicatore di posizione da utilizzare per accedere ai file contenuti in una risorsa.


###<a name="blob-container"></a>Contenitore di BLOB

Un contenitore di blob fornisce un raggruppamento di un insieme di BLOB. Contenitori BLOB vengono utilizzati in servizi multimediali come punto di contorno per il controllo di accesso e Locator condiviso accesso firma (SA) sulle risorse. Un account di archiviazione Azure può contenere un numero illimitato di contenitori blob. Un contenitore consentono di memorizzare un numero illimitato di BLOB.

>[AZURE.NOTE]Non tentare di modificare il contenuto della contenitori blob generati da servizi multimediali senza utilizzare API del servizio di supporto.

###<a id="locators"></a>Indicatori

S [Locator](https://msdn.microsoft.com/library/azure/hh974308.aspx)offrono un punto di ingresso per accedere ai file contenuti in una risorsa. Un criterio di accesso viene utilizzato per definire le autorizzazioni e la durata che un client ha accesso a una determinata attività. Indicatori possono avere una relazione molti-a uno a un criterio di accesso, in modo che diversi indicatori possono fornire ore di inizio diversi e tipi di connessione a client diversi durante l'uso di tutte le stesse autorizzazioni e le impostazioni di durata; a causa di limitazioni di criteri di accesso condiviso l'impostazione di servizi di archiviazione Azure, tuttavia, non è più di cinque Locator univoco associato a una determinata attività contemporaneamente. 

Servizi multimediali sono supportati due tipi di indicatori: Locator OnDemandOrigin, usato per trasmettere multimediali (ad esempio MPEG trattino, HLS o Smooth Streaming) o scaricare gradualmente Locator URL SA ed elementi multimediali usata per il caricamento o download to\from file multimediali di archiviazione Azure. 

Si noti che l'autorizzazione di elenco (AccessPermissions.List) non deve essere utilizzato durante la creazione di un indicatore di posizione OrDemandOrigin. 

###<a name="storage-account"></a>Account di archiviazione

Tutti gli accessi allo spazio di archiviazione di Azure viene eseguito tramite un account di archiviazione. Un account del servizio di supporto è possibile associare a uno o più account di archiviazione. Un account può contenere un numero illimitato di contenitori, come le dimensioni totali sono in 500TB per account di archiviazione.  Servizi multimediali di fornisce utensili livello SDK che consente di gestire più account di archiviazione e la distribuzione dei beni durante il caricamento a tali account in base a metriche o distribuzione casuale bilanciamento del carico. Per ulteriori informazioni, vedere Utilizzo di [Archiviazione Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

##<a name="jobs-and-tasks"></a>Processi e delle attività

In genere è utilizzato un [processo](https://msdn.microsoft.com/library/azure/hh974289.aspx) al processo (ad esempio, indice o codificare) una presentazione di audio/video. Quando si elabora più video, creare un processo per ogni video da codificare.

Un processo contiene i metadati di elaborazione da eseguire. Ogni processo contiene uno o più [attività](https://msdn.microsoft.com/library/azure/hh974286.aspx)che specificano un'attività di elaborazione atomica patrimonio input, in output risorse, un processore media e le relative impostazioni associate. Attività all'interno di un processo possono essere concatenate, nel punto in cui la risorsa di output di un'attività verrà fornita come input bene l'attività successiva. In questo modo può contenere un processo tutti di elaborazione necessario per una presentazione di elementi multimediali.

##<a id="encoding"></a>Codifica

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

Per informazioni su Encoder supportati, vedere [Encoder](media-services-encode-asset.md).


##<a name="live-streaming"></a>Flusso in diretta

In servizi multimediali di Windows Azure, un canale rappresenta una pipeline di elaborazione del contenuto di flusso live. Un canale riceve live flussi di input in uno dei due modi:

- Un codificatore live locale invia con più velocità RTP o Smooth Streaming (frammentato MP4) per il canale. È possibile utilizzare le seguenti Encoder live che generano multi-velocità Smooth Streaming: elementare, lingua inglese, Cisco. Le seguenti Encoder live output RTP: Adobe Flash Live Telestream Wirecast e Tricaster trascodificatori. I flussi acquisiti passano attraverso canali senza ulteriori elaborazioni. Quando richiesto, al cliente servizi multimediali di un flusso.

- Un flusso di velocità singolo (in uno dei formati seguenti: RTP (MPEG-TS)), RTP o Smooth Streaming (frammentato MP4)) viene inviato per il canale è abilitato per eseguire la codifica live con i servizi di supporto. Il canale avrà la codifica live di in entrata velocità singolo flusso in un flusso di video con più velocità (adattata). Quando richiesto, al cliente servizi multimediali di un flusso.

###<a name="channel"></a>Canale

Servizi di supporto, s [canale](https://msdn.microsoft.com/library/azure/dn783458.aspx)sono responsabili dell'elaborazione del contenuto di flusso live. Un canale fornisce un endpoint input (acquisizione URL) che è quindi fornire un trascodificatore live. Il canale riceve live flussi di input da trascodificatore live e rende disponibili per la trasmissione tramite uno o più StreamingEndpoints. Canali offrono un endpoint di anteprima (preview URL) che consente di visualizzare in anteprima e convalidare il flusso prima di recapito e ulteriori elaborazioni.

Quando si crea il canale, è possibile ottenere l'URL di caricamento e l'URL di anteprima. Per ottenere questi URL, non è necessario essere in stato avviato il canale. Quando si è pronti iniziare l'inserimento di dati da un trascodificatore live nel canale, è necessario avviare il canale. Una volta trascodificatore live viene avviato il caricamento di dati, è possibile visualizzare l'anteprima del flusso.

Ogni account servizi multimediali può contenere più canali, più programmi e più StreamingEndpoints. A seconda delle esigenze della larghezza di banda e la sicurezza StreamingEndpoint services possono essere dedicati per uno o più canali. Qualsiasi StreamingEndpoint può effettuare il pull da un canale.


###<a name="program"></a>Programma

Un [programma](https://msdn.microsoft.com/library/azure/dn783463.aspx) consente di controllare la pubblicazione e l'archiviazione dei segmenti di un flusso in diretta. I canali di gestire i programmi. Il canale e applicazione di una relazione è molto simile a supporti tradizionali in un canale dispone di un flusso costante di contenuto e un programma ambito è impostato su alcune evento programmato in tale canale.
È possibile specificare il numero di ore che si desidera conservare il contenuto registrato per il programma impostando la proprietà **ArchiveWindowLength** . Per un massimo di 25 ore, è possibile impostare questo valore da un minimo di 5 minuti.

ArchiveWindowLength determina anche la maggior quantità di client ora è possibile cercare indietro nel tempo dalla posizione corrente live. Programmi possono essere eseguiti il periodo di tempo specificato, ma verrà continuamente eliminato contenuto che si trova dietro la lunghezza della finestra. Questo valore di questa proprietà determina anche la durata delle manifesti client espandibile.

Ogni programma è associato a una risorsa. Per pubblicare il programma è necessario creare un indicatore di posizione per la risorsa associata. Con questo locator consentirà di generare un URL di trasmissione che è possibile fornire per i clienti.

Un canale supporta fino a tre programmi contemporaneamente in esecuzione in modo che è possibile creare più archivi di stesso flusso di posta in arrivo. In questo modo è possibile pubblicare e archiviare diverse parti di un evento in base alle esigenze. Ad esempio, l'esigenza consiste nell'archiviare 6 ore di un programma di trasmettere solo ultimi 10 minuti. A tale scopo, è necessario creare due programmi contemporaneamente in esecuzione. Un programma è impostato per archiviare 6 ore dell'evento, ma il programma non è stato pubblicato. Un altro programma è impostato per l'archiviazione di 10 minuti e questo programma viene pubblicato.


Per ulteriori informazioni, vedere:

- [Utilizzo di canali abilitati per eseguire codifica Live con servizi multimediali di Windows Azure](media-services-manage-live-encoder-enabled-channels.md)
- [Utilizzo dei canali che ricevono flusso in diretta con più velocità da Encoder locale](media-services-live-streaming-with-onprem-encoders.md)
- [Le quote e limitazioni](media-services-quotas-and-limitations.md).

##<a name="protecting-content"></a>Proteggere il contenuto

###<a name="dynamic-encryption"></a>Crittografia dinamiche

Servizi multimediali di Azure consente di proteggere gli elementi multimediali dal tempo che lascia il computer tramite lo spazio di archiviazione, elaborazione e recapito. Servizi multimediali consente di eseguire il contenuto in modo dinamico crittografato con la crittografia AES (Advanced Standard) (usando i tasti di crittografia a 128 bit) e crittografia comune (CENC) utilizzando PlayReady e/o Widevine DRM. Servizi multimediali vengono forniti anche un servizio per l'esecuzione di chiavi AES e le licenze PlayReady ai client autorizzati.

Attualmente, è possibile crittografare i formati di flussi seguenti: HLS, MPEG trattino e Smooth Streaming. Non è possibile crittografare unità 10K streaming formato o l'opzione progressivo Scarica.

Se si desidera per i servizi di supporto per la crittografia di un bene, è necessario associare una chiave di crittografia (CommonEncryption o EnvelopeEncryption) le attività e inoltre configurare i criteri di autorizzazione per la chiave.

Se si desidera eseguire il flusso di un bene crittografato di spazio di archiviazione, è necessario configurare i criteri di recapito del bene per specificare come si desidera consegnare le risorse.

Quando un flusso viene richiesta da un lettore, servizi multimediali di utilizza la chiave specificata per crittografare in modo dinamico il contenuto utilizzando una busta la crittografia (con AES) o comuni (con PlayReady o Widevine). Per decrittografare il flusso, il lettore richiederà la chiave del servizio di recapito chiave. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificata per la chiave.


###<a name="token-restriction"></a>Restrizione token

I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: aprire, token restrizione o limitazione IP. Il criterio con restrizioni token deve essere accompagnato da un token emesso da un servizio Token (sicurezza). Servizi multimediali sono supportati i token in JSON Web Token (JWT) e il formato semplice Web token (SWT). Servizi multimediali non fornisce servizi Token protetti. È possibile creare un servizio token di sicurezza personalizzati o utilizzare Microsoft Azure ACS ai token problema. Il servizio token di sicurezza deve essere configurato per creare un token firmato con specificato chiave e problema attestazioni specificato nella configurazione del token restrizione. Il servizio di recapito chiave servizi multimediali restituirà la chiave richiesta (o licenza) per il client se il token è valido e le richieste di token corrispondono quelli configurati per il tasto (o licenza).

Quando la configurazione del token limitata criteri, è necessario specificare la chiave primaria verifica, emittente e i parametri di gruppo di destinatari. La chiave primaria verifica contiene la chiave firmati con il token, emittente è il servizio token di sicuro che genera il token. Gruppo di destinatari (a volte chiamato ambito) descrive lo scopo del token o la risorsa il token autorizza l'accesso a. Il servizio di recapito chiave servizi multimediali convalida che questi valori nel token corrispondano ai valori nel modello.

Per ulteriori informazioni, vedere gli articoli seguenti:

[Proteggere con una panoramica del contenuto](media-services-content-protection-overview.md)
[Proteggi con AES a 128](media-services-protect-with-aes128.md)
[Proteggi con DRM](media-services-protect-with-drm.md)

##<a name="delivering"></a>L'esecuzione

###<a id="dynamic_packaging"></a>Salvataggio di un pacchetto dinamico

Quando si lavora con servizi multimediali di è consigliabile codificare i file mezzanine in una velocità adattata MP4 impostare e quindi si converte il set per il formato desiderato grazie [Imballaggio dinamico](media-services-dynamic-packaging-overview.md).


###<a name="streaming-endpoint"></a>Endpoint trasmissione

Un StreamingEndpoint rappresenta un servizio di trasmissione in grado di fornire contenuto direttamente a un'applicazione di Windows Media player client o a un contenuto rete (CDN) per la distribuzione (servizi multimediali di Windows Azure offre l'integrazione di Azure CDN.) Il flusso in uscita da un servizio StreamingEndpoint può essere un flusso live o un video su richiesta bene nell'account servizi multimediali. Inoltre, è possibile controllare la capacità del servizio StreamingEndpoint per gestire l'aumento della larghezza di banda esigenze modificando le unità della scala (noto anche come flusso unità). Si consiglia di assegnare una o più unità di scala per le applicazioni nell'ambiente di produzione. Unità scala offrono capacità uscita dedicato che può essere acquistata con incrementi di 200 MB/s e funzionalità aggiuntiva che include attualmente utilizzo dinamico imballaggio.

È consigliabile utilizzare crittografia dinamiche e/o imballaggio dinamico. Per utilizzare queste funzionalità, è necessario disporre almeno un'unità di trasmissione per l'endpoint da cui si prevede di flusso. Per ulteriori informazioni, vedere [proporzioni streaming unità](media-services-portal-manage-streaming-endpoints.md).

Per impostazione predefinita è possibile impostare fino a 2 endpoint nell'account servizi multimediali di flusso. Per richiedere un limite superiore, vedere [le quote e limitazioni](media-services-quotas-and-limitations.md).

Vengono addebitate solo quando il StreamingEndpoint è in stato di esecuzione.

###<a name="asset-delivery-policy"></a>Criteri di recapito bene

Uno dei passaggi del flusso di lavoro di distribuzione di contenuti di servizi multimediali la configurazione [dei criteri di recapito per risorse ](https://msdn.microsoft.com/library/azure/dn799055.aspx)che si desidera trasmettere. Il criterio di recapito bene indica servizi multimediali come desiderato per le risorse da consegnare: in quali streaming protocollo deve il bene essere dinamicamente incluso (ad esempio, trattino MPEG, HLS, Smooth Streaming o tutti), se non si desidera crittografare in modo dinamico il bene e come (busta o la crittografia comune).

Se si dispone di un bene crittografato di spazio di archiviazione, prima che la risorsa può essere alterata, server di streaming rimuove la crittografia di spazio di archiviazione e flusso il contenuto utilizzando i criteri di recapito specificato. Ad esempio, per fornire le risorse crittografate con chiave di crittografia avanzate crittografia AES (Standard), impostare il tipo di criteri DynamicEnvelopeEncryption. Per rimuovere la crittografia di spazio di archiviazione e flusso bene in chiaro, impostare il tipo di criteri NoDynamicEncryption.

###<a name="progressive-download"></a>Download graduale

Download graduale consente di iniziare la riproduzione multimediale prima di tutto il file è stato scaricato. È possibile scaricare solo gradualmente estensione MP4.

Si noti che è necessario decrittografare crittografati risorse se si desidera rendere disponibili per il download graduale.

Per offrire agli utenti con URL di download graduale, è necessario creare un indicatore di posizione OnDemandOrigin. La creazione dell'indicatore di posizione, fornisce il percorso di base per la risorsa. È quindi necessario aggiungere il nome del file MP4. Per esempio:

http://amstest1.streaming.mediaservices.Windows.NET/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>URL di trasmissione

Il contenuto ai client di flusso. Per offrire agli utenti con l'URL di flusso, è necessario creare un indicatore di posizione OnDemandOrigin. La creazione dell'indicatore di posizione, fornisce il percorso di base per la risorsa che contiene il contenuto che si desidera eseguire il flusso. Tuttavia, per poter eseguire il flusso il contenuto è necessario modificare ulteriormente il percorso. Per creare un URL completo al file manifesto streaming, è necessario concatenare valore Path del locator e manifesto (filename.ism) nome file. Aggiungere quindi /Manifest e un formato appropriato, se necessario, il percorso locator.

È anche possibile trasmettere il contenuto in una connessione SSL. A tale scopo, assicurarsi che l'URL di trasmissione iniziare con HTTPS.

Si noti che è possibile trasmettere solo su SSL se è stato creato l'endpoint di trasmissione in cui i contenuti dopo 10 settembre 2014. Se l'URL di trasmissione sono basati sui punti finali flussi creati dopo 10 settembre, l'URL contiene "streaming.mediaservices.windows.net" (il nuovo formato). URL di trasmissione che contengono "origin.mediaservices.windows.net" (il formato precedente) non supportano SSL. Se l'URL è nel vecchio formato e si desidera trasmettere su SSL, creare un nuovo endpoint di trasmissione. Utilizzare gli URL creati in base al nuovo endpoint di flusso per lo streaming del contenuto su SSL.

L'elenco seguente descrive i diversi formati di flussi e vengono forniti alcuni esempi:

- Smooth Streaming

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest


- MPEG TRATTINO

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=mpd-Time-CSF)



- Apple HTTP Live Streaming V4 (HLS)

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-AAPL)



- Apple HTTP Live Streaming V3 (HLS)

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-AAPL-V3)

- Unità 10K (per solo licenziatari a questo punto Adobe/Access)

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.streaming.mediaservices.Windows.NET/fecebb23-46f6-490d-8B70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=f4m-f4f)


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
