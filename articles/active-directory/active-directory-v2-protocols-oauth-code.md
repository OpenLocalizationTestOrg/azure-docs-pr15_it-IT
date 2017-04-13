

<properties
    pageTitle="Azure Active Directory v 2.0 OAuth autorizzazione codice flusso | Microsoft Azure"
    description="Creazione di applicazioni web che utilizza l'implementazione di Azure Active Directory del protocollo di autenticazione OAuth 2.0."
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>Flusso protocolli v 2.0 - codice di autorizzazione OAuth 2.0

La concessione di codice autorizzazione OAuth 2.0 può essere utilizzata nelle applicazioni che vengono installate in un dispositivo per accedere alle risorse protette, ad esempio web API.  Usa implementazione del v 2.0 modello di app di OAuth 2.0, è possibile aggiungere accedere e API accedere alle App per dispositivi mobili e desktop.  Questa guida è indipendente dalla lingua e viene descritto come inviare e ricevere messaggi HTTP senza utilizzare uno dei nostro raccolte Apri origine.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

Il flusso di codice autorizzazione OAuth 2.0 viene descritta nella [sezione 4.1 della specifica OAuth 2.0](http://tools.ietf.org/html/rfc6749).  Viene utilizzato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, inclusi [web App](active-directory-v2-flows.md#web-apps) e [App installate a livello nativo](active-directory-v2-flows.md#mobile-and-native-apps).  In questo modo App acquisire sicuro access_tokens che possono essere utilizzati per accedere alle risorse che sono protetti tramite l'endpoint v 2.0.  

## <a name="protocol-diagram"></a>Diagramma di protocollo
Un alto livello, il flusso di autenticazione intera per un'applicazione nativa/mobile aspetto un po' seguente:

![Flusso di codice Auth OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Richiesta di un codice di autorizzazione
Il flusso di codice autorizzazione inizia con il client indirizza all'utente di `/authorize` endpoint.  Nella convocazione, il client indica le autorizzazioni che necessarie per acquisire da parte dell'utente:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Fare clic sul collegamento seguente per eseguire questo richiesta! Dopo l'accesso, il browser deve essere reindirizzato al `https://localhost/myapp/` con un `code` nella barra degli indirizzi.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://Login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti non `common`, `organizations`, `consumers`e gli identificatori del tenant.  Per informazioni dettagliate, vedere [informazioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| response_type | Obbligatorio | Deve includere `code` per il flusso di codice di autorizzazione. |
| redirect_uri | consigliati | Redirect_uri dell'app, in cui risposte autenticazione possono essere inviate e ricevute tramite l'app.  Deve corrispondere esattamente una redirect_uris registrato nel portale, ad eccezione del fatto che deve essere url codificata.  Per App nativi e dispositivi mobili, è necessario utilizzare il valore predefinito di `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| ambito | Obbligatorio | Elenco delimitato da spazio di [ambiti](active-directory-v2-scopes.md) che si desidera concedere all'utente acconsente a.  |
| response_mode | consigliati | Specifica il metodo che deve essere utilizzato per restituire il risultante token l'app.  Può essere rappresentato da `query` o `form_post`.  |
| stato | consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token.  Può essere una stringa di tutto il contenuto che si desidera.  Un valore univoco generato in modo casuale viene usato in genere per [prevenire attacchi FALSO intersito richiesta](http://tools.ietf.org/html/rfc6749#section-10.12).  Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |
| prompt dei comandi | facoltativo | Indica il tipo di interazione dell'utente che è necessario.  I valori validi solo in questo momento sono 'login', 'Nessuno' e 'consenso'.  `prompt=login`impone all'utente di immettere le credenziali per tale richiesta negazione single sign-in.  `prompt=none`è l'operazione - garantisce che l'utente non viene visualizzata ogni richiesta interattiva qualsiasi.  Se la richiesta non può essere completata automaticamente tramite single-sign-on, l'endpoint v 2.0 restituirà un errore.  `prompt=consent`Attiva la finestra di dialogo di consenso OAuth dopo l'accesso, chiedere all'utente di concedere le autorizzazioni per l'app. |
| login_hint | facoltativo | Può essere utilizzato per pre-campo username/posta elettronica indirizzo della pagina di accesso dell'utente, se si conosce il proprio nome utente anticipo.  Spesso App utilizzerà questo parametro durante la nuova autenticazione, visto già estratti il nome utente da un precedente accesso tramite il `preferred_username` richiedere. |
| domain_hint | facoltativo | Può essere una delle `consumers` o `organizations`.  Se incluso, non verrà eseguita il processo di rilevamento basata su posta elettronica che l'utente attraversa nella pagina di accesso v 2.0 iniziali per un'esperienza utente leggermente più semplice.  Spesso App utilizzerà questo parametro durante la nuova autenticazione tramite l'estrazione di `tid` da un segno di aggiuntivo precedente.  Se il `tid` richiedere valore è `9188040d-6c67-4c5b-b112-36a304b66dad`, è necessario utilizzare `domain_hint=consumers`.  In caso contrario, utilizzare `domain_hint=organizations`. |

A questo punto, all'utente verrà chiesto di immettere le credenziali e completare l'autenticazione.  Endpoint v 2.0 garantisce inoltre che l'utente ha acconsentito alle autorizzazioni indicate nella `scope` parametro della query.  Se l'utente non ha acconsentito a qualsiasi di queste autorizzazioni, esso verrà chiesto all'utente di consenso per le autorizzazioni necessarie.  Dettagli delle [autorizzazioni, consenso e le app multi-tenant vengono fornite di seguito](active-directory-v2-scopes.md).

Dopo l'utente viene autenticato e concede consenso dell'utente, l'endpoint v 2.0 restituirà una risposta per l'app in indicata `redirect_uri`, utilizzando il metodo specificato nel `response_mode` parametro.

#### <a name="successful-response"></a>Risposta corretta
Una risposta corretta utilizzando `response_mode=query` ha un aspetto analogo:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| codice | Authorization_code che ha richiesto l'app. L'app è possibile utilizzare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione.  Authorization_codes sono molto temporanei, in genere che scadano dopo 10 minuti. |
| stato | Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |

#### <a name="error-response"></a>Risposta a un errore
Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che in grado di gestirle in modo appropriato l'app:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Codici di errore per gli errori di endpoint di autorizzazione

La tabella seguente descrive i codici di errore che possono essere restituiti il `error` parametro della risposta di errore.

| Codice di errore | Descrizione | Azione di client |
|------------|-------------|---------------|
| invalid_request | Errore di protocollo, ad esempio un parametro obbligatorio manca. | Correggere e inviare la richiesta. Si tratta di una soluzione di sviluppo in genere viene rilevato errore durante la prova iniziale.|
| unauthorized_client | L'applicazione client non è consentito per richiedere un codice di autorizzazione. | Il problema si verifica quando l'applicazione client non è registrato in Azure Active Directory o non viene aggiunto al tenant di Azure Active Directory dell'utente. L'applicazione è possibile richiedere all'utente con set di istruzioni per l'installazione dell'applicazione e aggiungerlo alla Azure Active Directory. |
| ACCESS_DENIED | Proprietario della risorsa negato consenso dell'utente | L'applicazione client può comunicare all'utente che non possa passare a meno che l'utente acconsente. |
| unsupported_response_type | Il server di autorizzazione non supporta il tipo di risposta nella richiesta. | Correggere e inviare la richiesta. Si tratta di una soluzione di sviluppo in genere viene rilevato errore durante la prova iniziale.|
|server_error | Il server ha rilevato un errore imprevisto. | Ripetere la richiesta. Questi errori possono provocare condizioni temporanee. L'applicazione client può indicare all'utente che la risposta è ritardata la scadenza di un errore temporaneo. |
| temporarily_unavailable | Il server è temporaneamente occupato per gestire la richiesta. | Ripetere la richiesta. L'applicazione client può indicare all'utente che la risposta subisce un ritardo dovuto una condizione temporanea. |
| invalid_resource |Risorsa di destinazione non è valida perché non è presente, Azure Active Directory non è possibile trovarlo o non sia configurato correttamente.| Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione è possibile richiedere all'utente con set di istruzioni per l'installazione dell'applicazione e aggiungerlo alla Azure Active Directory. |

## <a name="request-an-access-token"></a>Richiedere un token di accesso
Che è stato acquistato un authorization_code è stata concessa l'autorizzazione dall'utente, è possibile riscattare il `code` per un `access_token` per la risorsa desiderata, inviando un `POST` richiesta per il `/token` endpoint:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Provare a eseguire la richiesta di Postman! (Non dimenticare di sostituire il `code`)     [ ![Eseguire in Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | Obbligatorio | Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti non `common`, `organizations`, `consumers`e gli identificatori del tenant.  Per informazioni dettagliate, vedere [informazioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| grant_type | Obbligatorio | Deve essere `authorization_code` per il flusso di codice di autorizzazione. |
| ambito | Obbligatorio | Un elenco delimitato da spazio degli ambiti.  Gli ambiti richiesti in questo segmento devono essere equivalente a o un sottoinsieme degli ambiti della richiesta in offrendo prima.  Se gli ambiti specificati in questa richiesta estesi a più server delle risorse, l'endpoint v 2.0 verrà restituito un token per la risorsa specificato nell'ambito di prima.  Per ulteriori informazioni su ambiti, consultare [le autorizzazioni, consenso e gli ambiti](active-directory-v2-scopes.md).  |
| codice | Obbligatorio | Authorization_code che è stato acquistato in offrendo prima del flusso.   |
| redirect_uri | Obbligatorio | Lo stesso valore redirect_uri che è stato utilizzato per acquisire il authorization_code. |
| client_secret | necessari per web apps | Il segreto applicazione creata nel portale di registrazione di app per l'app.  Essa non utilizzare in un'applicazione nativa, perché client_secrets non possono essere archiviate in modo affidabile su dispositivi.  È necessario per web apps e web API, che hanno la possibilità di archiviare il client_secret in modo sicuro sul lato server. |

#### <a name="successful-response"></a>Risposta corretta
Una risposta corretta token si presenterà:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Access | Token di accesso. L'app è possibile utilizzare questo token per eseguire l'autenticazione per la risorsa protetta, ad esempio dell'API web. |
| token_type | Indica il valore di tipo di token. L'unico tipo che supporta Azure Active Directory è titolare  |
| expires_in | Per quanto tempo il token di accesso è valido (secondi). |
| ambito | Ambiti di Access è valido per. |
| refresh_token |  Un token di aggiornamento OAuth 2.0. L'app è possibile utilizzare questo token acquisire token di accesso aggiuntive dopo la scadenza del token di accesso corrente.  Refresh_tokens sono permanenti e può essere utilizzato per mantenere l'accesso alle risorse per lunghi periodi di tempo.  Per ulteriori informazioni, fare riferimento alla [Guida di riferimento token v 2.0](active-directory-v2-tokens.md).  |
| id_token | Un unsigned JSON Web Token (JWT). Base64Url di può app decodificare i segmenti del token per richiedere informazioni relative all'utente che ha effettuato l'accesso. L'app è possibile memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per qualsiasi autorizzazione o i limiti di sicurezza.  Per ulteriori informazioni su id_tokens vedere [riferimento token endpoint v 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Risposta a un errore
Aspetto delle risposte di errore:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |
| error_codes | Un elenco dei codici di errore specifico servizio token di sicurezza che possono risultare utili nella diagnostica.  |
| timestamp | L'ora dell'errore. |
| trace_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica.  |
| correlation_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica vari componenti. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Codici di errore per gli errori di endpoint token

| Codice di errore | Descrizione | Azione di client |
|------------|-------------|---------------|
| invalid_request | Errore di protocollo, ad esempio un parametro obbligatorio manca. | Correggere e inviare la richiesta |
| invalid_grant | Il codice di autorizzazione è valido o è scaduto. | Provare una nuova richiesta per il `/authorize` endpoint |
| unauthorized_client | Client autenticato non è autorizzato a utilizzare questo tipo di concedere l'autorizzazione. | Il problema si verifica quando l'applicazione client non è registrato in Azure Active Directory o non viene aggiunto al tenant di Azure Active Directory dell'utente. L'applicazione è possibile richiedere all'utente con set di istruzioni per l'installazione dell'applicazione e aggiungerlo alla Azure Active Directory. |
| invalid_client | Autenticazione client non riuscita. | Le credenziali del client non sono valide. Per correggere l'errore, l'amministratore dell'applicazione aggiorna le credenziali. |
| unsupported_grant_type | Il server di autorizzazione non supporta il tipo di concedere l'autorizzazione. | Modificare il tipo di concessione nella richiesta. Questo tipo di errore dovrebbe essere eseguita solo durante lo sviluppo e rilevato durante la prova iniziale. |
| invalid_resource | Risorsa di destinazione non è valida perché non è presente, Azure Active Directory non è possibile trovarlo o non sia configurato correttamente. | Indica che la risorsa, se presente, non è stata configurata nel tenant. L'applicazione è possibile richiedere all'utente con set di istruzioni per l'installazione dell'applicazione e aggiungerlo alla Azure Active Directory. |
| interaction_required | La richiesta richiede l'interazione dell'utente. Un passaggio di autenticazione aggiuntive, ad esempio, è necessario. | Ripetere la richiesta con la stessa risorsa. |
| temporarily_unavailable | Il server è temporaneamente occupato per gestire la richiesta. | Ripetere la richiesta. L'applicazione client può indicare all'utente che la risposta subisce un ritardo dovuto una condizione temporanea.|

## <a name="use-the-access-token"></a>Utilizzare il token di accesso
Ora che è stata acquisito un `access_token`, è possibile utilizzare il token nelle convocazioni alle API Web facilmente individuabili perché includono con la `Authorization` intestazione:

> [AZURE.TIP] Esecuzione della richiesta in Postman! (Sostituire il `Authorization` intestazione prima)     [ ![Eseguire in Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Aggiornare il token di accesso
Access_tokens hanno una durata breve ed è necessario aggiornarli dopo la scadenza per continuare l'accesso alle risorse.  È possibile eseguire l'invio di un altro `POST` richiesta del `/token` endpoint, in questo caso che fornisce il `refresh_token` anziché il `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Provare a eseguire la richiesta di Postman! (Non dimenticare di sostituire il `refresh_token`)     [ ![Eseguire in Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| tenant | Obbligatorio | Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti non `common`, `organizations`, `consumers`e gli identificatori del tenant.  Per informazioni dettagliate, vedere [informazioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| grant_type | Obbligatorio | Deve essere `refresh_token` per questo segmento del flusso di codice di autorizzazione. |
| ambito | Obbligatorio | Un elenco delimitato da spazio degli ambiti.  Gli ambiti richiesti in questo segmento devono essere equivalente a o un sottoinsieme degli ambiti richiesto il segmento richiesta authorization_code originale.  Se gli ambiti specificati in questa richiesta estesi a più server delle risorse, l'endpoint v 2.0 verrà restituito un token per la risorsa specificato nell'ambito di prima.  Per ulteriori informazioni su ambiti, consultare [le autorizzazioni, consenso e gli ambiti](active-directory-v2-scopes.md).  |
| refresh_token | Obbligatorio | Refresh_token che è stato acquistato nella seconda parte del flusso di.   |
| redirect_uri | Obbligatorio | Lo stesso valore redirect_uri che è stato utilizzato per acquisire il authorization_code. |
| client_secret | necessari per web apps | Il segreto applicazione creata nel portale di registrazione di app per l'app.  Essa non utilizzare in un'applicazione nativa, perché client_secrets non possono essere archiviate in modo affidabile su dispositivi.  È necessario per web apps e web API, che hanno la possibilità di archiviare il client_secret in modo sicuro sul lato server. |

#### <a name="successful-response"></a>Risposta corretta
Una risposta corretta token si presenterà:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Access | Token di accesso. L'app è possibile utilizzare questo token per eseguire l'autenticazione per la risorsa protetta, ad esempio dell'API web. |
| token_type | Indica il valore di tipo di token. L'unico tipo che supporta Azure Active Directory è titolare  |
| expires_in | Per quanto tempo il token di accesso è valido (secondi). |
| ambito | Ambiti di Access è valido per. |
| refresh_token |  Un nuovo token di aggiornamento OAuth 2.0. È necessario sostituire il token di aggiornamento precedente con questo token acquisita Aggiorna per assicurarsi che il token di aggiornamento rimangono valide per più a lungo possibile.  |
| id_token | Un unsigned JSON Web Token (JWT). Base64Url di può app decodificare i segmenti del token per richiedere informazioni relative all'utente che ha effettuato l'accesso. L'app è possibile memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per qualsiasi autorizzazione o i limiti di sicurezza.  Per ulteriori informazioni su id_tokens vedere [riferimento token endpoint v 2.0](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Risposta a un errore
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |
| error_codes | Un elenco dei codici di errore specifico servizio token di sicurezza che possono risultare utili nella diagnostica.  |
| timestamp | L'ora dell'errore. |
| trace_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica.  |
| correlation_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica vari componenti. |

Per una descrizione dei codici di errore e l'azione client consigliato, vedere [codici di errore per gli errori di token endpoint](#error-codes-for-token-endpoint-errors).
