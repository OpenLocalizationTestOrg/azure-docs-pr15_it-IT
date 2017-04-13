<properties
    pageTitle="Modifiche all'endpoint v 2.0 Azure AD | Microsoft Azure"
    description="Una descrizione delle modifiche apportate ai protocolli app modello v 2.0 anteprima pubblica."
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

# <a name="important-updates-to-the-v20-authentication-protocols"></a>Aggiornamenti importanti per i protocolli di autenticazione v 2.0
Sviluppatori attenzione! Nelle due settimane, si terrà alcuni aggiornamenti per i protocolli di autenticazione v 2.0 che potrebbe ultime modifiche per qualsiasi App che è stato scritto durante il periodo di anteprima.  

## <a name="who-does-this-affect"></a>Chi influisce?
Nessuna App scritta per usare la versione 2.0 orientato endpoint di autenticazione

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Sono disponibili ulteriori informazioni sull'endpoint v 2.0 [qui](active-directory-appmodel-v2-overview.md).

Se si dispone di creata un'app utilizzando l'endpoint v 2.0 scrivendo direttamente al protocollo v 2.0, utilizzare uno del nostro middlewares web OpenID connettersi o OAuth o utilizza altre 3 ° librerie di terze parti per eseguire l'autenticazione, è necessario essere pronti per testare i progetti e apportare le modifiche necessarie.

## <a name="who-doesnt-this-affect"></a>Chi non ciò influisce?
Qualsiasi App che sono stati scritti con l'endpoint di autenticazione produzione Azure Active Directory

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Questo protocollo è già soddisfatta e non siano verificati tutte le modifiche.

Inoltre, se si usa l'app **solo** la raccolta di ADAL per eseguire l'autenticazione, non è necessario apportare alcuna modifica.  ADAL ha magnetica l'app dalle modifiche.  

## <a name="what-are-the-changes"></a>Quali sono le modifiche?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Per rimuovere il valore di x5t dalle intestazioni JWT
Endpoint v 2.0 utilizza i token JWT ampiamente, che contengono una sezione di intestazione parametri ai relativi metadati sul token.  Se si decodifica l'intestazione di uno del nostro JWTs corrente, si otterrebbe simile:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

In entrambe le "x5t" e "scuola" identificano la chiave pubblica che deve essere utilizzata per convalidare la firma del token come recuperati l'endpoint metadati OpenID connettersi.

La modifica che stiamo qui consiste nel rimuovere la proprietà di "x5t".  È possibile continuare a utilizzare gli stessi meccanismi per convalidare i token, ma basarsi solo sulla proprietà "scuola" per recuperare la chiave pubblica corretta, come specificato nel protocollo OpenID connettersi. 

> [AZURE.IMPORTANT] **Il processo: verificare che l'app non dipende l'esistenza di valore x5t.**

### <a name="removing-profileinfo"></a>Rimozione di profile_info
In precedenza, l'endpoint v 2.0 è stato restituzione di un base 64 codificato JSON oggetto nelle risposte token chiamate `profile_info`.  Quando si richiede un token di accesso da endpoint v 2.0 mediante l'invio di una richiesta di:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Risposta dell'aspetto che assumerebbe l'oggetto JSON seguente:
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Il `profile_info` informazioni valore contenuto sull'utente che ha eseguito l'accesso a app: il nome visualizzato, nome, cognome, indirizzo di posta elettronica, identificatore e così via.  In primo luogo il `profile_info` è stato utilizzato per la memorizzazione nella cache token e visualizzare gli scopi.

È ora stiamo rimosso il `profile_info` valore, ma non occorre ancora state rese disponibili queste informazioni per gli sviluppatori in una posizione leggermente diverso.  Invece di `profile_info`, l'endpoint v 2.0 restituirà un `id_token` in ogni risposta token:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

È possibile decodificare e analizzare id_token per recuperare le stesse informazioni che sono state ricevute da profile_info.  Il id_token è un JSON Web Token (JWT), con il contenuto come specificato da OpenID connettersi.  Specificare il codice per eseguire in modo molto simile, è sufficiente per estrarre il segmento centrale (corpo) del id_token e base 64 decodificare per accedere all'oggetto JSON all'interno.

Nelle due settimane, è consigliabile codificare l'app per recuperare le informazioni sull'utente da uno il `id_token` o `profile_info`; Se è presente.  In questo modo quando viene effettuata la modifica, l'app diretta possa gestire la transizione da `profile_info` a `id_token` senza interruzioni.

> [AZURE.IMPORTANT] **Il processo: verificare che l'app non dipende l'esistenza del `profile_info` valore.**

### <a name="removing-idtokenexpiresin"></a>Rimozione di id_token_expires_in
Simile a `profile_info`, viene inoltre pertanto rimosso il `id_token_expires_in` parametro dalle risposte.  In precedenza, l'endpoint v 2.0 restituisce un valore per `id_token_expires_in` insieme a ogni risposta id_token, ad esempio in una risposta di autorizzazione:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

O in una risposta token:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Il `id_token_expires_in` valore indica il numero di secondi rimarrà valido per l'id_token.  A questo punto, viene pertanto rimosso il `id_token_expires_in` valore completamente.  Se, tuttavia, è possibile utilizzare lo standard OpenID connettersi `nbf` e `exp` dichiara di esaminare la validità di un id_token.  Vedere il [riferimento token v 2.0](active-directory-v2-tokens.md) per ulteriori informazioni su queste affermazioni.

> [AZURE.IMPORTANT] **Il processo: verificare che l'app non dipende l'esistenza del `id_token_expires_in` valore.**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>Modifica delle attestazioni restituite in base all'ambito = openid
Questa modifica sarà più significativo: infatti, essa viene influenzata dalle quasi tutte le app che usa l'endpoint v 2.0.  Molte applicazioni inviano richieste per l'endpoint v 2.0 utilizzando il `openid` definire l'ambito, ad esempio:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Oggi, quando l'utente concede l'autorizzazione per il `openid` ambito, l'app riceve un'ampia varietà di informazioni relative all'utente di id_token risultante.  Queste domande possono includere il nome della persona, username preferito, indirizzo di posta elettronica, ID oggetto e altre.

In questo aggiornamento si intende modificare le informazioni che il `openid` ambito mette a disposizione le app accedere, comform più efficaci con le specifiche OpenID connettersi.  Il `openid` ambito verrà solo consentire l'app accedere all'utente in e visualizzato un identificatore app specifico per l'utente nel `sub` richiedere del id_token.  Sulle attestazioni in un id_token con solo il `openid` ambito abbia concesso o meno sarà privo di informazioni personali.  Esempio id_token attestazioni sono:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Se si desidera ottenere personali informazioni personali sull'utente nell'app, l'app sarà necessario richiedere autorizzazioni aggiuntive da parte dell'utente.  È in corso introduce il supporto per due nuovi ambiti da specifiche OpenID connettersi: il `email` e `profile` ambiti – che consentono di eseguire questa operazione.

- Il `email` ambito è molto semplice: consente l'app di accedere all'indirizzo di posta elettronica principale dell'utente tramite la `email` richiedere la id_token.  Si noti che la `email` Richiedi non sempre vengono visualizzate in id_tokens-solo sarà incluso se disponibili nel profilo dell'utente.
- Il `profile` ID ambito mette a disposizione le app di accedere a tutte le altre informazioni di base relative all'utente: il nome, nomeutente preferito, oggetto e così via.

Questa sezione consente di codice l'app in modo minimo divulgazione: è possibile chiedere all'utente di solo il set di informazioni che richiede l'app per svolgere il proprio lavoro.  Se si vuole continuare a ottenere l'insieme completo di informazioni utente che sta ricevendo l'app, è necessario includere tutti gli tre ambiti nelle richieste di autorizzazione:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

L'app è possibile iniziare l'invio di `email` e `profile` ambiti immediatamente e l'endpoint v 2.0 verranno accettare questi due ambiti e iniziare la richiesta di autorizzazioni dagli utenti in base alle esigenze.  Tuttavia, la modifica di interpretazione della `openid` ambito non avrà effetto per alcune settimane.

> [AZURE.IMPORTANT] **Il processo: aggiungere il `profile` e `email` ambiti se l'app richiede informazioni relative all'utente.**  Si noti che ADAL includerà di entrambe le autorizzazioni in richieste per impostazione predefinita. 

### <a name="removing-the-issuer-trailing-slash"></a>Rimozione di autorità barra finale.
In precedenza, il valore di emittente visualizzato nei token da endpoint v 2.0 ha il modulo

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Guid nel punto in cui è stato tenantId del tenant di Azure Active Directory che ha rilasciato il token.  Con queste modifiche, il valore emittente diventa

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

in entrambi i token e nel documento di individuazione OpenID connettersi.

> [AZURE.IMPORTANT] **Il processo: verificare che l'app accetta il valore di emittente con e senza una barra rovesciata durante la convalida emittente.**

## <a name="why-change"></a>Perché cambiare?
La motivazione principale per introdurre queste modifiche deve essere conforme OpenID connettersi standard.  Per essere OpenID connettersi conforme, ci auguriamo che per ridurre al minimo le differenze tra l'integrazione con servizi di identità di Microsoft e con altri servizi di identità del settore.  Si desidera consentire agli sviluppatori di utilizzare le raccolte di autenticazione preferito Apri origine senza dover modificare le relative raccolte per gestire Microsoft differenze.

## <a name="what-can-you-do"></a>Cosa potete fare?
Fino alla data corrente, è possibile iniziare apportare tutte le modifiche descritte in precedenza.  È necessario immediatamente:

1.  **Rimuovere eventuali dipendenze nel `x5t` parametro di intestazione.**
2.  **Gestire la transizione da `profile_info` a `id_token` nelle risposte token.**
3.  **Rimuovere eventuali dipendenze nel `id_token_expires_in` parametro di risposta.**
3.  **Aggiungere il `profile` e `email` ambiti all'app se l'app richiede informazioni utente di base.**
4.  **Accettare valori emittente token con e senza una barra rovesciata.**

La [documentazione protocollo v 2.0](active-directory-v2-protocols.md) è già stato aggiornato per riflettere queste modifiche, in modo che è possibile utilizzare come riferimento per semplificare l'aggiornamento del codice.

Se hai altre domande nell'ambito delle modifiche apportate, fare clic raggiungere Contattaci su Twitter in @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Con quale frequenza si verificherà modifiche protocollo?
Abbiamo non prevede ulteriormente resistenza assume la forma dei protocolli di autenticazione.  Abbiamo intenzionalmente stiamo aggregazione queste modifiche in una versione in modo che non sia elaborata questo tipo di processo di aggiornamento nuovamente nel prossimo futuro.  Naturalmente, si continuerà a aggiungere caratteristiche al servizio di autenticazione convergente v 2.0 che è possibile sfruttare, ma le modifiche dovrebbero essere additivi e non interruzione il codice esistente.

Infine, si desidera si supponga di ringraziamento per valutando aspetti durante il periodo di anteprima.  Le informazioni dettagliate sui ed esperienze di nostro adottato sono state preziose finora e ci auguriamo che è necessario continuare a condividere le proprie opinioni e idee.

Buona codifica!

La divisione di identità di Microsoft
