<properties
   pageTitle="Le raccolte di autenticazione di Azure Active Directory | Microsoft Azure"
   description="La raccolta di autenticazione Azure Active Directory (ADAL) consente agli sviluppatori di applicazioni autenticare facilmente gli utenti nel cloud client o locale Active Directory (AD) e quindi ottenere token di accesso per la protezione delle chiamate API."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Raccolte di autenticazione di Azure Active Directory

L'autenticazione di Azure Active Directory raccolta (ADAL) consente agli sviluppatori di applicazioni client di autenticare facilmente gli utenti nel cloud o Active Directory (AD) locale e quindi ottenere token di accesso per la protezione delle chiamate API. ADAL sono disponibili numerose funzionalità che l'autenticazione di rendere più semplice per gli sviluppatori, ad esempio il supporto asincrono, una cache token configurabile che archivia i token di accesso e token di aggiornamento, aggiornamento automatico di token quando scade un token di accesso e un token di aggiornamento è disponibile e altro ancora. Se si gestisce la maggior parte della complessità, ADAL può meglio sviluppo sulla logica business nell'applicazione e facilmente assegnazione delle risorse senza essere esperti di sicurezza.

ADAL è disponibile in diverse piattaforme.

|Piattaforma|Nome pacchetto|Client/Server|Download|Codice sorgente|Documentazione ed esempi|
|---|---|---|---|---|---|
|Client .NET, Windows Store UWP Xamarin iOS e Android|Active Directory Authentication Library (ADAL) per .NET v3 |Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL per .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Documentazione](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET client, di Windows Store, Windows Phone 8.1 |Active Directory Authentication Library (ADAL) per .NET v2 |Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL per .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[Documentazione](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Active Directory Authentication Library (ADAL) per JavaScript|Client|[ADAL per JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL per JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Esempio: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X iOS|Active Directory Authentication Library (ADAL) per obiettivo C|Client|[ADAL per obiettivo-C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL per obiettivo-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Esempio: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Active Directory Authentication Library (ADAL) per Android|Client|[ADAL per Android (Repository centrale)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL per Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Esempio: [NativeClient Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node|Active Directory Authentication Library (ADAL) per Node|Client|[ADAL per node (npm)](https://www.npmjs.com/package/adal-node)|[ADAL per node (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Esempio: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node|Microsoft Azure Active Directory Passport autenticazione middleware per nodo|Client|[Azure Passport Directory attiva per node (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory per node (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Active Directory Authentication Library (ADAL) per Java|Client|[ADAL per Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL per Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Estensioni del protocollo identità per Microsoft .NET Framework 4.5|Server|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Azure Active Directory identità modello le estensioni per .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Gestore del Token JSON Web per Microsoft .net Framework 4.5|Server|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Azure Active Directory identità modello le estensioni per .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Middleware OWIN che consente a un'applicazione come utilizzare la tecnologia Microsoft per l'autenticazione.|Server|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Middleware OWIN che consente a un'applicazione di utilizzare OpenIDConnect per l'autenticazione.|Server|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Esempio: [DotNet di OpenIDConnecty Web App (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Middleware OWIN che consente a un'applicazione di utilizzare WS-Federation per l'autenticazione.|Server|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Esempio: [DotNet di WSFederation Web App (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>Scenari

Ecco tre scenari comuni in cui è possibile utilizzare ADAL per l'autenticazione.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Autenticazione degli utenti di un'applicazione Client a una risorsa remota

In questo scenario, uno sviluppatore dispone di un client, ad esempio un'applicazione WPF che richiede l'accesso a una risorsa remota protetta da Azure Active Directory, ad esempio dell'API web. Ha dispone di una sottoscrizione di Azure, si sa come richiamare l'API web downstream e si conosce tenant di Azure Active Directory che utilizza l'API web. Di conseguenza, è possibile utilizzare ADAL in modo da agevolare l'autenticazione con Azure Active Directory, la delega completamente l'esperienza di autenticazione per ADAL o gestendo in modo esplicito le credenziali utente per. Rende ADAL facile autenticazione dell'utente, ottenere un token di accesso e token di aggiornamento da Azure Active Directory e quindi utilizzare il token di accesso per rendere le richieste di API sul Web.

Per un esempio di codice in cui viene illustrato questo scenario mediante l'autenticazione di Azure Active Directory, vedere [Applicazione WPF Client nativa all'API Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Autenticazione di un'applicazione del Server per una risorsa remota

In questo scenario, uno sviluppatore è un'applicazione in esecuzione su un server che richiede l'accesso a una risorsa remota protetta da Azure Active Directory, ad esempio dell'API web. Ha dispone di una sottoscrizione di Azure, si sa come richiamare il servizio downstream e si conosce che tenant di Azure Active Directory l'API web utilizza. Di conseguenza, è possibile utilizzare ADAL per facilitare l'autenticazione con Azure Active Directory gestendo in modo esplicito le credenziali dell'applicazione. Rende ADAL consentono di recuperare un token da Azure Active Directory mediante credenziali client dell'applicazione e quindi utilizzare tale token per rendere le richieste di API sul Web. ADAL gestisce anche gestione della durata del token di accesso per la memorizzazione nella cache di essa e il rinnovo in base alle esigenze. Per un esempio di codice in cui viene illustrato questo scenario, vedere [Applicazione Console all'API Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Autenticazione di un'applicazione del Server per conto di un utente per accedere a una risorsa remota

In questo scenario, uno sviluppatore è un'applicazione in esecuzione su un server che richiede l'accesso a una risorsa remota protetta da Azure Active Directory, ad esempio dell'API web. La richiesta anche è necessaria per conto di un utente in Active Directory Azure. Ha dispone di una sottoscrizione di Azure, si sa come richiamare web downstream API e si conosce Azure AD tenant viene utilizzato il servizio. Una volta l'utente autenticato all'applicazione web, l'applicazione può ottenere un codice di autorizzazione per l'utente da Azure Active Directory. L'applicazione web quindi possibile utilizzare ADAL per ottenere un token di accesso e aggiornare i token per conto di un utente che usa le credenziali di autorizzazione client e codice associate all'applicazione da Azure Active Directory. Dopo l'applicazione web è in possesso del token di accesso, è possibile chiamare API web fino a quando il token di scadenza. Quando scade il token, l'applicazione web possa utilizzare ADAL per ottenere un nuovo token di accesso con l'aggiornamento token che è stato ricevuto in precedenza.


## <a name="see-also"></a>Vedere anche

[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Scenari di autenticazione per Azure Active directory](active-directory-authentication-scenarios.md)

[Esempi di codice Azure Active Directory](active-directory-code-samples.md)
