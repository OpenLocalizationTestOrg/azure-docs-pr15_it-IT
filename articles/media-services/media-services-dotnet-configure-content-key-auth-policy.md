<properties 
    pageTitle="Configurare i criteri di autorizzazione chiave contenuto utilizzando Media Services .NET SDK | Microsoft Azure" 
    description="Informazioni su come configurare un criterio di autorizzazioni per una chiave contenuto utilizzando Media Services .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016"
    ms.author="juliako;mingfeiy"/>



# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>Crittografia dinamiche: configurare i criteri di autorizzazione chiave contenuto

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##<a name="overview"></a>Panoramica

Servizi multimediali di Microsoft Azure consente di eseguire flussi MPEG-tratto Smooth Streaming e Streaming Live HTTP (HLS) è protetti con la crittografia AES (Advanced Standard) (usando i tasti di crittografia a 128 bit) o [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS è inoltre possibile recapitare flussi trattino crittografati con Widevine DRM. PlayReady e Widevine vengono crittografati per la specifica della crittografia comuni (ISO/IEC 23001 7 CENC).

Servizi multimediali vengono forniti anche un **Servizio di recapito tasto/licenze** da cui i clienti possono ottenere chiavi AES o licenze PlayReady/Widevine per riprodurre il contenuto crittografato.

Se si desidera per i servizi di supporto per la crittografia di un bene, è necessario associare una chiave di crittografia (**CommonEncryption** o **EnvelopeEncryption**) all'attività (come descritto [di seguito](media-services-dotnet-create-contentkey.md)) e inoltre configurare i criteri di autorizzazione per la chiave (come descritto in questo articolo).

Quando un flusso viene richiesta da un lettore, servizi multimediali di utilizza la chiave specificata per crittografare in modo dinamico il contenuto utilizzando la crittografia AES o DRM. Per decrittografare il flusso, il lettore richiederà la chiave del servizio di recapito chiave. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificata per la chiave.

Servizi multimediali sono supportati più metodi di autenticazione degli utenti che effettuano le richieste di codice. I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: restrizione **aprire** o **token** . Il criterio con restrizioni token deve essere accompagnato da un token emesso da un servizio Token (sicurezza). Servizi multimediali sono supportati i token nel formato **Semplice Web token** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e nel formato **JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Servizi multimediali non fornisce servizi Token protetti. È possibile creare un servizio token di sicurezza personalizzati o utilizzare Microsoft Azure ACS ai token problema. Il servizio token di sicurezza deve essere configurato per creare un token firmato con specificato chiave e problema attestazioni specificato nella configurazione restrizione token (come descritto in questo articolo). Se il token è valido e le richieste nel token corrispondono a quelli configurato per la chiave del contenuto, il servizio di recapito chiave servizi multimediali restituirà la chiave di crittografia al client.

Per ulteriori informazioni, vedere

[Autenticazione token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[MVC OWIN servizi di integrazione Azure multimediali in base a app con Azure Active Directory e limitare la distribuzione di contenuti chiave in base a reclami JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Usare Azure ACS ai token problema](http://mingfeiy.com/acs-with-key-services).

###<a name="some-considerations-apply"></a>Alcune considerazioni:

- Per poter usare imballaggio dinamico e dinamico crittografia, è necessario assicurarsi avere almeno una trasmissione unità riservato. Per ulteriori informazioni, vedere [come ridimensionare un servizio di supporto](media-services-portal-manage-streaming-endpoints.md).
- Le risorse devono contenere un insieme di velocità adattata MP4s o file Smooth Streaming velocità adattata. Per ulteriori informazioni, vedere [codifica una risorsa](media-services-encode-asset.md).
- Caricare e codificare le risorse utilizzando l'opzione **AssetCreationOptions.StorageEncrypted** .
- Se si prevede di più tasti contenuto che richiedono la stessa configurazione dei criteri, è consigliabile creare criteri di autorizzazione unica e riutilizzare con più tasti contenuti.
- Il servizio di recapito chiave memorizza ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni di criteri e restrizioni) per 15 minuti.  Se si crea un ContentKeyAuthorizationPolicy e specificare l'utilizzo di una restrizione "Token", testare e quindi aggiornare i criteri di restrizione "Apri", saranno necessarie circa 15 minuti prima che il criterio passa alla versione "Aperta" dei criteri.
- Se si aggiungono o aggiornare i criteri di recapito del bene, è necessario eliminare un indicatore di posizione esistente (se presente) e creare un nuovo indicatore di posizione.
- Al momento non è possibile crittografare unità 10K streaming formato o l'opzione progressivo Scarica.


##<a name="aes-128-dynamic-encryption"></a>Crittografia AES a 128 dinamica

###<a name="open-restriction"></a>Apri restrizione

Apri restrizione indica che il sistema verrà eseguita la chiave a qualsiasi utente che effettua una richiesta di chiave. Questa limitazione può risultare utile a scopo di testing.

Nell'esempio seguente viene creato un criterio di autorizzazione aperto e lo aggiunge alla chiave del contenuto.

pubblico statico void AddOpenAuthorizationPolicy (IContentKey contentKey) {/ / creare ContentKeyAuthorizationPolicy con restrizioni Apri / / e creare criteri di autorizzazione criteri IContentKeyAuthorizationPolicy = Context.
ContentKeyAuthorizationPolicies.
CreateAsync ("criteri di autorizzazione Apri"). Risultato;

Elenco<ContentKeyAuthorizationPolicyRestriction> restrizioni = nuovo elenco<ContentKeyAuthorizationPolicyRestriction>().
    
        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };
    
        restrictions.Add(restriction);
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");
    
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


###<a name="token-restriction"></a>Restrizione token

In questa sezione viene descritto come creare criteri di autorizzazione chiave contenuto e associarlo con la chiave del contenuto. I criteri di autorizzazione vengono illustrati i requisiti di autorizzazione devono essere soddisfatte per determinare se l'utente è autorizzato a ricevere la chiave (ad esempio, sono gli effetti di elenco "verifica chiave" contengono la chiave firmati con il token).

Per configurare l'opzione di restrizione token, è necessario usare un XML per descrivere i requisiti di autorizzazione del token. La configurazione di restrizione token XML deve essere conforme allo schema XML seguente.

####<a id="schema"></a>Schema di restrizione token
    
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

Quando la configurazione di **token** limitata criteri, è necessario specificare i parametri di** chiave di verifica**, **emittente** e **gruppo di destinatari** principali. La **chiave primaria verifica **contiene la chiave firmati con il token, **emittente** è il servizio token di sicuro che genera il token. Il **gruppo di destinatari** (a volte chiamato **ambito**) descrive lo scopo del token o la risorsa il token autorizza l'accesso a. Il servizio di recapito chiave servizi multimediali convalida che questi valori nel token corrispondano ai valori nel modello. 

Quando si utilizza **Media Services SDK per .NET**, è possibile utilizzare la classe **TokenRestrictionTemplate** per generare il token di restrizione.
Nell'esempio seguente viene creato un criterio di autorizzazione in modalità lettura token. In questo esempio, il client avrebbe presentare un token che contiene: accesso chiave (VerificationKey), un emittente token e sulle attestazioni necessari.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();
    
        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };
    
        restrictions.Add(restriction);
    
        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );
    
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    
        return tokenTemplateString;
    }
    
    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
    
        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
    
        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
    
        return TokenRestrictionTemplateSerializer.Serialize(template);
    }

####<a id="test"></a>Token di test

Per ottenere un token di test basato sulla restrizione token utilizzato per i criteri di autorizzazione chiave, eseguire le operazioni seguenti.
    
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
    
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


##<a name="playready-dynamic-encryption"></a>Crittografia PlayReady dinamiche 

Servizi multimediali consente di configurare i diritti e restrizioni desiderate per il runtime PlayReady DRM per imporre quando un utente tenta di riprodurre contenuto protetto. 

Per proteggere il contenuto con PlayReady, una delle operazioni che è necessario specificare i criteri di autorizzazione è una stringa XML che definisce il [modello di licenza PlayReady](media-services-playready-license-template-overview.md). In Media Services SDK per .NET, le classi **PlayReadyLicenseResponseTemplate** e **PlayReadyLicenseTemplate** consente di definire il modello di licenza PlayReady.

[In questo argomento](media-services-protect-with-drm.md) viene illustrato come crittografare il contenuto con **PlayReady** e **Widevine**.

###<a name="open-restriction"></a>Apri restrizione
    
Apri restrizione indica che il sistema verrà eseguita la chiave a qualsiasi utente che effettua una richiesta di chiave. Questa limitazione può risultare utile a scopo di testing.

Nell'esempio seguente viene creato un criterio di autorizzazione aperto e lo aggiunge alla chiave del contenuto.

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
    
        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
    
    
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

###<a name="token-restriction"></a>Restrizione token

Per configurare l'opzione di restrizione token, è necessario usare un XML per descrivere i requisiti di autorizzazione del token. La configurazione di restrizione token XML deve essere conforme allo schema XML mostrato in [questa](#schema) sezione.
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
                
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    
        return tokenTemplateString;
    }
    
    static private string GenerateTokenRequirements()
    {
    
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
    
        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
    
    
        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
    
        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 
    
    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
       
        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to playback the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


Per ottenere un token di test basato sulla restrizione token utilizzati per l'autorizzazione chiave criteri vedere [in questa](#test) sezione. 

##<a id="types"></a>Tipi di utilizzati quando si definisce ContentKeyAuthorizationPolicy

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType in

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>Passaggio successivo
Dopo avere configurato Criteri di autorizzazione della chiave del contenuto, passare all'argomento [come configurare i criteri di recapito della risorsa](media-services-dotnet-configure-asset-delivery-policy.md) .
 
