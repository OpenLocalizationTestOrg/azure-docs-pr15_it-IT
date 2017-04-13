<properties
    pageTitle="Utilizzo dinamico crittografia AES a 128 e il servizio di recapito chiave | Microsoft Azure"
    description="Servizi multimediali di Microsoft Azure consente di distribuire i contenuti crittografati con chiavi di crittografia AES 128 bit. Servizi multimediali vengono forniti anche il servizio di recapito chiave che offre le chiavi di crittografia per gli utenti autorizzati. In questo argomento viene illustrato come crittografare con AES a 128 e usare il servizio di recapito chiave dinamicamente."
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

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Utilizzo di dinamico crittografia AES a 128 e il servizio di recapito chiave

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Panoramica

>[AZURE.NOTE] Guardare [questo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) video per una panoramica su come proteggere il contenuto multimediale con la crittografia AES.

Servizi multimediali di Microsoft Azure consente di eseguire flussi smussati crittografati con la crittografia AES (Advanced Standard) (usando i tasti di crittografia a 128 bit) e Http Live-Streaming (HLS). Servizi multimediali vengono forniti anche il servizio di recapito chiave che offre le chiavi di crittografia per gli utenti autorizzati. Se si desidera per i servizi di supporto per la crittografia di un bene, è necessario associare una chiave di crittografia all'attività e inoltre configurare i criteri di autorizzazione per la chiave. Quando un flusso viene richiesta da un lettore, servizi multimediali di utilizza la chiave specificata per crittografare in modo dinamico il contenuto utilizzando la crittografia AES. Per decrittografare il flusso, il lettore richiederà la chiave del servizio di recapito chiave. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificata per la chiave.

Servizi multimediali sono supportati più metodi di autenticazione degli utenti che effettuano le richieste di codice. I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: aprire o token restrizione. Il criterio con restrizioni token deve essere accompagnato da un token emesso da un servizio Token (sicurezza). Servizi multimediali sono supportati i token nel formato [Semplice Web token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e nel formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Per ulteriori informazioni, vedere [configurare i criteri di autorizzazione della chiave del contenuto](media-services-protect-with-aes128.md#configure_key_auth_policy).

Per usufruire di crittografia dinamiche, è necessario disporre di un bene che contiene un insieme di file MP4 multi-velocità o con più velocità Smooth Streaming del file di origine. È anche necessario configurare i criteri di recapito per le risorse (descritto più avanti in questo argomento). Quindi, in base al formato specificato nell'URL di trasmissione, server Streaming via richiesta garantisce che il flusso venga recapitato nel protocollo scelto. Di conseguenza, è sufficiente archiviare e pagare per i file nel formato di archiviazione e la compilazione e fornire la risposta appropriata in base a richieste da un client di servizi Media.

In questo argomento potrebbe essere utile per gli sviluppatori che funzionano in applicazioni che forniscono supporto protetto. L'argomento viene illustrato come configurare il servizio di recapito chiave con criteri di autorizzazione in modo che solo client autorizzati anche venire visualizzato le chiavi di crittografia. Viene illustrato come utilizzare la crittografia dinamica.

>[AZURE.NOTE]Per iniziare a utilizzare la crittografia dinamica, è prima necessario ottenere almeno una unità in scala (noto anche come unità di trasmissione). Per ulteriori informazioni, vedere [come ridimensionare un servizio di supporto](media-services-portal-manage-streaming-endpoints.md).

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Dinamico crittografia AES a 128 e flusso di lavoro di recapito chiave servizio

Di seguito sono passaggi generali che è necessario eseguire per crittografare le risorse con AES, tramite il servizio di recapito chiave servizi multimediali e anche tramite crittografia dinamiche.

1. [Creazione di un bene e caricare i file in asset](media-services-protect-with-aes128.md#create_asset).
1. [Codifica bene contenente il file da velocità adattata che impostare MP4](media-services-protect-with-aes128.md#encode_asset).
1. [Creare una chiave del contenuto e associarlo bene codificato](media-services-protect-with-aes128.md#create_contentkey). Servizi di supporto, la chiave del contenuto contiene chiave di crittografia della risorsa.
1. [Configurare i criteri di autorizzazione della chiave del contenuto](media-services-protect-with-aes128.md#configure_key_auth_policy). I criteri di autorizzazione chiave contenuto devono essere configurato dall'utente e rispettare il client affinché la chiave del contenuto per essere recapitato al client.
1. [Configurare i criteri di recapito per una risorsa](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configurazione dei criteri di recapito include: principali URL di acquisizione e inizializzazione vettore IV (AES 128 richiede dello stesso vettore da fornire per la crittografia e decrittografia), protocollo di recapito (ad esempio MPEG trattino, HLS, unità 10K, Smooth Streaming o tutti), il tipo di crittografia dinamiche (ad esempio, busta o senza crittografia dinamico).

È possibile applicare criteri diversi per ogni protocollo sulla bene stesso. Ad esempio, è possibile applicare la crittografia PlayReady uniforme/trattino e busta AES a HLS. Tutti i protocolli non definite in un criterio di recapito (ad esempio, aggiungere un singolo criterio che specifica solo HLS come protocollo) non potranno streaming. L'eccezione è se non si dispone di alcun criterio di recapito bene definito tutto. Quindi, tutti i protocolli potrà in chiaro.

1. [Creare un indicatore di posizione su richiesta](media-services-protect-with-aes128.md#create_locator) per ottenere un URL di trasmissione.

L'argomento viene illustrato anche [come un'applicazione client può richiedere una chiave dal servizio di recapito chiave](media-services-protect-with-aes128.md#client_request).

Si noterà un.NET completo [esempio](media-services-protect-with-aes128.md#example) alla fine dell'argomento.

Nella figura seguente viene illustrato il flusso di lavoro descritto in precedenza. Di seguito viene utilizzato il token per l'autenticazione.

![Proteggere con AES a 128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Il resto di questo argomento fornisce una spiegazione dettagliata, esempi di codice e collegamenti ad argomenti che illustrano come ottenere le attività descritte in precedenza.

##<a name="current-limitations"></a>Limitazioni corrente

Se si aggiungono o aggiornare i criteri di recapito del bene, è necessario eliminare un indicatore di posizione esistente (se presente) e creare un nuovo indicatore di posizione.

##<a id="create_asset"></a>Creazione di un bene e caricare i file in asset

Per gestire e codificare flusso i video, è necessario caricare prima di tutto il contenuto in servizi multimediali di Microsoft Azure. Dopo aver caricato, il contenuto è archiviato in modo sicuro nel cloud per ulteriori elaborazioni e il flusso. 

Per informazioni dettagliate, vedere [Caricare file in un account di servizi di supporto](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codificare la risorsa che contiene il file a velocità adattata che impostare MP4

Con la crittografia dinamica è sufficiente consiste nel creare una risorsa che contiene un insieme di file MP4 multi-velocità o con più velocità Smooth Streaming del file di origine. Quindi, in base al formato specificato nella richiesta manifesto o frammento di Streaming via richiesta server garantisce ricevere il flusso del protocollo scelto. Di conseguenza, è sufficiente archiviare e pagare per i file nel formato di archiviazione e la compilazione e fornire la risposta appropriata in base a richieste da un client di servizi Media. Per ulteriori informazioni, vedere l'argomento [Introduttivo imballaggio dinamico](media-services-dynamic-packaging-overview.md) .

Per istruzioni su come codificare, vedere [come codificare un bene mediante Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Creare una chiave del contenuto e associarlo codificato bene

Servizi di supporto, la chiave del contenuto contiene la chiave che si desidera crittografare un bene con.

Per informazioni dettagliate, vedere [creare contenuto chiave](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configurare i criteri di autorizzazione della chiave del contenuto

Servizi multimediali sono supportati più metodi di autenticazione degli utenti che effettuano le richieste di codice. I criteri di autorizzazione chiave contenuto devono essere configurato dall'utente e soddisfare dal client (lettore) per la chiave consegnare al client. I criteri di autorizzazione chiave contenuto possono avere una o più restrizioni di autorizzazione: aprire, token restrizione o limitazione IP.

Per informazioni dettagliate, vedere [Configurare i criteri di autorizzazione chiave contenuto](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configurare i criteri di recapito bene 

Configurare i criteri di recapito per le risorse. Alcune informazioni che include la configurazione dei criteri di recapito bene:

- L'URL di acquisizione di chiave. 
- L'inizializzazione vettore (IV) da utilizzare per la crittografia della busta. AES 128 richiede dello stesso vettore da fornire per la crittografia e decrittografia. 
- Protocollo di recapito di risorse (ad esempio MPEG trattino, HLS, unità 10K, Smooth Streaming o tutti).
- Il tipo di crittografia dinamica (ad esempio, busta AES) o nessun crittografia dinamiche. 

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

##<a id="client_request"></a>Come il client può richiedere una chiave dal servizio di recapito chiave?

Nel passaggio precedente è impostata l'URL che fa riferimento a un file manifesto. Il client deve estrarre le informazioni necessarie dai file manifesto flussi per effettuare una richiesta al servizio di recapito chiave.

###<a name="manifest-files"></a>File manifesto

Il client deve estrarre l'URL (contenente anche contenuto chiave Id (scuola)) valore dal file manifesto. Il client tenterà quindi ottenere la chiave di crittografia dal servizio di recapito chiave. Il client deve anche per estrarre il valore di IV e usare decrittografare il flusso. Frammento seguente viene illustrato il <Protection> elemento del manifesto Smooth Streaming.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

Nel caso dei HLS, manifesto radice viene suddiviso in file segmento. 

Manifesto radice, ad esempio è: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) e contiene un elenco di nomi di file segmento.
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se si apre un file segmento nell'editor di testo (ad esempio, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), deve contenere #EXT X-chiave che indica che il file è crittografato.
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>Richiedere la chiave del servizio di recapito chiave

Il codice seguente viene illustrato come inviare una richiesta al servizio di recapito chiave servizi multimediali mediante una chiave consegna Uri (che è stato estratto dal manifesto) e un token (in questo argomento non parlare con le informazioni su come ottenere i token Web semplici da un servizio Token sicura).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>Esempio

1. Creare un nuovo progetto Console.
1. Utilizzare NuGet per installare e aggiungere le estensioni SDK .NET di Azure Media Services. Installazione di questo pacchetto, anche installa Media Services .NET SDK e aggiunge tutte le altre dipendenze necessari.
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

1. Sovrascrivere il codice nel file Program.cs con il codice riportato in questa sezione.
    
    Assicurarsi di aggiornare le variabili in modo che puntino a cartelle in cui si trovano i file di input.
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
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
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
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
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
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
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
