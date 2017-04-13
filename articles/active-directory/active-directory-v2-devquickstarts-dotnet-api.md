<properties
    pageTitle="Azure Active Directory v 2.0 .NET Web API | Microsoft Azure"
    description="Modalità di compilazione di un Api Web MVC .NET che accetti token da entrambi Account Microsoft personale e account aziendale o dell'istituto di istruzione."
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>Proteggere un web MVC API

Con l'endpoint v 2.0 Azure Active Directory, è possibile proteggere un API Web tramite token di accesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) , che consente agli utenti con account Microsoft personale e aziendale o dell'istituto di istruzione account per l'accesso sicuro l'API Web.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

In web ASP.NET API, è possibile eseguire questa operazione utilizzando middleware OWIN Microsoft incluso in .NET Framework 4.5.  Di seguito si userà OWIN per creare un'API Web MVC "Elenco da fare" che consente di creare e leggere le attività dall'elenco di attività dell'utente client.  API web convalida che le richieste in arrivo contengano un token di accesso valido e rifiutare tutte le richieste che non superano la convalida su una route protetta.  In questo esempio è stato creato utilizzando Visual Studio 2015.

## <a name="download"></a>Download
Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Per seguire, è possibile [scaricare struttura dell'app come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o duplicare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

App struttura include tutto il codice standard per una semplice API, ma non contiene tutte le parti alle identità. Se non si desidera seguire su carta, è possibile duplicare o il [download di esempio completo](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)oppure seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Assicurarsi di:

- Copia verso il basso l' **Id applicazione** assegnata all'applicazione in uso, sarà necessario immetterla breve.

Soluzione di visual studio contiene anche un "TodoListClient", ovvero una semplice applicazione WPF.  Il TodoListClient vengono utilizzati per dimostrare come un utente segni aggiuntivo e come un client può inviare richieste per l'API Web.  In questo caso, il TodoListClient e il TodoListService sono rappresentati da app stesso.  Per configurare il TodoListClient, è anche necessario:

- Aggiungere la piattaforma **Mobile** per l'app.


## <a name="install-owin"></a>Installare OWIN

Dopo aver registrato un'app, è necessario configurare le app per comunicare con l'endpoint v 2.0 al fine di convalidare in arrivo richieste e i token.

- Per iniziare, aprire la soluzione e aggiungere i pacchetti di NuGet middleware OWIN al progetto TodoListService tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurare l'autenticazione OAuth

- Aggiungere una classe di avvio OWIN al progetto TodoListService chiamato `Startup.cs`.  Destra fare clic su progetto--> **Aggiungi** --> **Nuovo elemento** --> ricerca per "OWIN".  Richiama middleware OWIN il `Configuration(…)` metodo all'avvio dell'applicazione.
- Modificare la dichiarazione di classe a `public partial class Startup` -è stata già parte di questa classe è implementato in un altro file.  Nel `Configuration(…)` metodo, effettuare una chiamata a ConfgureAuth(...) per configurare l'autenticazione per un'app web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Aprire il file `App_Start\Startup.Auth.cs` e implementare la `ConfigureAuth(…)` metodo, verrà impostata l'API Web per accettare i token da endpoint v 2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- È ora possibile utilizzare `[Authorize]` attributi per proteggere i controller e azioni con l'autenticazione del titolare OAuth 2.0.  Decorare il `Controllers\TodoListController.cs` classe con un tag di autorizzazione.  Questo impone all'utente di eseguire l'accesso prima di accedere alla pagina.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando un chiamante autorizzato correttamente richiama uno del `TodoListController` API, l'azione potrebbe essere necessario l'accesso alle informazioni sul chiamante.  OWIN consente di accedere ai crediti all'interno del token del titolare tramite il `ClaimsPrincpal` oggetto.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Infine, aprire la `web.config` file nella radice del progetto TodoListService e immettere i valori di configurazione di `<appSettings>` sezione.
  - Il `ida:Audience` corrisponde all' **Id applicazione** dell'app che è stata immessa nel portale.

## <a name="configure-the-client-app"></a>Configurare l'applicazione client
È possibile visualizzare il servizio di elenco Todo in azione, è necessario configurare il Client di elenco Todo consente di ottenere i token da endpoint v 2.0 ed effettuare chiamate al servizio.

- Nel progetto TodoListClient aprire `App.config` e immettere i valori di configurazione di `<appSettings>` sezione.
  - Il `ida:ClientId` Id applicazione sono stati copiati dal portale.

Infine, pulire, creare ed eseguire ogni progetto.  È ora disponibile una API Web MVC .NET che accetta token da entrambi gli account Microsoft personali e account aziendale o dell'istituto di istruzione.  Accedere alla TodoListClient e chiamare il web api per aggiungere attività all'elenco di attività dell'utente.

Per riferimento, di esempio completo (senza i valori di configurazione) [viene fornito come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)oppure è possibile duplicare il da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Passaggi successivi
È possibile procedere in argomenti aggiuntivi.  È consigliabile provare:

[Chiamare un API Web da un'App Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Per ulteriori risorse, vedere:
- [La Guida di sviluppo v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Tag StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Ottenere gli aggiornamenti di sicurezza per i prodotti

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e la sottoscrizione per gli avvisi di avviso di sicurezza.
