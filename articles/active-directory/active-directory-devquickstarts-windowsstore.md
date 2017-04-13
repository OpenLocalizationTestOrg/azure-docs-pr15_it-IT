<properties
    pageTitle="Guida introduttiva di Azure Active Directory Windows Store | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione di Windows Store che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-with-a-windows-store-app"></a>Integrazione di Azure Active Directory con un'App di Windows Store

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se si sviluppano soluzioni per un'app per Windows Store, Azure Active Directory rende semplice e diretto per poter eseguire l'autenticazione gli utenti con il proprio account Active Directory.  Consente inoltre sicuro utilizzare qualsiasi web API protetto da Azure Active Directory, ad esempio le API di Office 365 o l'API di Azure nell'applicazione.

Per le app desktop di Windows Store che è necessario accedere alle risorse protette, Azure Active Directory è Active Directory Authentication Library o ADAL.  Obiettivo esclusiva di ADAL è per rendere più semplice per l'app ottenere i token di accesso.  Per mostrare solo quanto è semplice, qui verrà creata un'app "Ricerca di Directory" di Windows Store che:

-   Ottiene access token per richiamare l'API di Azure Active Directory grafico utilizzando il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Ricerca in una directory per gli utenti con l'UPN specificato.
-   Gli utenti segni.

Per creare l'applicazione di lavoro completa, è necessario:

2. Registrare l'applicazione di Azure Active Directory.
3. Installare e configurare ADAL.
5. Utilizzare ADAL per ottenere i token da Azure Active Directory.

Per iniziare a, [scaricare un progetto di base](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Ogni è una soluzione di Visual Studio 2015.  È necessario anche un tenant di Azure Active Directory in cui è possibile creare utenti e registrare un'applicazione.  Se si dispone già di un tenant, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

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
- Anche nella scheda **Configura** , individuare la sezione "Autorizzazioni da altre applicazioni".  Aggiungere l'autorizzazione di **accesso nella directory dell'utente registrato** con **Autorizzazioni di delega**per l'applicazione "Azure Active Directory".  In questo modo sarà l'applicazione per l'API di grafico per gli utenti della query.

## <a name="2-install--configure-adal"></a>*2. installare e configurare ADAL*
Dopo aver creato un'applicazione di Azure Active Directory, è possibile installare ADAL e scrivere il codice di identità.  Affinché ADAL la possibilità di comunicare con Azure Active Directory, è necessario fornirgli alcune informazioni la registrazione di app.
-   Iniziare aggiungendo ADAL al progetto DirectorySearcher tramite la Console di gestione pacchetti.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Nel progetto DirectorySearcher aprire `MainPage.xaml.cs`.  Sostituire i valori di `Config Values` area per riflettere i valori di input al portale di Azure.  Il codice farà riferimento questi valori ogni volta che viene utilizzato ADAL.
    -   Il `tenant` è il dominio del tenant di Azure Active Directory, ad esempio contoso.onmicrosoft.com
    -   Il `clientId` è clientId dell'applicazione sono stati copiati dal portale.
-   È ora necessario individuare uri callback per l'app di Windows Store.  Impostare un punto di interruzione in questa riga la `MainPage` metodo:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Compilare la soluzione, accertarsi che tutti i riferimenti di pacchetto vengono ripristinati.  Se pacchetti sono assenti, aprire la gestione del pacchetto Nuget e ripristinare i pacchetti.
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

    authContext = new AuthenticationContext(authority);
}
```

- Individuare il `Search(...)` metodo, verrà richiamato quando l'utente fa clic sul pulsante "Cerca" nell'interfaccia utente dell'applicazione.  Questo metodo esegue una richiesta GET all'API di Azure Active Directory grafico per eseguire una query per gli utenti di cui UPN inizia con il termine di ricerca specificati.  Ma eseguire query su API di grafico, è necessario includere un Access nel `Authorization` intestazione della richiesta - questa entra ADAL in.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
        }
        return;
    }
    ...
}
```
- Quando l'app richiede un token chiamando `AcquireTokenAsync(...)`, ADAL proverà a restituire un token senza chiedere all'utente le credenziali.  Se ADAL determina che l'utente deve eseguire l'accesso ottenere un token, verrà visualizzare una finestra di dialogo di accesso, raccogliere le credenziali dell'utente e restituire un token volta eseguita l'autenticazione.  Se è in grado di restituire un token per qualsiasi motivo, ADAL il `AuthenticationResult` stato sarà un errore.

- A questo punto è possibile utilizzare l'Access appena acquisito.  Anche nel `Search(...)` metodo, collegare il token alla richiesta di ottenere API grafico nell'intestazione di autorizzazione:

```C#
// Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

```
- È inoltre possibile utilizzare il `AuthenticationResult` oggetto per visualizzare le informazioni relative all'utente l'App, ad esempio l'id dell'utente:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Infine, è possibile utilizzare ADAL per accedere all'utente dall'applicazione anche.  Quando l'utente fa clic sul pulsante "Disconnetti", si desidera verificare che la chiamata successiva a `AcquireTokenAsync(...)` verrà visualizzato un segno nella visualizzazione.  Con ADAL, è sufficiente cancellare la cache dei token:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Congratulazioni! Ora attiva una app di Windows Store che è in grado di autenticazione degli utenti, in modo sicuro chiamare API Web usando OAuth 2.0 e ottenere informazioni di base relative all'utente.  Se non è ancora disponibile, è il momento per popolare il tenant con alcuni utenti.  Eseguire l'app DirectorySearcher e accedere con uno di questi utenti.  Ricerca di altri utenti in base alle loro UPN.  Chiudere l'app e ripetere l'esecuzione.  Si noti come rimane intatta la sessione dell'utente.  Eseguire la disconnessione (facendo clic per visualizzare la barra inferiore) e accedere con un altro utente.

ADAL rende più facile tutte queste caratteristiche comuni identità incorporare nell'applicazione.  Si occupa di tutte le operazioni di dirty necessità - gestione della cache, supporto del protocollo OAuth, eseguire una presentazione all'utente con un account di accesso dell'interfaccia utente, l'aggiornamento di token scaduto e così via.  Ciò che occorre sapere è una singola chiamata all'API `authContext.AcquireToken*(…)`.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  È possibile procedere con per gli scenari di identità aggiuntive.  È consigliabile provare:

[Proteggere un Web .NET API con Azure Active Directory >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
