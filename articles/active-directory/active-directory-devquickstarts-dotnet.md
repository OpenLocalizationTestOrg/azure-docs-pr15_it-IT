<properties
    pageTitle="Guida introduttiva .NET di Azure Active Directory | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione Desktop di Windows .NET che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
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


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integrare un'App Desktop WPF di Windows Azure Active Directory

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se si sviluppano un'applicazione desktop, Azure Active Directory rende semplice e diretto per poter eseguire l'autenticazione gli utenti con il proprio account Active Directory.  Consente inoltre sicuro utilizzare qualsiasi web API protetto da Azure Active Directory, ad esempio le API di Office 365 o l'API di Azure nell'applicazione.

Per i client nativo .NET che è necessario accedere alle risorse protette, Azure Active Directory è Active Directory Authentication Library o ADAL.  Obiettivo esclusiva di ADAL è per rendere più semplice per l'app ottenere i token di accesso.  Per mostrare solo quanto è semplice, qui verrà creata un'applicazione di elenco di attività WPF .NET che:

-   Ottiene access token per richiamare l'API di Azure Active Directory grafico utilizzando il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Ricerca in una directory per gli utenti con un alias specificato.
-   Gli utenti segni.

Per creare l'applicazione di lavoro completa, è necessario:

2. Registrare l'applicazione di Azure Active Directory.
3. Installare e configurare ADAL.
5. Utilizzare ADAL per ottenere i token da Azure Active Directory.

Per iniziare a, [scaricare la struttura di app](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  È necessario anche un tenant di Azure Active Directory in cui è possibile creare utenti e registrare un'applicazione.  Se si dispone già di un tenant, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. registrare l'applicazione DirectorySearcher*
Per abilitare l'app ottenere i token, è prima necessario registrare nel tenant di Azure Active Directory e concessione dell'autorizzazione per accedere all'API di Azure Active Directory grafico:

-   Accedere al portale di gestione di Azure
-   Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**
-   Selezionare un tenant in cui si desidera registrare l'applicazione.
-   Fare clic sulla scheda **applicazioni** e fare clic su **Aggiungi** nel cassetto inferiore.
-   Seguire le istruzioni e creare una nuova **Applicazione Client nativa**.
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   **Reindirizzare Uri** è una combinazione di schema e stringa utilizzate per restituire le risposte token Azure Active Directory.  Immettere un valore specifico per l'applicazione, ad esempio `http://DirectorySearcher`.
-   Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda **Configura** .
- Anche nella scheda **Configura** , individuare la sezione "Autorizzazioni da altre applicazioni".  Per l'applicazione "Azure Active Directory", aggiungere l'autorizzazione di **Directory dell'organizzazione l'accesso** con **Autorizzazioni di delega**.  In questo modo sarà l'applicazione per l'API di grafico per gli utenti della query.

## <a name="2-install--configure-adal"></a>*2. installare e configurare ADAL*
Dopo aver creato un'applicazione di Azure Active Directory, è possibile installare ADAL e scrivere il codice di identità.  Affinché ADAL la possibilità di comunicare con Azure Active Directory, è necessario fornirgli alcune informazioni la registrazione di app.
-   Iniziare aggiungendo ADAL al progetto DirectorySearcher tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Nel progetto DirectorySearcher aprire `app.config`.  Sostituire i valori degli elementi di `<appSettings>` sezione per riflettere i valori di input al portale di Azure.  Il codice farà riferimento questi valori ogni volta che viene utilizzato ADAL.
    -   Il `ida:Tenant` è il dominio del tenant di Azure Active Directory, ad esempio contoso.onmicrosoft.com
    -   Il `ida:ClientId` è clientId dell'applicazione sono stati copiati dal portale.
    -   Il `ida:RedirectUri` è il reindirizzamento url registrato nel portale.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. utilizzare ADAL per ottenere i token da AAD*
Seguire il principio base dietro ADAL è che ogni volta che l'app è necessario un token di accesso, chiama semplicemente `authContext.AcquireTokenAsync(...)`, e ADAL non tutte le altre applicazioni.  

-   Nel `DirectorySearcher` progetto aperto `MainWindow.xaml.cs` e individuare il `MainWindow()` metodo.  Il primo passaggio consiste nel inizializzare l'app `AuthenticationContext` -ADAL della classe primaria.  Si tratta in cui si passano ADAL le coordinate è necessario comunicare con Azure Active Directory e indicare la modalità di memorizzazione nella cache token.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Individuare il `Search(...)` metodo, verrà richiamato quando l'utente cliks "Search" pulsante nell'interfaccia utente dell'applicazione.  Questo metodo esegue una richiesta GET all'API di Azure Active Directory grafico per eseguire una query per gli utenti di cui UPN inizia con il termine di ricerca specificati.  Ma eseguire query su API di grafico, è necessario includere un Access nel `Authorization` intestazione della richiesta - questa entra ADAL in.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Quando l'app richiede un token chiamando `AcquireTokenAsync(...)`, ADAL proverà a restituire un token senza chiedere all'utente le credenziali.  Se ADAL determina che l'utente deve eseguire l'accesso ottenere un token, verrà visualizzare una finestra di dialogo di accesso, raccogliere le credenziali dell'utente e restituire un token volta eseguita l'autenticazione.  Se ADAL è in grado di restituire un token per qualsiasi motivo, genera un `AdalException`.
- Si noti che la `AuthenticationResult` oggetto contiene un `UserInfo` oggetto che può essere utilizzato per raccogliere informazioni potrebbero essere necessarie l'app.  In DirectorySearcher, `UserInfo` viene utilizzato per personalizzare l'interfaccia utente dell'applicazione con l'id dell'utente.

- Quando l'utente fa clic sul pulsante "Disconnetti", si desidera verificare che la chiamata successiva a `AcquireTokenAsync(...)` chiede all'utente di accesso.  Con ADAL, è sufficiente cancellare la cache dei token:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Tuttavia, se l'utente non fa clic sul pulsante "Disconnetti", verrà desidera mantenere la sessione dell'utente per la volta successiva che eseguono DirectorySearcher.  Quando si avvia l'app, è possibile controllare cache token di ADAL per un token esistente e aggiornare l'interfaccia utente di conseguenza.  Nel `CheckForCachedToken()` metodo, eseguire un'altra chiamata a `AcquireTokenAsync(...)`, questa volta passando il `PromptBehavior.Never` parametro.  `PromptBehavior.Never`informa ADAL che l'utente non dovrebbe essere chiesto per accedere e ADAL se deve essere invece generata un'eccezione non è in grado di restituire un token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Congratulazioni! Ora dispone di un'applicazione WPF .NET che è in grado di autenticazione degli utenti, in modo sicuro chiamare API Web usando OAuth 2.0 funzionante e ottenere informazioni di base relative all'utente.  Se non è ancora disponibile, è il momento per popolare il tenant con alcuni utenti.  Eseguire l'app DirectorySearcher e accedere con uno di questi utenti.  Ricerca di altri utenti in base alle loro UPN.  Chiudere l'app e ripetere l'esecuzione.  Si noti come rimane intatta la sessione dell'utente.  Disconnettersi e accedere con un altro utente.

ADAL rende più facile tutte queste caratteristiche comuni identità incorporare nell'applicazione.  Si occupa di tutte le operazioni di dirty necessità - gestione della cache, supporto del protocollo OAuth, eseguire una presentazione all'utente con un account di accesso dell'interfaccia utente, l'aggiornamento di token scaduto e così via.  Ciò che occorre sapere è una singola chiamata all'API `authContext.AcquireTokenAsync(...)`.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  È possibile procedere con per altri scenari.  È consigliabile provare:

[Proteggere un Web .NET API con Azure Active Directory >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
