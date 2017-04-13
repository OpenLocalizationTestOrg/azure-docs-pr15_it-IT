<properties 
    pageTitle="Note sulla versione di servizi multimediali di | Microsoft Azure" 
    description="Note sulla versione servizi multimediali di Microsoft" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>

# <a name="azure-media-services-release-notes"></a>Note sulla versione di servizi multimediali di Azure

Queste note sulla versione riepilogano modifiche apportate da versioni precedenti e problemi noti.

>[AZURE.NOTE] Si desidera ascoltare dai clienti e analisi di risoluzione dei problemi che influiscono su è. Per riportare un problema o porre domande, inserire post nel [Forum MSDN servizi multimediali di Azure].


##<a id="issues"></a>Problemi noti attualmente

### <a id="general_issues"></a>Servizi multimediali di Microsoft problemi generali

Problema|Descrizione
---|---
Diverse intestazioni HTTP comuni non sono in API REST.|Se si sviluppano applicazioni servizi multimediali tramite l'API REST, si scoprire che alcuni campi comuni delle intestazioni HTTP (inclusi ID, richiesta CLIENT richiesta ID e ID richiesta di CLIENT invio) non sono supportate. Le intestazioni verranno aggiunti in un aggiornamento futuro.
Codifica percentuale non è consentita.|Servizi multimediali di utilizzerà il valore della proprietà IAssetFile.Name durante la creazione di URL per il contenuto di flusso (ad esempio http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Per questo motivo, non è consentita la codifica percentuale. Il valore della proprietà **Name** non può contenere i seguenti [caratteri percentuale codifica-riservati](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Inoltre, può essere presente solo uno '. " per l'estensione del file.
Il metodo ListBlobs che fa parte della versione di Azure archiviazione SDK 3. x ha esito negativo.|Servizi multimediali di genera URL SA in base alla versione [12-02-2012](http://msdn.microsoft.com/library/azure/dn592123.aspx) . Se si desidera utilizzare lo spazio di archiviazione Azure SDK per BLOB di elenco in un contenitore di blob, utilizzare il metodo [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) che fa parte della versione dello spazio di archiviazione Azure SDK 2. x. Il metodo ListBlobs che fa parte della versione dello spazio di archiviazione Azure SDK 3. x avrà esito negativo.
Servizi multimediali di limitazione meccanismo limita l'utilizzo delle risorse per le applicazioni che rendono eccessiva richiesta al servizio. Il servizio può restituire il codice di stato HTTP servizio non disponibile (503).|Per ulteriori informazioni, vedere la descrizione del codice di stato HTTP 503 nell'argomento con [Codici di errore servizi multimediali di Azure](http://msdn.microsoft.com/library/azure/dn168949.aspx) .
Quando si eseguono query entità, non esiste un limite di 1000 entità restituite contemporaneamente perché pubblico resto v2 limita i risultati della query ai risultati di 1000. | È necessario utilizzare **Salta** e **richiedere** (.NET) / **superiore** (REST) come descritto in [questo esempio viene .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [in questo esempio API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
Alcuni client possono provenire attraverso un problema di ripetizione tag nel manifesto Smooth Streaming.|Per ulteriori informazioni, vedere [questa](media-services-deliver-content-overview.md#known-issues) sezione.
Oggetti multimediali servizi .NET SDK Azure non possono essere serializzati e pertanto non funzionano con la cache di Azure.|Se si tenta di serializzare l'oggetto AssetCollection SDK per aggiungerla alla memorizzazione nella cache di Azure, viene generata un'eccezione.
Processi di codifica non riuscire con una stringa di messaggio "fase: DownloadFile. Codice: NullReferenceException ".|Flusso di lavoro di codifica tipico consiste nel caricare i file video inpui a una risorsa di input e invio di uno o più processi di codifica per tale risorsa input, senza modificare ulteriormente che risorse di input. Tuttavia, se si modifica la risorsa input (ad esempio mediante l'aggiunta, eliminazione o ridenominazione di file all'interno bene), quindi processi successivi potrebbero non riuscire con un errore DownloadFile. La soluzione consiste nell'eliminare bene input e ricaricare i file specifici di input a una nuova risorsa. 

##<a id="rest_version_history"></a>Cronologia delle versioni API REST

Per informazioni sulla cronologia delle versioni all'API REST di servizi di supporto, vedere [Riferimenti di API REST di servizi di Azure Media].

##<a id="july_changes16"></a>Versione finale di luglio 2016

###<a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Aggiornamenti del file manifesto (*. ISM) generati da codifica attività

Quando un'attività di codifica viene inviata a Media Encoder Standard o Azure Media Encoder, l'attività di codifica genera un [file di manifesto flusso](media-services-deliver-content-overview.md) (*. ISM) file nell'output di risorse. Con l'ultima versione, la sintassi di questo flusso manifesto è stata aggiornata.

>[AZURE.NOTE]La sintassi del file di manifesto (ISM) flusso è riservata per uso interno ed è soggetta a modifiche nelle versioni future. Non modificare o modificare il contenuto del file.

###<a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Un nuovo manifesto client (*. File ISMC) viene generato nell'output bene quando un'attività di codifica restituisce uno o più file MP4

Iniziando l'ultima versione, dopo il completamento di un'attività di codifica che genera una più file MP4, l'output bene conterrà anche un file di manifesto (*.ismc) client flusso. Il file .ismc consente di migliorare le prestazioni di trasmissione dinamiche. 

>[AZURE.NOTE]La sintassi del file manifesto (.ismc) client è riservata per uso interno ed è soggetta a modifiche nelle versioni future. Non modificare o modificare il contenuto del file.

Per ulteriori informazioni, vedere [questo](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### <a name="known-issues"></a>Problemi noti

Alcuni client possono provenire aross un problema di ripetizione tag nel manifesto Smooth Streaming. Per ulteriori informazioni, vedere [questa](media-services-deliver-content-overview.md#known-issues) sezione.

##<a id="apr_changes16"></a>Versione finale di aprile 2016

### <a name="azure-media-analytics"></a>Media Azure Analitica

Azure Media Servces introdotto Azure Media Analitica per business intelligence video potente. Per informazioni dettagliate, vedere [Panoramica di Analitica servizi multimediali di Azure](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (Preview)

Servizi multimediali di Azure consente ora crittografare in modo dinamico il HTTP Live Streaming (HLS) contenuto con FairPlay di Apple. Utilizzare il servizio di recapito licenza AMS per offrire FairPlay licenze ai client. Per informazioni più dettagliate, vedere [utilizzare i servizi di supporto Azure per trasmettere le HLS contenuto protetto con FairPlay di Apple ](media-services-protect-hls-with-fairplay.md).
  
##<a id="feb_changes16"></a>Versione finale di febbraio 2016

L'ultima versione di Azure Media Services SDK per .NET (3.5.3) contiene una correzione correlata Widevine. È stato il problema: non è stato riutilizzato AssetDeliveryPolicy per più risorse crittografate con Widevine. Come parte di questa correzione è stata aggiunta la seguente proprietà a SDK: **WidevineBaseLicenseAcquisitionUrl**.
    
    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},
        
    };

##<a id="jan_changes_16"></a>Versione finale di gennaio 2016

Codifica unità riservato rinominato per ridurre la confusione con nomi codificatore.

Le unità riservate codifica Basic, Standard e Premium vengono rinominate e S1, S2, e S3 riservati unità, rispettivamente.  I clienti che utilizzano destinatari codifica base oggi vedranno S1 come etichetta nel portale di Azure e nella fattura, mentre si Standard e Premium leggerà le etichette S2 e S3 rispettivamente. 

##<a id="dec_changes_15"></a>Versione finale di dicembre 2015

Il team di Azure SDK pubblicato una nuova versione del pacchetto di [Azure SDK per PHP](http://github.com/Azure/azure-sdk-for-php) che contiene gli aggiornamenti e nuove funzionalità per servizi multimediali di Microsoft Azure. In particolare, Azure Media Services SDK per PHP supporta ora le caratteristiche di [protezione dei contenuti](media-services-content-protection-overview.md) più recenti: dinamico crittografia AES e DRM (PlayReady e Widevine) con e senza restrizioni di Token. Inoltre, supporta il ridimensionamento [Codifica unità](media-services-dotnet-encoding-units.md).

Per ulteriori informazioni, vedere:

- Il blog di [Microsoft Azure Media Services SDK per PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
- I seguenti [esempi di codice](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) utili per iniziare rapidamente:
    - **vodworkflow_aes.PHP**: si tratta di un file PHP che illustra come utilizzare la crittografia AES a 128 dinamico e il servizio di recapito chiave. Si basa su campione .NET illustrato in [questo](media-services-protect-with-aes128.md) articolo.
    - **vodworkflow_aes.PHP**: si tratta di un file PHP che illustra come utilizzare il servizio di recapito di licenza e crittografia dinamico PlayReady. Si basa su campione .NET illustrato in [questo](media-services-protect-with-drm.md) articolo.
    - **scale_encoding_units.PHP**: si tratta di un file PHP che illustra come ridimensionare codifica unità riservato.


##<a id="nov_changes_15"></a>Versione finale di novembre 2015

Servizi multimediali di Azure offre il servizio di recapito Google Widevine licenza nel cloud. Per ulteriori informazioni, consultare [il blog di annuncio](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Vedere anche [in questa esercitazione](media-services-protect-with-drm.md) e [GitHub archivio](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Si noti che servizi di recapito licenza Widevine forniti da Azure Media Sevices in anteprima. Per ulteriori informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

##<a id="oct_changes_15"></a>Rilascio di ottobre 2015

Servizi di supporto Azure (AMS) è ora disponibile nei seguenti data center: Brasile sud ovest India, India Sud e India centrale. È possibile utilizzare il portale classica Azure per [creare gli account del servizio di supporto](media-services-portal-create-account.md) ed eseguire varie attività descritte [di seguito](https://azure.microsoft.com/documentation/services/media-services/). Tuttavia, codifica Live non è abilitata in questi centri dati. Inoltre, non tutti i tipi di codifica riservato unità sono disponibili in tali centri.

- Brasile Sud: Solo Standard e unità riservato di codifica di base sono disponibili
- India ovest, India Sud e India centrale: solo codifica riservato unità di base sono disponibili


##<a id="september_changes_15"></a>Versione finale di settembre 2015 

- AMS offre la possibilità di proteggere punti (VOD) e flussi in diretta con la tecnologia DRM modulare Widevine. È possibile utilizzare i seguenti partner dei servizi di recapito per offrire licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/). Per ulteriori informazioni, vedere [questo blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).

    È possibile utilizzare [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partire dalla versione 3.5.1) o all'API REST per configurare il AssetDeliveryConfiguration per utilizzare Widevine.  

- AMS il supporto per Apple ProRes video. A questo punto è possibile caricare i file video di origine di QuickTime che utilizzano Apple ProRes o altri codec. Per ulteriori informazioni, vedere [questo blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).

- È ora possibile usare Media Encoder Standard per l'estrazione di archivio di ritaglio secondari e live. Per ulteriori informazioni, vedere [questo blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

- Sono stati introdotti gli aggiornamenti di filtri seguenti: 

    - È ora possibile usare il formato di Apple HTTP Live Streaming (HLS) con solo audio filtro. Questo aggiornamento consente di rimuovere solo audio traccia specificando (solo audio = false) nell'URL.
    - Per definire filtri per le risorse, è ora possibile combinare più filtri (fino a 3) in un singolo URL.

    Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

- AMS supporta ora I fotogrammi in HLS v4. Supporto per I fotogrammi consente di ottimizzare la funzione di riavvolgere operazioni. Per impostazione predefinita, tutti gli output v4 HLS includono I-Frame playlist (EXT-X-I-FRAME-STREAM-INF).
 
    Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

##<a id="august_changes_15"></a>Versione finale di agosto 2015

- Azure Media Services SDK per Java V0.8.0 release e nuovi esempi sono ora disponibili. Per ulteriori informazioni, vedere:

    - [Post di blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Archivio di esempi Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- Aggiornamento di Media Player Azure con supporto flusso più audio. Per ulteriori informazioni, vedere:
    - [Post di blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>Versione finale di luglio 2015

- Annuncio la disponibilità generale di Media Encoder Standard. Per ulteriori informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

    Media Encoder Standard utilizza impostazioni predefinite descritte in [questa](http://go.microsoft.com/fwlink/?LinkId=618336) sezione. Nota Quando usare un predefinito per 4k codifica, è necessario ottenere il tipo di unità **Premium** riservati. Per ulteriori informazioni, vedere [come scala codifica](media-services-scale-media-processing-overview.md).
- Live didascalie in tempo reale con servizi multimediali di Windows Azure e Windows Media Player. Per ulteriori informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###<a name="media-services-net-sdk-updates"></a>Servizi multimediali di Microsoft .NET SDK aggiornamenti

Azure Media Services .NET SDK è ora versione 3.4.0.0. Le seguenti funzionalità è stata aggiunta in questa versione:  

- Implementato supporto per l'archiviazione dinamica. Si noti che non è possibile scaricare una risorsa che contiene un archivio di live.
- Implementato il supporto per i filtri dinamici.
- Funzionalità implementata che consente di mantenere contenitore dello spazio di archiviazione durante l'eliminazione di risorsa.
- Correzioni di bug correlate a provare i criteri di canali.
- Attivazione **flusso di lavoro Media Encoder Premium**.

##<a id="june_changes_15"></a>Versione finale di giugno 2015

###<a name="media-services-net-sdk-updates"></a>Servizi multimediali di Microsoft .NET SDK aggiornamenti

Azure Media Services .NET SDK è ora versione 3.3.0.0. Le seguenti funzionalità è stata aggiunta in questa versione:  

- supporto per le specifiche di individuazione di connettersi OpenId,
- supporto per la gestione di attivazione di chiavi lato provider di identità. 

Se si usa un provider di identità che espone OpenID connettersi documento di individuazione (come provider di servizi di seguenti: Azure Active Directory, Google, Salesforce), è possibile indicare a servizi multimediali di Windows Azure per ottenere chiavi per la convalida dei token JWT da OpenID di connessione specifiche di individuazione. 

Per ulteriori informazioni, vedere [Usare i tasti di Web Json da connettere OpenID specifiche di individuazione per funzionare con l'autenticazione di token JWT in servizi multimediali di Windows Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).


##<a id="may_changes_15"></a>Versione di maggio 2015

Annuncio le nuove funzionalità seguenti:

- [Un'anteprima della codifica Live con i servizi di supporto](media-services-manage-live-encoder-enabled-channels.md)
- [Manifesto dinamico](media-services-dynamic-manifest-overview.md)
- [Un'anteprima del processore multimediale Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>Versione finale di aprile 2015

 ###<a name="general-media-services-updates"></a>Aggiornamenti dei servizi Media generale

- [Annuncio Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
- A partire da Media Services resto 2.10, canali configurati per acquisire un protocollo RTP, vengono creati con principale e secondaria acquisizione URL. Per ulteriori informazioni, vedere [canale acquisizione configurazioni](media-services-live-streaming-with-onprem-encoders.md#channel_input)
- Aggiornamenti Media indicizzatore Azure
- Supporto per la lingua spagnola
- Nuovo formato xml di configurazione

Per ulteriori informazioni, vedere [questo blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).
###<a name="media-services-net-sdk-updates"></a>Servizi multimediali di Microsoft .NET SDK aggiornamenti

Azure Media Services .NET SDK è ora versione 3.2.0.0.

Di seguito sono riportati alcuni dei clienti affiancate aggiornamenti:

- **Modifica rilevante**: modificato **TokenRestrictionTemplate.Issuer** e **TokenRestrictionTemplate.Audience** essere di tipo stringa.
- Gli aggiornamenti correlati alla creazione personalizzata riprovare criteri.
- Correzioni di bug correlate a durante il caricamento o download di file.
- La classe **MediaServicesCredentials** accetta ora endpoint di controllo di accesso principale e secondaria per l'autenticazione.



##<a id="march_changes_15"></a>Versione finale di marzo 2015

### <a name="general-media-services-updates"></a>Aggiornamenti dei servizi Media generale

- Servizi multimediali sono ora disponibili integrazione CDN Azure. Per supportare l'integrazione, la proprietà **CdnEnabled** è stata aggiunta al **StreamingEndpoint**.  **CdnEnabled** può essere utilizzata con API REST a partire dalla versione 2.9 (per ulteriori informazioni, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)).  **CdnEnabled** può essere utilizzata con .NET SDK a partire dalla versione 3.1.0.2 (per ulteriori informazioni, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx)).
- Annuncio di **flusso di lavoro Media Encoder Premium**. Per ulteriori informazioni, vedere [Introduzione a Premium codifica in servizi multimediali di Windows Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).
 


##<a id="february_changes_15"></a>Versione finale di febbraio 2015

### <a name="general-media-services-updates"></a>Aggiornamenti dei servizi Media generale

API REST di servizi di supporto è ora versione 2.9. A partire da questa versione, è possibile attivare l'integrazione di rete CDN di Azure con streaming i punti finali. Per ulteriori informazioni, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

##<a id="january_changes_15"></a>Versione finale di gennaio 2015

### <a name="general-media-services-updates"></a>Aggiornamenti dei servizi Media generale

Annuncio di generale disponibilità (GA) di protezione del contenuto con crittografia dinamiche. Per ulteriori informazioni, vedere [Servizi multimediali di Windows Azure migliora la protezione flusso con la tecnologia disponibilità generale DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###<a name="media-services-net-sdk-updates"></a>Servizi multimediali di Microsoft .NET SDK aggiornamenti

Azure Media Services .NET SDK è ora versione 3.1.0.1.

In questa versione contrassegnato il costruttore Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate predefinito come obsoleto. Costruzione new accetta TokenType in come argomento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Versione finale di dicembre 2014

###<a name="general-media-services-updates"></a>Aggiornamenti dei servizi Media generale

- Alcuni aggiornamenti e nuove caratteristiche sono stati aggiunti al processore Azure indicizzatore Media. Per ulteriori informazioni, vedere [Le note sulla versione di Azure Media indicizzatore versione 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Aggiungere una nuova API REST che consente di aggiornare la codifica riservato unità: [EncodingReservedUnitType con altri](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- CORS aggiunto il supporto per il servizio di recapito chiave.
- Miglioramenti delle prestazioni di query sulle opzioni di criteri di autorizzazione sono stati eseguiti.
- Nel centro dati Cina, l' [URL di recapito chiave](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) è ora per ogni cliente (come nelle altre aree di dati).
- Durata destinazione di aggiunto HLS automatico. Quando si effettua la trasmissione live, HLS sempre inserito in modo dinamico. Per impostazione predefinita, servizi Media calcola automaticamente percentuale di confezioni per i prodotti di segmento HLS (FragmentsPerSegment) in base al fotogramma intervallo (KeyFrameInterval), noto anche come gruppo di immagini: GOP ricevuti dal codificatore di Live. Per ulteriori informazioni, vedere [uso di Azure servizi Live flusso di dati multimediali].
 
###<a name="media-services-net-sdk-updates"></a>Servizi multimediali di Microsoft .NET SDK aggiornamenti

- [Azure Media Services.NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) è ora versione 3.1.0.0.
- Aggiornare la dipendenza .net SDK in .NET Framework 4.5.
- Aggiungere una nuova API che consente di aggiornare codifica unità riservata. Per ulteriori informazioni, vedere [aggiornamento di tipo di unità riservate e crescenti destinatari codifica utilizzando .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Aggiunta JWT (JSON Web Token) supporto per l'autenticazione del token. Per ulteriori informazioni, vedere [autenticazione token JWT servizi multimediali di Windows Azure e della crittografia dinamico](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Aggiunto offset relativi per BeginDate ed ExpirationDate nel modello di licenza PlayReady.


##<a id="november_changes_14"></a>Versione finale di novembre 2014

- Servizi multimediali di ora consente di acquisire un contenuto live Smooth Streaming (FMP4) su una connessione SSL. Per l'acquisizione su SSL, assicurarsi di aggiornare l'URL di caricamento a HTTPS.  Per ulteriori informazioni su live trasmissione, vedere [utilizzo di Azure servizi Live flusso di dati multimediali].
- Si noti che al momento si non è possibile acquisire un flusso di live RTP tramite una connessione SSL.
- È anche possibile trasmettere il contenuto in una connessione SSL. A tale scopo, assicurarsi che l'URL di trasmissione iniziare con HTTPS.
- Si noti che è possibile trasmettere solo su SSL se è stato creato l'endpoint di trasmissione in cui i contenuti dopo 10 settembre 2014. Se l'URL di trasmissione sono basati sui punti finali flussi creati dopo 10 settembre, l'URL contiene "streaming.mediaservices.windows.net" (il nuovo formato). URL di trasmissione che contengono "origin.mediaservices.windows.net" (il formato precedente) non supportano SSL. Se l'URL è nel vecchio formato e si desidera trasmettere su SSL, [creare un nuovo endpoint di trasmissione](media-services-portal-manage-streaming-endpoints.md). Utilizzare gli URL creati in base al nuovo endpoint di flusso per lo streaming del contenuto su SSL.

##<a id="october_changes_14"></a>Versione di ottobre 2014

### <a id="new_encoder_release"></a>Servizi multimediali di Microsoft Encoder Release

Presentazione della nuova versione di elementi multimediali servizi Azure Media Encoder. Con le ultime Azure Media Encoder solo addebitate per generare l'output GB, ma in caso contrario il nuovo codificatore è caratteristica compatibile con il codificatore. Per ulteriori informazioni [Dettagli prezzi servizi di supporto]).

### <a id="oct_sdk"></a>Servizi multimediali di Microsoft .NET SDK 

Media Services SDK per le estensioni .NET è ora la versione 2.0.0.3.

Media Services SDK per .NET è ora versione 3.0.0.8.

Sono state apportate modifiche seguenti:

- Refactoring in classi di criteri Riprova.
- Aggiunta di stringa agente utente per ottenere intestazioni di richiesta http.
- Aggiunta di istruzione di compilazione di ripristino nuget.
- Risoluzione dei test di uno scenario per usare x509 certificato dal repository.
- Convalida delle impostazioni per l'aggiornamento del canale e il flusso di fine.
 

### <a name="new-github-repository-to-host-media-services-samples"></a>Nuovo repository GitHub per gli esempi di servizi multimediali di host

Esempi sono contenuti in [archivio GitHub esempi di servizi multimediali di Windows Azure](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Versione di settembre 2014

Metadati di altri servizi multimediali sono ora versione 2.7. Per ulteriori informazioni sugli aggiornamenti apportati alle resto, vedere [Riferimenti di API REST di servizi di Azure Media].

Media Services SDK per .NET è ora versione 3.0.0.7
 
### <a id="sept_14_breaking_changes"></a>Ultime modifiche

* **Origine** è stato rinominato in [StreamingEndpoint].
* Modificare il comportamento predefinito quando si utilizza il **Portale classica Azure** per codificare e quindi pubblicare file MP4.

In precedenza, quando tramite il portale classica di Azure per pubblicare una risorsa video MP4 file unico un URL SA verranno creati (URL SA consentono di scaricare il video da un archivio blob). Attualmente, quando si utilizza il portale classica Azure per la codifica e quindi si pubblica una risorsa video MP4 file unico, l'URL generato fa riferimento a un servizi multimediali di Windows Azure streaming endpoint.  Questa modifica non ha alcun effetto video MP4 direttamente caricati ai servizi di supporto e pubblicati senza essere codificati tramite servizi multimediali di Windows Azure.

Al momento si dispone di queste due opzioni per risolvere il problema.

* Attivare flusso unità e utilizzare imballaggio dinamico per eseguire il flusso bene MP4 come smussata trasmissione presentazione.

* Creare un url SA per scaricare il MP4 (o gradualmente riprodurre). Per ulteriori informazioni su come creare un indicatore di posizione SA, vedere [L'esecuzione del contenuto].


### <a id="sept_14_GA_changes"></a>Nuove caratteristiche/scenari che fanno parte di immissione GA

* **Processore multimediale indicizzatore**. Per ulteriori informazioni, vedere [L'indicizzazione di file multimediali con Azure Media indicizzatore].

* L'entità [StreamingEndpoint] ora consente di aggiungere i nomi di dominio personalizzato (host).

    Per un nome di dominio personalizzato da utilizzare come il nome dell'endpoint flusso servizi multimediali, è necessario aggiungere i nomi di host personalizzato per l'endpoint di trasmissione. Usare l'API REST dei servizi di supporto o .NET SDK per aggiungere i nomi di host personalizzato.
    
    Le considerazioni seguenti:
    
    * È necessario essere proprietari del nome di dominio personalizzato.
    
    * La proprietà del nome di dominio deve essere convalidata tramite servizi multimediali di Windows Azure. Per convalidare il dominio, creare un record CName che esegue il mapping <MediaServicesAccountId>.<parent domain> per verifydns. < zona dns mediaservices >. 
    
    * È necessario creare un altro record CName associato il nome host personalizzato (ad esempio sports.contoso.com) al nome di host del StreamingEndpont i file multimediali servizi (ad esempio amstest.streaming.mediaservices.windows.net).


    Per ulteriori informazioni, vedere la proprietà **CustomHostNames** nell'argomento [StreamingEndpoint] .

### <a id="sept_14_preview_changes"></a>Nuove caratteristiche/scenari che fanno parte di nella public Preview

* Trasmissione anteprima in tempo reale. Per ulteriori informazioni, vedere [uso di Azure servizi Live flusso di dati multimediali].

* Servizio di recapito chiave. Per ulteriori informazioni, vedere [utilizzo di crittografia AES a 128 dinamiche e il servizio di recapito chiave].

* Dinamico crittografia AES. Per ulteriori informazioni, vedere [utilizzo di crittografia AES a 128 dinamiche e il servizio di recapito chiave].

* Servizio di recapito licenze PlayReady. Per ulteriori informazioni, vedere [uso PlayReady dinamico crittografia e il servizio di recapito licenza].

* Crittografia PlayReady dinamiche. Per ulteriori informazioni, vedere [uso PlayReady dinamico crittografia e il servizio di recapito licenza].

* Modello di licenza PlayReady servizi multimediali di Microsoft. Per ulteriori informazioni, vedere [Informazioni generali sui modelli di elementi multimediali servizi PlayReady licenza].

* Spazio di archiviazione di flusso crittografato risorse. Per ulteriori informazioni, vedere [Flusso archiviazione crittografati del contenuto].

##<a id="august_changes_14"></a>Versione di agosto 2014

Quando si codifica una risorsa, una risorsa di output viene visualizzato al termine del processo di codifica. Fino a questa versione, codificatore di servizi multimediali di Azure prodotti metadati relativi a risorse di output. Iniziare con questa versione il codificatore produce anche i metadati sulle risorse input. Per ulteriori informazioni, vedere gli argomenti di [Metadati di Input] e [Output] .


##<a id="july_changes_14"></a>Versione finale di luglio 2014

Per la crittografia Packager servizi multimediali di Azure sono state apportate le correzioni di bug seguenti:

* Solo l'audio viene riprodotto quando transmuxing una risorsa di archivio live a Live Streaming HTTP: questo problema è stato risolto e l'ora di riproduzione di audio e video.

* Quando imballaggio un bene per HTTP Live Streaming e la crittografia AES 128 bit busta, i flussi di pacchetti non vengono riprodotti in dispositivi Android: questo problema è stato risolto e flusso nel pacchetto viene riprodotto in dispositivi Android che supportano HTTP Live Streaming.

##<a id="may_changes_14"></a>Versione di maggio 2014

### <a id="may_14_changes"></a>Aggiornamenti dei servizi Media generale

È ora possibile usare [Imballaggio dinamico] al flusso HTTP Live Streaming (HLS) v3. Flusso v3 HLS, aggiungere il percorso di individuazione di origine nel formato seguente: * .ism/manifest(format=m3u8-aapl-v3). Per ulteriori informazioni, vedere [Blog di Nick Drouin].

Dinamico imballaggio ora anche supporta l'esecuzione di HLS (v3 e v4) crittografate con PlayReady in base a Smooth Streaming statico crittografate con PlayReady. Per informazioni su come crittografare Smooth Streaming con PlayReady, vedere [protezione flusso smussate con PlayReady].

### <a name="may_14_donnet_changes"></a>Servizi multimediali di Microsoft .NET SDK aggiornamenti

I miglioramenti seguenti sono inclusi nella versione Media Services .NET SDK 3.0.0.5:

* Velocità e adattabilità per il caricamento o il download di risorse multimediali.

* Miglioramenti introdotti in Riprova logica e temporanei eccezioni: 

    * Logica di rilevamento e riprovare errore temporaneo sono state migliorate per eccezioni errori sono causate da query, salvare le modifiche, durante il caricamento o download di file. 
    
    * Quando si ottiene eccezioni Web (ad esempio durante una richiesta di token ACS), si noterà che errori irreversibili hanno esito negativo più rapidamente a questo punto.

Per ulteriori informazioni, vedere [Logica riprova in Media Services SDK per .NET].

##<a id="april_changes_14"></a>Versione finale di codificatore di aprile 2014

### <a name="april_14_enocer_changes"></a>Aggiornamenti del codificatore di servizi multimediali

* Aggiunta del supporto per il caricamento di file AVI creati utilizzando l'editor non lineare Prato picchi EDIUS, in cui il video è leggermente compresso utilizzando Prato picchi sede centrale/HQX codec. Per ulteriori informazioni, vedere [Prato picchi annuncia EDIUS 7 Streaming tramite il Cloud].

* Aggiunta del supporto per specificare la convenzione di denominazione per i file prodotti dal codificatore di elementi multimediali. Per ulteriori informazioni, vedere [Controllare Media servizio codificatore Output nomi di file].

* Aggiunta del supporto per sovrapposizioni di audio e/o video. Per ulteriori informazioni, vedere [Creazione di sovrapposizioni].

* Aggiunta del supporto per panorama insieme più segmenti video. Per ulteriori informazioni, vedere [Panorama segmenti Video].

* Correzione di un problema correlato a ricorrere di MP4s nel punto in cui l'audio è stata codificata con 1 MPEG Audio layer 3 (o MP3).


##<a id="jan_feb_changes_14"></a>Versioni di gennaio/febbraio 2014

### <a name="jan_fab_14_donnet_changes"></a>Servizi multimediali Azure di Microsoft .NET SDK 3.0.0.1, 3.0.0.2 e 3.0.0.3

Le modifiche in 3.0.0.1 e 3.0.0.2 includono:

* Problemi risolti relativi all'utilizzo di query LINQ con OrderBy istruzioni.

* Soluzioni test divisa in [GitHub] in test basate sull'unità e basati su scenari.

Per ulteriori informazioni sulle modifiche, vedere: [Rilascia Azure Media Services .NET SDK 3.0.0.1 e 3.0.0.2].

3.0.0.3 sono state apportate le modifiche seguenti:

* Aggiornare le dipendenze di archiviazione Azure versione 3.0.3.0. 

* È stato risolto il problema di compatibilità per 3.0. *.* Rilascia. 


##<a id="december_changes_13"></a>Versione finale di dicembre 2013

### <a name="dec_13_donnet_changes"></a>Servizi multimediali Azure di Microsoft .NET SDK 3.0.0.0

>[AZURE.NOTE] versioni 3.0.x.x non sono compatibili con le versioni 2.4.x.x.

La versione più recente di Media Services SDK è ora 3.0.0.0. È possibile scaricare il pacchetto più recente da Nuget o ottenere i bit da [GitHub].

A partire da Media Services SDK versione 3.0.0.0, è possibile riutilizzare i token di [Azure Active Directory Access Control Service (ACS)] . Per ulteriori informazioni, vedere la sezione "Riutilizzare di token di servizio controllo di accesso" nell'argomento della [connessione a servizi multimediali con Media Services SDK per .NET] .

### <a name="dec_13_donnet_ext_changes"></a>Servizi multimediali Azure di Microsoft .NET SDK estensioni 2.0.0.0

Estensioni di Azure Media servizi .NET SDK è un insieme di metodi di estensione e le funzioni di supporto che verranno semplificare il codice e renderlo più semplice sviluppare con servizi multimediali di Windows Azure. È possibile ottenere i bit più recente da [Azure Media servizi .NET SDK estensioni].

##<a id="november_changes_13"></a>Versione finale di novembre 2013

### <a name="nov_13_donnet_changes"></a>Servizi multimediali Azure di Microsoft .NET SDK modifiche

A partire da questa versione, Media Services SDK per .NET gestisce temporaneo errori che possono verificarsi durante le chiamate al livello all'API REST di servizi di supporto.

##<a id="august_changes_13"></a>Versione finale di agosto 2013

### <a name="aug_13_powershell_changes"></a>Cmdlet di PowerShell servizi multimediali incluso negli strumenti Sdk di Azure

I cmdlet di PowerShell di servizi di supporto seguenti sono ora inclusi negli [Strumenti di sdk azure].

* Get-AzureMediaServices 

    Ad esempio `Get-AzureMediaServicesAccount`.

* Nuovo AzureMediaServicesAccount 

    Ad esempio `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

* Nuovo AzureMediaServicesKey 

    Ad esempio `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

* Rimuovi AzureMediaServicesAccount 

    Ad esempio `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

##<a id="june_changes_13"></a>Versione finale di giugno 2013

### <a name="june_13_general_changes"></a>Modifica di servizi multimediali di Azure

Le modifiche indicate in questa sezione sono aggiornamenti inclusi nelle versioni servizi multimediali giugno 2013.

* Possibilità di creare un collegamento a un account del servizio di supporto più account di archiviazione. 

    StorageAccount
    
    Asset.StorageAccountName e Asset.StorageAccount

* Possibilità di aggiornare Job.Priority. 

* Notifica correlati entità e proprietà: 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Processo

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Modifiche Media Services .NET SDK Azure

Le modifiche seguenti sono incluse nel giugno 2013 rilascia Media Services SDK. Ultima Media Services SDK è disponibile in GitHub.

* A partire dalla versione 2.3.0.0, supporta Media Services SDK collegamento lo spazio di archiviazione più account a un account di servizi di supporto. Le seguenti API supportano questa caratteristica:
    
    Il tipo di IStorageAccount.
    
    Proprietà Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
    
    Proprietà StorageAccount.
    
    Proprietà StorageAccountName.
    
    Per ulteriori informazioni, vedere [Gestione di risorse multimediali servizi tra più account di archiviazione].

* Notifica API correlate. Iniziare con la versione 2.2.0.0 che avere la possibilità di ascoltare le notifiche di spazio di archiviazione di Azure coda. Per ulteriori informazioni, vedere [Gestione notifiche del processo di servizi di supporto].
    
    Proprietà Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
    
    Il tipo di Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
    
    Il tipo di Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
    
    Il tipo di Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
    
    Il tipo di Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
    
    Il tipo di Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dipendenza da Client di archiviazione Azure SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dipendenza da OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Versione finale di dicembre 2012

### <a name="dec_12_dotnet_changes"></a>Modifiche Media Services .NET SDK Azure

* IntelliSense: Aggiunta la documentazione Intellisense mancante di vario tipo.

* Microsoft.Practices.TransientFaultHandling.Core: Fissa un problema nel punto in cui il SDK fosse ancora una dipendenza a una versione precedente di questo assembly. A questo punto il SDK fa riferimento alla versione 5.1.1209.1 di questo assembly.

Risoluzioni per i problemi rilevati in novembre 2012 SDK:

* IAsset.Locators.Count: Questo conteggio è ora correttamente segnalato per nuove interfacce IAsset dopo l'eliminazione di indicatori tutti.

* IAssetFile.ContentFileSize: Questo valore viene ora correttamente impostato dopo un caricamento da IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: È possibile impostare questa proprietà ora durante la creazione di un file di risorse. Era precedentemente di sola lettura.

* IAssetFile.Upload(filepath): Fissa un problema nel punto in cui è stato generazione l'errore seguente di questo metodo di caricamento icona del durante il caricamento di più file per la risorsa. Errore: "Server non è possibile autenticare la richiesta. Verificare che il valore dell'intestazione di autorizzazione è costituito correttamente tra cui la firma."

* IAssetFile.UploadAsync: Fissa un problema nel punto in cui non più di 5 possono caricare i file contemporaneamente.

* IAssetFile.UploadProgressChanged: Questo evento è ora fornito da SDK.

* IAssetFile.DownloadAsync (stringa, BlobTransferClient, ILocator, CancellationToken): overload di questo metodo è ora fornito.

* IAssetFile.DownloadAsync: Fissa un problema nel punto in cui non più di 5 Impossibile scaricare i file contemporaneamente.

* IAssetFile.Delete(): Fissa un problema nel punto in cui chiamare delete può generare un'eccezione se nessun file caricato per la IAssetFile.

* Processi: Fissa un problema nel punto in cui concatenazione un "MP4 all'attività di flussi smussate" con un "PlayReady protezione attività" utilizzando un modello di processo non creare tutte le attività del tutto.

* EncryptionUtils.GetCertificateFromStore(): Questo metodo non è più eccezione un riferimento null a causa di errori di trovare il certificato in base a problemi di configurazione di certificati.

##<a id="november_changes_12"></a>Versione finale di novembre 2012

Le modifiche indicate in questa sezione sono inclusi in novembre 2012 (versione 2.0.0.0) aggiornamenti SDK. Queste modifiche possono richiedere il codice scritto per l'anteprima di giugno 2012 SDK rilasciare per modificare o riscrittura.

* Risorse
    
    IAsset.Create(assetName) è la funzione di creazione di risorse solo. IAsset.Create non è più Carica file durante la chiamata al metodo. Utilizzare IAssetFile per il caricamento.
    
    Il metodo IAsset.Publish e il valore di enumerazione AssetState.Publish sono state rimosse dal SDK di servizi. Qualsiasi codice basato su questo valore deve essere scritta nuovamente.

* FileInfo

    Questa classe è stato rimossa e sostituita da IAssetFile.

    IAssetFiles

    IAssetFile sostituisce FileInfo e presenta un comportamento diverso. Per utilizzarla, creare un'istanza di oggetto IAssetFiles, seguito da un caricamento di file tramite Media Services SDK o Azure archiviazione SDK. È possibile utilizzare overload IAssetFile.Upload seguenti:

    * IAssetFile.Upload(filePath): Icona del metodo che blocca il thread ed è consigliato solo quando il caricamento di un singolo file.
    
    * IAssetFile.UploadAsync (percorso file, blobTransferClient, locator, cancellationToken): metodo asincrono. Si tratta del meccanismo di caricamento preferito. 

    Errore noto: tramite cancellationToken verranno effettivamente Annulla il caricamento; Tuttavia, lo stato di annullamento delle attività può essere uno dei vari stati. Correttamente, è necessario intercettare e gestire le eccezioni.

* Indicatori
    
    Sono state rimosse le versioni specifiche di origine. Il contesto SA specifico. Locators.CreateSasLocator (bene, accessPolicy) verranno contrassegnate obsoleto o rimosse dal GA. Vedere la sezione Locator in nuove funzionalità per il nuovo comportamento.


##<a id="june_changes_12"></a>Versione Preview giugno 2012

Le seguenti funzionalità è stato della versione di novembre di SDK.

* Eliminazione di entità

    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey oggetti eliminati ora a livello di oggetto, ad esempio IObject.Delete() invece che richiede l'eliminazione della raccolta di cloudMediaContext.ObjCollection.Delete(objInstance).

* Indicatori

    È ora necessario creare utilizzando il metodo CreateLocator e utilizzare i valori di enumerazione LocatorType.SAS o LocatorType.OnDemandOrigin come argomento per il tipo specifico di locator che si desidera creare.

    Nuove proprietà sono stati aggiunti indicatori per rendere più semplice ottenere URI utilizzabile per il contenuto. Questo ristrutturazione di Locator concepiti per offrire una maggiore flessibilità per una futura estensibilità di terze parti e di aumentare facilità di utilizzo per le applicazioni client di elementi multimediali.

* Supporto per il metodo asincrono

    Il supporto asincrono è stata aggiunta a tutti i metodi.


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Forum MSDN di servizi multimediali Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Riferimento all'API REST di servizi multimediali Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Informazioni sui prezzi servizi multimediali di Microsoft]: http://azure.microsoft.com/pricing/details/media-services/
[Metadati di input]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadati di output]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[La distribuzione di contenuti]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[L'indicizzazione di file multimediali con indicizzatore Media Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Utilizzo dei servizi multimediali Azure flusso in diretta]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Utilizzo di dinamico crittografia AES a 128 e il servizio di recapito chiave]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Utilizza la crittografia PlayReady dinamiche e il servizio di recapito di licenza]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Informazioni generali sui modelli di licenza PlayReady servizi multimediali di Microsoft]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Spazio di archiviazione di flusso contenuto crittografato]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Salvataggio di un pacchetto dinamico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog di Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protezione flusso smussata con PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Riprovare logica di servizi multimediali di SDK per .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Picchi Prato annuncia EDIUS 7 Streaming tramite il Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[I nomi dei file di Output codificatore di servizi di controllo Media]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Creazione di sovrapposizioni]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Panorama segmenti Video]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Versioni di Azure Media servizi .NET SDK 3.0.0.1 e 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Servizio controllo di accesso Azure Active Directory (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Connettersi ai servizi di supporto con i servizi di supporto SDK per .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[.NET SDK estensioni dei servizi Media Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[strumenti sdk di Azure]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Gestione dei supporti servizi risorse tra più account di archiviazione]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Gestione degli elementi multimediali servizi notifiche del processo]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 
