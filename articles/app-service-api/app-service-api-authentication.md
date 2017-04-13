<properties
    pageTitle="Autenticazione e l'autorizzazione per le applicazioni di API in Azure App servizio | Microsoft Azure"
    description="Informazioni sui servizi di autenticazione e autorizzazione forniti Azure App servizio per l'API app."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="rachelap"/>

# <a name="authentication-and-authorization-for-api-apps-in-azure-app-service"></a>Autenticazione e l'autorizzazione per le applicazioni di API nel servizio App Azure

## <a name="overview"></a>Panoramica 

> [AZURE.NOTE] In questo argomento verrà migrato a un consolidati [App dei servizi di autenticazione / autorizzazione](../app-service/app-service-authentication-overview.md) argomento che copre Web, mobili e le API app.

Servizio di App Azure offre servizi di autenticazione e l'autorizzazione incorporati che implementano [OAuth 2.0](#oauth) e [OpenID connettersi](#oauth). Questo articolo descrive le opzioni disponibili per le applicazioni di API del servizio di Azure App e servizi.

Il diagramma seguente illustra alcune caratteristiche chiave di autenticazione del servizio di App:

* Pre-elabora le richieste in arrivo API, ovvero che funziona con qualsiasi linguaggio o framework supportato dal servizio di App.
* Fornisce diverse opzioni per il lavoro quantità autenticazione si desidera eseguire nel codice.
* Funziona per utente finale e autenticazione dell'account del servizio. 
* Supporta cinque provider di identità: Azure Active Directory, Facebook, Google, Twitter e Account Microsoft.
* Lo stesso vale per App API Web App e App Mobile.

![](./media/app-service-api-authentication/api-apps-overview.png)

## <a name="language-agnostic"></a>Indipendente dalla lingua

Elaborazione dell'autenticazione servizio App viene eseguita prima che le richieste di raggiungere l'app API, che indica che le caratteristiche di autenticazione sono per App API scritto in qualsiasi lingua o framework.  L'API possono essere basate sulle ASP.NET, Java, Node o qualsiasi framework che supporta il servizio di App.

Servizio di App passa il token web JSON (JWT) nell'intestazione di autorizzazione di una richiesta HTTP e il codice scritto in qualsiasi lingua o framework può ottenere le informazioni che necessarie dal token. Inoltre, servizio App offre facilitare l'accesso a dichiarazioni di uso più frequente impostando alcune intestazioni speciali, ad esempio le operazioni seguenti:

* X MS-CLIENT-NOME-PRINCIPALE
* X-MS-CLIENT-CAPITALE-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
In una API .NET, è possibile utilizzare il `Authorize` dell'attributo e per l'autorizzazione è possibile scrivere facilmente codice sulle attestazioni in base, in quanto tali informazioni viene popolate automaticamente in classi .NET.

## <a name="multiple-protection-options"></a>Diverse opzioni di protezione

Servizio App possono impedire anonime richieste di raggiungere l'app API, è possibile passare in tutte le richieste e convalidare i token delle richieste che li contengono o consente a tutte le richieste senza eseguire alcuna operazione su di essi:

1. Consenti solo le richieste autenticate per raggiungere l'API app.

    Se si riceve una richiesta anonima da un browser, servizio App verrà reindirizzati alla pagina di accesso per il provider di autenticazione (Azure Active Directory, Google, Twitter, e così via) che si è scelto. 

    Con questa opzione non è necessario scrivere codice autenticazione tutto nell'app e il codice di autorizzazione è stato semplificato perché attestazioni più importanti presenti nelle intestazioni HTTP.

2. Consentire a tutte le richieste di raggiungere l'app API, ma convalidare richieste autenticate e passare le informazioni di autenticazione nelle intestazioni HTTP.

    Questa opzione consente una maggiore flessibilità nella gestione delle richieste anonime, ma è necessario scrivere codice se si vuole impedire agli utenti anonimi di tramite l'API. Poiché le richieste più popolari vengono passate nelle intestazioni di richieste HTTP, il codice di autorizzazione è relativamente semplice.
    
3. Consentire a tutte le richieste raggiungere l'API, eseguire alcuna azione in informazioni di autenticazione nella richiesta.

    Questa opzione lascia le attività di autenticazione e autorizzazione interamente il codice dell'applicazione.

Nel [portale di Azure](https://portal.azure.com/), si seleziona l'opzione desiderata nella **autenticazione / autorizzazione** blade.

![](./media/app-service-api-authentication/authblade.png)

Per le opzioni 1 e 2, attivare **L'autenticazione del servizio di App**e nell'elenco **azione da eseguire quando richiesta non viene autenticato** scegliere **Login** o **Consenti richiesta (alcuna azione)**.  Se si sceglie di **eseguire l'accesso**, è necessario scegliere un provider di autenticazione e configurare il provider.

![](./media/app-service-api-authentication/actiontotake.png)

Per informazioni dettagliate su come configurare l'autenticazione, vedere [come configurare l'applicazione di servizio App per usare le credenziali Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). L'articolo si applica alle API App e App per dispositivi mobili che collegato ad altri articoli per gli altri provider di autenticazione.
 
## <a id="internal"></a>Autenticazione dell'account del servizio

Autenticazione del servizio di App funziona per gli scenari interni ad esempio per si chiama da un'applicazione di API per un'altra app API. In questo scenario si ottiene un token usando le credenziali per un account di servizio anziché le credenziali dell'utente finale. Un account del servizio è noto anche come un *servizio dell'entità* di Azure Active Directory e l'autenticazione tramite un account di questo tipo è noto anche come uno scenario di servizio. 

Per gli scenari di servizio per proteggere l'app API chiamata usando Azure Active Directory e fornire un token di autorizzazione dell'entità servizio AAD quando si chiama l'API app. Viene visualizzato un token fornendo il client client segreta dall'applicazione AAD e ID. Nessun codice speciale che solo Azure è necessaria, ad esempio usata per essere veri per la gestione di token Zumo servizi Mobile. Esempio di questo scenario mediante API ASP.NET App è coperto dal esercitazione [l'autenticazione dell'entità servizio per API App](app-service-api-dotnet-service-principal-auth.md).

Se si vuole gestire uno scenario di servizio servizio senza mediante l'autenticazione del servizio di App, è possibile utilizzare i certificati client o l'autenticazione di base. Per informazioni sui certificati client in Azure, vedere [How To Configure TLS reciproca Authentication per Web Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Per informazioni sull'autenticazione di base in ASP.NET, vedere [Filtri di autenticazione in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Autenticazione dell'account del servizio da un'app di logica di servizio App a un'app API è un caso particolare viene illustrato in [tramite l'API personalizzati ospitati in App servizio con logica App](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="mobile-client-authentication"></a>Autenticazione client mobili

Per informazioni su come gestire l'autenticazione dai client mobili, vedere la [documentazione sull'autenticazione in App per dispositivi mobili](../app-service-mobile/app-service-mobile-ios-get-started-users.md). Autenticazione del servizio di App funziona nello stesso modo per App per dispositivi mobili e le API app.
  
## <a name="more-information"></a>Ulteriori informazioni

Per ulteriori informazioni sull'autenticazione e autorizzazione in Azure App servizio, vedere le risorse seguenti:

* [Autenticazione App espansione / autorizzazione](/blog/announcing-app-service-authentication-authorization/)
* [Come configurare l'applicazione di servizio di App per utilizzare le credenziali Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (Include collegamenti per gli altri provider di autenticazione nella parte superiore della pagina). 

Per ulteriori informazioni su OAuth 2.0, OpenID connettersi e JSON Web token (JWT), vedere le risorse seguenti.

* [Guida introduttiva a OAuth 2.0] (http://shop.oreilly.com/product/0636920021810.do "Guida introduttiva a OAuth 2.0") 
* [Introduzione a OAuth2, OpenID connettersi e JSON Web token (JWT) - corso PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Creazione e la protezione di un API REST per più clienti in ASP.NET - corso PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Per ulteriori informazioni su Azure Active Directory, vedere le risorse seguenti.

* [Scenari di Azure Active Directory](http://aka.ms/aadscenarios)
* [Azure Active Directory dei Guida per gli sviluppatori](http://aka.ms/aaddev)
* [Esempi di Azure Active Directory](http://aka.ms/aadsamples)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stati spiegati in funzionalità di autenticazione e l'autorizzazione di servizio App che è possibile utilizzare per le applicazioni di API. Esercitazione successiva della serie introduttiva recupero viene illustrato come implementare [l'autenticazione utente nelle App servizio API App](app-service-api-dotnet-user-principal-auth.md).
