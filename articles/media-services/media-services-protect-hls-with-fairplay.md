<properties 
    pageTitle="Proteggere il contenuto con FairPlay di Apple e/o Microsoft PlayReady HLS | Microsoft Azure" 
    description="In questo argomento fornisce una panoramica e viene illustrato come utilizzare i servizi multimediali di Windows Azure crittografare in modo dinamico il contenuto HTTP Live Streaming (HLS) con FairPlay di Apple. Viene illustrato come utilizzare il servizio di recapito servizi multimediali di licenza per recapitare FairPlay licenze ai client." 
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

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Proteggere il contenuto con FairPlay di Apple e/o Microsoft PlayReady HLS

Servizi multimediali di Azure consente di crittografare in modo dinamico il contenuto HTTP Live Streaming (HLS) nei formati seguenti:  

- **Tasto Cancella AES a 128 busta** 

    L'intero blocco verrà crittografato utilizzando la modalità **CBC AES a 128** . La decrittografia del flusso è supportata dagli iOS e Windows Media player OSX a livello nativo. Per ulteriori informazioni, vedere [l'articolo](media-services-protect-with-aes128.md).

- **Apple FairPlay** 

    Gli esempi di video e audio singoli vengono crittografati utilizzando la modalità **CBC AES a 128** . **Streaming FairPlay** (F/s) è integrato con i sistemi operativi dispositivo, con supporto nativo iOS e programmi TV Apple. Safari in OS X consente f/s utilizzando supporto interface crittografati Media estensioni (EME).
- **Microsoft PlayReady**

Nella figura seguente mostra il flusso di lavoro **HLS + FairPlay e/o PlayReady crittografia dinamiche** .

![Proteggere con FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

In questo argomento viene illustrato come utilizzare i servizi multimediali di Windows Azure crittografare in modo dinamico il contenuto HLS con FairPlay di Apple. Viene illustrato come utilizzare il servizio di recapito servizi multimediali di licenza per recapitare FairPlay licenze ai client.

>[AZURE.NOTE] Se si desidera anche crittografare il contenuto HLS con PlayReady, è necessario creare una chiave di contenuto comune e associare le risorse. È anche necessario configurare i criteri di autorizzazione della chiave del contenuto, come descritto nell'argomento [con PlayReady dinamico crittografia comune](media-services-protect-with-drm.md) .

    
## <a name="requirements-and-considerations"></a>Requisiti e le informazioni

- Di seguito sono necessari quando si usa AMS per recapitare HLS crittografate con FairPlay e per recapitare FairPlay licenze.

    - Un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
    - Un account di servizi di supporto. Per creare un account di servizi multimediali, vedere [Creazione di Account](media-services-portal-create-account.md).
    - Iscriversi a [Apple sviluppo programma](https://developer.apple.com/).
    - Apple richiede il proprietario del contenuto ottenere il [pacchetto di distribuzione](https://developer.apple.com/contact/fps/). Stato della richiesta che è già implementata KSM (modulo di protezione chiave) con servizi multimediali di Windows Azure e che venga richiesta pacchetto f/s finale. Si verificherà istruzioni nel pacchetto f/s finale per generare certificazione e ottenere ASK, verrà utilizzato per configurare FairPlay. 

    - Azure Media Services .NET SDK versione **3.6.0** o versione successiva.

- Sul lato chiave recapito AMS, è necessario impostare le operazioni seguenti:
    - **App certificazione (CA)** - file PFX contenente chiave privata. Il file viene creato dal cliente e crittografato con una password allo stesso cliente. 
        
        Quando il cliente configura i criteri di recapito chiave, forniscono PFX nel formato di base 64 e la password.

        La procedura seguente viene descritto come generare un certificato pfx per FairPlay.
        
        1. Installare OpenSSL da https://slproweb.com/products/Win32OpenSSL.html
        
            Passare alla cartella contenente il certificato FairPlay e altri file recapitati da Apple.
        
        2. Riga di comando in cui convertire cer pem:
        
            "C:\OpenSSL-Win32\bin\openssl.exe" x509-informare der-in fairplay.cer-fuori fairplay out.pem
        
        3. Riga di comando in cui convertire pem pfx con la chiave privata (la password per il file pfx quindi richiesto da OpenSSL).
        
            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-esportare - fairplay out.pfx-privatekey.pem inkey-in fairplay out.pem - passin file:privatekey-pem-pass.txt 
        
    - **La password del certificato di app** - password cliente per la creazione del file. pfx.
    - **Certificato di app password ID** - il cliente deve caricare la password simile a come vengono caricare altri tasti AMS e usare il valore di enumerazione **ContentKeyType.FairPlayPfxPassword** . Di conseguenza, verrà visualizzato id AMS che tratta hanno bisogno di utilizzare all'interno dell'opzione di criteri di recapito chiave.
    - **iv** - valore casuale 16 byte, deve corrispondere iv nei criteri di recapito di risorse. Cliente genera l'IV e la inserisce in entrambe le posizioni: bene recapito criteri e chiave recapito opzione dei criteri. 
    - **CHIEDI** - CHIEDI (applicazione segreto chiave) viene visualizzato quando si genera certificazione nel portale per sviluppatori di Apple. Ogni team di sviluppo riceverà un ASK univoco. Salvare una copia della ricerca e archiviarlo in un luogo sicuro. È necessario configurare ASK come FairPlayAsk a servizi multimediali di Windows Azure in un secondo momento. 
    -  **Chiedere ID** - ottenuto quando il cliente carica ASk in AMS. Il cliente deve caricare ASk utilizzando il valore di enumerazione **ContentKeyType.FairPlayASk** . Di conseguenza, viene restituito l'id AMS si tratta di cosa deve essere utilizzato quando si imposta l'opzione di criteri di recapito chiave.

- Da parte del client f/s, è necessario impostare le operazioni seguenti:
    - **App certificazione (CA)** - file.cer/.der contenente chiave pubblica che OS utilizza per crittografare alcuni payload. AMS è necessario conoscere perché viene richiesto dal Windows Media player. Il servizio di recapito chiave decrittografa utilizzando la chiave privata corrispondente.

- Per la riproduzione di un flusso crittografato FairPlay, è necessario ottenere prima ASK parte reale e quindi generare un certificato reale. Processo crea tutte le 3 parti:

    -  DER, 
    -  PFX e 
    -  la password per il pfx.
 
- Client che supportano HLS con la crittografia **AES a 128 CBC** : Safari in OS X, TV, Apple iOS.

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Procedura di configurazione FairPlay dinamico crittografia e la licenza di servizi di recapito

Di seguito sono passaggi generali che è necessario eseguire per proteggere le risorse con FairPlay, tramite il servizio di recapito servizi multimediali di licenza e anche tramite crittografia dinamiche.

1. Creazione di un bene e caricare file in asset. 
1. Codificare la risorsa che contiene il file a velocità adattata che impostare MP4.
1. Creare una chiave del contenuto e associarlo bene codificato.  
1. Configurare i criteri di autorizzazione della chiave del contenuto. Quando si creano i criteri di autorizzazione chiave contenuto, è necessario specificare le informazioni seguenti: 
    
    - metodo di recapito (in questo caso FairPlay), 
    - Configurazione delle opzioni di criteri FairPlay. Per informazioni dettagliate su come configurare FairPlay, vedere metodo ConfigureFairPlayPolicyOptions() nell'esempio seguente.
    
        >[AZURE.NOTE] In genere, desiderata configurare le opzioni di criteri FairPlay solo una volta, poiché è sufficiente un insieme di certificazione e ASK.
-restrizioni (aperte o token) - e informazioni specifiche per il tipo di consegna chiave che definisce la modalità di recapito la chiave al client. 
    
2. Configurare i criteri di recapito della risorsa. La configurazione dei criteri di recapito include: 

    - protocollo di recapito (HLS) 
    - il tipo di crittografia dinamica (comuni CBC crittografia), 
    - URL di acquisizione di licenza. 
    
    >[AZURE.NOTE]Se si desidera fornire un flusso di crittografia con FairPlay + DRM un'altra, è necessario configurare i criteri di recapito 
    >
    >- Un IAssetDeliveryPolicy per configurare trattino con CENC (PlayReady + WideVine) e continuo con PlayReady. 
    >- Un altro IAssetDeliveryPolicy per configurare FairPlay per HLS

1. Creare un indicatore di posizione su richiesta per ottenere un URL di trasmissione.

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>Utilizza il recapito chiave FairPlay da applicazioni client/player

I clienti possono sviluppare App lettore con iOS SDK. Per essere in grado di riprodurre il contenuto di FairPlay clienti sono necessario implementare il protocollo di scambio di licenza. Il protocollo di scambio di licenza non è specificato da Apple. In questo caso, ciascun app come inviare richieste di recapito chiave. I servizi di consegna chiave AMS FairPlay prevede SPC in arrivo come messaggio post codificato www-form-url nel formato seguente: 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player non supporta la riproduzione di FairPlay all'esterno della casella. È necessario ottenere il lettore di esempio dall'account di sviluppo di Apple per ottenere la riproduzione di FairPlay in MAC OS x. 
 
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


##<a name="net-example"></a>Esempio di .NET


Nell'esempio seguente viene funzionalità che è stata introdotta in Azure Media Services SDK per .net-versione 3.6.0 (la possibilità di usare servizi multimediali di Windows Azure per fornire il contenuto crittografato con FairPlay). Il seguente comando pacchetto Nuget sono stato utilizzato per installare il pacchetto:

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Creare un progetto Console.
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
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
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
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
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
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
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
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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


##<a name="next-steps-media-services-learning-paths"></a>Passaggi successivi: Servizi multimediali percorsi formativi

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
