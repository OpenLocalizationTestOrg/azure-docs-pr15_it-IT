<properties
    pageTitle="Azure Active Directory AngularJS introduzione | Microsoft Azure"
    description="Modalità di compilazione di un'applicazione angolare JS singola pagina che si integra con Azure Active Directory per accedere e chiama Azure Active Directory protetto API utilizzando OAuth."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Protezione AngularJS singola pagina App con Azure Active Directory

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure Active Directory rende semplice e semplice per aggiungere accesso in Disconnetti e proteggere le chiamate API OAuth alle App singola pagina.  Consente l'app di autenticazione degli utenti con il proprio account Active Directory e utilizzare qualsiasi web API protetto da Azure Active Directory, ad esempio le API di Office 365 o l'API di Azure.

Per le applicazioni di javascript in esecuzione in un browser, Azure Active Directory è Active Directory Authentication Library o adal.js.  Obiettivo esclusiva di Adal.js è per rendere più semplice per l'app ottenere i token di accesso.  Per mostrare solo quanto è semplice, qui verrà creata un'applicazione di AngularJS l'elenco attività che:

- Disconnette l'utente all'App utilizzando Azure Active Directory come provider di identità.
- Consente di visualizzare alcune informazioni relative all'utente.
- In modo sicuro chiama per eseguire elenco API dell'app utilizzando i token del titolare da AAD.
- Disconnette l'utente dall'app.

Per creare l'applicazione di lavoro completa, è necessario:

2. Registrare l'applicazione di Azure Active Directory.
3. Installare ADAL e configurare il SPA.
5. Utilizzare ADAL per proteggere le pagine di SPA.

Per iniziare a, [scaricare la struttura di app](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) o il [download di esempio completo](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  È necessario anche un tenant di Azure Active Directory in cui è possibile creare utenti e registrare un'applicazione.  Se si dispone già di un tenant, [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. registrare l'applicazione DirectorySearcher*
Per abilitare l'app di autenticazione degli utenti e ottenere i token, è prima necessario registrare nel tenant di Azure Active Directory:

-   Accedere al [portale di gestione di Azure](https://manage.windowsazure.com)
-   Nel riquadro di spostamento a sinistra, fare clic su **Active Directory**
-   Selezionare un tenant in cui si desidera registrare l'applicazione.
-   Fare clic sulla scheda **applicazioni** e fare clic su **Aggiungi** nel cassetto inferiore.
-   Seguire le istruzioni e creare una nuova **applicazione Web e/o WebAPI**.
    -   Il **nome** dell'applicazione per descrivere l'applicazione agli utenti finali.
    -   **Reindirizzare Uri** è posizione in cui AAD restituirà i token.  Il percorso predefinito per questo esempio è`https://localhost:44326/`
-   Dopo aver completato la registrazione, AAD assegna l'app univoco **ID Client**.  È necessario questo valore nelle sezioni successive, quindi copiarlo dalla scheda **Configura** .
- Adal.js utilizza il flusso implicito OAuth per comunicare con Azure Active Directory.  È necessario attivare il flusso implicito per l'applicazione tramite:
    - Scaricare il manifesto dell'applicazione facendo clic su **Gestisci manifesto**.
    - Aprire il manifesto e individuare il `oauth2AllowImplicitFlow` proprietà. Impostare il relativo valore su `true`.
    - Per salvare e caricare il manifesto dell'applicazione facendo clic su **Gestisci manifesto** nuovamente.

## <a name="2-install-adal--configure-the-spa"></a>*2. ADAL installare e configurare il SPA*
Dopo aver creato un'applicazione di Azure Active Directory, è possibile installare adal.js e scrivere il codice di identità.

-   Iniziare aggiungendo adal.js al progetto TodoSPA tramite la Console di gestione pacchetti:
  - Scaricare [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) e aggiungerlo di `App/Scripts/` directory del progetto.
  - Scaricare [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) e aggiungerlo di `App/Scripts/` directory del progetto.
  - Caricare ogni script prima del termine del `</body>` in `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   Back-end deve contenete back-end di SPA all'API elenco eseguire per accettare i token dal browser, le informazioni di configurazione sulla registrazione di app. Nel progetto TodoSPA aprire `web.config`.  Sostituire i valori degli elementi di `<appSettings>` sezione per riflettere i valori di input al portale di Azure.  Il codice farà riferimento questi valori ogni volta che viene utilizzato ADAL.
    -   Il `ida:Tenant` è il dominio del tenant di Azure Active Directory, ad esempio contoso.onmicrosoft.com
    -   Il `ida:Audience` deve essere l' **ID Client** dell'applicazione sono stati copiati dal portale.

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3. utilizzare ADAL per proteggere le pagine di SPA*
Adal.js è stato creato per l'integrazione con provider di route e http AngularJS, che consente di proteggere singole visualizzazioni nel SPA.

- In `App/Scripts/app.js`, porta nel modulo adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- È ora possibile inizializzare il `adalProvider` con i valori di configurazione della registrazione applicazione, anche in `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- A questo punto per proteggere la `TodoList` visualizzare nell'app solo una riga di codice è necessaria - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Ora è un'applicazione sicura singola pagina con la possibilità di effettuare l'accesso agli utenti e inviare richieste protette token del titolare l'API di back-end.  Quando un utente sceglie il `TodoList` collegamento adal.js verrà automaticamente reindirizzati alla Azure Active Directory per accedi se necessario.  Inoltre, adal.js vengono associati automaticamente un Access a tutte le richieste ajax che vengono inviate agli back-end dell'applicazione.  Si tratta bare minimo necessario per creare un SPA con adal.js - ma sono disponibili numerose altre funzioni che risultano utili in stabilimenti termali:

- In modo esplicito problema di accesso e disconnessione richieste è possibile definire funzioni nei controller di che richiamano adal.js.  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- È anche consigliabile presentare informazioni utente nell'interfaccia utente dell'applicazione.  Il servizio di adal è già stato aggiunto per la `userDataCtrl` controller, è possibile accedere la `userInfo` oggetto in visualizzazione associata `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Sono inoltre disponibili molti scenari in cui è sapere se l'utente ha effettuato l'accesso o meno.  È inoltre possibile utilizzare il `userInfo` oggetto per raccogliere le informazioni.  Ad esempio, in `index.html` è possibile visualizzare il pulsante "Accesso" o "Disconnessione" in base allo stato di autenticazione:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Congratulazioni! La tua promozione Azure integrata singola pagina App è stata completata.  Possibile autenticare gli utenti, in modo sicuro chiamare il back-end tramite OAuth 2.0 e recupera informazioni di base relative all'utente.  Se non è ancora disponibile, è il momento per popolare il tenant con alcuni utenti.  Eseguire il per eseguire elenco SPA e accedere con uno di questi utenti.  Aggiungere attività agli utenti di elenco, disconnettersi e accedere di nuovo.

Adal.js rende più facile tutte queste caratteristiche comuni identità incorporare nell'applicazione.  Si occupa di tutte le operazioni di dirty necessità - gestione della cache, supporto del protocollo OAuth, eseguire una presentazione all'utente con un account di accesso dell'interfaccia utente, l'aggiornamento di token scaduto e così via.

Per riferimento, esempio completa (senza i valori di configurazione) verrà fornita [di seguito](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  È possibile procedere con per altri scenari.  È consigliabile provare:

[Chiamare dell'API Web CORS da un SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
