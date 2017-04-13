
<properties
    pageTitle="Azure Active Directory v 2.0 OAuth Client credenziali flusso | Microsoft Azure"
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
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>i protocolli v 2.0 - OAuth Client 2.0 credenziali flusso

[Concedono le credenziali del client OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4), definito talvolta "due punti OAuth" è utilizzabile per accedere alle risorse web ospitato utilizzando l'identità di un'applicazione.  Viene generalmente utilizzata per le interazioni al server che deve essere eseguito in background senza precense immediato di un utente finale.  Questi tipi di applicazioni vengono spesso denominati **daemon** o **account del servizio**.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

Nelle più comuni tre "tre schede OAuth," un'applicazione client viene concessa l'autorizzazione per accedere a una risorsa per conto di un utente specifico.  L'autorizzazione è **delegati** da parte dell'utente all'applicazione, in genere durante il processo di [consenso](active-directory-v2-scopes.md) .  Tuttavia, nel flusso di credenziali client concesse le autorizzazioni sono **direttamente** per l'applicazione.  Quando l'app presenta un token a una risorsa, vengono applicate la risorsa che app ha l'autorizzazione ad eseguire un'azione - non che alcuni utenti abbiano autorizzazione.

## <a name="protocol-diagram"></a>Diagramma di protocollo
Il flusso di credenziali client intero ha un aspetto simile al seguente: tutti i passaggi descritti in dettaglio di seguito.

![Flusso di credenziali client](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Ottenere l'autorizzazione di accesso diretto 
Esistono due modi in cui un'app riceve in genere diretta autorizzazione per accedere a una risorsa: tramite un elenco di controllo di accesso alla risorsa o assegnazione di autorizzazioni dell'applicazione in Azure Active Directory.  Altri modi che una risorsa è possibile scegliere di autorizzare il client e server ogni risorsa può scegliere il metodo più appropriata per l'applicazione.  Questi due metodi sono più comune in Azure Active Directory e sono consigliati per i client e le risorse per eseguire il flusso di credenziali client.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso
Un provider di risorse specificato potrebbe applicare un controllo di autorizzazione in base a un elenco di ID dell'applicazione che conosca e concede alcune particolare livello di accesso.  Quando la risorsa riceve un token da endpoint v 2.0, è possibile decodificare il token ed estrarre l'ID dell'applicazione del client dalla `appid` e `iss` reclami.  Quindi possibile confrontare che l'applicazione rispetto a un elenco di controllo di accesso (ACL) viene mantenuta.  Il metodo dell'elenco di controllo di accesso e granularità può variare notevolmente da una risorsa al.

Un caso di utilizzo comune per tali ACL testare canali per un'applicazione web o web api.  Web api può concedere solo un sottoinsieme delle relative autorizzazioni complete dei client diversi.  Ma per eseguire test to end sull'api, viene creato un client di prova che acquisisce token da endpoint v 2.0 e li invia all'api.  L'api è quindi possibile, ACL ID di applicazione del client test per l'accesso completo alle funzionalità intera dell'api.  Si noti che se si dispone di un elenco del servizio, deve essere assicurarsi di non solo convalidare il chiamante `appid`, ma anche verificare che la `iss` del token sia attendibile anche.

Questo tipo di autorizzazione in genere per daemon e account del servizio che devono accedere ai dati appartenenti a utenti consumer con un account Microsoft personale.  Per i dati di proprietà di organizzazioni, si consiglia di acquisire l'autorizzazione necessaria tramite applicazione perimssions.

### <a name="application-permissions"></a>Autorizzazioni per l'applicazione
Invece di usare ACL, API possono esporre un set di **autorizzazioni per l'applicazione** che è possibile concedere a un'applicazione.  Un'applicazione l'autorizzazione abbia concesso o meno a un'applicazione da un amministratore dell'organizzazione e possa essere usata solo per accedere ai dati di proprietà di tale organizzazione e tutti i dipendenti.  Ad esempio Microsoft Graph esposti autorizzazioni diverse per l'applicazione:

- Leggere i messaggi in tutte le cassette postali
- Leggere e scrivere mail in tutte le cassette postali
- Invio di posta come tutti gli utenti
- Si leggono dati directory
- [e altre informazioni](https://graph.microsoft.io)

Per acquisire queste autorizzazioni nell'app, è possibile eseguire la procedura seguente.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione di app

- Passare all'applicazione in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o [creare un'app](active-directory-v2-app-registration.md) se non è già.  È necessario assicurarsi che l'applicazione ha creato almeno un segreto dell'applicazione.
- Individuare la sezione **Autorizzazioni applicazione dirette** e aggiungere le autorizzazioni che richiede l'app.
- Assicurarsi che per **salvare** la registrazione di app

#### <a name="recommended-sign-the-user-into-your-app"></a>Consigliata: accede all'utente l'app

In genere durante la compilazione di un'applicazione che utilizza le autorizzazioni delle applicazioni, l'app sarà necessario disporre di una pagina/visualizzazione che consente all'amministratore di approvare le autorizzazioni dell'app.  Questa pagina può far parte del flusso di iscrizione dell'app, parte delle impostazioni dell'applicazione o un flusso di "connettersi" dedicato.  In molti casi, è opportuno per l'app visualizzare le informazioni "connettersi" visualizzazione solo dopo che un utente ha eseguito l'accesso con un aziendale o dell'istituto di istruzione account Microsoft.

L'utente l'accesso all'App consente di identificare organziation a cui appartiene l'utente prima di chiedere loro di approvare le autorizzazioni delle applicazioni.  Benché non sia strettamente necessario, è possibile consentono di creare un'esperienza più intuitiva per gli utenti dell'organizzazione.  Per accedere all'utente in, eseguire le [esercitazioni su protocollo v 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti per richiedere le autorizzazioni dall'amministratore della società, è possibile reindirizzare l'utente a v 2.0 **amministratore consenso all'endpoint**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Tenant directory che si desidera richiedere l'autorizzazione da.  Può essere fornito in formato nome descrittivo o guid.  Se non si sapere quali tenant appartiene l'utente e per consentire loro accedere con un tenant, utilizzare `common`. |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| redirect_uri | Obbligatorio | Redirect_uri nel punto in cui si desidera la risposta da inviare per l'app gestire.  Deve corrispondere esattamente una redirect_uris registrato nel portale, ad eccezione del fatto che deve essere con codificata url e può includere segmenti di percorso aggiuntive. |
| stato | consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token.  Può essere una stringa di tutto il contenuto che si desidera.  Lo stato viene utilizzato per codificare informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |

A questo punto, Azure Active Directory impone che può accedere solo un amministratore tenant per completare la richiesta.  L'amministratore verrà chiesto di approvare tutte le autorizzazioni di applicazione diretta richiesti per l'app nel portale di registrazione. 

##### <a name="successful-response"></a>Risposta corretta
Se l'amministratore approva le autorizzazioni per l'applicazione, si sarà la risposta corretta:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Tenant directory che abbia concesso o meno l'applicazione delle autorizzazioni che necessarie, in formato guid. |
| stato | Valore incluso nella richiesta che vengano restituita anche nella risposta token.  Può essere una stringa di tutto il contenuto che si desidera.  Lo stato viene utilizzato per codificare informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |
| admin_consent | Verrà impostato su `True`. |


##### <a name="error-response"></a>Risposta a un errore
Se l'amministratore non riconosce le autorizzazioni per l'applicazione, la risposta non riuscita sarà:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore.  |

Dopo aver ricevuto una risposta corretta dall'app per il provisioning di endpoint, l'app in possesso delle autorizzazioni di applicazione diretta che viene richiesta.  È possibile procedere in richiedendo un token per la risorsa desiderata.

## <a name="get-a-token"></a>Ottenere un token
Dopo aver acquistato l'autorizzazione necessaria per l'applicazione, è possibile procedere con l'acquisizione di token di accesso per API.  Per ottenere un token utilizzando il client grant credenziali, inviare una richiesta di POST per il `/token` endpoint v 2.0:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| ambito | Obbligatorio | Il valore passato per il `scope` parametro nella richiesta deve essere il resource identifier (App ID) della risorsa desiderata, apposta con la `.default` suffisso.  Per consentire ad esempio Microsoft Graph assegnato il valore deve essere `https://graph.microsoft.com/.default`.  Questo valore indica il punto finale v 2.0 di tutte le applicazione diretta autorizzazioni che è stato configurato per l'app, deve essere visualizzata un token di quelle relative alla risorsa desiderata. |
| client_secret | Obbligatorio | Il segreto applicazione generati nel portale di registrazione per l'app. |
| grant_type | Obbligatorio | Deve essere `client_credentials`. | 

#### <a name="successful-response"></a>Risposta corretta
Una risposta corretta avrà il formato:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Access | Token di accesso. L'app è possibile utilizzare questo token per eseguire l'autenticazione per la risorsa protetta, ad esempio dell'API web. |
| token_type | Indica il valore di tipo di token. L'unico tipo che supporta Azure Active Directory è `Bearer`.  |
| expires_in | Per quanto tempo il token di accesso è valido (secondi). |

#### <a name="error-response"></a>Risposta a un errore
Il formato sarà una risposta di errore:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

## <a name="use-a-token"></a>Utilizzare un token
Ora che è stato acquistato un token di, è possibile utilizzare tale token per richiedere la risorsa.  Quando scade il token, è sufficiente ripetere la richiesta per il `/token` endpoint per acquisire un token di accesso aggiornato.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Esempio di codice
Per visualizzare un esempio di un'applicazione che implementa di che il client_credentials concedere utilizzando l'amministratore consenso all'endpoint, fare riferimento a un [esempio di codice daemon v 2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
