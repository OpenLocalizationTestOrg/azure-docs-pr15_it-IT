<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Come creare un'applicazione web che ha accesso aggiuntivo, per l'abbonamento, e la gestione dei profili con Azure Active Directory B2C."
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
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure Active Directory B2C: Creare un'app web .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Utilizzando B2C Azure Active Directory (Azure Active Directory), è possibile aggiungere funzionalità di gestione delle identità potente strumento self-service per un'app web di alcuni rapidi passaggi. In questo articolo viene illustrato come creare un'app web .NET Controller di visualizzazione del modello MVC () che include utente per l'abbonamento, accesso e gestione dei profili. L'app verrà incluso il supporto per l'abbonamento e l'accesso con un nome utente o un messaggio di posta elettronica e utilizzando l'account di social networking, ad esempio Facebook e Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure Active Directory B2C

Prima di poter usare B2C di Azure Active Directory, è necessario creare una directory o del tenant. Una directory è un contenitore per tutti gli utenti, App, gruppi e altro.  Se non è una già disponibile, [creare una directory B2C](active-directory-b2c-get-started.md) prima di procedere in questa Guida.

## <a name="create-an-application"></a>Creare un'applicazione

È necessario creare un'app nella directory B2C. In questo modo le informazioni di Azure Active Directory che è necessario comunicare in modo sicuro con l'app. Per creare un'app, seguire [queste istruzioni](active-directory-b2c-app-registration.md).  Assicurarsi di:

- Includere un' **app web / dell'API web** nell'applicazione.
- Immettere `https://localhost:44316/` come un **reindirizzare URI**. È l'URL predefinito per questo esempio di codice.
- Copiare verso il basso l' **ID dell'applicazione** che sono state assegnate a un'applicazione.  Sarà necessario in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure Active Directory B2C, ogni esperienza utente è definita da un [criterio](active-directory-b2c-reference-policies.md). In questo esempio contiene tre esperienze identità: iscriversi, accedere e modificare il profilo. È necessario creare un criterio di ogni tipo, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure Active Directory B2C supporta anche un totale segno alto o in criteri che non sono disponibile in questa esercitazione.  L'iscrizione o Accedi criteri viene visualizzato in [questa esercitazione equivalente](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

Quando si creano tre criteri, assicurarsi di:

- Scegliere **ID utente per l'abbonamento** o **messaggio di posta elettronica per l'abbonamento** e il provider di identità.
- Scegliere il **nome visualizzato** e altri attributi iscrizione nei criteri di iscrizione.
- Scegliere la richiesta di **nome visualizzato** come applicazione Richiedi in ogni criterio. È possibile scegliere anche altri reclami.
- Copiare il **nome** di ogni criterio dopo averlo creato. È necessario i nomi dei criteri in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, si è pronti per creare l'applicazione.  

## <a name="download-the-code-and-configure-authentication"></a>Scaricare il codice e configurare l'autenticazione

Il codice per questo esempio [viene mantenuto per GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Per creare il campione durante il lavoro, è possibile [scaricare il progetto di base come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). È anche possibile duplicare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

Esempio completo è [disponibile](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) anche come file con estensione zip o sul `complete` ramo dell'archivio stesso.

Dopo avere scaricato il codice di esempio, aprire il file sln di Visual Studio per iniziare.

L'app comunica con Azure Active Directory B2C mediante l'invio di messaggi di autenticazione che specificano i criteri che desiderano eseguire come parte della richiesta HTTP. Per le applicazioni web .NET, è possibile utilizzare middleware OWIN Microsoft per inviare richieste di autenticazione OpenID connettersi, eseguire i criteri, gestire le sessioni utente e altro ancora.

Per iniziare, aggiungere i pacchetti di NuGet middleware OWIN al progetto tramite la Console di gestione di pacchetti di Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Aprire quindi il `web.config` file nella radice del progetto e immettere i valori di configurazione dell'applicazione nel `<appSettings>` sezione, sostituire i valori esistenti.

```
<configuration>
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
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Aggiungere una classe di avvio OWIN al progetto denominato `Startup.cs`. Pulsante destro del mouse sul progetto, scegliere **Aggiungi** **Nuovo elemento**e quindi eseguire una ricerca per "OWIN". **Assicurarsi di modificare la dichiarazione di classe a `public partial class Startup` **. Parte di questa classe è implementata automaticamente in un altro file. Richiama middleware OWIN il `Configuration(...)` metodo all'avvio dell'applicazione. In questo metodo, effettuare una chiamata a `ConfigureAuth(...)`, in cui configurare l'autenticazione per l'app.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Aprire il file `App_Start\Startup.Auth.cs` e implementare la `ConfigureAuth(...)` metodo.  I parametri specificati in `OpenIdConnectAuthenticationOptions` fungere da coordinate per l'app per comunicare con Azure Active Directory. È anche necessario configurare l'autenticazione cookie. Middleware OpenID connettersi utilizza cookie per mantenere le sessioni utente, tra le altre cose.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Inviare le richieste di autenticazione Azure Active Directory
L'app è ora configurato correttamente per comunicare con Azure Active Directory B2C utilizzando il protocollo di autenticazione OpenID connettersi.  Tutti i dettagli di creazione di messaggi di autenticazione, la convalida token da Azure Active Directory e gestione sessione utente è gestita OWIN.  È ora per avviare il flusso di ogni utente.

Quando un utente seleziona **iscriversi**, **accedere**o **modificare il profilo** in web app, l'azione associata viene richiamato `Controllers\AccountController.cs`. In ogni caso, è possibile utilizzare metodi OWIN incorporati per attivare i criteri destro:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

È inoltre possibile utilizzare un `[Authorize]` tag controller che richiede l'esecuzione di un determinato criterio se l'utente non è connesso. Apri `Controllers\HomeController.cs` e aggiungere la `[Authorize]` tag al controller di reclami.  OWIN verrà selezionato l'ultimo criterio configurato per eseguire quando viene richiamato il contrassegno di autorizzazione.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

È anche possibile utilizzare OWIN per eseguire la disconnessione l'utente dall'app. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Visualizzare le informazioni utente
Quando si autenticazione degli utenti mediante OpenID Connect, Azure Active Directory restituisce un token di ID all'app contenente **reclami**. Si tratta di offrire informazioni all'utente o conferme. È possibile utilizzare sulle attestazioni per personalizzare l'app.  

Aprire la `Controllers\HomeController.cs` file. È possibile accedere sulle attestazioni utente nei controller di tramite il `ClaimsPrincipal.Current` oggetto identità di protezione.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

È possibile accedere a qualsiasi richiesta di rimborso che l'applicazione riceve nello stesso modo.  Un elenco di tutte le attestazioni che riceve l'app è disponibile automaticamente nella pagina **delle attestazioni** .

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Infine, è possibile creare ed eseguire l'app. Iscriversi per l'app utilizzando un nome utente o indirizzo di posta elettronica. Disconnettersi e accedere con lo stesso account. Modificare il profilo dell'utente. Eseguire la disconnessione e iscriversi come un altro utente. Si noti che le informazioni visualizzate nella scheda **delle attestazioni** corrispondano alle informazioni che è configurato nei criteri.

## <a name="add-social-idps"></a>Aggiungere IDPs social networking

Attualmente, l'app supporta solo per un utente per l'abbonamento e l'accesso tramite **account locali**. Questi sono archiviati nella directory B2C di usare un nome utente e password. Utilizzo di Azure Active Directory B2C, è possibile aggiungere supporto per gli altri **provider di identità** (IDPs) senza modificare il proprio codice.

Per aggiungere l'app IDPs social networking, iniziare seguendo le istruzioni dettagliate in questi articoli. Per ogni IDP desiderato per il supporto, è necessario registrare un'applicazione di sistema e ottenere un ID client.

- [Impostare se stessi Facebook come un IDP](active-directory-b2c-setup-fb-app.md)
- [Configurare Google come un IDP](active-directory-b2c-setup-goog-app.md)
- [Impostare se stessi Amazon come un IDP](active-directory-b2c-setup-amzn-app.md)
- [Impostare se stessi LinkedIn come un IDP](active-directory-b2c-setup-li-app.md)

Dopo aver aggiunto il provider di identità alla directory B2C, è necessario modificare ognuna dei tre criteri per includere la nuova IDPs, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire di nuovo l'app.  Opzioni di abbonamento in ognuna delle identità esperienze e verrà visualizzato il nuovo IDPs aggiunto come accesso.

È possibile sperimentare i criteri e osservare l'effetto sull'applicazione di esempio. Aggiungere o rimuovere IDPs, modificare applicazione reclami o modificare gli attributi per l'abbonamento. Sperimentare fino a quando non è possibile vedere come criteri, le richieste di autenticazione e OWIN unire.

Per riferimento, l' esempio completo (senza i valori di configurazione) [fornito come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). È possibile anche clonare da GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
