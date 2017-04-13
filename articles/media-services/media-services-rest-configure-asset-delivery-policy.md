<properties 
    pageTitle="Configurazione dei criteri di recapito bene utilizzando API REST di servizi di supporto | Microsoft Azure" 
    description="In questo argomento viene illustrato come configurare i criteri di recapito bene diversi utilizzando API REST di servizi di supporto." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>Configurazione dei criteri di recapito bene

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Se si prevede di eseguire attività in modo dinamico crittografato, uno dei passaggi del flusso di lavoro di distribuzione di contenuti Media Services è configurato i criteri di recapito per risorse. Il criterio di recapito bene indica servizi multimediali come desiderato per le risorse da consegnare: in quali streaming protocollo deve il bene essere dinamicamente incluso (ad esempio, trattino MPEG, HLS, Smooth Streaming o tutti), se non si desidera crittografare in modo dinamico il bene e come (busta o la crittografia comune).

Questo argomento illustra il motivo e su come creare e configurare i criteri di recapito della risorsa.

>[AZURE.NOTE]Per poter usare imballaggio dinamico e dinamico crittografia, è necessario assicurarsi che almeno un unità in scala (noto anche come flusso unit). Per ulteriori informazioni, vedere [come ridimensionare un servizio di supporto](media-services-portal-manage-streaming-endpoints.md).
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
- Se si dispone di un bene con un indicatore di posizione flusso esistente, non è possibile collegare un nuovo criterio per la risorsa, scollegare un criterio esistente dal bene o aggiornare un criterio di recapito associato alla risorsa.  È innanzitutto necessario rimuovere locator trasmissione, regolare i criteri e quindi ricreare locator flusso.  È possibile utilizzare la stessa locatorId quando si ricrea locator flusso ma è necessario assicurarsi che non è possibile causare problemi per i client poiché contenuto può essere memorizzati nella cache per l'origine o di una rete CDN downstream.

>[AZURE.NOTE] Quando si utilizza l'API REST di servizi multimediali, le considerazioni seguenti:
>
>Quando si accede a entità in servizi di supporto, è necessario impostare i campi specifici dell'intestazione e i valori nelle convocazioni HTTP. Per ulteriori informazioni, vedere [il programma di installazione per lo sviluppo di API REST servizi multimediali](media-services-rest-how-to-use.md).

>Dopo aver completato la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 specificando un altro URI di servizi di supporto. È necessario effettuare chiamate successive al nuovo URI come descritto nella [connessione ai servizi multimediali utilizzando l'API REST](media-services-rest-connect-programmatically.md).


##<a name="clear-asset-delivery-policy"></a>Criteri di recapito Cancella bene

###<a id="create_asset_delivery_policy"></a>Creare criteri di recapito bene
La seguente richiesta HTTP crea un criterio di recapito risorse che specifica per non applicare crittografia dinamiche e per recapitare il flusso in uno dei seguenti protocolli: protocolli trattino MPEG, HLS e Smooth Streaming. 

Per informazioni sui valori che è possibile specificare quando si crea un AssetDeliveryPolicy, vedere la sezione [tipi utilizzati quando si definisce AssetDeliveryPolicy](#types) .   


Richiesta:
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Risposta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>Risorse di collegamento con i criteri di recapito bene

La seguente richiesta HTTP collega bene specificato al criterio di recapito bene per.

Richiesta:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Risposta:

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Criteri di recapito DynamicEnvelopeEncryption bene 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Creare la chiave del contenuto di tipo EnvelopeEncryption e collegare l'elemento

Quando si specificano i criteri di recapito DynamicEnvelopeEncryption, è necessario assicurarsi di creare un collegamento a una chiave del contenuto di tipo EnvelopeEncryption le risorse. Per ulteriori informazioni, vedere: [creazione di una chiave contenuta](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Ottenere l'URL di recapito

È possibile ottenere l'URL di recapito per il metodo di recapito specificato della chiave del contenuto creato nel passaggio precedente. Un client utilizza l'URL restituito per richiedere una chiave AES oppure un PlayReady licenza nell'ordine di riproduzione contenuto protetto.

Specificare il tipo di URL per ottenere nel corpo della richiesta HTTP. Se si desidera proteggere il contenuto con PlayReady, richiedere un URL di acquisizione di licenza PlayReady servizi multimediali, utilizzando 1 per il keyDeliveryType: {"keyDeliveryType": 1}. Se si desidera proteggere il contenuto con la crittografia della busta, richiedere un URL di acquisizione chiave specificando 2 per keyDeliveryType: {"keyDeliveryType": 2}.

Richiesta:
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

Risposta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>Creare criteri di recapito bene

La seguente richiesta HTTP crea **AssetDeliveryPolicy** configurata in modo da applicare la crittografia ingombro dinamico (**DynamicEnvelopeEncryption**) per il protocollo **HLS** (in questo esempio, altri protocolli non potranno streaming). 


Per informazioni sui valori che è possibile specificare quando si crea un AssetDeliveryPolicy, vedere la sezione [tipi utilizzati quando si definisce AssetDeliveryPolicy](#types) .   

Richiesta:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
Risposta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>Risorse di collegamento con i criteri di recapito bene

Vedere [risorse di collegamento con i criteri di recapito bene](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Criteri di recapito DynamicCommonEncryption bene 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Creare la chiave del contenuto di tipo CommonEncryption e collegare l'elemento

Quando si specificano i criteri di recapito DynamicCommonEncryption, è necessario assicurarsi di creare un collegamento a una chiave del contenuto di tipo CommonEncryption le risorse. Per ulteriori informazioni, vedere: [creazione di una chiave contenuta](media-services-rest-create-contentkey.md)).


###<a name="get-delivery-url"></a>Ottenere l'URL di recapito

È possibile ottenere l'URL di recapito per il metodo di recapito PlayReady della chiave del contenuto creato nel passaggio precedente. Un client utilizza l'URL restituito per richiedere una licenza PlayReady nell'ordine di riproduzione contenuto protetto. Per ulteriori informazioni, vedere [Ottenere un URL di recapito](#get_delivery_url).

###<a name="create-asset-delivery-policy"></a>Creare criteri di recapito bene

La seguente richiesta HTTP crea **AssetDeliveryPolicy** configurata in modo da applicare dinamico crittografia comune (**DynamicCommonEncryption**) per il protocollo **Smooth Streaming** (in questo esempio, altri protocolli non potranno streaming). 

Per informazioni sui valori che è possibile specificare quando si crea un AssetDeliveryPolicy, vedere la sezione [tipi utilizzati quando si definisce AssetDeliveryPolicy](#types) .   


Richiesta:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Se si desidera proteggere il contenuto utilizzando Widevine DRM, aggiornare i valori di AssetDeliveryConfiguration per utilizzare WidevineLicenseAcquisitionUrl (che ha il valore 7) e specificare l'URL di un servizio di recapito licenza. È possibile utilizzare i seguenti partner AMS per offrire licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/).

Per esempio: 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Per la crittografia con Widevine, solo sarà eseguita tramite trattino. Assicurarsi che specificare il protocollo di recapito bene trattino (2).
  
###<a name="link-asset-with-asset-delivery-policy"></a>Risorse di collegamento con i criteri di recapito bene

Vedere [risorse di collegamento con i criteri di recapito bene](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Tipi di utilizzati quando si definisce AssetDeliveryPolicy

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

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

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

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

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

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
