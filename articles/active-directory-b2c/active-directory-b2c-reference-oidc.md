<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Creazione di applicazioni web tramite l'implementazione di Azure Active Directory del protocollo di autenticazione OpenID connettersi."
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
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure B2C Directory attiva: Web effettuare l'accesso con la connessione OpenID

Connettere OpenID è un protocollo di autenticazione basato su OAuth 2.0, che può essere utilizzato per eseguire l'accesso sicuro agli utenti nelle applicazioni web.  Tramite l'implementazione di Azure Active Directory (Azure Active Directory) B2C di OpenID Connect altro di gestione delle identità esperienze nelle applicazioni web per Azure Active Directory ed è possibile assegnare per l'abbonamento, accesso. Questa guida illustra come eseguire questa operazione in modo indipendente dalla lingua. Ad esempio in come inviare e ricevere messaggi HTTP senza utilizzare uno dei nostro raccolte Apri origine.

[Connettere OpenID](http://openid.net/specs/openid-connect-core-1_0.html) estende il protocollo di *autorizzazione* OAuth 2.0 per l'utilizzo come protocollo di *autenticazione* . In questo modo è possibile eseguire il single sign-on utilizzando OAuth. Introduce il concetto di un `id_token`, ovvero un token di sicurezza che consente al client verificare l'identità dell'utente e ottenere informazioni di base del profilo utente.

Dal momento che estende OAuth 2.0, consente inoltre App acquisire sicuro **access_tokens**. È possibile utilizzare access_tokens per accedere alle risorse che è protetti da un [server di autorizzazione](active-directory-b2c-reference-protocols.md#the-basics). È consigliabile connettersi OpenID se si crea un'applicazione web che è ospitata su un server e accedervi tramite un browser. Se si desidera aggiungere la gestione delle identità per le applicazioni desktop o portatile con Azure Active Directory B2C, utilizzare [2.0 OAuth](active-directory-b2c-reference-oauth-code.md) anziché OpenID connettersi.

Azure Active Directory B2C estende il protocollo OpenID connettersi standard per eseguire più semplice di autenticazione e autorizzazione. Introduce il [**parametro di criteri**](active-directory-b2c-reference-policies.md), che consente di utilizzare OpenID connettersi per aggiungere esperienze utente all'applicazione in uso, ad esempio per l'abbonamento, accesso e gestione dei profili. Qui è verrà spiegato come utilizzare OpenID connettersi e i criteri per ognuna di queste esperienze implementare nelle applicazioni web. È inoltre verrà spiegato come ottenere access_tokens per l'accesso web API.

Le richieste HTTP di esempio seguente verranno utilizzato il nostro esempio B2C, **fabrikamb2c.onmicrosoft.com**, nonché il nostro esempio applicazione **https://aadb2cplayground.azurewebsites.net** e directory criteri. Che è possibile provare le richieste di se stessi usando questi valori liberamente o sostituirli con uno personalizzato.
Informazioni su come [ottenere il proprio tenant B2C, applicazione e criteri](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Inviare richieste di autenticazione
Quando un'applicazione web richiede l'autenticazione dell'utente ed eseguire un criterio, è possibile indirizzare all'utente di `/authorize` endpoint. Questa è la parte interattiva del flusso di in cui l'utente avrà effettivamente azione, in base ai criteri.

Nella convocazione, il client indica le autorizzazioni necessarie per acquisire dall'utente il `scope` parametro e i criteri per eseguire il `p` parametro. Tre esempi vengono forniti di seguito (con interruzioni di riga per migliorare la leggibilità), ognuna con criteri diversi. Per avere un'idea del funzionamento di ogni richiesta, provare a incollare la richiesta in un browser ed eseguirlo.

#### <a name="use-a-sign-in-policy"></a>Utilizzare un criterio di accesso

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilizzare un criterio per l'abbonamento

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilizzo di un criterio di modifica profilo

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Obbligatorio | ID applicazione di [Azure portale](https://portal.azure.com/) assegnati all'app. |
| response_type | Obbligatorio | Il tipo di risposta, deve includere `id_token` per OpenID connettersi. Se un'app web sarà necessario anche i token per chiamare un API web, è possibile utilizzare `code+id_token`, come i professionisti IT qui. |
| redirect_uri | Consigliati | Redirect_uri dell'app, in cui risposte autenticazione possono essere inviate e ricevute tramite l'app. È necessario esattamente corrisponde a uno dei redirect_uris registrati nel portale, ad eccezione del fatto che deve essere codificata URL. |
| ambito | Obbligatorio | Un elenco delimitato da spazio degli ambiti. Un valore singolo ambito indica di Azure Active Directory entrambe le autorizzazioni che sono stati richiesti. Il `openid` ambito indica un'autorizzazione per accedere all'utente e recuperare dati sull'utente sotto forma di **id_tokens** (altre novità in arrivo su questo avanti in questo articolo). Il `offline_access` ambito è facoltativa per le applicazioni web. Indica che l'app sarà necessario un **refresh_token** per l'accesso alle risorse di lunga durata. |
| response_mode | Consigliati | Il metodo che deve essere utilizzato per inviare authorization_code risultante tornare all'app. Può essere uno dei 'query', 'form_post' o 'frammento'.  'form_post' è consigliabile per motivi di sicurezza. |
| stato | Consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token. Può essere una stringa di tutto il contenuto che si desidera. Un valore univoco generato in modo casuale viene usato in genere per prevenire attacchi FALSO intersito richiesta. Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina in. |
| nonce | Obbligatorio | Valore incluso nella richiesta (generata mediante l'app) incluse in id_token risultante come attestazione. L'app può verificare questo valore per ridurre gli attacchi di riproduzione token. Il valore è in genere una stringa casuale e univoca che può essere usata per identificare l'origine della richiesta. |
| p | Obbligatorio | Criteri che verranno eseguito. È il nome di un criterio che viene creato nel tenant di B2C. Il valore di nome dei criteri deve iniziare con "b2c\_1\_". Ulteriori informazioni sui criteri di [politica Extensible](active-directory-b2c-reference-policies.md). |
| prompt dei comandi | Facoltativo | Il tipo di interazione dell'utente che è necessario. L'unico valore valido al momento è 'login', che impone all'utente di immettere le credenziali per tale richiesta. Il Single sign-on non avranno effetto. |

A questo punto, l'utente verrà chiesto di completamento del flusso di lavoro del criterio. Questo può comportare l'utente immettere il nome utente e password, accedere con un'identità di social networking, iscrizione della directory o qualsiasi altro numero di passaggi, a seconda di come viene definito il criterio.

Al termine del criterio, all'utente di Azure Active Directory restituirà una risposta per l'app in indicata `redirect_uri`, utilizzando il metodo specificato nel `response_mode` parametro. La risposta sarà esattamente la stessa operazione per ciascun dei casi precedenti indipendenti dei criteri che è stato eseguito.

Una risposta corretta utilizzando `response_mode=fragment` sarà simile a:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id_token | Id_token che ha richiesto l'app. È possibile utilizzare il id_token per verificare l'identità dell'utente e avviare una sessione con l'utente. Ulteriori informazioni sulla id_tokens e il relativo contenuto sono incluse nel [riferimento token di Azure Active Directory B2C](active-directory-b2c-reference-tokens.md). |
| codice | Authorization_code l'app richiesto, se è stata utilizzata `response_type=code+id_token`. L'app è possibile utilizzare il codice di autorizzazione per richiedere un Access per una risorsa di destinazione. Authorization_codes sono molto temporanei. In genere, scadono dopo 10 minuti. |
| stato | Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |

Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che in grado di gestirle in modo appropriato l'app:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione. |
| stato | Vedere la descrizione completa nella tabella precedente. Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |


## <a name="validate-the-idtoken"></a>Convalidare il id_token
Solo la ricezione di un id_token non è sufficiente per eseguire l'autenticazione utente, è necessario convalidare firma del id_token e verificare le attestazioni token in base alle esigenze dell'applicazione. Azure Active Directory B2C utilizza [JSON Web token (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e crittografia a chiave pubblica per firmare i token e verificare che siano validi.

Sono disponibili molte librerie Apri origine che sono disponibili per la convalida dei JWTs, a seconda del linguaggio preferenza. È consigliabile esplorare queste opzioni invece di implementare la logica di convalida. Qui le informazioni possano essere utili per scoprire come utilizzare queste raccolte in modo appropriato.

Azure Active Directory B2C ha un endpoint metadati OpenID connettersi, che consente a un'app recuperare informazioni su Azure Active Directory B2C in fase di esecuzione. Queste informazioni includono i punti finali, contenuto token e token di chiavi per la firma. Esiste un documento di metadati JSON per ogni criterio nel tenant di B2C. Ad esempio il documento di metadati per il `b2c_1_sign_in` criteri nel `fabrikamb2c.onmicrosoft.com` si trova in:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Le proprietà di questo documento configurazione è il `jwks_uri`, il cui valore per lo stesso criterio sarebbe:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

In Ordina per determinare il tipo di criteri è stato utilizzato per la firma di un id_token (e where recuperare i metadati dal), sono disponibili due opzioni. Prima di tutto, il nome del criterio sia il `acr` richiedere la id_token. Per informazioni su come analizzare le attestazioni da un id_token, vedere la [Guida di riferimento token Azure Active Directory B2C](active-directory-b2c-reference-tokens.md). L'altra opzione consiste nel codificare i criteri del valore di `state` parametro quando si invia la richiesta e decodifica per determinare il tipo di criteri è stata utilizzata. Dei metodi descritti è perfettamente valido.

Dopo aver acquistato il documento di metadati dall'endpoint metadati OpenID connettersi, è possibile utilizzare le chiavi pubbliche RSA 256 (che si trovano per l'endpoint) per convalidare la firma del id_token. Può essere più tasti elencati per l'endpoint in qualsiasi punto nel tempo, ciascuna identificata da un `kid`. L'intestazione della id_token contiene anche un `kid` richiesta di rimborso che indica che questi tasti è stato utilizzato per firmare il id_token. Vedere il [riferimento token di Azure Active Directory B2C](active-directory-b2c-reference-tokens.md) per ulteriori informazioni, incluse sezioni [convalida token](active-directory-b2c-reference-tokens.md#validating-tokens) e [informazioni importanti sulla firma attivazione chiave](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Dopo avere convalidato la firma della id_token, sono disponibili diverse azioni che è necessario verificare, ad esempio:

- È necessario convalidare il `nonce` richiedere impedire attacchi di riproduzione token. Il valore deve essere specificato nella richiesta di accesso.
- È necessario convalidare il `aud` richiedere per assicurarsi che il id_token sia stato rilasciato per l'app. Il valore deve essere l'ID di applicazione dell'app.
- È necessario convalidare il `iat` e `exp` attestazioni per assicurarsi che il id_token è scaduto.

Esistono diverse ulteriori convalida da eseguire, descritto dettagliatamente in [OpenID connettersi Core specifiche](http://openid.net/specs/openid-connect-core-1_0.html).  È anche consigliabile convalidare aggiuntive sulle attestazioni, in base al proprio scenario. Di seguito sono riportate alcune operazioni di convalida comuni:

- Assicurarsi che l'utente/organizzazione è iscritta per l'app.
- Assicurarsi che gli utenti abbiano autorizzazione diritti.
- Garantire che è stata eseguita forza della funzione di autenticazione, ad esempio l'autenticazione a più fattori Azure.

Per ulteriori informazioni sulle attestazioni in un id_token, vedere il [riferimento token di Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

Dopo avere convalidato completamente la id_token, è possibile iniziare una sessione con l'utente e in crediti il id_token consente di ottenere informazioni relative all'utente nell'app. Queste informazioni possono essere utilizzate per la visualizzazione, i record, autorizzazioni e così via.

## <a name="get-a-token"></a>Ottenere un token
Se tutti esigenze web app per eseguire criteri, è possibile ignorare le prossime sezioni. Queste sezioni sono applicabili solo a web App è necessario eseguire autenticato chiamate a dell'API web e anche protetto da Azure Active Directory B2C.

È possibile riscattare authorization_code che è stato acquistato (tramite `response_type=code+id_token`) per un token per la risorsa desiderata mediante l'invio di un `POST` richiesta del `/token` endpoint. L'unica risorsa che è possibile richiedere un token per è attualmente web back-end dell'applicazione API. La convenzione per richiedendo un token a se stessi consiste nell'usare l'ID client dell'applicazione come ambito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obbligatorio | I criteri che è stato utilizzato per acquisire il codice di autorizzazione. È possibile utilizzare un criterio diverso nella richiesta. Nota aggiungere questo parametro per la **stringa di query**, non nel corpo del POST. |
| client_id | Obbligatorio | ID applicazione di [Azure portale](https://portal.azure.com/) assegnati all'app. |
| grant_type | Obbligatorio | Il tipo di concessione, che deve essere `authorization_code` per il flusso di codice di autorizzazione. |
| ambito | Consigliati | Un elenco delimitato da spazio degli ambiti. Un valore singolo ambito indica di Azure Active Directory entrambe le autorizzazioni che sono stati richiesti. Il `openid` ambito indica un'autorizzazione per accedere all'utente e recuperare dati sull'utente sotto forma di **id_tokens**. Può essere utilizzato per ottenere i token al Web back-end dell'applicazione API, che è rappresentato dallo stesso ID applicazione client. Il `offline_access` ambito indica che l'app sarà necessario un **refresh_token** per l'accesso alle risorse di lunga durata. |
| codice | Obbligatorio | Authorization_code che è stato acquistato in offrendo prima del flusso. |
| redirect_uri | Obbligatorio | Redirect_uri dell'applicazione in cui è stato ricevuto il authorization_code. |
| client_secret | Obbligatorio | Il segreto applicazione generati nel [portale di Azure](https://portal.azure.com/). Segreto questa applicazione è un elemento di sicurezza importanti. È consigliabile archiviarlo in modo sicuro nel server. È necessario prestare attenzione per ruotare il segreto del client periodicamente. |

Una risposta corretta token si presenterà:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| not_before | L'ora in cui il token è considerato valido, nel tempo periodo. |
| token_type | Il valore di tipo di token. L'unico tipo che supporta Azure Active Directory è titolare. |
| Access | Firmato token JWT richiesto. |
| ambito | Gli ambiti che il token è valido, che possono essere utilizzati per la memorizzazione nella cache token per successivi utilizzi. |
| expires_in | L'intervallo di tempo di Access è valido (secondi). |
| refresh_token | Un refresh_token OAuth 2.0. L'app è possibile utilizzare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente.  Refresh_tokens sono prolungato e può essere utilizzato per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per ulteriori informazioni, fare riferimento alla [Guida di riferimento token B2C](active-directory-b2c-reference-tokens.md). Si noti che è necessario utilizzato l'ambito `offline_access` nelle autorizzazioni e le richieste di token per ricevere un refresh_token. |

Aspetto delle risposte di errore:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione. |

## <a name="use-the-token"></a>Utilizzare il token
Ora che è stata acquisito un `access_token`, è possibile utilizzare il token nelle convocazioni per il back-end web API facilmente individuabili perché includono con la `Authorization` intestazione:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aggiornare il token
Il id_tokens sono temporanei. È necessario aggiornarli dopo la scadenza per continuare a essere in grado di accedere alle risorse. È possibile eseguire l'invio di un altro `POST` richiesta per il `/token` endpoint. In questo caso, fornire il `refresh_token` anziché il `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parametro | Obbligatorio | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| p | Obbligatorio | I criteri che è stato utilizzato per acquisire refresh_token originale. È possibile utilizzare un criterio diverso nella richiesta. Nota aggiungere questo parametro per la **stringa di query**, non nel corpo del POST. |
| client_id | Obbligatorio | ID applicazione di [Azure portale](https://portal.azure.com/) assegnati all'app. |
| grant_type | Obbligatorio | Il tipo di concessione, che deve essere `refresh_token` per questo segmento del flusso di codice di autorizzazione. |
| ambito | Consigliati | Un elenco delimitato da spazio degli ambiti. Un valore singolo ambito indica di Azure Active Directory entrambe le autorizzazioni che sono stati richiesti. Il `openid` ambito indica un'autorizzazione per accedere all'utente e recuperare dati sull'utente sotto forma di **id_tokens**. Può essere utilizzato per ottenere i token al Web back-end dell'applicazione API, che è rappresentato dallo stesso ID applicazione client. Il `offline_access` ambito indica che l'app sarà necessario un **refresh_token** per l'accesso alle risorse di lunga durata. |
| redirect_uri | Consigliati | Redirect_uri dell'applicazione in cui è stato ricevuto il authorization_code. |
| refresh_token | Obbligatorio | Refresh_token originale che è stato acquistato nella seconda parte del flusso di. Si noti che è necessario utilizzato l'ambito `offline_access` nelle autorizzazioni e le richieste di token per ricevere un refresh_token. |
| client_secret | Obbligatorio | Il segreto applicazione generati nel [portale di Azure](https://portal.azure.com/). Segreto questa applicazione è un elemento di sicurezza importanti. È consigliabile archiviarlo in modo sicuro nel server. È necessario prestare attenzione per ruotare il segreto del client periodicamente. |

Una risposta corretta token si presenterà:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| not_before | L'ora in cui il token è considerato valido, nel tempo periodo. |
| token_type | Il valore di tipo di token. L'unico tipo che supporta Azure Active Directory è titolare. |
| Access | Firmato token JWT richiesto. |
| ambito | Gli ambiti che il token è valido, che possono essere utilizzati per la memorizzazione nella cache token per successivi utilizzi. |
| expires_in | L'intervallo di tempo di Access è valido (secondi). |
| refresh_token | Un refresh_token OAuth 2.0. L'app è possibile utilizzare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente.  Refresh_tokens sono prolungato e può essere utilizzato per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per ulteriori informazioni, fare riferimento alla [Guida di riferimento token B2C](active-directory-b2c-reference-tokens.md). |

Aspetto delle risposte di errore:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione. |


## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione

Quando si desidera firmare l'utente dall'app, non è sufficiente cancellare i cookie dell'app o alla fine in caso contrario la sessione con l'utente. È inoltre necessario reindirizzare l'utente di Azure Active Directory per eseguire la disconnessione. Se non si riesce a tale scopo, l'utente potrebbe essere possibile autenticare di nuovo all'app senza immettere nuovamente le proprie credenziali. In questo modo gli utenti potranno una single sign-on sessione valido con Azure Active Directory.

È possibile reindirizzare semplicemente all'utente di `end_session_endpoint` che è presente nell'elenco dello stesso documento metadati OpenID connettersi descritto nella precedente sezione "Convalida la id_token":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| p | Obbligatorio | Criteri che si desiderano usare per accedere all'utente dall'applicazione. |
| post_logout_redirect_uri | Consigliati | L'URL che l'utente deve reindirizzato alla dopo aver completato disconnessione. Se non è incluso, l'utente verrà visualizzato un messaggio generico da Azure Active Directory B2C.  |

> [AZURE.NOTE]
    Mentre si indirizza all'utente di `end_session_endpoint` eliminerà alcuni lo stato dell'utente single sign-on con Azure Active Directory B2C, non convalida l'utente dalla sessione del provider (IDP) di social networking identità dell'utente. Se l'utente seleziona IDP stesso durante un accesso successivo, si verrà essere autenticati nuovamente, senza immettere le credenziali. Se l'utente desidera disconnettersi dall'applicazione B2C, non necessariamente significa che desiderano disconnettersi completamente il proprio account Facebook. Tuttavia, nel caso gli account locali sessione dell'utente verrà terminata correttamente.

## <a name="use-your-own-b2c-tenant"></a>Usare il proprio tenant B2C

Se si vuole provare queste richieste per se stessi, è necessario eseguire questi tre passaggi e quindi sostituire i valori di esempio sopra con il proprio:

- [Creare un tenant B2C](active-directory-b2c-get-started.md)e usare il nome del tenant nella richiesta.
- [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un redirect_uri. Desiderato includere un **web app o web api** nell'app e, facoltativamente creare un **segreto dell'applicazione**.
- [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.
