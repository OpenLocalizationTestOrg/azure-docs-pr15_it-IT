<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione desktop di Windows che include accesso aggiuntivo, per l'abbonamento, e la gestione dei profili con Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure Active Directory B2C: Creare un'app desktop di Windows

Utilizzando B2C Azure Active Directory (Azure Active Directory), è possibile aggiungere funzionalità di gestione delle identità potente strumento self-service per l'app desktop in pochi passaggi brevi. In questo articolo illustra come creare un'app "elenco da fare".NET Windows Presentation Foundation (WPF) che include utente per l'abbonamento, accesso e gestione dei profili. L'app verrà incluso il supporto per l'abbonamento e l'accesso con un nome utente o un messaggio di posta elettronica. Supporto per l'abbonamento e accesso verrà incluso anche usando l'account di social networking, ad esempio Facebook e Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure Active Directory B2C

Prima di poter usare B2C di Azure Active Directory, è necessario creare una directory o del tenant.  Una directory è un contenitore per tutti gli utenti, App, gruppi e altro. Se non è una già disponibile, [creare una directory B2C](active-directory-b2c-get-started.md) prima di procedere in questa Guida.

## <a name="create-an-application"></a>Creare un'applicazione

È necessario creare un'app nella directory B2C. In questo modo le informazioni di Azure Active Directory che è necessario comunicare in modo sicuro con l'app. Per creare un'app, seguire [queste istruzioni](active-directory-b2c-app-registration.md).  Assicurarsi di:

- Includere un **client nativo** nell'applicazione.
- Copiare il **reindirizzamento URI** `urn:ietf:wg:oauth:2.0:oob`. È l'URL predefinito per questo esempio di codice.
- Copiare l' **ID dell'applicazione** che sono state assegnate a un'applicazione. Sarà necessario in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri

In Azure Active Directory B2C, ogni esperienza utente è definita da un [criterio](active-directory-b2c-reference-policies.md). In questo esempio contiene tre esperienze identità: iscriversi, accedere e modificare il profilo. È necessario creare un criterio per ogni tipo, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Quando si creano tre criteri, assicurarsi di:

- Scegliere **l'ID utente per l'abbonamento** o **messaggio di posta elettronica per l'abbonamento** in e il provider di identità.
- Scegliere **nome visualizzato** e altri attributi iscrizione nei criteri di iscrizione.
- Scegliere **nome** e **ID** sulle attestazioni come attestazioni applicazione per ogni criterio. È possibile scegliere anche altri reclami.
- Copiare il **nome** di ogni criterio dopo averlo creato. Deve avere il prefisso `b2c_1_`.  È necessario i nomi dei criteri in un secondo momento.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo la creazione di tre criteri è stata completata, si è pronti per creare l'applicazione.

## <a name="download-the-code"></a>Scaricare il codice

Il codice per questa esercitazione [viene mantenuto per GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Per creare il campione durante il lavoro, è possibile [scaricare un progetto di base come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). È anche possibile duplicare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L'app completa è [disponibile](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) anche come file con estensione zip o sul `complete` diramazione dell'archivio stesso.

Dopo avere scaricato il codice di esempio, aprire il file sln di Visual Studio per iniziare. Il `TaskClient` progetto è l'applicazione desktop WPF che l'utente interagisce con. Ai fini di questa esercitazione, chiama web back-end attività API, ospitato in Azure, che contiene l'elenco di attività di ogni utente.  Non è necessario creare web API, è già in esecuzione dell'utente.

Per informazioni su come dell'API web autentica in modo sicuro richieste con Azure Active Directory B2C, consultare l' [articolo introduzione dell'API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Eseguire i criteri
L'app comunica con Azure Active Directory B2C mediante l'invio di messaggi di autenticazione che specificano i criteri che desiderano eseguire come parte della richiesta HTTP. Per le applicazioni desktop di .NET, è possibile utilizzare l'anteprima di una raccolta di autenticazione Microsoft (MSAL) per inviare messaggi di autenticazione OAuth 2.0, eseguire i criteri e ottenere i token emessi web API di chiamata.

### <a name="install-msal"></a>Installare MSAL
Aggiungere MSAL per il `TaskClient` progetto tramite la Console di gestione di pacchetti di Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Immettere i dettagli B2C
Aprire il file `Globals.cs` e sostituire i valori di proprietà con uno personalizzato. Questa classe viene utilizzata in `TaskClient` ai valori di riferimento comune.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>Creare il PublicClientApplication
La classe principale di MSAL è `PublicClientApplication`. Questa classe rappresenta l'applicazione nel sistema di Azure Active Directory B2C. Durante la creazione di un'istanza di Inizializza app `PublicClientApplication` in `MainWindow.xaml.cs`. Può essere utilizzato in tutta la finestra.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>Avviare un flusso di abbonamento
Quando un utente seleziona l'opzione segni di backup, si desidera avviare un flusso di abbonamento che utilizza i criteri per l'abbonamento che è stato creato. Se si utilizza MSAL, è sufficiente chiamare `pca.AcquireTokenAsync(...)`. I parametri passare a `AcquireTokenAsync(...)` determinare quali token viene visualizzato, il criterio utilizzato la richiesta di autenticazione e altro ancora.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>Avviare un flusso di accesso
È possibile avviare un flusso di accesso allo stesso modo avviare un flusso di iscrizione. Quando un utente accede, effettuare la stessa chiamata a MSAL, questa volta utilizzando i criteri di accesso:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Avviare un flusso di modifica profilo
Nuovo, è possibile eseguire un criterio di modifica profilo nello stesso modo:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

In tutti i casi, MSAL restituirà un token in `AuthenticationResult` o genera un'eccezione. Ogni volta che viene visualizzato un token da MSAL, è possibile utilizzare il `AuthenticationResult.User` oggetto per aggiornare i dati utente nell'app, ad esempio l'interfaccia utente. ADAL memorizza anche il token per l'uso in altre parti dell'applicazione.


### <a name="check-for-tokens-on-app-start"></a>Verificare la presenza di token nella schermata start di app
È anche possibile utilizzare MSAL per tenere traccia di stato di accesso dell'utente.  In questa app, si vuole che l'utente per rimanere connessi anche se si chiude l'app e aprire nuovamente.  Tornare all'interno di `OnInitialized` ignorare, usare del MSAL `AcquireTokenSilent` metodo per verificare la presenza di cache token:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>Chiamare l'API di attività
Ora hanno implementato MSAL per eseguire criteri e recuperare i token.  Quando si desidera utilizzare uno questi token di chiamare l'API di attività, è possibile utilizzare nuovamente del MSAL `AcquireTokenSilent` metodo per verificare la presenza di cache token:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

Quando la chiamata a `AcquireTokenSilentAsync(...)` ha avuto esito positivo e un token si trova nella cache, è possibile aggiungere il token di `Authorization` intestazione della richiesta HTTP. Web attività API utilizzerà questa intestazione per la richiesta di leggere l'elenco di attività dell'utente:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Disconnessione l'utente
Infine, è possibile utilizzare MSAL per terminare la sessione di un utente con l'app quando l'utente seleziona **Disconnetti**.  Quando si utilizza MSAL, ciò avviene deselezionando tutti i token dalla cache di token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Infine, creare ed eseguire l'esempio.  Iscriversi per l'app utilizzando un nome utente o indirizzo di posta elettronica. Disconnettersi e accedere con lo stesso account. Modificare il profilo dell'utente. Eseguire la disconnessione e iscriversi utilizzando un altro utente.

## <a name="add-social-idps"></a>Aggiungere IDPs social networking

Attualmente, l'app supporta solo utente per l'abbonamento e accesso che utilizzano **account locali**. Questi sono archiviati nella directory B2C di usare un nome utente e password. Utilizzo di Azure Active Directory B2C, è possibile aggiungere supporto per gli altri provider di identità (IDPs) senza modificare il proprio codice.

Per aggiungere l'app IDPs social networking, iniziare seguendo le istruzioni dettagliate in questi articoli. Per ogni IDP desiderato per il supporto, è necessario registrare un'applicazione di sistema e ottenere un ID client.

- [Impostare se stessi Facebook come un IDP](active-directory-b2c-setup-fb-app.md)
- [Configurare Google come un IDP](active-directory-b2c-setup-goog-app.md)
- [Impostare se stessi Amazon come un IDP](active-directory-b2c-setup-amzn-app.md)
- [Impostare se stessi LinkedIn come un IDP](active-directory-b2c-setup-li-app.md)

Dopo aver aggiunto il provider di identità alla directory B2C, è necessario modificare ognuna dei tre criteri per includere la nuova IDPs, come descritto nell' [articolo di riferimento dei criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire di nuovo l'app. Opzioni di abbonamento in ognuna delle identità esperienze e verrà visualizzato il nuovo IDPs aggiunto come accesso.

È possibile sperimentare i criteri e osservare gli effetti sull'applicazione di esempio. Aggiungere o rimuovere IDPs, modificare applicazione reclami o modificare gli attributi per l'abbonamento. Sperimentare fino a quando non è possibile vedere come criteri, le richieste di autenticazione e MSAL unire.

Per riferimento, l' esempio completo [fornito come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). È possibile anche clonare da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
