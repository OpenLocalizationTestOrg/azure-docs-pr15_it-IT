<properties
pageTitle="Applicazione nativa di Azure Active Directory v 2.0 .NET | Microsoft Azure"
description="Come creare un'applicazione nativa .NET che firma utenti con entrambi Account Microsoft personale e account aziendale o dell'istituto di istruzione."
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>Aggiungere l'accesso a un'app Desktop di Windows

Con l'endpoint v 2.0, è possibile aggiungere rapidamente l'autenticazione per le applicazioni desktop con il supporto per entrambi gli account Microsoft personali e account aziendale o dell'istituto di istruzione.  Consente inoltre l'app comunicare in modo sicuro con un web back-end api, come [Microsoft Graph](https://graph.microsoft.io) e alcune delle [API unificata di Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE] Non tutte le caratteristiche e gli scenari di Azure Active Directory (AD) sono supportati dall'endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

Per le [applicazioni native .NET in esecuzione su un dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), Azure Active Directory è Microsoft identità Authentication Library o MSAL.  Obiettivo esclusiva di MSAL è per rendere più semplice per l'app ottenere i token per richiamare servizi web.  Per mostrare solo quanto è semplice, qui verrà creata un'app di elenco di attività WPF .NET che:

- L'utente accede & Ottiene accedere token utilizzando il [protocollo di autenticazione OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
- Chiama in modo sicuro un servizio web elenco attività, è inoltre protetto da 2.0 OAuth back-end.
- Uscire l'utente.

## <a name="download-sample-code"></a>Scaricare il codice di esempio

Il codice per questa esercitazione viene mantenuto [sul GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Per seguire, è possibile [scaricare struttura dell'app come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) o duplicare la struttura:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

L'app completa viene fornito alla fine dell'esercitazione anche.

## <a name="register-an-app"></a>Registrare un'app
Creare una nuova app [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)oppure seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Assicurarsi di:

- Copia verso il basso l' **Id applicazione** assegnata all'applicazione in uso, sarà necessario immetterla breve.
- Aggiungere la piattaforma **Mobile** per l'app.

## <a name="install--configure-msal"></a>Installare e configurare MSAL
Dopo aver creato un'app registrata con Microsoft, è possibile installare MSAL e scrivere il codice di identità.  Affinché MSAL la possibilità di comunicare endpoint v 2.0, è necessario fornirgli alcune informazioni la registrazione di app.

-   Iniziare aggiungendo MSAL al progetto TodoListClient tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   Nel progetto TodoListClient aprire `app.config`.  Sostituire i valori degli elementi di `<appSettings>` sezione per riflettere i valori di input al portale di registrazione di app.  Il codice farà riferimento questi valori ogni volta che viene utilizzato MSAL.
    -   Il `ida:ClientId` corrisponde all' **Id applicazione** dell'app sono stati copiati dal portale.

- Nel progetto elenco attività servizio aprire `web.config` nella radice del progetto.  
    - Sostituire il `ida:Audience` valore con lo stesso **Id applicazione** dal portale.

## <a name="use-msal-to-get-tokens"></a>Utilizzare MSAL per ottenere i token
Seguire il principio base dietro MSAL è che ogni volta che l'app è necessario un token di accesso, è sufficiente chiamare `app.AcquireToken(...)`, e MSAL non tutte le altre applicazioni.  

-   Nel `TodoListClient` progetto aperto `MainWindow.xaml.cs` e individuare il `OnInitialized(...)` metodo.  Il primo passaggio consiste nel inizializzare l'app `PublicClientApplication` -classe primaria del MSAL che rappresenta le applicazioni native.  Verrà visualizzata nel punto in cui si passano MSAL le coordinate che è necessario comunicare con Azure Active Directory e indicare la modalità di memorizzazione nella cache token.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- Quando si avvia l'app, si desidera controllare e verificare se l'utente è già stato effettuato l'accesso all'app.  Tuttavia, non si desidera richiamare un'interfaccia utente accesso ancora - verrà effettuata l'utente fare clic su "Accedi" per eseguire questa operazione.  Anche nel `OnInitialized(...)` metodo:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- Se l'utente non è connesso e fanno clic sul pulsante "Accessohttp", si desidera richiamare un account di accesso dell'interfaccia utente e richiedere all'utente di immettere le credenziali.  Implementare il gestore del pulsante accesso:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
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


}
```

- Se l'utente correttamente segni di, MSAL riceverà e memorizzare nella cache un token dell'utente ed è possibile procedere alla chiamata di `GetTodoList()` metodo con fiducia.  Tutto ciò che resta per ottenere le attività dell'utente è l'implementazione di `GetTodoList()` metodo.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Correre

Congratulazioni! È ora disponibile un'app di .NET WPF di lavoro che è in grado di autenticazione degli utenti e sicuro chiamare API Web usando OAuth 2.0.  Eseguire gli entrambi i progetti e accedere con un account Microsoft personale oppure un account aziendale o dell'istituto di istruzione.  Aggiungere attività all'elenco di attività dell'utente.  Eseguire la disconnessione ed esegue nuovamente l'accesso con un altro utente per visualizzare il proprio elenco attività.  Chiudere l'app e ripetere l'esecuzione.  Si noti come sessione la rimane intatta: perché l'app memorizza i token in un file locale.

MSAL semplifica incorporare funzionalità identità comuni l'app, con l'account personale e aziendale.  Si occupa di tutte le operazioni di dirty necessità - gestione della cache, supporto del protocollo OAuth, eseguire una presentazione all'utente con un account di accesso dell'interfaccia utente, l'aggiornamento di token scaduto e così via.  Ciò che occorre sapere è una singola chiamata all'API `app.AcquireTokenAsync(...)`.

Per riferimento, di esempio completo (senza i valori di configurazione) [viene fornito come un file ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)oppure è possibile duplicare il da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Passaggi successivi

È possibile procedere in argomenti più avanzati.  È consigliabile provare:

- [Proteggere l'API Web TodoListService con l'endpoint v 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Per ulteriori risorse, vedere:  

- [La Guida di sviluppo v 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Tag "msal" StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Ottenere gli aggiornamenti di sicurezza per i prodotti

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e la sottoscrizione per gli avvisi di avviso di sicurezza.
