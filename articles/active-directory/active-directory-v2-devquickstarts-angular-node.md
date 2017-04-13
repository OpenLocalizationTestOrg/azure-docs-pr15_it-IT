<properties
    pageTitle="Guida introduttiva di Azure Active Directory v 2.0 AngularJS | Microsoft Azure"
    description="Come creare un'app angolare JS singola pagina che accede account aziendale o dell'istituto di istruzione e gli utenti che dispongono entrambi gli account Microsoft personali."
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Aggiungere accesso a un'app di singola pagina AngularJS - NodeJS

In questo articolo verranno aggiunte effettuare l'accesso con l'account di Microsoft con tecnologia a un'app AngularJS utilizzando l'endpoint v 2.0 Azure Active Directory. endpoint v 2.0 consentono di eseguire una singola integrazione nell'app e autenticazione degli utenti con account sia personali e di lavoro o dell'istituto di istruzione.

In questo esempio è un'app di singola pagina di elenco da fare semplice che archivia le attività in un back-end API REST, scritto in NodeJS e protette tramite i token del titolare OAuth da Azure Active Directory.  App AngularJS utilizzerà il nostro Apri origine JavaScript autenticazione raccolta [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) per gestire l'intera procedura di accesso in e acquisire token per le chiamate API REST.  Per eseguire l'autenticazione ad altre API REST, ad esempio [Microsoft Graph](https://graph.microsoft.com) o le API di gestione delle risorse di Azure, è possibile utilizzare lo stesso modello.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="download"></a>Download

Per iniziare, è necessario scaricare e installare [Node](https://nodejs.org).  Quindi è possibile duplicare o [scaricare](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) un'app di base:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

App struttura include tutto il codice standard per un'app AngularJS semplice, ma non contiene tutte le parti alle identità.  Se non si desidera seguire su carta, è possibile duplicare o il [download](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) di esempio completo.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Registrare un'app

Prima di tutto, creare un'app nel [Portale di registrazione App](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)oppure seguire questa [procedura dettagliata](active-directory-v2-app-registration.md).  Assicurarsi di:

- Aggiungere la piattaforma **Web** per l'app.
- Immettere il corretto **Reindirizzare URI**. In questo esempio il valore predefinito è `http://localhost:8080`.
- Lasciare la casella di controllo **Consenti flusso impliciti** abilitato. 

Copia verso il basso l' **ID dell'applicazione** che sono state assegnate a un'applicazione, sarà necessario immetterla subito. 

## <a name="install-adaljs"></a>Installare adal.js
Per iniziare, passare a project scaricato e installato adal.js.  Se si dispone [bower](http://bower.io/) installato, è possibile eseguire solo questo comando.  Per le differenze tra le versioni delle dipendenze, scegliere solo la versione successiva.
```
bower install adal-angular#experimental
```

In alternativa, è possibile scaricare manualmente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) e [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Aggiungere che entrambi i file di `app/lib/adal-angular-experimental/dist` directory.

A questo punto aprire il progetto in un editor di testo e caricare adal.js alla fine del corpo della pagina:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Configurare l'API REST

Mentre si stiamo varie configurazioni, consente di ottenere l'uso di API REST back-end.  Prompt dei comandi, installare tutti i pacchetti necessari eseguendo (assicurarsi che sia attiva nella directory di primo livello del progetto):

```
npm install
```

A questo punto aprire `config.js` e sostituire il `audience` valore:

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
     
     ...
}
```

API REST utilizzerà questo valore per convalidare i token che riceve dall'app angolare su richieste AJAX.  Nota che questo semplice API REST archivia dati in memoria, pertanto ogni volta per interrompere il server, verranno perse tutte le attività create in precedenza.

È sempre questa impostazione verranno dedicato che illustrano come funziona l'API REST.  È possibile scoprirne il codice, ma se si desidera ulteriori informazioni sulla protezione web API con Azure Active Directory, vedere [questo articolo](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>Utenti di accesso
Ora scrivere codice identità.  È già notato che adal.js contiene un provider di AngularJS, viene riprodotto correttamente con meccanismi di routing angolare.  Iniziare aggiungendo il modulo adal all'app:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

È ora possibile inizializzare il `adalProvider` con l'ID dell'applicazione:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Ideale, ora adal.js contiene tutte le informazioni che necessarie per proteggere le app ed effettuare l'accesso agli utenti.  Per forzare l'accesso per una determinata route nell'app, tutto ciò che serve è una riga di codice:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

A questo punto quando un utente sceglie il `TodoList` collegamento adal.js verrà automaticamente reindirizzati alla Azure Active Directory per l'accesso se necessario.  È possibile inviare le richieste di accesso e disconnessione anche in modo esplicito chiamando adal.js nei controller di:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>Visualizzare le informazioni utente
Ora che l'utente ha effettuato l'accesso, è probabile che sia necessario per accedere ai dati di autenticazione dell'utente connesso nell'applicazione.  Adal.js espone tali informazioni nella `userInfo` oggetto.  Per accedere a questo oggetto in una visualizzazione, aggiungere adal.js all'ambito radice del controller di corrispondente:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Quindi è possibile risolvere direttamente il `userInfo` oggetto nella visualizzazione: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

È inoltre possibile utilizzare il `userInfo` oggetto per determinare se l'utente ha effettuato l'accesso o meno.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Chiamare API REST
Infine, è possibile ottenere token e chiamare API REST per creare, leggere, aggiornare ed eliminare attività.  Anche a proposito  Non è necessario effettuare *un'operazione*.  Adal.js automaticamente prendersi cura della Guida, la memorizzazione nella cache e aggiornamento dei token.  Inoltre prendersi cura di collegamento di tali token alle richieste AJAX in uscita inviati all'API REST.  

Questo testo esattamente come funziona? È tutto ciò grazie a vantaggi della [AngularJS intercettori](https://docs.angularjs.org/api/ng/service/$http), che consente di adal.js da trasformare in ingresso e in uscita messaggi http.  Inoltre, adal.js si presuppone che tutte le richieste di inviano allo stesso dominio come la finestra deve utilizzare i token destinati lo stesso ID applicazione app AngularJS.  Questo è il motivo per cui è stato usato lo stesso ID dell'applicazione in angolare app e le API REST NodeJS.  Naturalmente, è possibile ignorare questo comportamento e indicare adal.js per ottenere i token per altre API REST, se necessario, ma è per questo semplice scenario le impostazioni predefinite.

Ecco un frammento che mostra quanto sia semplice per inviare richieste con i token del titolare da Azure Active Directory:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Congratulazioni!  L'app integrate singola pagina di Azure Active Directory è stata completata.  Procedere, eseguire fiocco.  Possibile autenticare gli utenti, in modo sicuro chiamare l'API REST con OpenID connessione back-end e recupera informazioni di base relative all'utente.  All'esterno della casella, supporta tutti gli utenti con un Account Microsoft personale oppure un account aziendale o dell'istituto di istruzione da Azure Active Directory.  Provare l'app eseguendo:

```
node server.js
```

In un browser passare al `http://localhost:8080`.  Accedere con un account Microsoft personale oppure un account di lavoro o dell'istituto di istruzione.  Aggiungere attività all'elenco di attività dell'utente e Disconnetti.  Provare ad accedere con l'altro tipo di account. Se è necessario un tenant di Azure Active Directory per creare gli utenti di lavoro o dell'istituto di istruzione, [informazioni su come ottenere uno qui](active-directory-howto-tenant.md) (disponibile gratuitamente).

Per ulteriori informazioni su come l'endpoint v 2.0, tornare alla [Guida per gli sviluppatori v 2.0](active-directory-appmodel-v2-overview.md).  Per ulteriori risorse, vedere:

- [Esempi di Azure su GitHub >>](https://github.com/Azure-Samples)
- [Azure Active Directory in caso di riversamento Stack >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Azure documentazione di Active Directory in [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Ottenere gli aggiornamenti di sicurezza per i prodotti

È consigliabile ottenere notifiche di quando si presentano problemi di protezione, visitare [questa pagina](https://technet.microsoft.com/security/dd252948) e la sottoscrizione per gli avvisi di avviso di sicurezza.
