<properties
    pageTitle="Azure Active Directory v 2.0 protocolli | Microsoft Azure"
    description="Guida alle protocolli supportati dall'endpoint v 2.0 Azure Active Directory."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20--openid-connect"></a>Connettere i protocolli - OAuth 2.0 & OpenID v 2.0

Endpoint v 2.0 è possibile usare Azure Active Directory per identità-come-a-service con protocolli standard, OpenID connettersi e OAuth 2.0.  Mentre il servizio è conformi agli standard, possono essere presenti differenze tra alcuna due implementazione di essi.  Le informazioni in questo caso sarà utile se si sceglie di scrivere codice inviando direttamente & gestisce le richieste HTTP o parti di usare un 3o aprire Raccolta origini, invece di eseguire una delle raccolte nostro Apri origine.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Nozioni fondamentali
In quasi tutti i flussi OAuth & OpenID connessione, sono disponibili quattro parti coinvolti nella versione di exchange:

![Ruoli OAuth 2.0](../media/active-directory-v2-flows/protocols_roles.png)

- Il **Server di autorizzazione** corrisponde all'endpoint v 2.0.  È responsabile per garantire l'identità dell'utente, la concessione e revoca dell'accesso alle risorse e i token di emissione.  È anche nota come provider di identità: gestisce in modo sicuro che fare con le informazioni dell'utente, l'accesso e le relazioni di trust tra parti di un flusso.
- Il **Proprietario della risorsa** è in genere l'utente finale.  Si tratta di terze parti che appartengono i dati e ha la possibilità di terze parti accedere a tali dati o delle risorse.
- Il **OAuth Client** è l'app, identificato dal relativo ID dell'applicazione.  È in genere la parte che l'utente finale interagisce con e richiede i token dal server di autorizzazione.  Il client l'autorizzazione per accedere alla risorsa dal proprietario della risorsa.
- Il **Server delle risorse** sia in cui si trovano la risorsa o dati.  Considera attendibile il Server di autorizzazione per eseguire l'autenticazione in modo sicuro e autorizzare il OAuth Client e utilizza titolare access_tokens per garantire che è possibile concedere l'accesso a una risorsa.


## <a name="app-registration"></a>Registrazione di App
Tutte le applicazioni che utilizza l'endpoint v 2.0 dovranno essere registrata in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) prima possibile interagire con OAuth o OpenID connettersi.  Il processo di registrazione app verrà raccogliere e assegnare alcuni valori all'applicazione in uso:

- **Id applicazione** che identifica l'app
- Un **URI reindirizzare** o un **Identificatore di pacchetto** che può essere utilizzato per indirizzare le risposte tornare all'app
- Alcuni altri valori specifici di uno scenario.

Per ulteriori informazioni, informazioni su come [registrare un'app](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Punti finali
Dopo aver registrato, l'app comunica con Azure Active Directory mediante l'invio di richieste per l'endpoint v 2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Nel punto in cui il `{tenant}` possibile eseguire una delle quattro diversi valori:

| Valore | Descrizione |
| ----------------------- | ------------------------------- |
| `common` | Consente agli utenti con account Microsoft personale e gli account aziendale o dell'istituto di istruzione da Azure Active Directory per accedere all'applicazione. |
| `organizations` | Consente solo agli utenti con un account aziendale o dell'istituto di istruzione da Azure Active Directory per accedere all'applicazione. |
| `consumers` | Consente solo agli utenti con un account Microsoft personale (MSA) per accedere all'applicazione. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`o`contoso.onmicrosoft.com` | Consente solo agli utenti con un account aziendale o dell'istituto di istruzione da un determinato tenant di Azure Active Directory per accedere all'applicazione.  Può essere utilizzato il nome di dominio descrittivo del tenant Azure Active Directory o identificatore guid del tenant.  |

Per ulteriori informazioni su come interagire con questi endpoint, scegliere un tipo specifico app riportata di seguito.

## <a name="tokens"></a>Token
L'implementazione v 2.0 di OAuth 2.0 e connettere OpenID verificare ampio uso di token del titolare, inclusi i token del titolare rappresentati come JWTs. Un token del titolare è un token di sicurezza leggera che concede l'accesso "titolare" a una risorsa protetta. In questo caso, "titolare" è tutte le parti che possono presentare il token. Se una festa necessario eseguire l'autenticazione con Azure Active Directory per ricevere il token del titolare, se non sono prese i passaggi necessari per proteggere il token in trasmissione e lo spazio di archiviazione, può essere intercettare e utilizzato da una parte indesiderata. Alcuni token di sicurezza dispone di un meccanismo incorporato per impedire che averli autorizzati, i token del titolare non dispone di questo meccanismo mentre devono avvenire in un canale sicuro, ad esempio protezione a livello di trasporto (HTTPS). Se un token del titolare viene trasmesso in chiaro, attacco di un uomo aggiuntivo può essere usato da una festa dannosa per acquisire il token e usarlo per un accesso non autorizzato a una risorsa protetta. Possono essere applicati sicurezza anche quando l'archiviazione o la memorizzazione nella cache token del titolare per successivi utilizzi. Sempre assicurarsi che l'app trasmette e archivia i token del titolare in modo sicuro. Per altre considerazioni sulla protezione sui token del titolare, vedere [RFC 6750 sezione 5](http://tools.ietf.org/html/rfc6750).

Ulteriori dettagli dei diversi tipi di token utilizzato nell'endpoint v 2.0 è disponibile nel [riferimento token endpoint v 2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protocolli

Se si è pronti per vedere alcune richieste di esempio, Guida introduttiva a uno del sotto esercitazioni.  Ognuno di essi corrisponde a uno scenario di autenticazione specifico.  Se è necessaria assistenza determinare il flusso di destro dell'utente, vedere [tipi di App che è possibile creare con la versione 2.0](active-directory-v2-flows.md).

- [Creare Mobile e applicazione nativa con OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Creare Web App con ID aperta la connessione](active-directory-v2-protocols-oidc.md)
- [Creazione di applicazioni singola pagina con il flusso impliciti OAuth 2.0](active-directory-v2-protocols-implicit.md)
- [Compilazione daemon o Server lato processi con le credenziali del Client OAuth 2.0 flusso](active-directory-v2-protocols-oauth-client-creds.md)
- Ottenere i token in una API Web con OAuth 2.0 per conto di flusso di (disponibile a breve)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
