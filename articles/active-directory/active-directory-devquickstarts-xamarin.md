<properties
    pageTitle="Azure Active Directory Xamarin introduzione | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione di Xamarin che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Integrare un'App Xamarin Azure Active Directory

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin consente di scrivere App per dispositivi mobili in c# che possono essere eseguiti sui iOS, Android e Windows (PC e dispositivi mobili). Se si crea un'app utilizzando Xamarin, Azure Active Directory rende semplice e diretto per poter eseguire l'autenticazione gli utenti con il proprio account Active Directory. Consente inoltre sicuro utilizzare qualsiasi web API protetto da Azure Active Directory, ad esempio le API di Office 365 o l'API di Azure nell'applicazione.

Per le applicazioni di Xamarin che devono accedere alle risorse protette, Azure Active Directory è Active Directory Authentication Library o ADAL. Obiettivo esclusiva di ADAL è per rendere più semplice per l'app ottenere i token di accesso. Per mostrare solo quanto è semplice, qui verrà creata un'app "Ricerca di Directory" che:

-   Viene eseguito su iOS, Android, Desktop di Windows, Windows Phone e Windows Store.
- Utilizza una libreria di classi portabile singolo (PCL) per eseguire l'autenticazione degli utenti e ottenere token per l'API di Azure Active Directory grafico
-   Ricerca in una directory per gli utenti con l'UPN specificato.

Per creare l'applicazione di lavoro completa, è necessario:

2. Configurare l'ambiente di sviluppo Xamarin
2. Registrare l'applicazione di Azure Active Directory.
3. Installare e configurare ADAL.
5. Utilizzare ADAL per ottenere i token da Azure Active Directory.

Per iniziare a, [scaricare un progetto di base](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ogni è una soluzione di Visual Studio 2013. È necessario anche un tenant di Azure Active Directory in cui è possibile creare utenti e registrare un'applicazione. Se si dispone già di un tenant, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>*0. configurare l'ambiente di sviluppo Xamarin*
Poiché questa esercitazione include progetti per iOS, Android e Windows, è necessario Visual Studio e Xamarin insieme. Per creare l'ambiente necessarie, seguire le istruzioni di completamento [e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) su MSDN. Queste istruzioni includono materiale che è possibile esaminare per altre informazioni su Xamarin mentre è in attesa per i programmi di installazione completare. 

Una volta aver completato la configurazione necessaria, aprire la soluzione in Visual Studio per iniziare. Si noterà sei progetti: cinque progetti specifici della piattaforma e una libreria di classi portabile condivisi per tutte le piattaforme,`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1. registrare l'applicazione di ricerca di Directory*
Per abilitare l'app ottenere i token, è prima necessario registrare nel tenant di Azure Active Directory e concessione dell'autorizzazione per accedere all'API di Azure Active Directory grafico:

-   Accedere al [portale di gestione di Azure](https://manage.windowsazure.com)
-   Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**
-   Selezionare un tenant in cui si desidera registrare l'applicazione.
-   Fare clic sulla scheda **applicazioni** e fare clic su **Aggiungi** nel cassetto inferiore.
-   Seguire le istruzioni e creare una nuova **Applicazione Client nativa**.
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali
    -   **Reindirizzare Uri** è una combinazione di schema e stringa utilizzate per restituire le risposte token Azure Active Directory. Immettere un valore, ad esempio `http://DirectorySearcher`.
-   Dopo aver completato la registrazione, AAD assegna l'app un identificatore univoco client. È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda **Configura** .
- Anche nella scheda **Configura** , individuare la sezione "Autorizzazioni da altre applicazioni". Per l'applicazione "Azure Active Directory", aggiungere l'autorizzazione di **Directory dell'organizzazione l'accesso** con **Autorizzazioni di delega**. In questo modo sarà l'applicazione per l'API di grafico per gli utenti della query.

## <a name="2-install--configure-adal"></a>*2. installare e configurare ADAL*
Dopo aver creato un'applicazione di Azure Active Directory, è possibile installare ADAL e scrivere il codice di identità. Affinché ADAL la possibilità di comunicare con Azure Active Directory, è necessario fornirgli alcune informazioni la registrazione di app.
-   Iniziare aggiungendo ADAL a ognuno dei progetti della soluzione tramite la Console di gestione pacchetti.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Si noterà che due riferimenti di raccolta vengano aggiunti a ogni progetto - parte PCL di ADAL e una parte specifica della piattaforma.

-   Nel progetto DirectorySearcherLib aprire `DirectorySearcher.cs`. Modificare i valori dei membri di classe per riflettere i valori di input al portale di Azure. Il codice farà riferimento questi valori ogni volta che viene utilizzato ADAL.
    -   Il `tenant` è il dominio del tenant di Azure Active Directory, ad esempio contoso.onmicrosoft.com
    -   Il `clientId` è clientId dell'applicazione sono stati copiati dal portale.
    - Il `returnUri` è redirectUri immesso nel portale, ad esempio `http://DirectorySearcher`.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. utilizzare ADAL per ottenere i token da AAD*
*Almost* tutta la logica dell'applicazione autenticazione si trova in `DirectorySearcher.SearchByAlias(...)`. Tutto ciò che è necessario nei progetti specifici della piattaforma consiste nel passare un parametro di scelta rapida per la `DirectorySearcher` PCL.

- Aprire innanzitutto `DirectorySearcher.cs` e aggiungere un nuovo parametro per il `SearchByAlias(...)` metodo. `IPlatformParameters`è il parametro contestuale che incapsula gli oggetti specifici della piattaforma che ADAL deve eseguire l'autenticazione.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   Successivamente, inizializzare il `AuthenticationContext` -ADAL della classe primaria. Si tratta in cui si passano ADAL le coordinate necessarie per comunicare con Azure Active Directory. Quindi chiama `AcquireTokenAsync(...)`, che accetta la `IPlatformParameters` oggetto e richiama il flusso di autenticazione necessario per restituire un token all'app.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`tenterà innanzitutto restituire un token per la risorsa richiesta (API grafico in questo caso) senza chiedere conferma all'utente di immettere le credenziali (tramite la memorizzazione nella cache o aggiornamento dei token precedente). Solo se necessario, verrà visualizzato l'utente il segno di Azure Active Directory nella pagina prima di acquisire token richiesto.


- È quindi possibile allegare il token di accesso alla richiesta di API grafico nell'intestazione di autorizzazione:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

È tutto per il `DirectorySearcher` PCL e l'applicazione dell'identità correlate codice.  È ora per chiamare la `SearchByAlias(...)` metodo nelle visualizzazioni di entrambe le piattaforme, e dove necessario aggiungere codice per correttamente la gestione del ciclo di vita dell'interfaccia utente.

####<a name="android"></a>Android:
- In `MainActivity.cs`, aggiungere una chiamata a `SearchByAlias(...)` nel pulsante fare clic su gestore:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- È necessario ignorare il `OnActivityResult` metodo ciclo di vita per inoltrare qualsiasi tipo di autenticazione reindirizza il metodo appropriato.  ADAL fornisce un metodo di supporto per l'oggetto in Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Desktop di Windows:
- In `MainWindow.xaml.cs`, è sufficiente effettuare una chiamata a `SearchByAlias(...)` passando un `WindowInteropHelper` sul desktop `PlatformParameters` oggetto:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>iOS:
- In `DirSearchClient_iOSViewController.cs`, il file iOS `PlatformParameters` oggetto accetta semplicemente un riferimento al Controller di visualizzazione:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Universale di Windows:
- In Windows universale, aprire `MainPage.xaml.cs` e implementare la `Search` metodo, che utilizza un metodo di supporto in un progetto condiviso per l'aggiornamento dell'interfaccia utente in base alle esigenze.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Congratulazioni! È ora disponibile un lavoro app Xamarin che è in grado di autenticazione degli utenti e sicuro chiamare API Web usando OAuth 2.0 su cinque diverse piattaforme. Se non è ancora disponibile, è il momento per popolare il tenant con alcuni utenti. Eseguire l'app DirectorySearcher e accedere con uno di questi utenti. Ricerca di altri utenti in base alle loro UPN.

ADAL rende più facile incorporare caratteristiche comuni di identità dell'applicazione. Si occupa di tutte le operazioni di dirty necessità - gestione della cache, supporto del protocollo OAuth, eseguire una presentazione all'utente con un account di accesso dell'interfaccia utente, l'aggiornamento di token scaduto e così via. Ciò che occorre sapere è una singola chiamata all'API `authContext.AcquireToken*(…)`.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). È possibile procedere con per gli scenari di identità aggiuntive. È consigliabile provare:

[Proteggere un Web .NET API con Azure Active Directory >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
