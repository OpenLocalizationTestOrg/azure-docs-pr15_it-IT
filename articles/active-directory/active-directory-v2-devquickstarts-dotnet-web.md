<properties
    pageTitle="App Web di Azure Active Directory v 2.0 .NET | Microsoft Azure"
    description="Come creare un'App Web MVC .NET che firma utenti con entrambi Account Microsoft personale e account aziendale o dell'istituto di istruzione."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Aggiungere l'accesso a un'app web MVC .NET

Con l'endpoint v 2.0, è possibile aggiungere rapidamente l'autenticazione per le applicazioni web con il supporto per entrambi gli account Microsoft personali e account aziendale o dell'istituto di istruzione.  Nelle applicazioni web ASP.NET, è possibile eseguire questa operazione utilizzando middleware OWIN Microsoft incluso in .NET Framework 4.5.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

 Di seguito verrà creata un'applicazione web che utilizza OWIN per accedere all'utente, visualizzare le informazioni relative all'utente e accedere all'utente dall'app.
 
 ## <a name="download"></a>Download
Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Per seguire, è possibile [scaricare struttura dell'app come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o duplicare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

L'app completa viene fornito alla fine dell'esercitazione anche.

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)oppure seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Assicurarsi di:

- Copia verso il basso l' **Id applicazione** assegnata all'applicazione in uso, sarà necessario immetterla breve.
- Aggiungere la piattaforma **Web** per l'app.
- Immettere il corretto **Reindirizzare URI**. Uri reindirizza indica di Azure Active Directory in cui devono essere indirizzate risposte autenticazione - è l'impostazione predefinita per questa esercitazione `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Installare e configurare l'autenticazione OWIN
In questo caso, si verrà configurare middleware OWIN per utilizzare il protocollo di autenticazione OpenID connettersi.  OWIN verrà utilizzato per eseguire le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente, tra l'altro.

-   Per iniziare, aprire la `web.config` file nella radice del progetto, quindi immettere valori di configurazione dell'applicazione di `<appSettings>` sezione.
    -   Il `ida:ClientId` corrisponde all' **Id applicazione** assegnata all'app nel portale di registrazione.
    -   Il `ida:RedirectUri` è il **Reindirizzamento Uri** immesso nel portale.

-   Aggiungere quindi i pacchetti di NuGet middleware OWIN al progetto tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Aggiungere un "OWIN avvio classe" al progetto denominata `Startup.cs` destra fare clic su progetto--> **Aggiungi** --> **Nuovo elemento** --> ricerca per "OWIN".  Richiama middleware OWIN il `Configuration(...)` metodo all'avvio dell'applicazione.
-   Modificare la dichiarazione di classe a `public partial class Startup` -è stata già parte di questa classe è implementato in un altro file.  Nel `Configuration(...)` metodo, effettuare una chiamata a ConfigureAuth(...) per configurare l'autenticazione per un'app web  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   Aprire il file `App_Start\Startup.Auth.cs` e implementare la `ConfigureAuth(...)` metodo.  I parametri specificati in `OpenIdConnectAuthenticationOptions` verrà utilizzata come coordinate per l'app per comunicare con Azure Active Directory.  È anche necessario configurare l'autenticazione Cookie - middleware OpenID connettersi utilizza cookie sotto le quinte.

```C#
public void ConfigureAuth(IAppBuilder app)
             {
                     app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

                     app.UseCookieAuthentication(new CookieAuthenticationOptions());

                     app.UseOpenIdConnectAuthentication(
                             new OpenIdConnectAuthenticationOptions
                             {
                                     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                                     // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                     // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                                     ClientId = clientId,
                                     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>Inviare richieste di autenticazione
L'app è ora configurato correttamente per comunicare con l'endpoint v 2.0 mediante il protocollo di autenticazione OpenID connettersi.  Tutti i dettagli di problemi di creazione di messaggi di autenticazione, convalida i token da Azure Active Directory e gestione sessione utente è gestita OWIN.  È ora per concedere agli utenti un modo per accedere e disconnettersi.

- È possibile utilizzare autorizzare tag in un controller di richiedere che l'utente accede prima di accedere a una determinata pagina.  Apri `Controllers\HomeController.cs`e aggiungere la `[Authorize]` tag al controller di dettagli.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   È anche possibile utilizzare OWIN per inviare le richieste di autenticazione da direttamente all'interno del codice.  Apri `Controllers\AccountController.cs`.  Nel riquadro azioni SignIn() e SignOut() problemi verifica OpenID connessione e disconnessione richieste, rispettivamente.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   A questo punto, aprire `Views\Shared\_LoginPartial.cshtml`.  Si tratta in cui verrà Mostra all'utente di collegamenti di accesso e disconnessione dell'applicazione e stampare il nome dell'utente in una visualizzazione.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="display-user-information"></a>Visualizzare le informazioni utente
Durante l'autenticazione con OpenID connessione degli utenti, l'endpoint v 2.0 restituisce un id_token all'app che contiene [i reclami](active-directory-v2-tokens.md#id_tokens)o offrire informazioni all'utente o conferme.  È possibile utilizzare le richieste per personalizzare l'app:

- Aprire la `Controllers\HomeController.cs` file.  È possibile accedere sulle attestazioni dell'utente nei controller di tramite il `ClaimsPrincipal.Current` oggetto identità di protezione.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>Correre

Infine, creare ed eseguire l'app!   Accedere con un Account Microsoft personale oppure un account aziendale o dell'istituto di istruzione e notare come l'identità dell'utente viene riflesso nella barra di spostamento superiore.  È ora disponibile un'app web protetta tramite i protocolli standard che è possono autenticare gli utenti con il proprio account personali e di lavoro o dell'istituto di istruzione.

Per riferimento, di esempio completo (senza i valori di configurazione) [viene fornito come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)oppure è possibile duplicare il da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Passaggi successivi

È possibile procedere in argomenti più avanzati.  È consigliabile provare:

[Proteggere un API Web con l'endpoint v 2.0 >>](active-directory-devquickstarts-webapi-dotnet.md)

Per ulteriori risorse, vedere:
- [La Guida di sviluppo v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Tag StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Ottenere gli aggiornamenti di sicurezza per i prodotti

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e la sottoscrizione per gli avvisi di avviso di sicurezza.
