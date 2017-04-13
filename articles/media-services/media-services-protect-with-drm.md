<properties
    pageTitle="Crittografia PlayReady e/o Widevine dinamico comuni | Microsoft Azure"
    description="Servizi multimediali di Microsoft Azure consente di eseguire flussi MPEG-tratto Smooth Streaming e Streaming Live Http (HLS) è protetti con Microsoft PlayReady DRM. Consente inoltre a recapito trattino crittografata con Widevine DRM. In questo argomento viene illustrato come crittografare in modo dinamico con PlayReady e Widevine DRM."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>Crittografia PlayReady e/o Widevine dinamico comuni

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Servizi multimediali di Microsoft Azure consente di eseguire flussi MPEG-tratto Smooth Streaming e Streaming Live HTTP (HLS) è protetti con [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). Consente inoltre fornire i flussi di TRATTEGGIO crittografati con Widevine DRM licenze. PlayReady e Widevine vengono crittografati per la specifica della crittografia comuni (ISO/IEC 23001 7 CENC). È possibile utilizzare [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partire dalla versione 3.5.1) o all'API REST per configurare il AssetDeliveryConfiguration per utilizzare Widevine.

Servizi multimediali di fornisce un servizio per l'esecuzione di PlayReady e Widevine DRM licenze. Servizi multimediali vengono forniti anche API che consentono di configurare i diritti e restrizioni desiderate per il runtime PlayReady o Widevine DRM per imporre quando un utente riproduce contenuto protetto. Quando un utente richiede un contenuto protetto DRM, l'applicazione di Windows Media player richiederà una licenza dal servizio di licenza AMS. Il servizio di licenza AMS genererà una licenza per il lettore se è autorizzato. Una licenza PlayReady o Widevine contiene la chiave di decrittografia che può essere utilizzata per il lettore client per decrittografare e flusso dei contenuti.


È inoltre possibile utilizzare i seguenti partner AMS per offrire licenze Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) [castLabs](http://castlabs.com/company/partners/azure/). Per ulteriori informazioni, vedere: integrazione con [Axinom](media-services-axinom-integration.md) e [castLabs](media-services-castlabs-integration.md).

Servizi multimediali di supporta molti modi diversi di autorizzazione degli utenti che effettuano le richieste di codice. I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: aprire o token restrizione. Il criterio con restrizioni token deve essere accompagnato da un token emesso da un servizio Token (sicurezza). Servizi multimediali sono supportati i token nel formato [Semplice Web token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e nel formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Per ulteriori informazioni, vedere Configurare i criteri di autorizzazione della chiave del contenuto.

Per usufruire di crittografia dinamiche, è necessario disporre di un bene che contiene un insieme di file MP4 multi-velocità o con più velocità Smooth Streaming del file di origine. È anche necessario configurare i criteri di recapito per le risorse (descritto più avanti in questo argomento). Quindi, in base al formato specificato nell'URL di trasmissione, server Streaming via richiesta garantisce che il flusso venga recapitato nel protocollo scelto. Di conseguenza, è sufficiente archiviare e pagare per i file in un formato di archiviazione e servizi multimediali la compilazione e fornire la risposta HTTP appropriata in base a ogni richiesta da un client.

In questo argomento potrebbe essere utile per gli sviluppatori che funzionano in applicazioni che forniscono supporto è protetto con più DRMs, ad esempio PlayReady e Widevine. L'argomento viene illustrato come configurare il servizio di recapito licenza PlayReady con criteri di autorizzazione in modo che solo client autorizzati anche venire visualizzato PlayReady o Widevine licenze. Viene spiegato come utilizzare la crittografia di crittografia dinamiche con PlayReady o Widevine DRM sulla trattino.

>[AZURE.NOTE]Per iniziare a utilizzare la crittografia dinamica, è prima necessario ottenere almeno una unità in scala (noto anche come unità di trasmissione). Per ulteriori informazioni, vedere [come ridimensionare un servizio di supporto](media-services-portal-manage-streaming-endpoints.md).


##<a name="download-sample"></a>Download di esempio

È possibile scaricare l'esempio descritto in questo articolo da [qui](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configurazione di crittografia comune dinamiche e servizi di recapito licenza DRM

Di seguito sono passaggi generali che è necessario eseguire per proteggere le risorse con PlayReady tramite il servizio di recapito servizi multimediali di licenza e anche tramite crittografia dinamiche.

1. Creazione di un bene e caricare file in asset.
1. Codificare la risorsa che contiene il file a velocità adattata che impostare MP4.
1. Creare una chiave del contenuto e associarlo bene codificato. Servizi di supporto, la chiave del contenuto contiene chiave di crittografia della risorsa.
1. Configurare i criteri di autorizzazione della chiave del contenuto. I criteri di autorizzazione chiave contenuto devono essere configurato dall'utente e rispettare il client affinché la chiave del contenuto per recapitare al client.

Quando si creano i criteri di autorizzazione chiave contenuto, è necessario specificare quanto segue: metodo PlayReady oppure Widevine, restrizioni sul recapito (aperti o token) e informazioni specifiche per il tipo di consegna chiave che definisce la modalità di recapito la chiave al client (modello di licenza[PlayReady](media-services-playready-license-template-overview.md) o [Widevine](media-services-widevine-license-template-overview.md) ).
1. Configurare i criteri di recapito per una risorsa. La configurazione dei criteri di recapito include: protocollo di recapito (ad esempio MPEG trattino, HLS, unità 10K, Smooth Streaming o tutti), il tipo di crittografia dinamiche (ad esempio comuni crittografia) PlayReady o URL di acquisizione di licenza Widevine.

È possibile applicare criteri diversi per ogni protocollo sulla bene stesso. Ad esempio, è possibile applicare la crittografia PlayReady uniforme/trattino e busta AES a HLS. Tutti i protocolli non definite in un criterio di recapito (ad esempio, aggiungere un singolo criterio che specifica solo HLS come protocollo) non potranno streaming. L'eccezione è se non si dispone di alcun criterio di recapito bene definito tutto. Quindi, tutti i protocolli potrà in chiaro.
1. Creare un indicatore di posizione su richiesta per ottenere un URL di trasmissione.

Si noterà un esempio di .NET completo alla fine dell'argomento.

Nella figura seguente viene illustrato il flusso di lavoro descritto in precedenza. Di seguito viene utilizzato il token per l'autenticazione.

![Proteggere con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Il resto di questo argomento fornisce una spiegazione dettagliata, esempi di codice e collegamenti ad argomenti che illustrano come ottenere le attività descritte in precedenza.

##<a name="current-limitations"></a>Limitazioni corrente

Se si aggiungono o si aggiorna un criterio di recapito bene, è necessario eliminare locator associato (se presente) e creare un nuovo indicatore di posizione.

Limitazione per la crittografia con Widevine con servizi multimediali di Windows Azure: più tasti contenuti non sono attualmente supportati.

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>Creazione di un bene e caricare i file in asset

Per gestire e codificare flusso i video, è necessario caricare prima di tutto il contenuto in servizi multimediali di Microsoft Azure. Dopo aver caricato, il contenuto è archiviato in modo sicuro nel cloud per ulteriori elaborazioni e il flusso.

Per informazioni dettagliate, vedere [Caricare file in un account di servizi di supporto](media-services-dotnet-upload-files.md).

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codificare la risorsa che contiene il file a velocità adattata che impostare MP4

Con la crittografia dinamica è sufficiente consiste nel creare una risorsa che contiene un insieme di file MP4 multi-velocità o con più velocità Smooth Streaming del file di origine. Quindi, in base al formato specificato nella richiesta di manifesto e frammento, il server Streaming via richiesta garantisce ricevere il flusso del protocollo scelto. Di conseguenza, è sufficiente archiviare e pagare per i file nel formato di archiviazione e la compilazione e fornire la risposta appropriata in base a richieste da un client di servizi Media. Per ulteriori informazioni, vedere l'argomento [Introduttivo imballaggio dinamico](media-services-dynamic-packaging-overview.md) .

Per istruzioni su come codificare, vedere [come codificare un bene mediante Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).


##<a id="create_contentkey"></a>Creare una chiave del contenuto e associarlo codificato bene

Servizi di supporto, la chiave del contenuto contiene la chiave che si desidera crittografare un bene con.

Per informazioni dettagliate, vedere [creare contenuto chiave](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurare i criteri di autorizzazione della chiave del contenuto

Servizi multimediali sono supportati più metodi di autenticazione degli utenti che effettuano le richieste di codice. I criteri di autorizzazione chiave contenuto devono essere configurato dall'utente e soddisfare dal client (lettore) per la chiave consegnare al client. I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: aprire o token restrizione.

Per informazioni dettagliate, vedere [Configurare i criteri di autorizzazione chiave contenuto](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurare i criteri di recapito bene 

Configurare i criteri di recapito per le risorse. Alcune informazioni che include la configurazione dei criteri di recapito bene:

- L'URL di acquisizione di licenza DRM. 
- Protocollo di recapito di risorse (ad esempio MPEG trattino, HLS, unità 10K, Smooth Streaming o tutti). 
- Il tipo di crittografia dinamiche (in questo caso, la crittografia comuni). 

Per informazioni dettagliate, vedere [configurare i criteri di recapito della risorsa ](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Creare un flusso locator per ottenere un URL di trasmissione su richiesta

È necessario fornire all'utente l'URL di trasmissione per passaggio uniforme, TRATTINI o HLS.

>[AZURE.NOTE]Se si aggiungono o aggiornare i criteri di recapito del bene, è necessario eliminare un indicatore di posizione esistente (se presente) e creare un nuovo indicatore di posizione.

Per istruzioni su come pubblicare un bene e generare un URL di trasmissione, vedere [creare un URL di trasmissione](media-services-deliver-streaming-content.md).

##<a name="get-a-test-token"></a>Ottenere un test token

È possibile ottenere un test token basata sulla restrizione token utilizzato per i criteri di autorizzazione chiave.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

    
È possibile utilizzare il [Lettore AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) per testare il flusso.

##<a id="example"></a>Esempio


Nell'esempio seguente viene funzionalità che è stata introdotta in Azure Media Services SDK per .net-versione 3.5.2 (in particolare, la possibilità di definire un modello di licenza Widevine e richiedere una licenza di Widevine servizi multimediali di Windows Azure). Il seguente comando pacchetto Nuget sono stato utilizzato per installare il pacchetto:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Creare un nuovo progetto Console.
1. Utilizzare NuGet per installare e aggiungere Azure Media Services .NET SDK.
2. Aggiungere altri riferimenti: Configuration.
2. Aggiungere file di configurazione che contiene il nome dell'account e la chiave:
    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. È possibile ottenere almeno un'unità di trasmissione per l'endpoint flusso dal quale si prevede di recapito del contenuto. Per ulteriori informazioni, vedere: [configurare i punti finali flussi](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Sovrascrivere il codice nel file Program.cs con il codice riportato in questa sezione.
    
    Assicurarsi di aggiornare le variabili in modo che puntino a cartelle in cui si trovano i file di input.
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
                    Console.ReadLine();
                }
        
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
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
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
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
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
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
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>Passaggio successivo

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Vedere anche

[CENC con Multi-DRM e il controllo dell'accesso](media-services-cenc-with-multidrm-access-control.md)

[Configurare imballaggio Widevine con AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Annuncio di servizi di recapito Google Widevine licenza in servizi multimediali di Windows Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
