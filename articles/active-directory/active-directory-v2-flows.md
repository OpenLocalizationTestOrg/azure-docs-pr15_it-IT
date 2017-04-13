<properties
    pageTitle="Tipi di endpoint v 2.0 | Microsoft Azure"
    description="I tipi di applicazioni e gli scenari supportati per l'endpoint v 2.0 Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="types-of-apps-for-the-v20-endpoint"></a>Tipi di App per l'endpoint v 2.0
Endpoint v 2.0 supporta l'autenticazione per un'ampia varietà di architetture di app moderna, ognuno dei quali si basano i protocolli standard [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) e/o [OpenID connettersi](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  In questo documento descrive brevemente i tipi di App che è possibile creare, indipendentemente dalla lingua o piattaforma si preferisce.  Verrà illustrati gli scenari di alto livelli prima di [passare direttamente all'interno del codice](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Nozioni fondamentali
Tutte le applicazioni che utilizza l'endpoint v 2.0 dovranno essere registrati in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Il processo di registrazione app verrà raccogliere e assegnare alcuni valori all'applicazione in uso:

- **Id applicazione** che identifica l'app
- **Reindirizzare URI** che può essere utilizzato per indirizzare le risposte tornare all'app
- Alcuni altri valori specifici di uno scenario.  Per informazioni dettagliate, informazioni su come [registrare un'app](active-directory-v2-app-registration.md).

Dopo aver registrato, l'app comunica con Azure Active Directory mediante l'invio di richieste per l'endpoint v 2.0 Azure Active Directory.  Sono disponibili Framework Apri origine e raccolte che coprano i dettagli di tali richieste oppure è possibile implementare la logica di autenticazione creando le richieste di questi endpoint:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Web App
Per le applicazioni web (.NET, PHP, Java, trascrizione, Python, nodo, e così via) accessibili tramite un browser, è possibile eseguire utente procedi [OpenID connettersi](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  In OpenID connettere l'applicazione web riceve un `id_token`, un token di sicurezza che verifica identità dell'utente e vengono fornite informazioni utente in forma di attestazioni:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app nel [riferimento token v 2.0](active-directory-v2-tokens.md).

In App server web, il flusso di autenticazione di accesso esegue le operazioni di alto livello:

![Immagine di corsie Web App](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Convalida delle id_token usando una chiave pubblica firma ricevuta dall'endpoint v 2.0 è sufficiente per verificare l'identità dell'utente e impostare un cookie sessione che può essere utilizzato per identificare l'utente nella pagina successiva richieste.

Per visualizzare questo scenario in azione, provare a usare uno degli esempi di codice di accesso web app nella sezione [Guida introduttiva](active-directory-appmodel-v2-overview.md#getting-started) .

Oltre ai semplice accesso aggiuntivo, un'app di server web anche potrebbe essere necessario accedere qualche altro servizio web, ad esempio un API REST.  In questo caso il server web app possono svolgere un flusso di connettersi OpenID & OAuth 2.0 combinato, tramite il [codice di autorizzazione 2.0 OAuth flusso](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Questo scenario è descritto sotto l' [argomento Guida introduttiva WebAPI Web App](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>API Web
È possibile utilizzare il punto finale v 2.0 per proteggere i servizi web, ad esempio API Web REST dell'applicazione.  Anziché i cookie di sessione e id_tokens, API Web usare access_tokens OAuth 2.0 per proteggere i dati e le richieste in arrivo di autenticazione.  Il chiamante di un'API Web aggiunge un Access nell'intestazione di autorizzazione di una richiesta HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

API Web di Access quindi è possibile utilizzare per verificare l'identità del chiamante API ed estrarre informazioni sul chiamante da crediti codificati nella tabella di Access.  Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app nel [riferimento token v 2.0](active-directory-v2-tokens.md).

Un'API Web può concedere utenti la possibilità di in/acconsentire-al rifiuto esplicito di alcune funzionalità o dati tramite l'esposizione autorizzazioni, note anche come [ambiti](active-directory-v2-scopes.md).  Per un'app chiamante acquisire l'autorizzazione a un ambito, l'utente deve consenso all'ambito durante un flusso.  Endpoint v 2.0 farà che richiede all'utente di autorizzazione e le autorizzazioni di registrazione in tutti i access_tokens che riceve l'API Web.  Tutto l'API Web deve preoccupare di convalida access_tokens riceve ogni chiamata ed esecuzione dei controlli l'autorizzazione appropriata.

Un'API Web possa ricevere access_tokens da tutti i tipi di App, inclusi App server web, desktop e App per dispositivi mobili, App singola pagina, daemon lato server e anche altre API Web.  Il flusso di alto livello per l'autenticazione dell'api web è il seguente:

![Immagine di corsie dell'API Web](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Per ulteriori informazioni sui authorization_codes, refresh_tokens e i passaggi dettagliati del access_tokens, consultare il [protocollo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Per informazioni su come proteggere un web api con access_tokens OAuth2, vedere gli esempi di codice api web nella [sezione Guida introduttiva](active-directory-appmodel-v2-overview.md#getting-started).


## <a name="mobile-and-native-apps"></a>Dispositivi mobili e App nativo
App che vengono installate in un dispositivo, ad esempio App per dispositivi mobili e desktop, è spesso necessario accedere a servizi back-end o API Web che archiviare i dati ed eseguire varie funzioni per conto di un utente.  Queste App è possono aggiungere accesso e l'autorizzazione a servizi back-end tramite il [codice di autorizzazione 2.0 OAuth flusso](active-directory-v2-protocols-oauth-code.md).  

In questo flusso un'app riceve un authorization_code da endpoint v 2.0 al momento dell'utente accesso, che rappresenta l'autorizzazione dell'app per chiamare i servizi di back-end per conto dell'utente attualmente effettuato l'accesso.  L'app può scambiare authoriztion_code sullo sfondo per un Access 2.0 OAuth e un refresh_token.  L'app consentono di Access per l'autenticazione alle API Web richieste HTTP e consentono di refresh_token di sfruttare access_tokens nuovo alla scadono di quelli meno recenti.

![Applicazione nativa corsie immagine](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>App singola pagina (javascript)
Molte applicazioni moderne dispone di una singola pagina App (SPA) front-end principalmente scritta in javascript e si usa spesso Framework, ad esempio AngularJS, Ember.js, Durandal e così via.  L'endpoint di Azure Active Directory v 2.0 supporta queste App utilizzando [OAuth 2.0 impliciti flusso](active-directory-v2-protocols-implicit.md).

In questo flusso l'app riceve i token dalle 2.0 autorizzare endpoint direttamente, senza eseguire qualsiasi scambi al server di back-end.  In questo modo tutti logica di autenticazione e gestione di sessione eseguire posizionare interamente nel client javascript senza eseguire reindirizzamenti pagina aggiuntiva.

![Immagine di corsie flusso implicito](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Per visualizzare questo scenario in azione, provare a usare uno degli esempi di codice app singola pagina nella sezione [Guida introduttiva](active-directory-appmodel-v2-overview.md#getting-started) .

### <a name="daemonsserver-side-apps"></a>App lato daemon/server
App che contengono i processi di esecuzione prolungata o che funzionare senza la presenza di un utente necessario anche un modo per accedere alle risorse protette, ad esempio API Web.  Queste applicazioni di eseguire l'autenticazione e ottenere token utilizzando identità dell'applicazione, anziché delegata identità di un utente, utilizzando il client 2.0 OAuth flusso credenziali.

In questo flusso l'app Ottiene i token tramite l'interazione diretta con la `/token` endpoint:

![Daemon App corsie immagine](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Per creare un'app daemon, consultare la documeenation credenziali client nella sezione [Guida introduttiva](active-directory-appmodel-v2-overview.md#getting-started) oppure per [questa app esempio .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="current-limitations"></a>Limitazioni corrente
Questi tipi di App non è attualmente supportati da endpoint v 2.0, ma sono della mappa.  Limitazioni aggiuntive e limitazioni per l'endpoint v 2.0 sono descritti nell' [articolo limitazioni v 2.0](active-directory-v2-limitations.md).

### <a name="chained-web-apis-on-behalf-of"></a>Concatenate web API (per conto dell'utente)
Molte architetture includano un'API Web che è necessario chiamare un'altra API Web downstream sia protetto da endpoint v 2.0.  Questo scenario è comune nei client nativi che hanno un back-end API Web, che a sua volta chiama un servizio Online Microsoft, ad esempio Office 365 o l'API di grafico.

Questo scenario API Web concatenato può essere supportato utilizzando la concessione delle credenziali di titolare OAuth 2.0 Jwt, nota anche come [On-Behalf-Of flusso](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  Tuttavia, via per conto di flusso non è attualmente implementato nell'endpoint v 2.0.  Per vedere come funziona il questo flusso in Azure AD generalmente disponibile service, vedere l' [esempio di codice per conto di GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).
