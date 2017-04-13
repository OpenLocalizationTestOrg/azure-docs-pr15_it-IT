<properties
    pageTitle="Guida introduttiva .NET di Azure Active Directory | Microsoft Azure"
    description="Come creare un'App Web MVC .NET che si integra con Azure Active Directory per accedere."
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

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>Accesso dell'App Web ASP.NET & Disconnetti con Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure Active Directory rende semplice e diretto affidare la gestione delle identità dell'applicazione web, fornendo singolo accesso e disconnessione con poche righe di codice.  Nelle applicazioni web Asp.NET, è possibile eseguire questa operazione utilizzando implementazione Microsoft del middleware OWIN basato sulla community, incluso in .NET Framework 4.5.  Di seguito si userà OWIN per:
-   Accedere all'app utilizzando Azure Active Directory come provider di identità dell'utente.
-   Visualizzare le informazioni relative all'utente.
-   Eseguire l'accesso all'utente dall'app.

Per eseguire questa operazione, è necessario:

1. Registrare un'applicazione di Azure Active Directory
2. Configurare l'app per utilizzare la pipeline di autenticazione OWIN.
3. Utilizzare OWIN per inoltrare le richieste di accesso e disconnessione di Azure Active Directory.
4. Stampare i dati relativi all'utente.

Per iniziare a, [scaricare la struttura di app](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  È necessario anche un tenant di Azure Active Directory in cui si desidera registrare l'applicazione.  Se non hai già, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>*1. registrare un'applicazione di Azure Active Directory*
Per abilitare l'app eseguire l'autenticazione degli utenti, è necessario prima di registrare una nuova applicazione nel tenant.

- Accedere al portale di gestione Azure.
- Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**.
- Selezionare il tenant nel punto in cui si desidera registrare l'applicazione.
- Fare clic sulla scheda **applicazioni** e fare clic su Aggiungi nel cassetto inferiore.
- Seguire le istruzioni e creare una nuova **applicazione Web e/o WebAPI**.
    - Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   L' **URL di accesso** è l'URL di base dell'app.  Il valore predefinito della struttura è `https://localhost:44320/`.
    - **App ID URI** è un identificatore univoco dell'applicazione.  La convenzione consiste nell'usare `https://<tenant-domain>/<app-name>`, ad esempio`https://contoso.onmicrosoft.com/my-first-aad-app`
- Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda Configura.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurare l'app da usare la pipeline di autenticazione OWIN*
In questo caso, si verrà configurare middleware OWIN per utilizzare il protocollo di autenticazione OpenID connettersi.  OWIN verrà utilizzato per eseguire le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente, tra l'altro.

-   Per iniziare, aggiungere i pacchetti di NuGet middleware OWIN al progetto tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Aggiungere una classe di avvio OWIN al progetto chiamato `Startup.cs` destra fare clic su progetto--> **Aggiungi** --> **Nuovo elemento** --> ricerca per "OWIN".  Richiama middleware OWIN il `Configuration(...)` metodo all'avvio dell'applicazione.
-   Modificare la dichiarazione di classe a `public partial class Startup` -è stata già parte di questa classe è implementato in un altro file.  Nel `Configuration(...)` metodo, effettuare una chiamata a ConfgureAuth(...) per configurare l'autenticazione per un'app web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Aprire il file `App_Start\Startup.Auth.cs` e implementare la `ConfigureAuth(...)` metodo.  I parametri specificati in `OpenIDConnectAuthenticationOptions` verrà utilizzata come coordinate per l'app per comunicare con Azure Active Directory.  È anche necessario configurare l'autenticazione Cookie - middleware OpenID connettersi utilizza cookie sotto le quinte.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   Infine, aprire la `web.config` file nella radice del progetto e immettere i valori di configurazione di `<appSettings>` sezione.
    -   L'app `ida:ClientId` è il Guid copiato dal portale di Azure nel passaggio 1.
    -   Il `ida:Tenant` è il nome del tenant di Azure Active Directory, ad esempio "contoso.onmicrosoft.com".
    -   Il `ida:PostLogoutRedirectUri` indica di Azure Active Directory in cui un utente deve essere reindirizzato una richiesta di disconnessione completamento.

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3. utilizzare OWIN per inoltrare le richieste di accesso e disconnessione di Azure Active Directory*
L'app è ora configurato correttamente per comunicare con Azure Active Directory utilizzando il protocollo di autenticazione OpenID connettersi.  Tutti i dettagli di problemi di creazione di messaggi di autenticazione, convalida i token da Azure Active Directory e gestione sessione utente è gestita OWIN.  È ora per concedere agli utenti un modo per accedere e disconnettersi.

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   A questo punto, aprire `Views\Shared\_LoginPartial.cshtml`.  Si tratta in cui verrà Mostra all'utente di collegamenti di accesso e disconnessione dell'applicazione e stampare il nome dell'utente in una visualizzazione.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## <a name="4--display-user-information"></a>*4. visualizzare le informazioni utente*
Quando l'autenticazione degli utenti con OpenID connettersi, Azure Active Directory restituisce un id_token all'applicazione che contiene "reclami" o offrire informazioni all'utente o conferme.  È possibile utilizzare le richieste per personalizzare l'app:

- Aprire la `Controllers\HomeController.cs` file.  È possibile accedere sulle attestazioni dell'utente nei controller di tramite il `ClaimsPrincipal.Current` oggetto identità di protezione.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Infine, creare ed eseguire l'app!  Se non è ancora disponibile, è ora per creare un nuovo utente nel tenant di con un *. dominio onmicrosoft.com.  Accedere con l'utente e notare come l'identità dell'utente viene riflesso nella barra di spostamento superiore.  Disconnettersi e accedere con un altro utente nel tenant.  Se sta automatizzare particolarmente ambizioso, eseguire la registrazione ed eseguire un'altra istanza di tale applicazione (con il proprio clientId) e controllo vedere single sign-in azione.

Per riferimento, l' esempio completo (senza i valori di configurazione) [sono disponibili qui](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

È possibile procedere in argomenti più avanzati.  È consigliabile provare:

[Proteggere un Web API con Azure Active Directory >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
