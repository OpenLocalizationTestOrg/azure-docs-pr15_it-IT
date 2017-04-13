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

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure B2C Directory attiva: Il flusso di codice autorizzazione 2.0 OAuth

La concessione di codice autorizzazione OAuth 2.0 può essere utilizzata nelle applicazioni che vengono installate in un dispositivo per accedere alle risorse protette, ad esempio web API. Tramite l'implementazione di Azure Active Directory (Azure Active Directory) B2C di OAuth 2.0, è possibile aggiungere attività di gestione delle identità per l'abbonamento, accesso e altri alle App per dispositivi mobili e desktop. Questa guida è indipendente dal linguaggio. Viene descritto come inviare e ricevere messaggi HTTP senza utilizzare uno dei nostro raccolte Apri origine.

<!-- TODO: Need link to libraries -->

Il flusso di codice autorizzazione OAuth 2.0 descritto nella [sezione 4.1 della specifica OAuth 2.0](http://tools.ietf.org/html/rfc6749). È possibile utilizzare per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, inclusi [web App](active-directory-b2c-apps.md#web-apps) e [App installate a livello nativo](active-directory-b2c-apps.md#mobile-and-native-apps). In questo modo App acquisire sicuro **access_tokens**, che possono essere utilizzati per accedere alle risorse che sono protetti da un [server di autorizzazione](active-directory-b2c-reference-protocols.md#the-basics).

Questa Guida verrà focalizzare l'attenzione su un particolare gusto OAuth 2.0 autorizzazione codice flusso -**client pubblica**. Un client pubblico è tutte le applicazioni client che non possono essere attendibile per gestire in modo sicuro l'integrità di una password segreta. Sono inclusi App per dispositivi mobili, applicazioni desktop e pressoché qualsiasi applicazione che viene eseguita in un dispositivo ed è necessario ottenere access_tokens. Se si desidera aggiungere un'app web di gestione delle identità con Azure Active Directory B2C, utilizzare [La connessione OpenID](active-directory-b2c-reference-oidc.md) anziché OAuth 2.0.

Azure Active Directory B2C estende lo standard che 2.0 OAuth scorra per eseguire più semplice di autenticazione e autorizzazione. Introduce il [**parametro di criteri**](active-directory-b2c-reference-policies.md), che consente di utilizzare OAuth 2.0 per aggiungere esperienze utente all'applicazione in uso, ad esempio per l'abbonamento, accesso e gestione dei profili. Qui spiegato come utilizzare criteri e OAuth 2.0 per implementare ognuna di queste esperienze nelle applicazioni native. È anche possibile viene illustrato come ottenere access_tokens per l'accesso web API.

Le richieste HTTP di esempio seguente utilizzerà la directory B2C di esempio, **fabrikamb2c.onmicrosoft.com**, nonché il nostro applicazione di esempio e criteri. È possibile provare le richieste su se stessi utilizzando i valori o sostituirli con uno personalizzato.
Informazioni su come [ottenere il proprio directory B2C, applicazione e criteri](#use-your-own-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. ottenere un codice di autorizzazione
Il flusso di codice autorizzazione inizia con il client indirizza all'utente di `/authorize` endpoint. Questa è la parte interattiva del flusso di in cui l'utente avrà effettivamente azione. Nella convocazione, il client indica le autorizzazioni necessarie per acquisire dall'utente il `scope` parametro e i criteri per eseguire il `p` parametro. Tre esempi vengono forniti di seguito (con interruzioni di riga per migliorare la leggibilità), ognuna con criteri diversi.

#### <a name="use-a-sign-in-policy"></a>Utilizzare un criterio di accesso

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Utilizzare un criterio per l'abbonamento

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Utilizzo di un criterio di modifica profilo

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Obbligatorio | ID applicazione di [Azure portale](https://portal.azure.com) assegnati all'app. |
| response_type | Obbligatorio | Il tipo di risposta, deve includere `code` per il flusso di codice di autorizzazione. |
| redirect_uri | Obbligatorio | Redirect_uri dell'app, in cui risposte autenticazione possono essere inviate e ricevute tramite l'app. È necessario esattamente corrisponde a uno dei redirect_uris registrati nel portale, ad eccezione del fatto che deve essere codificata URL. |
| ambito | Obbligatorio | Un elenco delimitato da spazio degli ambiti. Un valore singolo ambito indica di Azure Active Directory entrambe le autorizzazioni che sono stati richiesti. Con l'ID client come l'ambito indica che l'app è necessario un **token di accesso** che può essere utilizzata con il proprio servizio o dell'API web, rappresentato dallo stesso ID di client.  Il `offline_access` ambito indica che l'app sarà necessario un **refresh_token** per l'accesso alle risorse di lunga durata.  È inoltre possibile utilizzare il `openid` ambito per richiedere un **id_token** di Azure Active Directory B2C. |
| response_mode | Consigliati | Il metodo che deve essere utilizzato per inviare authorization_code risultante tornare all'app. Può essere uno dei 'query', 'form_post' o 'frammento'.
| stato | Consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token. Può essere una stringa di tutto il contenuto che si desidera. Un valore univoco generato in modo casuale viene usato in genere per prevenire attacchi FALSO intersito richiesta. Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio di in una pagina o i criteri in fase di esecuzione. |
| p | Obbligatorio | Criteri che verranno eseguito. È il nome di un criterio che viene creato nella directory B2C. Il valore di nome dei criteri deve iniziare con "b2c\_1\_". Ulteriori informazioni sui criteri di [politica Extensible](active-directory-b2c-reference-policies.md). |
| prompt dei comandi | Facoltativo | Il tipo di interazione dell'utente che è necessario. L'unico valore valido al momento è 'login', che impone all'utente di immettere le credenziali per tale richiesta. Il Single sign-on non avranno effetto. |

A questo punto, l'utente verrà chiesto di completamento del flusso di lavoro del criterio. Questo può comportare l'utente immettere il nome utente e password, accedere con un'identità di social networking, iscrizione della directory o qualsiasi altro numero di passaggi, a seconda di come viene definito il criterio.

Al termine del criterio, all'utente di Azure Active Directory restituirà una risposta per l'app in indicata `redirect_uri`, utilizzando il metodo specificato nel `response_mode` parametro. La risposta sarà esattamente la stessa operazione per ciascun dei casi precedenti indipendenti dei criteri che è stato eseguito.

Una risposta corretta che utilizza `response_mode=query` ha un aspetto analogo:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| codice | Authorization_code che ha richiesto l'app. L'app è possibile utilizzare il codice di autorizzazione per richiedere un Access per una risorsa di destinazione. Authorization_codes sono molto temporanei. In genere, scadono dopo 10 minuti. |
| stato | Vedere la descrizione completa nella tabella precedente. Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |

Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che in grado di gestirle in modo appropriato l'app:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consentono agli sviluppatori di individuare la causa principale di un errore di autenticazione. |
| stato | Vedere la descrizione completa della prima tabella in questa sezione. Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |


## <a name="2-get-a-token"></a>2. ottenere un token
Ora che è stato acquistato un authorization_code, è possibile riscattare il `code` per un token per la risorsa desiderata mediante l'invio di un `POST` richiesta per il `/token` endpoint. In Azure Active Directory B2C, l'unica risorsa che è possibile richiedere un token per è il sito web back-end dell'applicazione API. Convenzione utilizzata per richiedere un token a se stessi consiste nell'usare l'ID client dell'applicazione come ambito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| p | Obbligatorio | I criteri che è stato utilizzato per acquisire il codice di autorizzazione. È possibile utilizzare un criterio diverso nella richiesta. Nota aggiungere questo parametro per la *stringa di query*, non nel corpo del POST. |
| client_id | Obbligatorio | ID applicazione di [Azure portale](https://portal.azure.com) assegnati all'app. |
| grant_type | Obbligatorio | Il tipo di concessione, che deve essere `authorization_code` per il flusso di codice di autorizzazione. |
| ambito | Consigliati | Un elenco delimitato da spazio degli ambiti. Un valore singolo ambito indica di Azure Active Directory entrambe le autorizzazioni che sono stati richiesti. Con l'ID client come l'ambito indica che l'app è necessario un **token di accesso** che può essere utilizzata con il proprio servizio o dell'API web, rappresentato dallo stesso ID di client.  Il `offline_access` ambito indica che l'app sarà necessario un **refresh_token** per l'accesso alle risorse di lunga durata.  È inoltre possibile utilizzare il `openid` ambito per richiedere un **id_token** di Azure Active Directory B2C. |
| codice | Obbligatorio | Authorization_code che è stato acquistato in offrendo prima del flusso. |
| redirect_uri | Obbligatorio | Redirect_uri dell'applicazione in cui è stato ricevuto il authorization_code. |

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
| Access | Firma token JSON Web Token (JWT) richiesto. |
| ambito | Gli ambiti che il token è valido, che possono essere utilizzati per la memorizzazione nella cache token per successivi utilizzi. |
| expires_in | L'intervallo di tempo che il token è valido (secondi). |
| refresh_token | Un refresh_token OAuth 2.0. L'app è possibile utilizzare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. Refresh_tokens sono prolungato e può essere utilizzato per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per ulteriori informazioni, fare riferimento alla [Guida di riferimento token B2C](active-directory-b2c-reference-tokens.md). |

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
| error_description | Messaggio di errore specifici che consentono agli sviluppatori di individuare la causa principale di un errore di autenticazione. |

## <a name="3-use-the-token"></a>3. utilizzare il token
Ora che è stata acquisito un `access_token`, è possibile utilizzare il token nelle convocazioni per il back-end web API facilmente individuabili perché includono con la `Authorization` intestazione:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. aggiornare il token
Token di accesso e Id sono temporanei. È necessario aggiornarli dopo la scadenza per continuare a essere in grado di accedere alle risorse. È possibile eseguire l'invio di un altro `POST` richiesta per il `/token` endpoint. Questa volta fornire la `refresh_token` anziché il `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametro | Obbligatorio? | Descrizione |
| ----------------------- | ------------------------------- | -------- |
| p | Obbligatorio | I criteri che è stato utilizzato per acquisire refresh_token originale. È possibile utilizzare un criterio diverso nella richiesta. Nota aggiungere questo parametro per la *stringa di query*, non nel corpo del POST. |
| client_id | Consigliati | ID applicazione di [Azure portale](https://portal.azure.com) assegnati all'app. |
| grant_type | Obbligatorio | Il tipo di concessione, che deve essere `refresh_token` per questo segmento del flusso di codice di autorizzazione. |
| ambito | Consigliati | Un elenco delimitato da spazio degli ambiti. Un valore singolo ambito indica di Azure Active Directory entrambe le autorizzazioni che sono stati richiesti. Con l'ID client come l'ambito indica che l'app è necessario un **token di accesso** che può essere utilizzata con il proprio servizio o dell'API web, rappresentato dallo stesso ID di client.  Il `offline_access` ambito indica che l'app sarà necessario un **refresh_token** per l'accesso alle risorse di lunga durata.  È inoltre possibile utilizzare il `openid` ambito per richiedere un **id_token** di Azure Active Directory B2C. |
| redirect_uri | Facoltativo | Redirect_uri dell'applicazione in cui è stato ricevuto il authorization_code. |
| refresh_token | Obbligatorio | Refresh_token originale che è stato acquistato nella seconda parte del flusso di. |

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
| Access | Firma token JSON Web Token (JWT) richiesto. |
| ambito | Gli ambiti che il token è valido, che possono essere utilizzati per la memorizzazione nella cache token per successivi utilizzi. |
| expires_in | L'intervallo di tempo che il token è valido (secondi). |
| refresh_token | Un refresh_token OAuth 2.0. L'app è possibile utilizzare questo token per acquisire token aggiuntivi dopo la scadenza del token corrente. Refresh_tokens sono prolungato e può essere utilizzato per mantenere l'accesso alle risorse per lunghi periodi di tempo. Per ulteriori informazioni, fare riferimento alla [Guida di riferimento token B2C](active-directory-b2c-reference-tokens.md). |

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

## <a name="use-your-own-b2c-directory"></a>Utilizzare directory B2C

Se si vuole provare queste richieste per se stessi, è necessario eseguire questi tre passaggi e quindi sostituire i valori di esempio sopra con il proprio:

- [Creare una directory B2C](active-directory-b2c-get-started.md) e usare il nome della cartella nella richiesta.
- [Creare un'applicazione](active-directory-b2c-app-registration.md) per ottenere un ID applicazione e un redirect_uri. Si desidera includere un **client nativo** nell'app.
- [Creare i criteri](active-directory-b2c-reference-policies.md) per ottenere i nomi dei criteri.
