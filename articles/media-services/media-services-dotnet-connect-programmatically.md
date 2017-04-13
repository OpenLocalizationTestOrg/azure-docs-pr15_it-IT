<properties 
    pageTitle="Connessione all'Account di servizi di supporto utilizzando .NET" 
    description="In questo argomento viene illustrato come connettersi a servizi multimediali abbonamento .NET." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-sdk-for-net"></a>Connessione all'Account di servizi multimediali utilizzando Media Services SDK per .NET

> [AZURE.SELECTOR]
- [RESTO](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect-programmatically.md)


In questo argomento viene descritto come ottenere una livello di programmazione connessione ai servizi multimediali di Microsoft Azure durante la programmazione con Media Services SDK per .NET.


## <a name="connecting-to-media-services"></a>Connettersi ai servizi di supporto

Per connettersi ai servizi di supporto a livello di programmazione, deve hanno precedentemente configurare un account Azure, configurare servizi multimediali in tale account e quindi impostare un progetto di Visual Studio per lo sviluppo con Media Services SDK per .NET. Per ulteriori informazioni, vedere il programma di installazione per lo sviluppo con Media Services SDK per .NET.

Al termine del processo di configurazione account servizi multimediali, è stato acquistato i seguenti valori di connessione richiesta. Consente di stabilire connessioni livello di programmazione per i servizi di supporto.

- Il nome dell'account servizi multimediali.

- La chiave account servizi multimediali.

Per trovare questi valori, accedere al portale di gestione di Azure, selezionare il proprio account di servizio di supporto e fare clic sull'icona "**Gestione delle CHIAVI**" nella parte inferiore della finestra del portale. Fare clic sull'icona accanto a ogni casella di testo valore copiato negli Appunti di sistema.


## <a name="creating-a-cloudmediacontext-instance"></a>Creazione di un'istanza di CloudMediaContext

Per avviare la programmazione per servizi multimediali di è necessario creare un'istanza di **CloudMediaContext** che rappresenta il contesto di server. **CloudMediaContext** include i riferimenti agli insiemi importanti inclusi processi, risorse, file, i criteri di accesso e indicatori.

>[AZURE.NOTE] La classe **CloudMediaContext** non è affidabili. È consigliabile creare un nuovo CloudMediaContext per thread o per set di operazioni.


CloudMediaContext ha cinque costruttore overload. È consigliabile utilizzare costruttori che accettano **MediaServicesCredentials** come parametro. Per ulteriori informazioni, vedere i **Token servizio controllo di accesso riutilizzo** che segue. 

Nell'esempio seguente viene utilizzato il costruttore CloudMediaContext(MediaServicesCredentials credentials) pubblico:

    // _cachedCredentials and _context are class member variables. 
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);
    
    _context = new CloudMediaContext(_cachedCredentials);


## <a name="reusing-access-control-service-tokens"></a>Riutilizzo del servizio token di accesso del controllo

In questa sezione viene illustrato come riutilizzare i token di servizio controllo di accesso tramite costruttori CloudMediaContext che accettano MediaServicesCredentials come parametro.


[Controllo dell'accesso Azure Active Directory](https://msdn.microsoft.com/library/hh147631.aspx) (noto anche come servizio controllo di accesso o ACS) è un servizio basato su cloud che offre un modo semplice di autenticazione e autorizzazione degli utenti di accedere alle applicazioni web. Servizi multimediali di Microsoft Azure controlla l'accesso ai relativi servizi attraverso protocollo OAuth che richiede un token di ACS. Servizi multimediali di riceve i token ACS da un server di autorizzazione.

Quando si sviluppano con Media Services SDK, è possibile scegliere di non gestire i token perché SDK codice responsabili di è. Tuttavia, consentendo alle SDK di gestire i token ACS conduce alle richieste di token non necessarie. Richiesta di token di tempo e utilizza le risorse client e server. Inoltre, il server ACS limita le richieste se il tasso è troppo alto. Il limite è 30 richieste al secondo, per ulteriori informazioni, vedere [Limitazioni del servizio ACS](https://msdn.microsoft.com/library/gg185909.aspx) .

A partire da Media Services SDK versione 3.0.0.0, è possibile riutilizzare i token ACS. Il **CloudMediaContext** che accettano **MediaServicesCredentials** come parametro consentono i token ACS tra più contesti di condivisione. La classe MediaServicesCredentials include le credenziali di servizi di supporto. Se token ACS è disponibile e la data di scadenza è nota, è possibile creare una nuova istanza di MediaServicesCredentials del token e passare al costruttore di CloudMediaContext. Si noti che Media Services SDK Aggiorna automaticamente i token ogni volta che scadono. Esistono due modi per riutilizzare i token ACS, come illustrato negli esempi seguenti.

- È possibile memorizzare l'oggetto **MediaServicesCredentials** in memoria (ad esempio, in una variabile di classe statica). Quindi, passare l'oggetto nella cache al costruttore CloudMediaContext. L'oggetto MediaServicesCredentials contiene un token ACS che può essere riutilizzato se è ancora valido. Se il token non è valido, verrà aggiornato dal Media Services SDK utilizzando le credenziali fornite al costruttore MediaServicesCredentials.

    Si noti che l'oggetto **MediaServicesCredentials** Ottiene un token valido dopo il RefreshToken la chiamata. **CloudMediaContext** chiama il metodo **RefreshToken** nel costruttore. Se si intende salvare i valori dei token in un archivio esterno, assicurarsi che verificare se il valore di TokenExpiration è valido prima di salvare i dati token. Se non è valido, chiamare RefreshToken prima di memorizzazione nella cache.

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

        
        // Use the cached credentials to create a new CloudMediaContext object.
        if(_cachedCredentials == null)
        {
            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
        }
        
        CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- È anche possibile memorizzare la stringa AccessToken e i valori TokenExpiration. I valori in un secondo momento potrebbero essere utilizzati per creare un nuovo oggetto MediaServicesCredentials con i dati memorizzati nella cache token.  Questa operazione è particolarmente utile per gli scenari in cui il token può essere sicuro condivisi tra più processi o computer.

    Frammenti di codice seguenti chiamano i metodi SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage e UpdateTokenDataInExternalStorageIfNeeded che non sono definiti in questo esempio. È possibile definire questi metodi per archiviare, recuperare e aggiornare i dati token in un archivio esterno. 

        CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
        
        // Get token values from the context.
        var accessToken = context1.Credentials.AccessToken;
        var tokenExpiration = context1.Credentials.TokenExpiration;
        
        // Save token values for later use. 
        // The SaveTokenDataToExternalStorage method should check 
        // whether the TokenExpiration value is valid before saving the token data. 
        // If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
        SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
        
    Usare i valori dei token salvati per creare MediaServicesCredentials.


        var accessToken = "";
        var tokenExpiration = DateTime.UtcNow;
        
        // Retrieve saved token values.
        GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
        
        // Create a new MediaServicesCredentials object using saved token values.
        MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
        {
            AccessToken = accessToken,
            TokenExpiration = tokenExpiration
        };
        
        CloudMediaContext context2 = new CloudMediaContext(credentials);

    Aggiornare la copia token nel caso in cui è stato aggiornato il token da Media Services SDK. 
    
        if(tokenExpiration != context2.Credentials.TokenExpiration)
        {
            UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
        }
        

- Se si hanno più account di servizi multimediali (ad esempio, per carico condivisione scopi o geografico liste di distribuzione) è possibile memorizzare gli oggetti MediaServicesCredentials con la raccolta di System.Collections.Concurrent.ConcurrentDictionary (insieme ConcurrentDictionary rappresenta un insieme di affidabile di coppie chiave/valore che è possibile accedervi contemporaneamente da più thread). È quindi possibile utilizzare il metodo GetOrAdd per ottenere le credenziali memorizzate nella cache. 

        // Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
        private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
            new ConcurrentDictionary<string, MediaServicesCredentials>();
        

        // Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
        static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
        {
            CloudMediaContext cloudMediaContext;
            MediaServicesCredentials mediaServicesCredentials;
        
            mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
                accountName,
                valueFactory => new MediaServicesCredentials(accountName, accountKey));
        
            cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
        
            return cloudMediaContext;
        }
        
## <a name="connecting-to-a-media-services-account-located-in-the-north-china-region"></a>Connessione a un account di servizi multimediali presenti nell'area Cina Nord America

Se l'account si trova nell'area Cina Nord America, utilizzare il costruttore seguente:

    public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Per esempio:


    _context = new CloudMediaContext(
        new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
        _mediaServicesAccountName,
        _mediaServicesAccountKey,
        "urn:WindowsAzureMediaServices",
        "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## <a name="storing-connection-values-in-configuration"></a>La memorizzazione di valori di connessione configurazione

È altamente consigliabile memorizzare valori di connessione, in particolare sensibili valori, ad esempio il nome dell'account e la password, nella configurazione. Inoltre, è consigliabile crittografare i dati di configurazione riservati. È possibile crittografare l'intero file di configurazione utilizzando Windows File System (crittografia). Per attivarla in un file, pulsante destro del mouse sul file, scegliere **proprietà**e abilitare la crittografia nella scheda Impostazioni **Avanzate** . Oppure è possibile creare una soluzione personalizzata per la crittografia di parti selezionate di un file di configurazione con la configurazione protetta. Vedere [crittografia configurazione informazioni mediante la configurazione protetta](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Il file app seguente contiene i valori di connessione richiesta. I valori di <appSettings> elemento sono i valori obbligatori ottenuto dal processo di installazione account servizi multimediali.

    <configuration>
      <appSettings>
        <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
        <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
      </appSettings>
    </configuration>


Per recuperare i valori di connessione dalla configurazione, è possibile utilizzare la classe **ConfigurationManager** e quindi assegnare i valori ai campi nel codice:
    
    private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
    private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
