<properties
    pageTitle="Azure Active Directory v 2.0 implicito flusso | Microsoft Azure"
    description="Creazione di applicazioni web che utilizza implementazione v 2.0 Azure Active Directory del flusso di impliciti per App singola pagina."
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

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v 2.0 protocolli - stabilimenti termali utilizzando il flusso implicito
Con l'endpoint v 2.0, è possibile accedere agli utenti in App singola pagina con un account sia personali e di lavoro o dell'istituto di istruzione da Microsoft.  Singola pagina e altre app di JavaScript in esecuzione principalmente in una faccia browser alcune interessanti richiede quando si tratta di autenticazione:

- Le caratteristiche di protezione di queste applicazioni sono molto diverse dalle applicazioni web basata su server tradizionale.
- Molti server di autorizzazione e provider di identità non supporta le richieste di CORS.
- Browser pagina intera reindirizza lontano da app diventano particolarmente invasivi per l'esperienza utente.

Queste applicazioni (pensare: AngularJS, Ember.js, React.js, e così via) Azure Active Directory supporta il flusso di concessione impliciti 2.0 OAuth.  Il flusso implicito è descritto nella [Specifica 2.0 OAuth](http://tools.ietf.org/html/rfc6749#section-4.2).  Il vantaggio principale è che consente l'app ottenere i token da Azure Active Directory senza eseguire un server di back-end scambio di credenziali.  In questo modo l'app accedere all'utente, gestire sessione e ottenere il codice JavaScript token da altri web API tutti all'interno del client.  Esistono alcune considerazioni sulla protezione importanti da considerare quando si utilizza il flusso implicito - soprattutto nel [client](http://tools.ietf.org/html/rfc6749#section-10.3) e [rappresentazione dell'utente](http://tools.ietf.org/html/rfc6749#section-10.3).

Se si desidera utilizzare il flusso implicito e Azure Active Directory per aggiungere l'autenticazione all'app JavaScript, è consigliabile che utilizzare la libreria JavaScript Apri origine, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Sono disponibili alcuni esercitazioni AngularJS [qui](active-directory-appmodel-v2-overview.md#getting-started) per iniziare.  

Tuttavia, se si preferisce evitare di utilizzare una raccolta nell'app singola pagina e inviare messaggi di protocollo a se stessi, seguire i passaggi generali seguenti.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).
    
## <a name="protocol-diagram"></a>Diagramma di protocollo
Accedi impliciti intero flusso simile alla seguente: tutti i passaggi descritti in dettaglio di seguito.

![OpenId connettere le corsie](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Per disconnettersi inizialmente l'utente in un'applicazione, è possibile inviare una richiesta di autorizzazione [OpenID connettersi](active-directory-v2-protocols-oidc.md) e ottenere un `id_token` dal punto finale v 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Fare clic sul collegamento seguente per eseguire questo richiesta! Dopo l'accesso, il browser deve essere reindirizzato al `https://localhost/myapp/` con un `id_token` nella barra degli indirizzi.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://Login.microsoftonline.com/Common/oauth2/v2.0/Authorize...</a>

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti non `common`, `organizations`, `consumers`e gli identificatori del tenant.  Per informazioni dettagliate, vedere [informazioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| response_type | Obbligatorio | Deve includere `id_token` per connettere OpenID accesso.  Comprendere anche la response_type `token`. Utilizzo `token` qui consentirà l'app ricevere un token di accesso immediatamente dal punto finale di autorizzazione senza dover apportare una seconda richiesta per l'endpoint di autorizzazione.  Se si usa il `token` response_type, il `scope` parametro deve contenere un ambito che indica la risorsa per rilasciare il token per. |
| redirect_uri | consigliati | Redirect_uri dell'app, in cui risposte autenticazione possono essere inviate e ricevute tramite l'app.  Deve corrispondere esattamente una redirect_uris registrato nel portale, ad eccezione del fatto che deve essere url codificata. |
| ambito | Obbligatorio | Un elenco delimitato da spazio degli ambiti.  Per connettere OpenID, deve includere l'ambito `openid`, che viene convertita nell'autorizzazione "Di accesso" nel consenso dell'interfaccia utente.  Facoltativamente è anche consigliabile includere il `email` o `profile` [ambiti](active-directory-v2-scopes.md) per l'accesso ai dati aggiuntivi relativi all'utente.  È possibile includere altri ambiti della richiesta per la richiesta di consenso alle diverse risorse.  |
| response_mode | consigliati | Specifica il metodo che deve essere utilizzato per restituire il risultante token l'app.  Dovrebbe essere `fragment` per il flusso di impliciti.  |
| stato | consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token.  Può essere una stringa di tutto il contenuto che si desidera.  Un valore univoco generato in modo casuale viene usato in genere per [prevenire attacchi FALSO intersito richiesta](http://tools.ietf.org/html/rfc6749#section-10.12).  Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |
| nonce | Obbligatorio | Valore incluso nella richiesta di generati da app, incluse in id_token risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione token.  Il valore è in genere una stringa casuale e univoca che può essere usata per identificare l'origine della richiesta.  |
| prompt dei comandi | facoltativo | Indica il tipo di interazione dell'utente che è necessario.  I valori validi solo in questo momento sono 'login', 'Nessuno' e 'consenso'.  `prompt=login`impone all'utente di immettere le credenziali per tale richiesta negazione single sign-in.  `prompt=none`è l'operazione - garantisce che l'utente non viene visualizzata ogni richiesta interattiva qualsiasi.  Se la richiesta non può essere completata automaticamente tramite single-sign-on, l'endpoint v 2.0 restituirà un errore.  `prompt=consent`Attiva la finestra di dialogo di consenso OAuth dopo l'accesso, chiedere all'utente di concedere le autorizzazioni per l'app. |
| login_hint | facoltativo | Può essere utilizzato per pre-campo username/posta elettronica indirizzo della pagina di accesso dell'utente, se si conosce il proprio nome utente anticipo.  Spesso App utilizzerà questo parametro durante la nuova autenticazione, visto già estratti il nome utente da un precedente accesso tramite il `preferred_username` richiedere. |
| domain_hint | facoltativo | Può essere una delle `consumers` o `organizations`.  Se incluso, non verrà eseguita il processo di rilevamento basata su posta elettronica che l'utente attraversa nella pagina di accesso v 2.0 iniziali per un'esperienza utente leggermente più semplice.  Spesso App utilizzerà questo parametro durante la nuova autenticazione tramite l'estrazione di `tid` richiedere dalla id_token.  Se il `tid` richiedere valore è `9188040d-6c67-4c5b-b112-36a304b66dad`, è necessario utilizzare `domain_hint=consumers`.  In caso contrario, utilizzare `domain_hint=organizations`. |

A questo punto, all'utente verrà chiesto di immettere le credenziali e completare l'autenticazione.  Endpoint v 2.0 garantisce inoltre che l'utente ha acconsentito alle autorizzazioni indicate nella `scope` parametro della query.  Se l'utente non ha acconsentito a qualsiasi di queste autorizzazioni, esso verrà chiesto all'utente di consenso per le autorizzazioni necessarie.  Dettagli delle [autorizzazioni, consenso e le app multi-tenant vengono fornite di seguito](active-directory-v2-scopes.md).

Dopo l'utente viene autenticato e concede consenso dell'utente, l'endpoint v 2.0 restituirà una risposta per l'app in indicata `redirect_uri`, utilizzando il metodo specificato nel `response_mode` parametro.

#### <a name="successful-response"></a>Risposta corretta

Una risposta corretta utilizzando `response_mode=fragment` e `response_type=id_token+token` l'aspetto seguente, con interruzioni di riga per migliorare la leggibilità:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Access | Se inclusi `response_type` include `token`. Token di accesso per l'app la richiesta, in questo caso Microsoft Graph.  Il token di accesso non deve essere decodificare o controllare in caso contrario, possono essere considerato come una stringa opaca. |
| token_type | Se inclusi `response_type` include `token`.  È sempre uguale a `Bearer`. |
| expires_in | Se inclusi `response_type` include `token`.  Indica il numero di secondi che il token è valido per la memorizzazione nella cache scopi. |
| ambito | Se inclusi `response_type` include `token`.  Indica l'ambito per il quale sarà valido di Access. |
| id_token | Id_token che ha richiesto l'app. È possibile utilizzare il id_token per verificare l'identità dell'utente e avviare una sessione con l'utente.  Ulteriori informazioni sulla id_tokens e il relativo contenuto è incluso nel [riferimento token endpoint v 2.0](active-directory-v2-tokens.md).  |
| stato | Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |


#### <a name="error-response"></a>Risposta a un errore
Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che in grado di gestirle in modo appropriato l'app:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |

## <a name="validate-the-idtoken"></a>Convalidare il id_token
Solo la ricezione di un id_token non sono sufficiente per eseguire l'autenticazione dell'utente. è necessario convalidare firma del id_token e verificare le attestazioni token per requisiti dell'app.  Endpoint v 2.0 utilizza [JSON Web token (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e crittografia a chiave pubblica per firmare i token e verificare che siano validi.

È possibile scegliere di convalidare il `id_token` nel client di codice, ma in genere è consigliabile consiste nell'inviare la `id_token` a un server di back-end ed eseguire la convalida sono.  Dopo avere convalidato la firma della id_token, esistono alcune delle attestazioni che è necessario verificare.  Vedere [Guida di riferimento token v 2.0](active-directory-v2-tokens.md) per ulteriori informazioni, inclusi [I token di convalida](active-directory-v2-tokens.md#validating-tokens) e [Importanti informazioni sulla firma chiave di attivazione](active-directory-v2-tokens.md#validating-tokens).  È consigliabile che si avvalgono di una raccolta per l'analisi e la convalida token - almeno per è disponibile la maggior parte delle lingue e piattaforme.
<!--TODO: Improve the information on this-->

È inoltre possibile convalidare aggiuntive sulle attestazioni in base al proprio scenario.  Di seguito sono riportate alcune operazioni di convalida comuni:

- Verifica della compatibilità utente/organizzazione è iscritta per l'app.
- Garantire l'utente ha l'autorizzazione diritti
- Verifica della compatibilità forza della funzione di autenticazione è stata eseguita, ad esempio l'autenticazione a più fattori.

Per ulteriori informazioni sulle attestazioni in un id_token, vedere il [riferimento token endpoint v 2.0](active-directory-v2-tokens.md).

Dopo avere convalidato completamente la id_token, è possibile iniziare una sessione con l'utente e in crediti il id_token consente di ottenere informazioni relative all'utente nell'app.  Queste informazioni possono essere utilizzate per visualizzare record, autorizzazioni, e così via.

## <a name="get-access-tokens"></a>Ottenere i token di accesso

Ora che è stato effettuato l'accesso all'utente l'app singola pagina, è possibile ottenere token di accesso per chiamate web API protette da Azure Active Directory, ad esempio [Microsoft Graph](https://graph.microsoft.io).  Anche se già ricevuto un token utilizzando il `token` response_type, è possibile utilizzare questo metodo per acquisire i token a risorse aggiuntive senza dover reindirizzare all'utente di accedere di nuovo.

Nel flusso OpenID Connetti/OAuth normale, si farebbe questo effettuando una richiesta per la versione 2.0 `/token` endpoint.  Tuttavia, l'endpoint v 2.0 non supporta le richieste di CORS, in modo che le chiamate AJAX per recuperare e aggiornare i token non è compresa nella domanda.  Se, tuttavia, è possibile utilizzare il flusso implicito in un iframe nascosto per ottenere nuovi token per altri web API: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Provare a copiare e incollare la seguente richiesta in una scheda del browser! (Non dimenticare di sostituire il `domain_hint` e `login_hint` i valori con i valori corretti per l'utente)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti non `common`, `organizations`, `consumers`e gli identificatori del tenant.  Per informazioni dettagliate, vedere [informazioni di base sul protocollo](active-directory-v2-protocols.md#endpoints). |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| response_type | Obbligatorio | Deve includere `id_token` per connettere OpenID accesso.  Comprendere anche altri response_types, ad esempio `code`. |
| redirect_uri | consigliati | Redirect_uri dell'app, in cui risposte autenticazione possono essere inviate e ricevute tramite l'app.  Deve corrispondere esattamente una redirect_uris registrato nel portale, ad eccezione del fatto che deve essere url codificata. |
| ambito | Obbligatorio | Un elenco delimitato da spazio degli ambiti.  Per ottenere i token, includere tutti [gli ambiti](active-directory-v2-scopes.md) necessari per la risorsa di interesse.  |
| response_mode | consigliati | Specifica il metodo che deve essere utilizzato per restituire il risultante token l'app.  Può essere una delle `query`, `form_post`, o `fragment`.  |
| stato | consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token.  Può essere una stringa di tutto il contenuto che si desidera.  Un valore univoco generato in modo casuale viene usato in genere per prevenire attacchi FALSO intersito richiesta.  Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |
| nonce | Obbligatorio | Valore incluso nella richiesta di generati da app, incluse in id_token risultante come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione token.  Il valore è in genere una stringa casuale e univoca che può essere usata per identificare l'origine della richiesta.  |
| prompt dei comandi | Obbligatorio | Per l'aggiornamento e su come ottenere i token in un iframe nascosto, è necessario utilizzare `prompt=none` per garantire che non si blocchi nella pagina di accesso v 2.0 iframe e restituisce immediatamente. |
| login_hint | Obbligatorio | Per l'aggiornamento e su come ottenere i token in un iframe nascosto, è necessario includere il nome utente dell'utente in questo suggerimento per distinguere tra più sessioni che l'utente abbia in un determinato momento. È possibile estrarre il nome utente da un precedente accesso tramite il `preferred_username` richiedere. |
| domain_hint | Obbligatorio | Può essere una delle `consumers` o `organizations`.  Per l'aggiornamento e su come ottenere i token in un iframe nascosto, è necessario includere il domain_hint nella richiesta.  È necessario estrarre il `tid` richiedere da id_token di un segno di precedente per determinare il valore da utilizzare.  Se il `tid` richiedere valore è `9188040d-6c67-4c5b-b112-36a304b66dad`, è necessario utilizzare `domain_hint=consumers`.  In caso contrario, utilizzare `domain_hint=organizations`. |

Grazie alla `prompt=none` parametro, la richiesta avrà l'esito positivo o esito negativo immediatamente e tornare all'applicazione.  Verrà inviata una risposta corretta per l'app in indicata `redirect_uri`, utilizzando il metodo specificato nel `response_mode` parametro.

#### <a name="successful-response"></a>Risposta corretta
Una risposta corretta utilizzando `response_mode=fragment` ha un aspetto analogo:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Access | Token che ha richiesto l'app. |
| token_type | È sempre uguale a `Bearer`. |
| stato | Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |
| expires_in | Per quanto tempo il token di accesso è valido (secondi). |
| ambito | Gli ambiti per cui il token di accesso è valido. |

#### <a name="error-response"></a>Risposta a un errore
Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che l'app possibile gestirli in modo appropriato.  In caso di `prompt=none`, un errore previsto sarà:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |

Se viene visualizzato questo messaggio di errore nella richiesta di iframe, l'utente deve in modo interattivo accedere nuovamente recuperare un token di nuovo.  È possibile scegliere di gestire questa situazione in modo significativo per l'applicazione.

## <a name="refreshing-tokens"></a>Aggiornamento dei token

Entrambi `id_token`s e `access_token`s scadrà dopo un breve periodo di tempo, in modo che l'app deve essere preparato per aggiornare questi token periodicamente.  Per aggiornare entrambi i tipi di token, è possibile eseguire la stessa richiesta iframe nascosti dall'alto utilizzando il `prompt=none` parametro per controllare il comportamento di Azure Active Directory.  Se si desidera ricevere un nuovo `id_token`, assicurarsi di usare `response_type=id_token` e `scope=openid`, nonché un `nonce` parametro.


## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione

Il OpenIdConnect `end_session_endpoint` non è attualmente supportato da endpoint v 2.0. Questo errore indica che l'app non è possibile inviare una richiesta all'endpoint v 2.0 per terminare una sessione e cancellare i cookie impostati da endpoint v 2.0.
Per accedere a un utente, l'app può semplicemente terminare il proprio sessione con l'utente e lasciare la sessione dell'utente con l'endpoint v 2.0-pulsante.  La volta successiva che l'utente prova ad accedere, verranno visualizzati in una pagina "scegliere account" con il proprio account attivamente firmato aggiuntivo nell'elenco.
Nella pagina, l'utente può scegliere di disconnettersi da qualunque account, terminare la sessione con l'endpoint v 2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->