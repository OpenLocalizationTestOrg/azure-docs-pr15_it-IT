<properties 
    pageTitle="Configurazione dei criteri di protezione del contenuto tramite il portale di Azure | Microsoft Azure" 
    description="In questo articolo viene illustrato come utilizzare il portale Azure per configurare i criteri di protezione del contenuto. Inoltre, l'articolo viene illustrato come abilitare la crittografia dinamica per le risorse." 
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
    ms.date="10/24/2016"    
    ms.author="juliako"/>

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configurazione dei criteri di protezione del contenuto tramite il portale di Azure

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Panoramica

Microsoft Azure Media Services (AMS) consente di proteggere gli elementi multimediali dal tempo che lascia il computer tramite lo spazio di archiviazione, elaborazione e recapito. Servizi multimediali consente di eseguire il contenuto è crittografato in modo dinamico con la crittografia AES (Advanced Standard) (usando i tasti di crittografia a 128 bit), crittografia comune (CENC) utilizzando PlayReady e/o Widevine DRM e FairPlay di Apple. 

AMS offre un servizio per l'esecuzione di licenze DRM e AES deselezionare tasti ai client autorizzati. Portale di Azure consente di creare una **chiave/licenza criteri di autorizzazione** per tutti i tipi di crittografia.

In questo articolo viene illustrato come configurare i criteri di protezione del contenuto con il portale di Azure. Inoltre, l'articolo viene illustrato come applicare dinamico crittografia alle risorse.

> [AZURE.NOTE]  Se si utilizza il portale classico Azure per creare criteri di protezione, i criteri non vengano visualizzati nel [portale di Azure](https://portal.azure.com/). Tuttavia, tutti i vecchi criteri ancora presenti. È possibile esaminare mediante Azure Media Services .NET SDK o lo strumento di [Elementi multimediali di Azure-esplorazione di servizi](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (per visualizzare i criteri, pulsante destro del mouse sulla bene -> Visualizza informazioni (F4) -> fare clic sulla scheda contenuto chiavi -> fare clic sulla chiave). 
> 
> Se si desidera crittografare le risorse usando nuovi criteri, configurarli con il portale di Azure, fare clic su Salva e riapplicare la crittografia dinamiche. 

## <a name="start-configuring-content-protection"></a>Avviare la configurazione di protezione del contenuto

Per utilizzare il portale per avviare la configurazione di protezione dei contenuti globali al proprio account AMS, eseguire le operazioni seguenti:

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il proprio account di servizi multimediali di Windows Azure.
2. Selezionare **Impostazioni** > **la protezione del contenuto**.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>Criteri di autorizzazione chiave/licenza

AMS supporta più metodi di autenticazione degli utenti che effettuano le richieste di chiave o la licenza. I criteri di autorizzazione chiave contenuto devono essere configurato dall'utente e rispettare i client affinché la chiave/licenza per delived al client. I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: restrizione **aprire** o **token** .

Portale di Azure consente di creare una **chiave/licenza criteri di autorizzazione** per tutti i tipi di crittografia.

###<a name="open"></a>Apri 

Apri restrizione significa che il sistema verrà eseguita la chiave a qualsiasi utente che effettua una richiesta di chiave. Questa limitazione può risultare utile a scopo di testing. 

### <a name="token"></a>Token

Il criterio con restrizioni token deve essere accompagnato da un token emesso da un servizio Token (sicurezza). Servizi multimediali sono supportati i token in JSON Web Token (JWT) e il formato semplice Web token (SWT). Servizi multimediali non fornisce servizi Token protetti. È possibile creare un servizio token di sicurezza personalizzati o utilizzare Microsoft Azure ACS ai token problema. Il servizio token di sicurezza deve essere configurato per creare un token firmato con specificato chiave e problema attestazioni specificato nella configurazione del token restrizione. Il servizio di recapito chiave servizi multimediali restituirà la chiave richiesta (o licenza) per il client se il token è valido e le richieste di token corrispondono quelli configurati per il tasto (o licenza).

Quando la configurazione del token limitata criteri, è necessario specificare la chiave primaria verifica emittente e i parametri di gruppo di destinatari. La chiave primaria verifica contiene la chiave firmati con il token, emittente è il servizio token di sicuro che genera il token. Gruppo di destinatari (a volte chiamato ambito) descrive lo scopo del token o la risorsa il token autorizza l'accesso a. Il servizio di recapito chiave servizi multimediali convalida che questi valori nel token corrispondano ai valori nel modello.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Modello di diritti PlayReady

Per informazioni dettagliate sul modello di diritti PlayReady, vedere [Informazioni generali sui modelli di elementi multimediali servizi PlayReady licenza](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Non persistente

Se si configura licenza come non permanente, vengono solo archiviato in memoria mentre il lettore sta usando la licenza.  

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistent

Se si configura la licenza come persistente, questo viene salvato nell'archivio permanente sul client.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Modello di diritti Widevine

Per informazioni dettagliate sul modello di diritti Widevine, vedere [Informazioni generali sui modelli di licenza Widevine](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Base

Quando si seleziona **base**, il modello verrà creato con tutti i valori predefiniti.

### <a name="advanced"></a>Avanzate

Per informazioni sull'opzione di avanzamento delle configurazioni Widevine, consultare [questo](media-services-widevine-license-template-overview.md) argomento.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configurazione di FairPlay

Per abilitare la crittografia FairPlay, è necessario fornire il certificato di App e applicazione segreto chiave (ASK) tramite l'opzione di configurazione FairPlay. Per informazioni dettagliate sulla configurazione di FairPlay e dei requisiti, vedere [questo](media-services-protect-hls-with-fairplay.md) articolo.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Applicare la crittografia dinamica per le risorse

Per usufruire di crittografia dinamiche, è necessario eseguire le operazioni seguenti:

- Codificare il file di origine in una serie di file MP4 adattata velocità.
- È possibile ottenere almeno un'unità di trasmissione su richiesta per l'endpoint flusso da cui si intende distribuire il contenuto. Per ulteriori informazioni, vedere [come scala flusso unità riservate su richiesta](media-services-portal-manage-streaming-endpoints.md).

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selezione di una risorsa che si desidera crittografare

Per visualizzare tutte le attività, selezionare **Impostazioni** > **risorse**.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Crittografa con AES o DRM

Quando si preme **Crittografa** su una risorsa, viene visualizzata con due opzioni: **AES** o **DRM**. 

#### <a name="aes"></a>AES

AES Cancella verrà attivata la crittografia chiave in tutti i protocolli di trasmissione: Smooth Streaming HLS e MPEG-tratto.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

Quando si seleziona la scheda DRM, viene visualizzata con diverse opzioni dei criteri di protezione del contenuto (che è necessario configurare a questo punto) + un insieme di protocolli di flusso.

- **PlayReady e Widevine con trattino MPEG** - verrà crittografato in modo dinamico il flusso MPEG-tratto con PlayReady e Widevine DRMs.
- **PlayReady e Widevine con FairPlay con HLS + trattino MPEG** - verrà dinamicamente crittografato è flusso MPEG-tratto con PlayReady e Widevine DRMs. Verrà inoltre crittografato i flussi HLS con FairPlay.
- **PlayReady solo con Smooth Streaming, HLS e trattino MPEG** - verrà dinamicamente crittografare Smooth Streaming, HLS, MPEG-tratto flussi con PlayReady DRM.
- **Widevine solo con trattino MPEG** - dinamicamente verrà crittografato è MPEG-tratto con Widevine DRM.
- **FairPlay solo con HLS** - verrà crittografato in modo dinamico il flusso HLS con FairPlay.

Per abilitare la crittografia FairPlay, è necessario fornire il certificato di App e applicazione segreto chiave (ASK) tramite l'opzione di configurazione FairPlay della stessa e di impostazioni di protezione del contenuto.

![Proteggere il contenuto](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Dopo avere effettuato la selezione di crittografia, scegliere **Applica**.

##<a name="next-steps"></a>Passaggi successivi

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





