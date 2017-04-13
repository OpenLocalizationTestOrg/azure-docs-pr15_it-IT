<properties
    pageTitle="Guida introduttiva .NET di Azure Active Directory | Microsoft Azure"
    description="Modalità di compilazione di un API Web MVC .NET che si integra con Azure Active Directory per l'autenticazione e l'autorizzazione."
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Proteggere un API Web utilizzando i token del titolare da Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se si crea un'applicazione che fornisce l'accesso alle risorse protette è necessario sapere come proteggere le risorse da access ingiustificate.
Azure Active Directory rende semplice e facilmente proteggere un web API con token di accesso 2.0 titolare OAuth solo alcune righe di codice.

Nelle applicazioni web Asp.NET, è possibile eseguire questa operazione utilizzando implementazione Microsoft del middleware OWIN basato sulla community, incluso in .NET Framework 4.5.  Di seguito si userà OWIN per creare un sito web "Elenco da fare" API:
-   Consente di definire quali API protetti.
-   Consente di verificare che le chiamate all'API Web contengano un Token di accesso valido.

Per eseguire questa operazione, è necessario:

1. Registrare un'applicazione di Azure Active Directory
2. Configurare l'app per utilizzare la pipeline di autenticazione OWIN.
3. Configurare un'applicazione client per chiamare la per eseguire elenco API Web

Per iniziare a, [scaricare la struttura di app](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Ogni è una soluzione di Visual Studio 2013.  È necessario anche un tenant di Azure Active Directory in cui si desidera registrare l'applicazione.  Se non hai già, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrare un'applicazione di Azure Active Directory*
Per proteggere l'applicazione, è innanzitutto necessario creare un'applicazione nel tenant e offrire Azure AD alcune parti chiave delle informazioni.

-   Accedere al [portale di gestione di Azure](https://manage.windowsazure.com)
-   Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**
-   Selezionare un tenant in cui si desidera registrare l'applicazione.
-   Fare clic sulla scheda **applicazioni** e fare clic su **Aggiungi** nel cassetto inferiore.
-   Seguire le istruzioni e creare una nuova **applicazione Web e/o WebAPI**.
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali.  Immettere "Per elencare servizio".
    -   **Reindirizzare Uri** è una combinazione di Azure Active Directory da utilizzare per restituire tutti i token l'app richiesto combinazione e stringa. Immettere `https://localhost:44321/` per questo valore.
-   Dopo aver completato la registrazione, passare alla scheda **Configura** e individuare il campo **App ID URI** .  Immettere un identificatore di tenant specifiche per questo valore, ad esempio`https://contoso.onmicrosoft.com/TodoListService`
- Salvare la configurazione.  Lasciare aperto il portale: è anche necessario registrare subito l'applicazione client.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. configurare l'app da usare la pipeline di autenticazione OWIN*

Una volta registrato un'applicazione con Azure Active Directory, è necessario configurare un'applicazione di comunicare con Azure Active Directory per convalidare in arrivo richieste e i token.

-   Per iniziare, aprire la soluzione e aggiungere i pacchetti di NuGet middleware OWIN al progetto TodoListService tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Aggiungere una classe di avvio OWIN al progetto TodoListService chiamato `Startup.cs`.  Destra fare clic su progetto--> **Aggiungi** --> **Nuovo elemento** --> ricerca per "OWIN".  Richiama middleware OWIN il `Configuration(…)` metodo all'avvio dell'applicazione.
-   Modificare la dichiarazione di classe a `public partial class Startup` -è stata già parte di questa classe è implementato in un altro file.  Nel `Configuration(…)` metodo, effettuare una chiamata a ConfgureAuth(...) per configurare l'autenticazione per un'app web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Aprire il file `App_Start\Startup.Auth.cs` e implementare la `ConfigureAuth(…)` metodo.  I parametri specificati in `WindowsAzureActiveDirectoryBearerAuthenticationOptions` verrà utilizzata come coordinate per l'app per comunicare con Azure Active Directory.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   È ora possibile utilizzare `[Authorize]` attributi per proteggere i controller e azioni con l'autenticazione del titolare JWT.  Decorare il `Controllers\TodoListController.cs` classe con un tag di autorizzazione.  Questo impone all'utente di eseguire l'accesso prima di accedere alla pagina.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando un chiamante autorizzato correttamente richiama uno del `TodoListController` API, l'azione potrebbe essere necessario l'accesso alle informazioni sul chiamante.  OWIN consente di accedere ai crediti all'interno del token del titolare tramite il `ClaimsPrincpal` oggetto.  
- È un requisito comune per web API per convalidare "ambiti" presenti nel token - in modo che l'utente finale ha acconsentito alle autorizzazioni necessarie per accedere al servizio di elenco Todo:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   Infine, aprire la `web.config` file nella radice del progetto TodoListService e immettere i valori di configurazione di `<appSettings>` sezione.
  - Il `ida:Tenant` è il nome del tenant di Azure Active Directory, ad esempio "contoso.onmicrosoft.com".
  - Il `ida:Audience` è l'URI ID App dell'applicazione che è stata immessa nel portale di Azure.

## <a name="3--configure-a-client-application--run-the-service"></a>*3. configurare un'applicazione client ed eseguire il servizio*
È possibile visualizzare il servizio di elenco Todo in azione, è necessario configurare il Client di elenco Todo consente di ottenere i token da AAD ed effettuare chiamate al servizio.

- Tornare al [Portale di gestione Azure](https://manage.windowsazure.com)
- Creare una nuova applicazione nel tenant di Azure Active Directory e selezionare **Applicazione Client nativa** prompt dei comandi di risultante.
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   Immettere `http://TodoListClient/` per il valore di **Reindirizzamento Uri** .
- Dopo aver completato la registrazione, AAD assegna l'app univoco **Id Client**. È necessario questo valore in passaggi successivi, pertanto copiarlo dalla scheda Configura.
- Anche nella scheda **Configura** , individuare la sezione "Autorizzazioni da altre applicazioni". Fare clic su "Aggiungi applicazione". Selezionare "Tutte le app" nell'elenco a discesa "Mostra" e fare clic sul segno di spunta superiore. Individuare e fare clic su per eseguire elenco servizio e fare clic sul segno di spunta nella parte inferiore per aggiungere l'applicazione. Selezionare "Per eseguire elenco servizio di accesso" nell'elenco a discesa "Delegato autorizzazioni" e salvare la configurazione.


- In Visual Studio, aprire `App.config` il TodoListClient project e immettere i valori di configurazione di `<appSettings>` sezione.
  - Il `ida:Tenant` è il nome del tenant di Azure Active Directory, ad esempio "contoso.onmicrosoft.com".
  - Il `ida:ClientId` ID app sono stati copiati dal portale di Azure.
  - Il `todo:TodoListResourceId` è l'URI ID App dell'applicazione di servizio dell'elenco eseguire immesso nel portale di Azure.

Infine, pulire, creare ed eseguire ogni progetto.  Se non è ancora disponibile, è ora per creare un nuovo utente nel tenant di con un *. dominio onmicrosoft.com.  Accedere al client l'elenco attività con l'utente e aggiungere l'elenco dell'utente per eseguire alcune attività.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  È possibile procedere con per più scenari di identità aggiuntive.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
