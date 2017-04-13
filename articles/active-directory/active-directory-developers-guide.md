<properties
   pageTitle="Azure Active Directory Guida per gli sviluppatori | Microsoft Azure"
   description="In questo articolo viene fornita una guida completa alle risorse di sviluppo di Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Azure Active Directory Guida per gli sviluppatori

## <a name="overview"></a>Panoramica
Come un di gestione delle identità piattaforma (IDMaaS), gli sviluppatori Azure Active Directory (AD) offre un modo efficace per integrare nelle applicazioni di gestione delle identità. Gli articoli seguenti forniscono informazioni generali sull'implementazione e le caratteristiche chiave di Azure Active Directory. È consigliabile leggere nell'ordine o passare a [Guida introduttiva](#getting-started) se si è pronti per eseguire un'analisi.


1. [Vantaggi dell'integrazione di Azure Active Directory](./develop/active-directory-how-to-integrate.md): scoprire perché l'integrazione con Azure Active Directory offre la soluzione migliore per l'accesso sicuro e autorizzazioni.

1. [Scenari di autenticazione di Azure Active Directory](active-directory-authentication-scenarios.md): sfruttare l'autenticazione semplificata di Azure Active Directory per fornire accesso all'applicazione.

1. [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md): informazioni su come aggiungere, aggiornare e rimuovere le applicazioni da Azure Active Directory e sulle linee guida di personalizzazione per App integrate.

1. [API di Azure Active Directory Graph](active-directory-graph-api.md): utilizzare l'API di Azure Active Directory grafico per accedere a livello di programmazione Azure Active Directory mediante i punti finali API REST. API di Azure Active Directory grafico è possibile accedere tramite [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph fornisce un'API unificata che consente l'accesso a più servizio cloud di Microsoft API, tramite un unico endpoint API REST e con un token di accesso singolo.

1. [Le raccolte di autenticazione di Azure Active Directory](active-directory-authentication-libraries.md): facilmente autenticazione degli utenti per ottenere i token di accesso tramite le raccolte di autenticazione di Azure Active Directory per .NET, JavaScript, C obiettivo, Android e altro.


## <a name="getting-started"></a>Guida introduttiva

Queste esercitazioni sono specifiche per le piattaforme più e consentono di iniziare rapidamente a sviluppare con Azure Active Directory. Come prerequisito, è necessario [ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guide di avvio rapido applicazione mobile e PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Universale di Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Universale di Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Integrazione diretta con OAuth 2.0](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Guide di avvio rapido applicazione Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![Connettere OpenID](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node](active-directory-devquickstarts-openidconnect-nodejs.md)|[Integrazione diretta con OpenID connettersi](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>Guide di avvio rapido dell'API Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>Guida alla Guida introduttiva di directory di query

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API di grafico](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>Procedure

Questi articoli viene descritto come eseguire attività specifiche utilizzando Azure Active Directory:

- [Ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md)
- [Accedere a qualsiasi utente di Azure Active Directory utilizzando il modello di applicazione multi-tenant](active-directory-devhowto-multi-tenant-overview.md)
- Abilitare l'accesso SSO cross-app tramite ADAL, [Android](active-directory-sso-android.md) e i dispositivi [iOS](active-directory-sso-ios.md)
- [Rendere l'applicazione AppSource Certified per Azure Active Directory](active-directory-devhowto-appsource-certified.md)
- [Elenco dell'applicazione nella raccolta di applicazione di Azure Active Directory](active-directory-app-gallery-listing.md)
- [Inviare web App per Office 365 al Dashboard di venditore](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Comprendere il manifesto dell'applicazione di Azure Active Directory](active-directory-application-manifest.md)
- [Comprendere le linee guida di personalizzazione per i pulsanti di acquisizione di accesso e app nell'applicazione client](active-directory-branding-guidelines.md)
- [Anteprima: Modalità di compilazione di applicazioni che eseguire l'accesso agli utenti con account sia personale e aziendale o dell'istituto di istruzione](active-directory-appmodel-v2-overview.md)
- [Anteprima: Modalità di compilazione di applicazioni che effettua l'iscrizione e accedere consumatori](../active-directory-b2c/active-directory-b2c-overview.md)
- [Anteprima: configurazione token durata in Azure Active Directory](active-directory-configurable-token-lifetimes.md) tramite PowerShell. Vedere [le operazioni di politica](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e l' [entità di criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) per informazioni dettagliate sulla configurazione tramite l'API di Azure Active Directory grafico.

## <a name="reference"></a>Guida di riferimento

In questi articoli sono un riferimento foundation per resto e raccolta autenticazione API, protocolli, gli errori, esempi di codice e i punti finali.  

###  <a name="support"></a>Supporto tecnico
- [Domande tag](http://stackoverflow.com/questions/tagged/azure-active-directory): soluzioni trovare Azure Active Directory su Overflow dello Stack, cercare i contrassegni [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).
- Vedere il [glossario per sviluppatori di Azure Active Directory](active-directory-dev-glossary.md) per le definizioni di alcuni dei termini comuni relativi all'integrazione e sviluppo di applicazioni.

### <a name="code"></a>Codice

- [Le raccolte Apri origine di Azure Active Directory](http://github.com/AzureAD): è il modo più semplice per trovare l'origine di una raccolta utilizzando l' [elenco o una raccolta](active-directory-authentication-libraries.md).

- [Esempi di Azure Active Directory](https://github.com/azure-samples?query=active-directory): è il modo più semplice per scorrere l'elenco degli esempi utilizzando l' [indice di esempi di codice](active-directory-code-samples.md).

- [Active Directory autenticazione raccolta (ADAL) per .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - documentazione di riferimento è disponibile per [l'ultima versione principale](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) e [versione principale precedente](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API di grafico

- [Riferimento all'API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): Guida di riferimento per Azure Active Directory grafico API REST. [Visualizza l'esperienza di riferimento interattiva API di grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Grafico API ambiti di autorizzazione](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): ambiti di autorizzazione OAuth 2.0 che consentono di controllare l'accesso ai dati di directory in un tenant di un'app.

### <a name="authentication-and-authorization-protocols"></a>Protocolli di autenticazione e autorizzazioni

- [Firma chiave di attivazione di Azure Active Directory](active-directory-signing-key-rollover.md): informazioni sulle Azure AD accedere digitazione attivazione chiave e su come aggiornare la chiave per i motivi più comuni dell'applicazione.

- [Protocollo OAuth 2.0: tramite la concessione di codice autorizzazione](active-directory-protocols-oauth-code.md): grant codice autorizzazione del protocollo OAuth 2.0, è possibile utilizzare per autorizzare l'accesso alle applicazioni Web e le API Web in Azure Active Directory del tenant.

- [Protocollo OAuth 2.0: comprendere la concessione implicita](active-directory-dev-understanding-oauth2-implicit-grant.md): ulteriori informazioni sulle concedere l'autorizzazione implicita e se è appropriata per l'applicazione.

- [Protocollo OAuth 2.0: servizio per servizio le chiamate utilizzando le credenziali di Client](active-directory-protocols-oauth-service-to-service.md): le credenziali del Client di 2.0 OAuth di concessione consente a un servizio web (riservate client) usare le proprie credenziali per l'autenticazione durante la chiamata a un altro servizio web, invece di rappresentazione di un utente. In questo scenario, il client è in genere a un servizio web intermedio, un servizio daemon o sito Web.

- [Protocollo OpenID Connect 1.0: accesso e l'autenticazione](active-directory-protocols-openid-connect-code.md): il OpenID Connect 1.0 protocollo estende OAuth 2.0 per l'utilizzo come protocollo di autenticazione. Un'applicazione client può ricevere un id_token per gestire il processo di accesso, o per migliorare il flusso di codice di autorizzazione per ricevere un id_token e l'autorizzazione codice.

- [Guida di riferimento protocollo SAML 2.0](active-directory-saml-protocol-reference.md): protocollo di SAML 2.0 consente alle applicazioni assicurare un'esperienza single sign-on agli utenti.

- [Protocollo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory supporta la federazione WS 1.2 per la specifica di Web Services Federation versione 1.2. Per ulteriori informazioni sul documento metadati federazione, vedere [Federazione metadati](active-directory-federation-metadata.md).

- [Tipi di token e Richiedi supportati](active-directory-token-and-claims.md): È possibile usare questa guida per comprendere e valutare delle attestazioni nei token SAML 2.0 e JSON Web token (JWT).

## <a name="videos"></a>Video

### <a name="build"></a>Generazione

Queste presentazioni Panoramica sullo sviluppo di App tramite gli altoparlanti caratteristica di Azure Active Directory che lavorano direttamente il team di progettazione. Le presentazioni coprono fondamentali, inclusi IDMaaS, autenticazione, la federazione delle identità e il single sign-on.

- [Microsoft Identity: Stato dell'unione e futuro](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: Gestione delle identità come servizio per le applicazioni moderne](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Sviluppo di applicazioni web moderno con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Sviluppo di applicazioni native moderne con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure venerdì
[Azure venerdì](https://azure.microsoft.com/documentation/videos/azure-friday/) è una ricorrente venerdì serie di video di 1:1 si impegna costantemente per garantire breve (10-15 minuti) intervista con esperti in una serie di argomenti Azure.  Utilizzare la funzionalità di filtro servizi nella pagina per visualizzare tutti i video di Azure Active Directory.

- [Identità Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identità Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identità Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Social networking

- [Blog del Team di Active Directory](http://blogs.technet.com/b/ad/): progressi nel mondo di Azure Active Directory.

- [Blog del Team di Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): Azure Active Directory informazioni specifiche relative all'API di grafico.

- [Identità cloud](http://www.cloudidentity.net): considerazioni sulla gestione delle identità come servizio, da un PM Directory attiva principale Azure.  

- [Azure Active Directory su Twitter](https://twitter.com/azuread): gli annunci di Azure Active Directory presenti 140 caratteri.

## <a name="windows-server-on-premises-development"></a>Sviluppo di Windows Server locale
Per indicazioni sull'uso di sviluppo di Windows Server e Active Directory Federation Services (ADFS), vedere:

- [Scenari di ADFS Active Directory per gli sviluppatori](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): viene fornita una panoramica dei componenti di ADFS e sul suo funzionamento, con informazioni dettagliate sugli scenari di autenticazione supportati/autorizzazione.
- [Procedure dettagliate ADFS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): un elenco di articoli dimostrativo, le istruzioni dettagliate sull'implementazione di flussi di autenticazione/autorizzazione correlati.
