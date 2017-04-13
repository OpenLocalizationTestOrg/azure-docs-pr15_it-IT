<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="I tipi di applicazioni è possibile creare nel B2C di Azure Active Directory."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure B2C Directory attiva: Tipi di applicazioni

Azure Active Directory (Azure Active Directory) B2C supporta l'autenticazione per un'ampia varietà di architetture app moderno. Tutti gli elementi si basano i protocolli standard [OAuth 2.0](active-directory-b2c-reference-protocols.md) o [OpenID connettersi](active-directory-b2c-reference-protocols.md). In questo documento descrive brevemente i tipi di App che è possibile creare, indipendentemente dalla lingua o piattaforma si preferisce. Consente inoltre di comprendere gli scenari di alto livello per [avviare la creazione di applicazioni](active-directory-b2c-overview.md#getting-started).

## <a name="the-basics"></a>Nozioni fondamentali
Tutte le app che usa Azure Active Directory B2C devono essere registrati nella [directory B2C](active-directory-b2c-get-started.md) tramite il [Portale di Azure](https://portal.azure.com/). Il processo di registrazione app raccoglie e l'assegna alcuni valori all'applicazione in uso:

- Un **ID applicazione** che identifica l'app.
- **Reindirizzare URI** che può essere utilizzato per indirizzare le risposte tornare all'app.
- Tutti gli altri valori specifici di uno scenario. Per ulteriori informazioni, informazioni su come [registrare un'app](active-directory-b2c-app-registration.md).

Una volta registrata l'app, comunica con Azure Active Directory mediante l'invio di richieste per l'endpoint v 2.0 Azure Active Directory:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Ogni richiesta inviata a Azure Active Directory B2C specifica i **criteri**. Un criterio controlla il comportamento di Azure Active Directory. È anche possibile usare questi endpoint per creare un set di esperienze utente personalizzabile. Criteri comuni includono abbonamento, accesso e modifica profilo criteri. Se non si ha familiarità con i criteri, è consigliabile leggere su Azure Active Directory B2C [politica extensible](active-directory-b2c-reference-policies.md) prima di continuare.

L'interazione di tutte le app con un endpoint v 2.0 segue un modello di alto livello simile:

1. L'app indirizza l'utente all'endpoint v 2.0 per eseguire un [criterio](active-directory-b2c-reference-policies.md).
2. L'utente completa il criterio secondo la definizione di criteri.
4. L'app riceve un tipo di token di sicurezza da endpoint v 2.0.
5. L'app Usa il token di sicurezza per accedere a informazioni protette o una risorsa protetta.
6. Il server di risorse convalida i token di sicurezza per verificare che è possibile concedere l'accesso.
7. L'app consente di aggiornare periodicamente il token di sicurezza.

<!-- TODO: Need a page for libraries to link to -->
Questa procedura può essere diverso leggermente in base al tipo di app che si sta creando. Raccolte Apri origine consente di risolvere i dettagli dell'utente.

## <a name="web-apps"></a>Web App
Per le applicazioni web (inclusi .NET, PHP, Java, trascrizione, Python e Node) per cui sono ospitate in un server e accedere tramite un browser, Azure Active Directory B2C supporta [OpenID connettersi](active-directory-b2c-reference-protocols.md) per tutte le esperienze utente. Sono inclusi accesso, per l'abbonamento e gestione dei profili. Nell'implementazione di Azure Active Directory B2C di connettersi OpenID, un'applicazione web avvia tali esperienze utente eseguendo le richieste di autenticazione di Azure Active Directory. Il risultato della richiesta è un `id_token`. Questo token di sicurezza rappresenta l'identità dell'utente. Vengono inoltre fornite informazioni relative all'utente in forma di attestazioni:

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

Ulteriori informazioni sui tipi di token e attestazioni disponibili per un'app in [token B2C fare riferimento](active-directory-b2c-reference-tokens.md).

In un'app web ogni esecuzione di un [criterio](active-directory-b2c-reference-policies.md) accetta procedura seguente:

![Immagine di corsie Web App](./media/active-directory-b2c-apps/webapp.png)

Convalida della `id_token` utilizzando una chiave di firma pubblica ricevuto da Azure Active Directory è sufficiente per verificare l'identità dell'utente. Consente di impostare anche un cookie sessione che può essere utilizzato per identificare l'utente nella pagina successiva richieste.

Per visualizzare questo scenario in azione, vedere uno degli esempi di codice di accesso web app nella [che guida introduttiva di sezione](active-directory-b2c-overview.md#getting-started).

Oltre a facilitare l'accesso in semplice, un'app di server web anche potrebbe essere necessario accedere a un servizio web back-end. In questo caso, l'applicazione web può eseguire un differenti [flusso OpenID connettersi](active-directory-b2c-reference-oidc.md) e acquisire token mediante i codici di autorizzazione e aggiornare i token. Questo scenario è illustrato nella [sezione API Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>API Web
È possibile utilizzare Azure Active Directory B2C per proteggere i servizi web, ad esempio dell'API web REST dell'applicazione. Web API possono utilizzare OAuth 2.0 per proteggere i dati mediante l'autenticazione delle richieste HTTP utilizzando i token. Il chiamante di un web API aggiunge un token nell'intestazione di autorizzazione di una richiesta HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

API web quindi possibile utilizzare il token per verificare l'identità del chiamante API e come estrarre informazioni sul chiamante da attestazioni codificate nel token. Ulteriori informazioni sui tipi di token e attestazioni disponibili per un'app in [token di Azure Active Directory B2C fare riferimento](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure Active Directory B2C attualmente supporta solo web API che accedono ai propri clienti conosciuti. Ad esempio, l'app completa può includere un'app iOS, un'app di Android e dell'API web back-end. Questa architettura è completamente supportata. Consentire un client di partner, ad esempio un'altra app iOS accedere al web stesso che API non è attualmente supportato. Tutti i componenti dell'app completa necessario condividere un ID singola applicazione.

API web possa ricevere i token da molti tipi di client, inclusi web apps, desktop e App per dispositivi mobili, App singola pagina, sul lato server daemon e altre web API. Ecco un esempio del flusso di completamento per un'applicazione web che chiama dell'API web:

![Immagine di corsie dell'API Web App Web](./media/active-directory-b2c-apps/webapi.png)

Per ulteriori informazioni sui codici di autorizzazione, i token di aggiornamento e la procedura per ottenere i token, consultare il [protocollo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Per informazioni su come proteggere un web API utilizzando B2C di Azure Active Directory, vedere web esercitazioni API la [Guida introduttiva di sezione](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Dispositivi mobili e App nativo
App che vengono installate nei dispositivi, ad esempio App per dispositivi mobili e desktop, è spesso necessario accedere a servizi back-end o web API per conto di utenti. È possibile aggiungere esperienze di gestione delle identità personalizzate per le app nativi e i servizi back-end di chiamata in modo sicuro con Azure Active Directory B2C e il [flusso di codice autorizzazione OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

In questo flusso l'app esegue [criteri](active-directory-b2c-reference-policies.md) e riceve un `authorization_code` da Azure Active Directory quando l'utente ha completato il criterio. Il `authorization_code` rappresenta l'autorizzazione dell'app per chiamare servizi back-end per conto dell'utente attualmente connesso. L'app è possibile scambiare quindi la `authorization_code` in background per un `id_token` e `refresh_token`.  L'app è possibile utilizzare il `id_token` per eseguire l'autenticazione in un web back-end API richieste HTTP. Inoltre possibile utilizzare il `refresh_token` per ottenere un nuovo `id_token` scadenza uno meno recente.

> [AZURE.NOTE]
    Azure Active Directory B2C attualmente supporta solo i token utilizzati per accedere a un servizio web back-end dell'applicazione. Ad esempio, l'app completa può includere un'app iOS, un'app di Android e dell'API web back-end. Questa architettura è completamente supportata. Se si consente l'app iOS accedere a un web partner API tramite token di accesso OAuth 2.0 non è attualmente supportato. Tutti i componenti dell'app completa necessario condividere un ID singola applicazione.

![Immagine corsie App nativo](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitazioni corrente
Azure Active Directory B2C attualmente non supporta i tipi seguenti di App, ma che siano presenti le roadmapy. Limitazioni aggiuntive e limitazioni relative a Azure Active Directory B2C sono descritti nel [restrizioni e limitazioni](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>App singola pagina (JavaScript)
Molte applicazioni moderne sono pagine singole app front-end scritte principalmente in JavaScript. Utilizzano spesso una struttura, ad esempio AngularJS, Ember.js o Durandal. Generalmente disponibile il servizio di Azure Active Directory supporta queste App utilizzando il flusso implicito OAuth 2.0. Tuttavia, questo flusso non è ancora disponibile in Azure Active Directory B2C.

### <a name="daemonsserver-side-apps"></a>App daemon/lato server
App che contengono i processi di esecuzione prolungata o che funzionare senza la presenza di un utente necessario anche un modo per accedere alle risorse protette, ad esempio web API. Queste applicazioni possono eseguire l'autenticazione e di ottenere token utilizzando l'identità dell'applicazione (anziché un delegato identità utente) e usando il client OAuth 2.0 credenziali flusso.

Questo flusso non è attualmente supportato da Azure Active Directory B2C. Queste App per ottenere i token solo dopo che si è verificato un flusso di utente interattivo.

### <a name="web-api-chains-on-behalf-of-flow"></a>Dell'API Web concatena (per conto di flusso)
Molte architetture includono un web API che è necessario chiamare un altro web downstream API, nel punto in cui sia protetto da Azure Active Directory B2C. Questo scenario è comune nei client nativi che hanno un API Web back-end. Si chiama un servizio online Microsoft come l'API di Azure Active Directory grafico.

Questo scenario dell'API web concatenate può essere supportato utilizzando la concessione di credenziali del titolare OAuth 2.0 JWT, noto anche come il flusso per conto dell'utente.  Tuttavia, il flusso per conto dell'utente non è attualmente implementato in B2C di Active Directory Azure.
