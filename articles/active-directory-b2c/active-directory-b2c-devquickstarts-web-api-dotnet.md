<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Come creare un'applicazione web che chiama dell'API web utilizzando Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure Active Directory B2C: Chiamare dell'API web da un'app web .NET

Utilizzando B2C Azure Active Directory (Azure Active Directory), è possibile aggiungere funzionalità di gestione delle identità potente strumento self-service per il web apps e web API in pochi passaggi brevi. In questo articolo viene illustrato come creare un'app web .NET Controller di visualizzazione del modello MVC () "elenco da fare" che chiama dell'API web utilizzando i token del titolare

In questo articolo non viene illustrata come implementare l'accesso, per l'abbonamento e gestione dei profili con Azure Active Directory B2C. È incentrata su web chiamata API dopo l'utente è già autenticato. Se non è ancora disponibile, è necessario avviare con [.NET web app esercitazione introduttiva](active-directory-b2c-devquickstarts-web-dotnet.md) per informazioni sui concetti di base di Azure Active Directory B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure Active Directory B2C

Prima di poter usare B2C di Azure Active Directory, è necessario creare una directory o del tenant.  Una directory è un contenitore per tutti gli utenti, App, gruppi e altro ancora.  Se non è una già disponibile, [creare una directory B2C](active-directory-b2c-get-started.md) prima di procedere in questa Guida.

## <a name="create-an-application"></a>Creare un'applicazione

È necessario creare un'app nella directory B2C. In questo modo le informazioni di Azure Active Directory che è necessario comunicare in modo sicuro con l'app. In questo caso, il web app e l'API web rappresentato da un singolo **ID applicazione**, poiché comprendono un'app logica. Per creare un'app, seguire [queste istruzioni](active-directory-b2c-app-registration.md). Assicurarsi di:

- Includere un' **app web / dell'API web** nell'applicazione.
- Immettere `https://localhost:44316/` come **URL di risposta**. È l'URL predefinito per questo esempio di codice.
- Copiare l' **ID dell'applicazione** che sono state assegnate a un'applicazione. È anche necessario seguente in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure Active Directory B2C, ogni esperienza utente è definita da un [criterio](active-directory-b2c-reference-policies.md). Questa applicazione web contiene tre esperienze identità: iscriversi, accedere e modificare il profilo. È necessario creare un criterio di ogni tipo, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando si creano tre criteri, assicurarsi di:

- Scegliere il **nome visualizzato** e altri attributi iscrizione nei criteri di iscrizione.
- Scegliere le richieste di applicazione **nome visualizzato** e **ID** in ogni criterio. È possibile scegliere anche altri reclami.
- Copiare il **nome** di ogni criterio dopo averlo creato. Deve avere il prefisso `b2c_1_`. È necessario i nomi dei criteri in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo avere creato i tre criteri, si è pronti per creare l'applicazione.

Si noti che in questo articolo viene illustrato come utilizzare i criteri appena creata. Per informazioni su come funzionano i criteri in Azure Active Directory B2C, iniziare con [.NET web app esercitazione introduttiva](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione [viene mantenuto per GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Per creare il campione durante il lavoro, è possibile [scaricare il progetto di base come file ZIP](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). È anche possibile duplicare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

L'app completa è [disponibile](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) anche come file con estensione zip o sul `complete` ramo dell'archivio stesso.

Dopo avere scaricato il codice di esempio, aprire il file sln di Visual Studio per iniziare.

## <a name="configure-the-task-web-app"></a>Configurare attività web app

Per ottenere `TaskWebApp` per comunicare con Azure Active Directory B2C, è necessario fornire alcuni parametri comuni. Nel `TaskWebApp` progetto aperto il `web.config` file nella radice del progetto e sostituire i valori nel `<appSettings>` sezione. È possibile lasciare il `AadInstance`, `RedirectUri`, e `TaskServiceUrl` i valori così come sono.

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Ottenere i token di accesso, chiamare l'API di attività

In questa sezione verrà descritto come utilizzare il token ricevuto durante l'accesso con Azure Active Directory B2C per accedere a un web API anche protetti con Azure Active Directory B2C.

Nell'articolo vengono descritte le procedure per proteggere l'API. Per informazioni su come dell'API web autentica in modo sicuro richieste con Azure Active Directory B2C, consultare l' [articolo introduzione dell'API web](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="save-the-sign-in-token"></a>Salvare il segno di token

Prima di tutto, eseguire l'autenticazione utente (con uno qualsiasi dei criteri di) e ricevere un token di accesso da Azure Active Directory B2C.  Se non si sa come eseguire criteri, tornare indietro e provare il [.NET web app esercitazione introduttiva](active-directory-b2c-devquickstarts-web-dotnet.md) per informazioni sui concetti di base di Azure Active Directory B2C.

Aprire il file `App_Start\Startup.Auth.cs`.  È stata apportata una modifica importante è necessario apportare al `OpenIdConnectAuthenticationOptions` -è necessario impostare `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

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
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Ottenere un token nei controller di

Il `TasksController` è responsabile per la comunicazione con il web API, invio di richieste HTTP all'API per leggere, creare ed eliminare attività.  Essendo che l'API è protetto da Azure Active Directory B2C, è necessario innanzitutto recuperare il token che è stato salvato nel passaggio precedente.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
    ...
}
```

Il `BootstrapContext` contiene il segno di token che è stato acquistato eseguendo uno dei criteri di B2C.

### <a name="read-tasks-from-the-web-api"></a>Leggere le attività dal web API

Quando si dispone di un token, è possibile allegare alle HTTP `GET` richiedere la `Authorization` intestazione in modo sicuro chiamare `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Creare ed eliminare attività sul web API

Seguono lo stesso modello quando si inviano `POST` e `DELETE` richieste sul Web API, utilizzando il `BootstrapContext` per recuperare il token di accesso. Abbiamo implementato l'azione crea automaticamente. È possibile provare di completamento dell'operazione di eliminazione in `TasksController.cs`.

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Infine, creare ed eseguire l'app. Iscriversi e accedere e creare attività per l'utente effettuato l'accesso. Disconnettersi e accedere come un altro utente. Creare attività di tale utente. Si noti come le attività vengono archiviate per utente sull'API, poiché l'API estrae l'identità dell'utente da token che riceve.

Per riferimento, l' esempio completo [fornito come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). È possibile anche clonare da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
