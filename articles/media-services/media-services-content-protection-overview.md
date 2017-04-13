<properties 
    pageTitle="Protezione Panoramica del contenuto | Microsoft Azure" 
    description="In questo articolo viene fornita una panoramica della protezione del contenuto con i servizi di supporto." 
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
    ms.date="09/27/2016" 
    ms.author="juliako"/>

#<a name="protecting-content-overview"></a>Protezione Panoramica del contenuto


Servizi multimediali di Microsoft Azure consente di proteggere gli elementi multimediali dal tempo che lascia il computer tramite lo spazio di archiviazione, elaborazione e recapito. Servizi multimediali consente di distribuire i contenuti live e su richiesta in modo dinamico crittografati con la crittografia AES (Advanced Standard) (usando i tasti di crittografia a 128 bit) o una qualsiasi delle principali DRMs: Microsoft PlayReady, Google Widevine e FairPlay di Apple. Servizi multimediali vengono forniti anche un servizio per l'esecuzione di chiavi AES e DRM (PlayReady, Widevine e FairPlay) licenze ai client autorizzati. 

Nella figura seguente illustra i flussi di lavoro di protezione del contenuto che supporta AMS. 

![Proteggere con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Per utilizzare la crittografia dinamica, è prima necessario ottenere almeno un'unità riservata flusso sull'endpoint flusso da cui si desidera eseguire il flusso contenuto crittografato.

In questo argomento viene [concetti e terminologia](media-services-content-protection-overview.md) i contenuti pertinenti per informazioni sulla protezione del contenuto con AMS. L'argomento anche contiene [collegamenti](media-services-content-protection-overview.md#common-scenarios) ad argomenti che illustrano come ottenere le attività di protezione del contenuto. 

##<a name="dynamic-encryption"></a>Crittografia dinamiche

Servizi multimediali di Microsoft Azure consente di inviare il contenuto in modo dinamico crittografato con AES chiave di cancellazione o la crittografia DRM: Microsoft PlayReady, Google Widevine e FairPlay di Apple.

Attualmente, è possibile crittografare i formati di flussi seguenti: HLS, MPEG trattino e Smooth Streaming. Non è possibile crittografare unità 10K streaming formato o l'opzione progressivo Scarica.

Se si desidera per i servizi di supporto per la crittografia di un bene, è necessario associare una chiave di crittografia (CommonEncryption o EnvelopeEncryption) le attività e inoltre configurare i criteri di autorizzazione per la chiave.

È anche necessario configurare i criteri di recapito della risorsa. Se si desidera eseguire il flusso di un bene crittografato di spazio di archiviazione, assicurarsi di specificare come si vuole offrire mediante la configurazione dei criteri di recapito della risorsa.

Quando un flusso viene richiesta da un lettore, servizi multimediali di utilizza la chiave specificata per crittografare in modo dinamico il contenuto mediante AES chiave di cancellazione o la crittografia DRM. Per decrittografare il flusso, il lettore richiederà la chiave del servizio di recapito chiave. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificata per la chiave.

>[AZURE.NOTE]Per usufruire di crittografia dinamiche, è prima necessario ottenere almeno un'unità di trasmissione su richiesta per l'endpoint flusso dal quale si prevede di recapito del contenuto crittografato. Per ulteriori informazioni, vedere [come servizi multimediali di scala](media-services-portal-manage-streaming-endpoints.md).

##<a name="storage-encryption"></a>Crittografia di archiviazione

Utilizzare la crittografia di spazio di archiviazione per crittografare il contenuto chiaro localmente utilizzando la crittografia AES 256 bit e quindi caricarlo su Azure lo spazio di archiviazione in cui è archiviato crittografati inattivi. Risorse è protetti con crittografia di archiviazione sono automaticamente in formato non crittografati e inseriti in un file system crittografato prima di codifica e facoltativamente nuovamente crittografati prima di caricare nuovamente come una nuova risorsa di output. Il caso di utilizzo principale per la crittografia di spazio di archiviazione è quando si desidera proteggere i file di supporto di input di alta qualità con crittografia inattivi su disco.

Per offrire una risorsa crittografato di spazio di archiviazione, è necessario configurare i criteri di recapito del bene in modo che servizi multimediali di sa come si desidera distribuire il contenuto. Prima che la risorsa può essere alterata, server di streaming rimuove la crittografia di spazio di archiviazione e flusso il contenuto utilizzando i criteri di consegna specificata (ad esempio AES, crittografia comune o senza crittografia).

## <a name="common-encryption-cenc"></a>Crittografia comune (CENC)

Viene utilizzata la crittografia comune per la crittografia di contenuto con PlayReady o / e Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Usa la crittografia aapl cbcs

Cbcs aapl viene usato per crittografare il contenuto con FairPlay.

## <a name="envelope-encryption"></a>Crittografia busta 

Usare questa opzione se si desidera proteggere il contenuto con chiave chiaro AES a 128. Se si desidera un'opzione più sicura, scegliere una del DRMs elencati in questo argomento. 

##<a name="licenses-and-keys-delivery-service"></a>Servizio di recapito licenze e chiavi

Servizi multimediali di fornisce un servizio per l'esecuzione di licenze DRM (PlayReady, Widevine, FairPlay) e AES deselezionare tasti ai client autorizzati. È possibile utilizzare [il portale di Azure](media-services-portal-protect-content.md), API REST o Media Services SDK per .NET per configurare i criteri di autenticazione e l'autorizzazione per le licenze e chiavi.

##<a name="token-restriction"></a>Restrizione token

I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: aprire o token restrizione. Il criterio con restrizioni token deve essere accompagnato da un token emesso da un servizio Token (sicurezza). Servizi multimediali sono supportati i token in JSON Web Token (JWT) e il formato semplice Web token (SWT). Servizi multimediali non fornisce servizi Token protetti. È possibile creare un servizio token di sicurezza personalizzati o utilizzare Microsoft Azure ACS ai token problema. Il servizio token di sicurezza deve essere configurato per creare un token firmato con specificato chiave e problema attestazioni specificato nella configurazione del token restrizione. Il servizio di recapito chiave servizi multimediali restituirà la chiave richiesta (o licenza) per il client se il token è valido e le richieste di token corrispondono quelli configurati per il tasto (o licenza).

Quando la configurazione del token limitata criteri, è necessario specificare la chiave primaria verifica, emittente e i parametri di gruppo di destinatari. La chiave primaria verifica contiene la chiave firmati con il token, emittente è il servizio token di sicuro che genera il token. Gruppo di destinatari (a volte chiamato ambito) descrive lo scopo del token o la risorsa il token autorizza l'accesso a. Il servizio di recapito chiave servizi multimediali convalida che questi valori nel token corrispondano ai valori nel modello.

##<a name="streaming-urls"></a>URL di trasmissione

Se la risorsa è stata crittografata con più DRM, è consigliabile utilizzare un tag di crittografia nell'URL flusso: (formato = 'm3u8 aapl' crittografia = 'xxx').

Le considerazioni seguenti:

- È possibile specificare solo zero o un tipo di crittografia.
- Tipo di crittografia non deve essere specificato nell'url se solo una crittografia è stata applicata per la risorsa.
- Tipo di crittografia è maiuscole e minuscole.
- È possibile specificare i tipi di crittografia seguenti:  
    - **cenc**: crittografia comune (Playready o Widevine)
    - **cbcs aapl**: Fairplay
    - **CBC**: la crittografia AES a busta.

##<a name="common-scenarios"></a>Scenari comuni

Gli argomenti seguenti viene illustrato come proteggere il contenuto di spazio di archiviazione, offrire dinamicamente crittografato multimediali, utilizzare il servizio di recapito tasto/licenza AMS

- [Proteggere con AES](media-services-protect-with-aes128.md) 
- [Proteggere con PlayReady e/o Widevine](media-services-protect-with-drm.md)
- [Flusso del protetta contenuto HLS con FairPlay di Apple e/o PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Altri scenari

- Informazioni su [come integrare servizio licenze PlayReady Azure con server di crittografia/trasmissione](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [Utilizzare castLabs per fornire licenze DRM su servizi multimediali di Windows Azure](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Collegamenti correlati

[Annuncio PlayReady come servizio e dinamico crittografia AES con servizi multimediali di Windows Azure](http://mingfeiy.com/playready)

[Azure PlayReady servizi multimediali licenza recapito prezzi spiegazione](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Come eseguire il debug per flusso di crittografia AES in servizi multimediali di Windows Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticazione token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[MVC OWIN servizi di integrazione Azure multimediali in base a app con Azure Active Directory e limitare la distribuzione di contenuti chiave in base a reclami JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usare Azure ACS ai token problema](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
