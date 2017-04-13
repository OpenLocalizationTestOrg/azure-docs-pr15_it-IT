<properties
   pageTitle="Raccolte di autenticazione di Azure Active Directory v 2.0 | Microsoft Azure"
   description="Client compatibile raccolte e raccolte di middleware server e raccolta correlata, origine e collegamenti a esempi, per l'endpoint v 2.0 Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Raccolte di autenticazione di Azure Active Directory v 2.0
L'endpoint di Azure Active Directory (Azure Active Directory) v 2.0 supporta i protocolli di OAuth 2.0 e OpenID Connect 1.0 standard di settore. È possibile utilizzare raccolte diverse da Microsoft e altre organizzazioni con l'endpoint v 2.0.

Quando si creazione un'applicazione che utilizza l'endpoint v 2.0, è consigliabile utilizzare raccolte in cui vengono scritte da protocollo dominio esperti seguono una metodologia di sviluppo del ciclo di vita SDL (Security), ad esempio [quella seguita da Microsoft][Microsoft-SDL]. Se si decide di supporto manualmente il codice per i protocolli, è consigliabile eseguire metodologia SDL e prestare particolare attenzione per le considerazioni sulla sicurezza in specifiche standard per ogni protocollo.

## <a name="types-of-libraries"></a>Tipi di raccolte

Azure Active Directory 2.0 funziona con due tipi di raccolte:

- **Raccolte di client**. Native client e server utilizzare librerie di client per ottenere token di accesso per la chiamata a una risorsa, ad esempio Microsoft Graph.
- **Raccolte di middleware server**. App Web di utilizzare raccolte middleware server per l'accesso utente. API Web utilizzano raccolte middleware server per convalidare i token che vengono inviati dai client nativo o da altri server.

## <a name="library-support"></a>Supporto per una raccolta
Poiché è possibile scegliere qualsiasi raccolta conformi agli standard quando si utilizza l'endpoint v 2.0, è importante sapere dove reperire il supporto. Per problemi e le richieste di funzionalità nel codice della libreria, contattare il proprietario di una raccolta. Per problemi e le richieste di funzionalità di implementazione protocollo lato servizio, contattare Microsoft.

Raccolte sono disponibili in due categorie di supporto:

- **Supportata da Microsoft**. Microsoft offre correzioni per le raccolte e ha eseguito SDL diligente in queste raccolte.
- **Compatibili**. Microsoft ha verificato queste raccolte in scenari di base e confermato che funzionano con l'endpoint v 2.0. Microsoft non fornisce risoluzioni per le raccolte e non è eseguita una revisione di queste raccolte. Problemi e le richieste di funzionalità devono essere indirizzate al progetto Apri origine della raccolta.

Per un elenco di raccolte che funzionano con l'endpoint v 2.0, vedere le sezioni avanti in questo articolo.

## <a name="microsoft-supported-client-libraries"></a>Raccolte supportate Microsoft client
| Piattaforma| Nome di una raccolta| Download | Codice sorgente | Esempio |
| :-: | :-: | :-: | :-: | :-: |
| Xamarin .NET, Windows Store | Raccolta di autenticazione Microsoft (MSAL) per .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL per .NET (GitHub)][ClientLib-NET-Repo] | [Esempio di client nativi desktop di Windows][ClientLib-NET-Sample] |
| Node | Plug-in Microsoft Azure Active Directory Passport.js | [Passport-Azure Active Directory (npm)][ClientLib-Node-Lib] | [Passport-Azure Active Directory (GitHub)][ClientLib-Node-Repo] | Prossimamente |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Raccolte di middleware supportati Microsoft server
| Piattaforma| Nome di una raccolta| Download | Codice sorgente | Esempio |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4. x | OWIN OpenID connettersi Middleware per ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Progetto Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Esempio di applicazione Web][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4. x | OWIN titolare OAuth Middleware per ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Progetto Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Esempio dell'API Web][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET core | OWIN OpenID connettersi Middleware per Core .NET | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Protezione ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Esempio di applicazione Web][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET core | OWIN titolare OAuth Middleware per Core .NET | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Protezione ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | Prossimamente |
| Node | Microsoft Azure Active Directory Passport.js plug-in | [Passport-Azure Active Directory (npm)][ServerLib-Node-Lib] | [Passport-Azure Active Directory (GitHub)][ServerLib-Node-Repo] | [Esempio di applicazione Web][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Raccolte di client compatibile
| Piattaforma| Nome di una raccolta | Versione testata | Codice sorgente | Esempio |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Esempio di applicazione nativa](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Esempio di applicazione nativa](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello](https://adodson.com/hello.js/) | 1.13.5 | [Hello](https://github.com/MrSwitch/hello.js) | Prossimamente |
| Pallone Python | [Pallone OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Pallone OAuthlib](https://github.com/lepture/flask-oauthlib) | Prossimamente |
| Trascrizione | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | Prossimamente |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Raccolte di middleware server compatibili
Prossimamente

## <a name="related-content"></a>Contenuti correlati
Per ulteriori informazioni sull'endpoint v 2.0 Azure Active Directory, vedere [Panoramica di Azure Active Directory app modello v 2.0][AAD-App-Model-V2-Overview].

Per facilitare perfezionare e modellare i contenuti, utilizzare la funzione di commenti Disqus alla fine di questo articolo per fornire un feedback.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
