<properties
   pageTitle="Esempi di codice di Azure Active Directory | Microsoft Azure"
   description="Indice degli esempi di codice di Azure Active Directory, organizzati per scenario."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Esempi di codice di Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

È possibile utilizzare Microsoft Azure Active Directory (Azure Active Directory) per aggiungere l'autenticazione e l'autorizzazione per le applicazioni web e web API. In questa sezione sono disponibili collegamenti a esempi di codice che mostrano come farlo e frammenti di codice che è possibile utilizzare nelle applicazioni. Nella pagina di esempio codice, sono disponibili in modalità lettura-personali che gli altri argomenti della Guida con requisiti, installazione e configurazione preliminare. E il codice commenti per comprendere le sezioni critiche.

Per comprendere lo scenario di base per ogni tipo di esempio, vedere scenari di autenticazione per Azure Active Directory.

Collaborazione per il nostro esempi su GitHub: [esempi di Microsoft Azure Active Directory e la documentazione](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Browser all'applicazione Web

In questi esempi viene illustrato come scrivere un'applicazione web che indirizza il browser dell'utente per accere al Azure Active Directory.



| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C# / .NET | [DotNet di OpenIDConnect Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Consente di OpenID connettersi (ASP.Net OpenID connettersi OWIN middleware) autenticazione degli utenti da un tenant di Azure Active Directory.
| C# / .NET | [Web App-tenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Un multi-tenant applicazione web MVC .NET che utilizza OpenID connettersi (ASP.Net OpenID connettersi OWIN middleware) per eseguire l'autenticazione degli utenti da più tenant di Azure Active Directory.
| C# / .NET | [DotNet di WSFederation Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | Utilizzare WS-Federation (ASP.Net WS-Federation OWIN middleware) per eseguire l'autenticazione degli utenti da un tenant di Azure Active Directory.






## <a name="single-page-application-spa"></a>Applicazione della singola pagina (SPA)

In questo esempio viene illustrato come scrivere un'applicazione singola pagina protetta con Azure Active Directory.  

| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| JavaScript, c# / .NET | [SinglePageApp DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | Utilizzare ADAL per JavaScript e Azure Active Directory per proteggere un'app basata su AngularJS singola pagina implementata con un'estremità posteriore dell'API web ASP.NET.


## <a name="native-application-to-web-api"></a>Applicazione nativa per dell'API Web

Questi esempi di codice viene illustrato come creare applicazioni client nativi che chiamare web API che sono protetti da Azure Active Directory. Utilizzano [Una raccolta di autenticazione Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) e [2.0 OAuth in Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| JavaScript | [Cordova di MultiTarget NativeClient](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | Utilizzare il plug-in ADAL per Apache Cordova per creare un'app Apache Cordova chiama dell'API web che utilizza Azure Active Directory per l'autenticazione.
| C# / .NET | [NativeClient DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | Un'applicazione WPF .NET che chiama dell'API web è protetto con Azure Active Directory.
| C# / .NET | [NativeClient WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Un'applicazione di Windows Store che chiama dell'API web è protetto con Azure Active Directory.
| C# / .NET | [WindowsStore NativeClient-WebAPI-tenant](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | Applicazione di Windows Store la chiamata a un sito web multi-tenant API è protetto con Azure Active Directory.
| C# / .NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | Applicazione client nativo che chiama API, che ottiene un token di agire per conto dell'utente originale e quindi utilizza il token per chiamare un altro web API web.
| C# / .NET | [NativeClient WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Un'applicazione di Windows Store per Windows Phone 8.1 che chiama dell'API web è protetto da Azure Active Directory.
| ObjC | [NativeClient iOS](https://github.com/Azure-Samples/active-directory-ios) | Un'applicazione di iOS che chiama dell'API web richiede Azure Active Directory per l'autenticazione.
| C# / .NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | Applicazione client nativi che include una logica per elaborare un token JWT in un web API, invece di usare OWIN middleware.
| C# / Xamarin | [NativeClient Xamarin Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Un'associazione Xamarin per il nativo Azure Active Directory autenticazione raccolta (ADAL) per la raccolta Android.
| C# / Xamarin | [IOS di Xamarin NativeClient](https://github.com/Azure-Samples/active-directory-xamarin-ios) | Un'associazione Xamarin per il nativo Azure Active Directory autenticazione raccolta (ADAL) per iOS.
| C# / Xamarin | [DotNet di MultiTarget NativeClient](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Un progetto Xamarin che è destinato a cinque piattaforme e chiama dell'API web è protetto da Azure Active Directory.
| C# / .NET | [NativeClient Headless DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Un'applicazione nativa che esegue l'autenticazione non interattiva e chiama dell'API web è protetto da Azure Active Directory.



## <a name="web-application-to-web-api"></a>Applicazione Web dell'API Web

Questi esempi di codice mostra come usare [OAuth 2.0 in Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx) per creare applicazioni web che chiama web API che sono protetti da Azure Active Directory.

| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C# / .NET | [Web App-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | Chiamare un API web con autorizzazioni dell'utente connesso.
|  C# / .NET | [Web App-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | Chiamare un API web con le autorizzazioni dell'applicazione.
| C# / .NET | [Web App-WebAPI-OAuth2-identità utente-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | Aggiungere autorizzazione con [2.0 OAuth in Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx) a un'applicazione web esistente in modo che è possibile chiamare un API web.
| JavaScript | [WebAPI Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | Configurare un servizio di API REST integrata con Azure Active Directory per la protezione API. Include un server Node con un'API Web.
| C# / .NET | [Web App-WebAPI-tenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  Un multi-tenant MVC web applicazione che utilizza OpenID connettersi (ASP.Net OpenID connettersi OWIN middleware) per l'autenticazione degli utenti da un tenant di Azure Active Directory. Utilizza un codice di autorizzazione per richiamare l'API di grafico.

## <a name="server-or-daemon-application-to-web-api"></a>Server o Daemon applicazione Web API

Questi esempi di codice viene illustrato come creare un'applicazione daemon o server che ottiene risorse da un web API utilizzando [Una raccolta di autenticazione Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) e [2.0 OAuth in Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C# / .NET | [Daemon DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | Un'applicazione console chiama dell'API web. Le credenziali client sono una password.
| C# / .NET | [Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | Applicazione console che chiama dell'API web. Le credenziali client sono un certificato.


## <a name="calling-azure-ad-graph-api"></a>Chiamata API di Azure Active Directory grafico

In questi esempi di codice viene illustrato come compilare le applicazioni di chiamare l'API di Azure Active Directory grafico per leggere e scrivere dati directory.

| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| Java | [Linguaggio di GraphAPI Web App](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | Un'applicazione web che utilizza l'API di grafico per accedere ai dati di Azure Active directory.
| PHP | [PHP di GraphAPI Web App](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | Un'applicazione web che utilizza l'API di grafico per accedere ai dati di Azure Active directory.
| C# / .NET | [DotNet di GraphAPI Web App](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Un'applicazione web che utilizza l'API di grafico per accedere ai dati di Azure Active directory.
| C# / .NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | Questa applicazione console viene comuni chiamate di lettura e scrittura all'API di grafico e viene illustrato come eseguire l'assegnazione di licenze utente e aggiornare Anteprima foto e i collegamenti di un utente.
| C# / .NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Applicazione console che utilizza la query differenziale dell'API di grafico per ottenere periodiche modifiche agli oggetti utente in un tenant di Azure Active Directory.
| C# / .NET | [Web App-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | Un'applicazione MVC utilizza query grafico API per generare un organigramma semplice della società.
| PHP | [Web App-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | Applicazione PHP che chiama l'API di grafico per registrare un'estensione e quindi leggere, aggiornare ed eliminare i valori nell'attributo estensione.


## <a name="authorization"></a>Autorizzazione

Questi esempi di codice viene illustrato come utilizzare Azure Active Directory per l'autorizzazione.

| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C# / .NET | [DotNet di GroupClaims Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Eseguire controllo dell'accesso basato sui ruoli (RBAC) utilizzando attestazioni basate su gruppo di Azure Active Directory in un'applicazione che è integrata con Azure Active Directory.
| C# / .NET | [DotNet di RoleClaims Web App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Eseguire controllo dell'accesso basato sui ruoli (RBAC) utilizzando i ruoli di applicazione Azure Active Directory in un'applicazione che è integrata con Azure Active Directory.


## <a name="legacy-walkthroughs"></a>Procedure dettagliate legacy

Queste procedure dettagliate usare tecnologia leggermente precedente, ma potrebbero essere di interesse.

| Lingua/piattaforma | Esempio | Descrizione
| ----------------- | ------ | -----------
| C# / .NET | [Autorizzazione basato sui ruoli e basata su ACL in un'applicazione di Microsoft Azure Active Directory](http://go.microsoft.com/fwlink/?LinkId=331694) | È possibile eseguire autorizzazioni basato sui ruoli (RBAC) e basata su ACL in un'applicazione che è integrata con Azure Active Directory.
| C# / .NET |  [Autenticazione AAL - app di Windows Store per servizio REST:](http://go.microsoft.com/fwlink/?LinkId=330605) |  Utilizzare [Una raccolta di autenticazione Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) (in precedenza AAL) per la versione Beta di Windows Store per aggiungere funzionalità di autenticazione utente per un'app di Windows Store.
| C# / .NET | [Autenticazione di ADAL - App nativo al servizio REST - con AAD tramite la finestra del Browser](http://go.microsoft.com/fwlink/?LinkId=259814) |  Utilizzare [Una raccolta di autenticazione Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) per aggiungere funzionalità di autenticazione utente a un client WPF.
| C# / .NET | [Autenticazione di ADAL - App nativo al servizio REST - con ACS tramite la finestra del Browser](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  Utilizzare [Una raccolta di autenticazione Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) e [2.0 di servizio controllo di accesso (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) per aggiungere funzionalità di autenticazione utente a un client WPF.
| C# / .NET | [ADAL - autenticazione per Server](http://go.microsoft.com/fwlink/?LinkId=259816) | Utilizzare [Una raccolta di autenticazione Azure Active Directory (ADAL)](active-directory-authentication-libraries.md) per proteggere le chiamate dei servizi da un processo di lato server un servizio di MVC4 Web API REST.
| C# / .NET | [Aggiunta di Sign-On per l'applicazione Web che utilizza Azure Active Directory](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | Configurare un'applicazione di .NET per eseguire single sign-on web contro la directory aziendale di Azure Active Directory.
| C# / .NET | [Sviluppo di applicazioni Web multi-Tenant con Azure Active Directory](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | Utilizzare Azure Active Directory per aggiungere il single sign-on e le funzionalità di accesso directory di un'applicazione di .NET per lavorare tra più organizzazioni.
JAVA | [Applicazione di esempio Java per Azure Active Directory grafico API](http://go.microsoft.com/fwlink/?LinkId=263969) | Utilizzare l'API di grafico per accedere ai dati di directory da Azure Active Directory.
PHP | [Applicazione di esempio PHP per Azure Active Directory grafico API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Utilizzare l'API di grafico per accedere ai dati di directory da Azure Active Directory.
| C# / .NET | [Applicazione di esempio per Azure Active Directory grafico API](http://go.microsoft.com/fwlink/?LinkID=262648) | Utilizzare l'API di grafico per accedere ai dati di directory da Azure Active Directory.
| C# / .NET | [Applicazione di esempio per Query differenziale Azure Active Directory grafico](http://go.microsoft.com/fwlink/?LinkId=275398) | Utilizzare la query differenziale dell'API di grafico per ottenere periodiche modifiche agli oggetti utente in un tenant di Azure Active Directory.
| C# / .NET | [Applicazione di esempio per l'integrazione di domanda Cloud multi-Tenant di Azure Active Directory](http://go.microsoft.com/fwlink/?LinkId=275397) | Integrare un'applicazione multi-tenant Azure Active Directory.
| C# / .NET | [Protezione di un'applicazione di Windows Store e servizio Web REST utilizzando Azure Active Directory](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | Creare una risorsa dell'API web semplice e un'applicazione client di Windows Store con Azure Active Directory e [Azure Active Directory autenticazione raccolta (ADAL)](active-directory-authentication-libraries.md).
| C# / .NET| [Utilizzando il grafico API eseguire Query su Azure Active Directory](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Configurare un'applicazione di Microsoft .NET come utilizzare l'API di Azure Active Directory grafico per accedere ai dati di una directory tenant di Azure Active Directory.

## <a name="see-also"></a>Vedere anche

##### <a name="other-resources"></a>Altre risorse

[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Azure Active Directory grafico API concettuale e materiale di riferimento](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Libreria di supporto dell'API di Azure Active Directory grafico](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

