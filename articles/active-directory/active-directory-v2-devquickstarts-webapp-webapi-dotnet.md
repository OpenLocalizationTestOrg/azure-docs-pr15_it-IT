<properties
    pageTitle="App Web di Azure Active Directory v 2.0 .NET | Microsoft Azure"
    description="Come creare un'App Web di .NET MVC tale web chiamate servizi tramite account di Microsoft personali e di lavoro o account dell'istituto di istruzione per effettua l'accesso."
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
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>Chiamare un API web da un'app web .NET

Con endpoint v 2.0, è possibile aggiungere l'App web di autenticazione rapidamente e web API con account aziendale o dell'istituto di istruzione e il supporto per entrambi gli account Microsoft personali.  In questo caso, verrà creata un'app web MVC che gli utenti con OpenID connettersi, da middleware OWIN Microsoft per assistenza nella firma.  L'applicazione web verrà ottenere token di accesso OAuth 2.0 per un sito web api protetto da 2.0 OAuth che consente di creare, leggere ed eliminare in un determinato utente "elenco di cose da fare".

In questa esercitazione sarà dedicata principalmente all'utilizzo MSAL per acquisire e utilizzare i token di accesso in un'app web descritta in completo [qui](active-directory-v2-flows.md#web-apps).  Come prerequisiti, può essere necessario prima di tutto informazioni su come [aggiungere accessohttp base per un'app web](active-directory-v2-devquickstarts-dotnet-web.md) o su come [proteggere dell'API web](active-directory-v2-devquickstarts-dotnet-api.md).

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="download-sample-code"></a>Scaricare il codice di esempio

Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Per seguire, è possibile [scaricare struttura dell'app come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) o duplicare la struttura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

In alternativa, è possibile [scaricare l'app completa come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) o duplicare l'app completa:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app presso [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)oppure seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Assicurarsi di:

- Copia verso il basso l' **Id applicazione** assegnata all'applicazione in uso, sarà necessario immetterla breve.
- Creare un' **App segreto** del tipo di **Password** e copiare verso il basso il relativo valore per un uso successivo
- Aggiungere la piattaforma **Web** per l'app.
- Immettere il corretto **Reindirizzare URI**. Uri reindirizza indica di Azure Active Directory in cui devono essere indirizzate risposte autenticazione - è l'impostazione predefinita per questa esercitazione `https://localhost:44326/`.


## <a name="install-owin"></a>Installare OWIN
Aggiungere i pacchetti di NuGet OWIN middleware per il `TodoList-WebApp` del progetto tramite la Console di gestione pacchetti.  Middleware OWIN verrà utilizzato per eseguire le richieste di accesso e disconnessione, gestire la sessione dell'utente e ottenere informazioni sull'utente, tra l'altro.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Accedere all'utente
Configurare middleware OWIN per utilizzare il [protocollo di autenticazione OpenID connettersi](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  

-   Aprire la `web.config` file nella radice della `TodoList-WebApp` project e immettere i valori di configurazione dell'applicazione nel `<appSettings>` sezione.
    -   Il `ida:ClientId` corrisponde all' **Id applicazione** assegnata all'app nel portale di registrazione.
    - Il `ida:ClientSecret` è il **Segreto App** è stato creato nel portale di registrazione.
    -   Il `ida:RedirectUri` è il **Reindirizzamento Uri** immesso nel portale.
- Aprire la `web.config` file nella radice della `TodoList-Service` project e sostituire il `ida:Audience` con lo stesso **Id applicazione** precedente.


- Aprire il file `App_Start\Startup.Auth.cs` e aggiungere `using` istruzioni per le raccolte dall'alto.
- Nello stesso file implementare la `ConfigureAuth(...)` metodo.  I parametri specificati in `OpenIDConnectAuthenticationOptions` verrà utilizzata come coordinate per l'app per comunicare con Azure Active Directory.

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
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Utilizzare MSAL per ottenere i token di accesso
Nel `AuthorizationCodeReceived` notifica, si desidera utilizzare [2.0 OAuth in combinazione con OpenID connettersi](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) per riscattare authorization_code per un token di accesso al servizio di elenco di cose da fare.  MSAL può semplificare questo processo è:

- Prima di tutto, installare la versione di anteprima di MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- E aggiungerne un altro `using` istruzione per il `App_Start\Startup.Auth.cs` file per MSAL.
- Aggiungere un nuovo metodo di `OnAuthorizationCodeReceived` gestore eventi.  Il gestore utilizzerà MSAL per acquisire un token di accesso all'API di elenco di cose da fare e verrà archiviata il token nella cache token del MSAL per usarle in seguito:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- Nelle applicazioni web, MSAL presenta una cache token extensible che può essere usata per archiviare i token.  In questo esempio viene implementata la `NaiveSessionCache` che utilizza l'archiviazione di sessione http ai token di cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Chiamare l'API Web
A questo punto è possibile utilizzare Access che è stato acquistato nel passaggio 3.  Aprire l'app web `Controllers\TodoListController.cs` file, semplificando così tutte le richieste CRUD all'API di elenco di cose da fare.

- È possibile utilizzare MSAL nuovamente qui per recuperare access_tokens dalla cache di MSAL.  Prima di tutto, aggiungere un `using` istruzione per MSAL al file.

    `using Microsoft.Identity.Client;`

- Nel `Index` , MSAL utilizzo dell'azione `AcquireTokenSilentAsync` metodo per ottenere un Access che può essere utilizzato per leggere i dati dal servizio di elenco attività:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- Il token risultante viene quindi aggiunta alla richiesta HTTP GET come la `Authorization` intestazione, il servizio di elenco attività utilizza la richiesta.
- Se il servizio di elenco attività restituisce un `401 Unauthorized` risposta, access_tokens in MSAL valide per qualche motivo.  In questo caso, si deve rilasciare qualsiasi access_tokens dalla cache di MSAL e Mostra un messaggio potrebbe essere necessario accedere nuovamente, che verrà riavviato il flusso di acquisizione token all'utente.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- Allo stesso modo, se MSAL è in grado di restituire un Access per qualsiasi motivo, è consigliabile chiedere all'utente di accedere di nuovo.  Questo è sufficiente intercettazione qualsiasi `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- L'esatta stesso `AcquireTokenSilentAsync` chiamata è implementd nel `Create` e `Delete` azioni.  Nelle applicazioni web, è possibile utilizzare questo metodo MSAL per ottenere access_tokens ogni volta che non è presente l'app.  MSAL consente di risolvere acquisizione, memorizzazione nella cache e aggiornamento dei token dell'utente.

Infine, creare ed eseguire l'app!  Accedere con un Account Microsoft o un Account di Azure Active Directory e notare come l'identità dell'utente viene riflesso nella barra di spostamento superiore.  Aggiungere ed eliminare alcuni elementi dall'elenco di attività dell'utente per visualizzare che 2.0 OAuth protetto in modo esclusivo chiamate API in azione.  È ora disponibile un'app web & API web, entrambe protette tramite i protocolli standard, che è possono autenticare gli utenti con il proprio account personali e di lavoro o dell'istituto di istruzione.

Per riferimento, l' esempio completo (senza i valori di configurazione) [sono disponibili qui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori risorse, vedere:
- [La Guida di sviluppo v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Tag StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Ottenere gli aggiornamenti di sicurezza per i prodotti

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e la sottoscrizione per gli avvisi di avviso di sicurezza.
