<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Come creare un'API Web .NET tramite B2C Directory Active Azure, protetti tramite token di accesso OAuth 2.0 per l'autenticazione."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure B2C Directory attiva: Creare un sito web .NET API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con B2C Azure Active Directory (Azure Active Directory), è possibile proteggere un web API tramite token di accesso OAuth 2.0. Questi token consentono le applicazioni client che consente di eseguire l'autenticazione all'API di Azure Active Directory B2C. In questo articolo viene illustrato come creare un'API "elenco da fare".NET Controller di visualizzazione del modello MVC () che consente agli utenti di operazioni CRUD. Web API è protetto con Azure Active Directory B2C e consente solo agli utenti autenticati di gestire il proprio elenco attività.

## <a name="create-an-azure-ad-b2c-directory"></a>Creare una directory di Azure Active Directory B2C

Prima di poter usare B2C di Azure Active Directory, è necessario creare una directory o del tenant. Una directory è un contenitore per tutti gli utenti, App, gruppi e altro. Se non è una già disponibile, [creare una directory B2C](active-directory-b2c-get-started.md) prima di procedere in questa Guida.

## <a name="create-an-application"></a>Creare un'applicazione

È necessario creare un'app nella directory B2C. In questo modo le informazioni di Azure Active Directory che è necessario comunicare in modo sicuro con l'app. Per creare un'app, seguire [queste istruzioni](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere **un'app web** o **dell'API web** nell'applicazione.
- Utilizzare il **reindirizzamento identificatore uniform resource** `https://localhost:44316/` per l'applicazione web. Questo è il percorso predefinito del client web app per questo esempio di codice.
- Copiare l' **ID dell'applicazione** che sono state assegnate a un'applicazione. È necessario in un secondo momento.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure Active Directory B2C, ogni esperienza utente è definita da un [criterio](active-directory-b2c-reference-policies.md). Il client in questo esempio contiene tre esperienze identità: iscriversi, accedere e modificare il profilo. Sarà necessario creare un criterio per ogni tipo, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando si creano i tre criteri, assicurarsi di:

- Scegliere **l'ID utente per l'abbonamento** o **messaggio di posta elettronica per l'abbonamento** in e il provider di identità.
- Scegliere **nome visualizzato** e altri attributi iscrizione nei criteri di iscrizione.
- Scegliere **nome** e **ID** sulle attestazioni come attestazioni applicazione per ogni criterio. È possibile scegliere anche altri reclami.
- Copiare il **nome** di ogni criterio dopo averlo creato. È necessario i nomi dei criteri in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo la creazione di tre criteri è stata completata, si è pronti per creare l'applicazione.

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione [viene mantenuto per GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Per creare il campione durante il lavoro, è possibile [scaricare un progetto di base come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). È anche possibile duplicare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

L'app completa è [disponibile](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) anche come file con estensione zip o sul `complete` ramo dell'archivio stesso.

Dopo avere scaricato il codice di esempio, aprire il file sln di Visual Studio per iniziare. Il file di soluzione contiene due progetti: `TaskWebApp` e `TaskService`. `TaskWebApp`è un'applicazione web MVC che l'utente interagisce con. `TaskService`è web back-end dell'applicazione API contenente l'elenco di attività di ogni utente.

## <a name="configure-the-task-web-app"></a>Configurare attività web app

Quando un utente interagisce con `TaskWebApp`, il client invia le richieste di Azure Active Directory e consente di ottenere i token che possono essere utilizzati per chiamare la `TaskService` web API. Per accedere all'utente e ottenere i token, è necessario fornire `TaskWebApp` con alcune informazioni sull'applicazione. Nel `TaskWebApp` progetto aperto il `web.config` file nella radice del progetto e sostituire i valori nel `<appSettings>` sezione.  È possibile lasciare il `AadInstance`, `RedirectUri`, e `TaskServiceUrl` valori come-è.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

In questo articolo non tratta la creazione di `TaskWebApp` client.  Per informazioni su come creare un'app web usando B2C di Azure Active Directory, vedere [le esercitazioni di .NET web app](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Proteggere l'API

Quando si dispone di un client che chiama l'API per conto di utenti, è possibile proteggere `TaskService` utilizzando i token del titolare OAuth 2.0. L'API accettare e convalidare i token utilizzando Open Web interfaccia Microsoft per la raccolta .NET (OWIN).

### <a name="install-owin"></a>Installare OWIN
Iniziare con l'installazione la pipeline di autenticazione OAuth OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Immettere i dettagli B2C
Aprire la `web.config` file nella radice della `TaskService` project e sostituire i valori nel `<appSettings>` sezione. Questi valori verranno utilizzati nell'intera raccolta API e OWIN.  È possibile lasciare il `AadInstance` valore invariato.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Aggiungere una classe di avvio OWIN
Aggiungere una classe di avvio OWIN per il `TaskService` progetto denominato `Startup.cs`.  Pulsante destro del mouse sul progetto, scegliere **Aggiungi** **Nuovo elemento**e quindi cercare OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Configurare l'autenticazione OAuth 2.0
Aprire il file `App_Start\Startup.Auth.cs`e implementare la `ConfigureAuth(...)` metodo:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Proteggere il controller di attività
Dopo l'applicazione è configurato per l'autenticazione OAuth 2.0, è possibile proteggere l'API web mediante l'aggiunta di un `[Authorize]` tag al controller di attività. Si tratta del controller in tutta la gestione di elenco di cose da fare entrerà in vigore, in modo che si consiglia di proteggere il controller intero livello di classe. È possibile aggiungere anche il `[Authorize]` tag alle singole azioni per un controllo più preciso.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Ottenere le informazioni utente dal token
`TasksController`Archivia le attività in un database in cui ogni attività dispone di un utente associato "appartiene" l'attività. Il proprietario è identificato da dell'utente **ID oggetto**. (Perché è necessario aggiungere l'ID di oggetto di un'applicazione Richiedi in tutti i criteri.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Infine, creare ed eseguire entrambe `TaskWebApp` e `TaskService`. Iscriversi per l'app tramite un indirizzo di posta elettronica o il nome utente. Creare alcune attività nell'elenco di attività dell'utente e notare come vengono resi persistenti nell'API anche dopo arrestare e riavviare il client.

## <a name="edit-your-policies"></a>Modificare i criteri

Dopo un'API deve essere protetto tramite B2C di Azure Active Directory, è possibile sperimentare criteri dell'applicazione e visualizzare gli effetti (o mancano derivato) sull'API. È possibile modificare le richieste di applicazione nei criteri e modificare le informazioni utente che sono disponibile in web API. Saranno disponibile al proprio web MVC .NET API in reclami che si aggiungono il `ClaimsPrincipal` oggetto, come descritto in precedenza in questo articolo.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
