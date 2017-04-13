<properties 
    pageTitle="Configurare i criteri di recapito bene con .NET SDK | Microsoft Azure" 
    description="In questo argomento viene illustrato come configurare i criteri di recapito diversi bene con Azure Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurare i criteri di recapito bene con .NET SDK
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>Panoramica

Se si prevede di risorse del recapito crittografato, uno dei passaggi del flusso di lavoro di distribuzione di contenuti multimediali Services è configurato i criteri di recapito per risorse. Il criterio di recapito bene indica servizi multimediali come desiderato per le risorse da consegnare: in quali streaming protocollo deve il bene essere dinamicamente incluso (ad esempio, trattino MPEG, HLS, Smooth Streaming o tutti), se non si desidera crittografare in modo dinamico il bene e come (busta o la crittografia comune).

Questo argomento illustra il motivo e su come creare e configurare i criteri di recapito della risorsa.

>[AZURE.NOTE]Per poter usare imballaggio dinamico e dinamico crittografia, è necessario assicurarsi di almeno un'unità di scala (noto anche come flusso unità). Per ulteriori informazioni, vedere [come ridimensionare un servizio di supporto](media-services-portal-manage-streaming-endpoints.md).
>
>Inoltre, le risorse devono contenere un insieme di velocità adattata MP4s o file Smooth Streaming velocità adattata.

È possibile applicare criteri diversi per la risorsa stessa. Ad esempio, è possibile applicare la crittografia PlayReady per la crittografia AES busta e Smooth Streaming trattino MPEG e HLS. Tutti i protocolli non definite in un criterio di recapito (ad esempio, aggiungere un singolo criterio che specifica solo HLS come protocollo) non potranno streaming. L'eccezione è se non si dispone di alcun criterio di recapito bene definito tutto. Quindi, tutti i protocolli potrà in chiaro.

Se si desidera fornire una risorsa crittografato di spazio di archiviazione, è necessario configurare i criteri di recapito della risorsa. Prima che la risorsa può essere alterata, server di streaming rimuove la crittografia di spazio di archiviazione e flusso il contenuto utilizzando i criteri di recapito specificato. Ad esempio, per fornire le risorse crittografate con chiave di crittografia busta avanzate crittografia AES (Standard), impostare il tipo di criteri **DynamicEnvelopeEncryption**. Per rimuovere la crittografia di spazio di archiviazione e flusso bene in chiaro, impostare il tipo di criteri **NoDynamicEncryption**. Esempi che illustrano come configurare questi tipi di criteri seguono.

Come si configurano i criteri di recapito bene a seconda del tipo sarà dinamicamente creare un pacchetto, in modo dinamico crittografare e flusso i protocolli di flussi seguenti: flussi Smooth Streaming, HLS, MPEG trattino e unità 10K.

L'elenco seguente include i formati di utilizzare il flusso uniforme, HLS, trattino e unità 10K.

Smooth Streaming:

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG TRATTINO

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

UNITÀ 10K

{streaming endpoint servizi multimediali di nome account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Per istruzioni su come pubblicare un bene e generare un URL di trasmissione, vedere [creare un URL di trasmissione](media-services-deliver-streaming-content.md).

##<a name="considerations"></a>Considerazioni

- Non è possibile eliminare un AssetDeliveryPolicy associata a una risorsa mentre è presente un indicatore di posizione su richiesta (streaming) per tale risorsa. Il suggerimento consiste nel rimuovere il criterio bene dalla prima di eliminare il criterio.
- Un flusso locator non è possibile creare su una risorsa crittografato di spazio di archiviazione quando non è impostato alcun criterio di recapito della risorsa.  Se la risorsa non lo spazio di archiviazione crittografato, il sistema sarà possibile creare un indicatore di posizione e flusso bene in chiaro senza un criterio di recapito della risorsa.
- È possibile creare più criteri di recapito bene associati a una singola risorsa, ma è possibile specificare solo un modo per gestire un AssetDeliveryProtocol specificato.  Indicare se si tenta di collegare due criteri di recapito per specificare il protocollo AssetDeliveryProtocol.SmoothStreaming che verrà generato un errore perché il sistema non riconosce i contenuti che si vuole da applicare quando un client richiede Smooth Streaming.
- Se si dispone di un bene con un indicatore di posizione flusso esistente, non è possibile collegare un nuovo criterio per la risorsa (è possibile scollegare un criterio esistente dal bene o aggiornare un criterio di recapito associato alla risorsa).  È innanzitutto necessario rimuovere locator trasmissione, regolare i criteri e quindi ricreare locator flusso.  È possibile utilizzare la stessa locatorId quando si ricrea locator flusso ma è necessario assicurarsi che non è possibile causare problemi per i client poiché contenuto può essere memorizzati nella cache per l'origine o di una rete CDN downstream.


##<a name="clear-asset-delivery-policy"></a>Criteri di recapito bene chiaro

Il metodo **ConfigureClearAssetDeliveryPolicy** seguente specifica per non applicare crittografia dinamiche e per recapitare il flusso in uno dei seguenti protocolli: protocolli trattino MPEG, HLS e Smooth Streaming. È possibile applicare questo criterio alle risorse di archiviazione crittografato.

Per informazioni sui valori che è possibile specificare quando si crea un AssetDeliveryPolicy, vedere la sezione [tipi utilizzati quando si definisce AssetDeliveryPolicy](#types) .

pubblico statico void ConfigureClearAssetDeliveryPolicy (IAsset asset) {IAssetDeliveryPolicy criteri = Context. AssetDeliveryPolicies.Create ("Criteri Cancella", AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

risorse. DeliveryPolicies.Add(policy); }

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Criteri di recapito DynamicCommonEncryption bene


Il metodo **CreateAssetDeliveryPolicy** seguente crea **AssetDeliveryPolicy** configurata in modo da applicare dinamico crittografia comune (**DynamicCommonEncryption**) a un protocollo flusso smussato (altri protocolli non potranno streaming). Il metodo accetta due parametri: **risorse** (le attività a cui si desidera applicare i criteri di recapito) e **IContentKey** (la chiave del contenuto di tipo **CommonEncryption** per ulteriori informazioni, vedere: [creazione di una chiave contenuta](media-services-dotnet-create-contentkey.md#common_contentkey)).

Per informazioni sui valori che è possibile specificare quando si crea un AssetDeliveryPolicy, vedere la sezione [tipi utilizzati quando si definisce AssetDeliveryPolicy](#types) .


statica CreateAssetDeliveryPolicy void pubblico (IAsset bene, IContentKey chiave) {Uri acquisitionUrl = chiave. GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

Dizionario < AssetDeliveryPolicyConfigurationKey, string > assetDeliveryPolicyConfiguration = nuovo dizionario < AssetDeliveryPolicyConfigurationKey, stringa > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}};

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Servizi multimediali di Azure consente inoltre di aggiungere la crittografia Widevine. Nell'esempio seguente viene PlayReady e Widevine da aggiungere al criterio di recapito delle risorse.


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        

        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Per la crittografia con Widevine, solo sarà eseguita tramite trattino. Assicurarsi di specificare trattino nel protocollo di recapito risorse.


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Criteri di recapito DynamicEnvelopeEncryption bene 

Il metodo **CreateAssetDeliveryPolicy** seguente crea **AssetDeliveryPolicy** configurata in modo da applicare la crittografia ingombro dinamico (**DynamicEnvelopeEncryption**) a protocolli Smooth Streaming, HLS e trattino (se si decide di non specificare alcuni protocolli, potrà essere streaming). Il metodo accetta due parametri: **risorse** (le attività a cui si desidera applicare i criteri di recapito) e **IContentKey** (la chiave del contenuto di tipo **EnvelopeEncryption** per ulteriori informazioni, vedere: [creazione di una chiave contenuta](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Per informazioni sui valori che è possibile specificare quando si crea un AssetDeliveryPolicy, vedere la sezione [tipi utilizzati quando si definisce AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Tipi di utilizzati quando si definisce AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


