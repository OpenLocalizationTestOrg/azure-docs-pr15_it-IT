<properties
    pageTitle="Guida introduttiva di Azure Active Directory Windows Phone | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione di Windows Phone che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>



# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Integrazione di Azure Active Directory con un'App di Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se si sviluppano soluzioni per un'app di Windows Phone 8.1, Azure Active Directory rende semplice e diretto per poter eseguire l'autenticazione gli utenti con il proprio account Active Directory.  Consente inoltre sicuro utilizzare qualsiasi web API protetto da Azure Active Directory, ad esempio le API di Office 365 o l'API di Azure nell'applicazione.

> [AZURE.NOTE] In questo esempio Usa ADAL v 2.0.  Per le nuove tecnologie, è consigliabile invece provare il nostro [esercitazione universale di Windows tramite ADAL alla versione 3.0](active-directory-devquickstarts-windowsstore.md).  Se si creano effettivamente un'app per Windows Phone 8.1, questo è il posto giusto.  V 2.0 ADAL è ancora completamente supportata e il modo consigliato di sviluppo di App agianst Windows Phone 8.1 Usa Azure Active Directory.

Per i client nativo .NET che è necessario accedere alle risorse protette, Azure Active Directory è Active Directory Authentication Library o ADAL.  Obiettivo esclusiva di ADAL è per rendere più semplice per l'app ottenere i token di accesso.  Per mostrare solo quanto è semplice, qui verrà creata una "ricerca di Directory" app di Windows Phone 8.1 che:

-   Ottiene access token per richiamare l'API di Azure Active Directory grafico utilizzando il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Ricerca in una directory per gli utenti con l'UPN specificato.
-   Gli utenti segni.

Per creare l'applicazione di lavoro completa, è necessario:

2. Registrare l'applicazione di Azure Active Directory.
3. Installare e configurare ADAL.
5. Utilizzare ADAL per ottenere i token da Azure Active Directory.

Per iniziare a, [scaricare un progetto di base](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Ogni è una soluzione di Visual Studio 2013.  È necessario anche un tenant di Azure Active Directory in cui è possibile creare utenti e registrare un'applicazione.  Se si dispone già di un tenant, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>*1. registrare l'applicazione di ricerca di Directory*
Per abilitare l'app ottenere i token, è prima necessario registrare nel tenant di Azure Active Directory e concessione dell'autorizzazione per accedere all'API di Azure Active Directory grafico:

-   Accedere al [portale di gestione di Azure](https://manage.windowsazure.com)
-   Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**
-   Selezionare un tenant in cui si desidera registrare l'applicazione.
-   Fare clic sulla scheda **applicazioni** e fare clic su **Aggiungi** nel cassetto inferiore.
-   Seguire le istruzioni e creare una nuova **Applicazione Client nativa**.
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   **Reindirizzare Uri** è una combinazione di schema e stringa utilizzate per restituire le risposte token Azure Active Directory.  Immettere un valore di segnaposto per il momento, ad esempio `http://DirectorySearcher`.  È necessario sostituire questo valore in un secondo momento.
-   Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda **Configura** .
- Anche nella scheda **Configura** , individuare la sezione "Autorizzazioni da altre applicazioni".  Per l'applicazione "Azure Active Directory", aggiungere l'autorizzazione di **Directory dell'organizzazione l'accesso** con **Autorizzazioni di delega**.  In questo modo sarà l'applicazione per l'API di grafico per gli utenti della query.

## <a name="2-install--configure-adal"></a>*2. installare e configurare ADAL*
Dopo aver creato un'applicazione di Azure Active Directory, è possibile installare ADAL e scrivere il codice di identità.  Affinché ADAL la possibilità di comunicare con Azure Active Directory, è necessario fornirgli alcune informazioni la registrazione di app.
-   Iniziare aggiungendo ADAL al progetto DirectorySearcher tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Nel progetto DirectorySearcher aprire `MainPage.xaml.cs`.  Sostituire i valori di `Config Values` area per riflettere i valori di input al portale di Azure.  Il codice farà riferimento questi valori ogni volta che viene utilizzato ADAL.
    -   Il `tenant` è il dominio del tenant di Azure Active Directory, ad esempio contoso.onmicrosoft.com
    -   Il `clientId` è clientId dell'applicazione sono stati copiati dal portale.
-   È ora necessario individuare uri callback per l'app di Windows Phone.  Impostare un punto di interruzione in questa riga la `MainPage` metodo:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Eseguire l'app e copiare prevedere il valore di `redirectUri` quando l'interruzione.  Dovrebbe essere simile alla

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Tornando nella scheda della **configurazione** dell'applicazione nel portale di gestione di Azure, sostituire il valore di **RedirectUri** con questo valore.  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. utilizzare ADAL per ottenere i token da AAD*
Seguire il principio base dietro ADAL è che ogni volta che l'app è necessario un token di accesso, chiama semplicemente `authContext.AcquireToken(…)`, e ADAL non tutte le altre applicazioni.  

-   Il primo passaggio consiste nel inizializzare l'app `AuthenticationContext` -ADAL della classe primaria.  Si tratta in cui si passano ADAL le coordinate è necessario comunicare con Azure Active Directory e indicare la modalità di memorizzazione nella cache token.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Individuare il `Search(...)` metodo, verrà richiamato quando l'utente cliks "Search" pulsante nell'interfaccia utente dell'applicazione.  Questo metodo esegue una richiesta GET all'API di Azure Active Directory grafico per eseguire una query per gli utenti di cui UPN inizia con il termine di ricerca specificati.  Ma eseguire query su API di grafico, è necessario includere un Access nel `Authorization` intestazione della richiesta - questa entra ADAL in.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Se è necessaria l'autenticazione interattivo, ADAL verrà usata Web autenticazione gestore (Rubrica di Windows Phone) e [il modello di continuazione](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) per visualizzare Azure AD accedere nella pagina.  Quando l'utente accede, l'app deve passare ADAL i risultati di interazione della Rubrica di Windows.  Questo è semplice come l'implementazione di `ContinueWebAuthentication` interfaccia:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- A questo punto è possibile utilizzare il `AuthenticationResult` che ADAL restituito all'applicazione in uso.  Nel `QueryGraph(...)` callback, allegare Access è stato acquistato per la richiesta GET nell'intestazione di autorizzazione:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- È inoltre possibile utilizzare il `AuthenticationResult` oggetto per visualizzare le informazioni relative all'utente nell'app. Nel `QueryGraph(...)` metodo, utilizzare il risultato per visualizzare l'id dell'utente nella pagina:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Infine, è possibile utilizzare ADAL per accedere all'utente dall'applicazione anche.  Quando l'utente fa clic sul pulsante "Disconnetti", si desidera verificare che la chiamata successiva a `AcquireTokenSilentAsync(...)` avrà esito negativo.  Con ADAL, è sufficiente cancellare la cache dei token:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Congratulazioni! Ora attiva una app di Windows Phone è in grado di autenticazione degli utenti, in modo sicuro chiamare API Web usando OAuth 2.0 e ottenere informazioni di base relative all'utente.  Se non è ancora disponibile, è il momento per popolare il tenant con alcuni utenti.  Eseguire l'app DirectorySearcher e accedere con uno di questi utenti.  Ricerca di altri utenti in base alle loro UPN.  Chiudere l'app e ripetere l'esecuzione.  Si noti come rimane intatta la sessione dell'utente.  Disconnettersi e accedere con un altro utente.

ADAL rende più facile tutte queste caratteristiche comuni identità incorporare nell'applicazione.  Si occupa di tutte le operazioni di dirty necessità - gestione della cache, supporto del protocollo OAuth, eseguire una presentazione all'utente con un account di accesso dell'interfaccia utente, l'aggiornamento di token scaduto e così via.  Ciò che occorre sapere è una singola chiamata all'API `authContext.AcquireToken*(…)`.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  È possibile procedere con per gli scenari di identità aggiuntive.  È consigliabile provare:

[Proteggere un Web .NET API con Azure Active Directory >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]