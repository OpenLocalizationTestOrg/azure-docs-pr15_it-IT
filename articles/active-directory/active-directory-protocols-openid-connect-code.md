<properties
    pageTitle="Panoramica di Azure Active Directory .NET protocollo | Microsoft Azure"
    description="In questo articolo viene descritto come utilizzare messaggi HTTP per autorizzare l'accesso al web API nel tenant di utilizzo di Azure Active Directory e OpenID connettersi e le applicazioni web."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Autorizzare l'accesso alle applicazioni web mediante la connessione OpenID e Azure Active Directory

[Connettere OpenID](http://openid.net/specs/openid-connect-core-1_0.html) è un livello di identità semplice basato su protocollo OAuth 2.0. 2.0 OAuth definisce i meccanismi per ottenere e utilizzare **i token di accesso** per accedere alle risorse protette, ma non definiscono metodi standard per fornire le informazioni sull'identità. Connettere OpenID implementata autenticazione come un'estensione per il processo di autorizzazione OAuth 2.0, fornire informazioni degli utenti finali sotto forma di un `id_token` che consente di verificare l'identità dell'utente, nonché vengono fornite informazioni di base del profilo utente.

Connettere OpenID è l'indicazione se si sta creando un'applicazione web che è ospitata su un server e accedervi tramite un browser.

## <a name="authentication-flow-using-openid-connect"></a>Flusso di autenticazione con OpenID connessione

Il flusso di accesso più semplice contiene la procedura seguente: ciascuno di essi viene descritto in dettaglio riportata di seguito.

![OpenId connettersi flusso di autenticazione](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>Inviare la richiesta di accesso

Quando l'applicazione web richiede l'autenticazione dell'utente, è necessario indicare all'utente di `/authorize` endpoint. La richiesta è simile alla prima frazione di di [OAuth 2.0 autorizzazione codice flusso](active-directory-protocols-oauth-code.md), con alcune importanti differenze:

- La richiesta deve includere l'ambito `openid` nel `scope` parametro.
- Il `response_type` parametro deve includere `id_token`.
- Deve includere la richiesta di `nonce` parametro.

In modo che una richiesta di esempio sarà simile alla seguente:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti sono identificatori tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` o `common` per i token indipendente tenant |
| client_id | Obbligatorio | Id applicazione assegnata all'app quando è registrato con Azure Active Directory. È possibile trovare nel portale classica di Azure. Fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura** |
| response_type | Obbligatorio | Deve includere `id_token` per connettere OpenID accesso.  Comprendere anche altri response_types, ad esempio `code`. |
| ambito | Obbligatorio | Un elenco delimitato da spazio degli ambiti.  Per connettere OpenID, deve includere l'ambito `openid`, che viene convertita nell'autorizzazione "Di accesso" nel consenso dell'interfaccia utente.  È possibile includere altri ambiti della richiesta per la richiesta di consenso dell'utente. |
| nonce | Obbligatorio | Un valore incluso nella richiesta di generati da app, che verranno incluse nella finestra di `id_token` come attestazione.  L'app può verificare questo valore per ridurre gli attacchi di riproduzione token.  Il valore è in genere una stringa casuale e univoco o GUID che può essere utilizzato per identificare l'origine della richiesta.  |
| redirect_uri | consigliati | Redirect_uri dell'app, in cui risposte autenticazione possono essere inviate e ricevute tramite l'app.  Deve corrispondere esattamente una redirect_uris registrato nel portale, ad eccezione del fatto che deve essere url codificata. |
| response_mode | consigliati | Specifica il metodo che deve essere utilizzato per inviare authorization_code risultante tornare all'app.  Sono supportati i valori `form_post` per *formato HTTP post* o `fragment` per *frammento di URL*.  Per le applicazioni web è consigliabile usare `response_mode=form_post` per garantire il trasferimento più sicuro di token all'applicazione.  
| stato | consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token.  Può essere una stringa di tutto il contenuto che si desidera.  Un valore univoco generato in modo casuale viene usato in genere per [prevenire attacchi FALSO intersito richiesta](http://tools.ietf.org/html/rfc6749#section-10.12).  Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |
| prompt dei comandi | facoltativo | Indica il tipo di interazione dell'utente che è necessario.  I valori validi solo in questo momento sono 'login', 'Nessuno' e 'consenso'.  `prompt=login`impone all'utente di immettere le credenziali per tale richiesta negazione single sign-in.  `prompt=none`è l'operazione - garantisce che l'utente non viene visualizzata ogni richiesta interattiva qualsiasi.  Se la richiesta non può essere completata automaticamente tramite single-sign-on, l'endpoint verrà restituito un errore.  `prompt=consent`Attiva la finestra di dialogo di consenso OAuth dopo l'utente accede, chiedere all'utente di concedere le autorizzazioni per l'app. |
| login_hint | facoltativo | Può essere utilizzato per pre-campo username/posta elettronica indirizzo della pagina di accesso dell'utente, se si conosce il proprio nome utente anticipo.  Spesso App utilizzerà questo parametro durante la nuova autenticazione, visto già estratti il nome utente da un precedente accesso tramite il `preferred_username` richiedere. |


A questo punto, all'utente verrà chiesto di immettere le credenziali e completare l'autenticazione.

### <a name="sample-response"></a>Risposta di esempio

Una risposta di esempio, dopo l'utente autenticato, potrebbe simile al seguente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id_token | Il `id_token` che ha richiesto l'app. È possibile utilizzare il `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente.  |
| stato | Valore incluso nella richiesta che vengano restituita anche nella risposta token. Un valore univoco generato in modo casuale viene usato in genere per [prevenire attacchi FALSO intersito richiesta](http://tools.ietf.org/html/rfc6749#section-10.12).  Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |

### <a name="error-response"></a>Risposta a un errore
Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che in grado di gestirle in modo appropriato l'app:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
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

## <a name="validate-the-idtoken"></a>Convalidare il id_token

Solo la ricezione di un `id_token` non sono sufficienti per eseguire l'autenticazione dell'utente. è necessario convalidare la firma e sulle attestazioni in verificare la `id_token` per requisiti dell'app. L'endpoint di Azure Active Directory utilizza JSON Web token (JWTs) e crittografia a chiave pubblica per firmare i token e verificare che siano validi.

È possibile scegliere di convalidare il `id_token` nel client di codice, ma in genere è consigliabile consiste nell'inviare la `id_token` a un server di back-end ed eseguire la convalida sono. Dopo avere convalidato la firma del `id_token`, esistono alcune delle attestazioni è necessario verificare.

È inoltre possibile convalidare aggiuntive sulle attestazioni in base al proprio scenario. Di seguito sono riportate alcune operazioni di convalida comuni:

- Verifica della compatibilità utente/organizzazione è iscritta per l'app.
- Garantire l'utente ha l'autorizzazione diritti
- Verifica della compatibilità forza della funzione di autenticazione è stata eseguita, ad esempio l'autenticazione a più fattori.

Dopo avere convalidato completamente la `id_token`, è possibile iniziare una sessione con l'utente e utilizzare le richieste nel `id_token` per ottenere informazioni relative all'utente nell'app. Queste informazioni possono essere utilizzate per visualizzare record, autorizzazioni, e così via. Per ulteriori informazioni sui tipi di token e sulle attestazioni, leggere [supportati Token e tipi di attestazione](active-directory-token-and-claims.md).

## <a name="send-a-sign-out-request"></a>Inviare una richiesta di disconnessione

Quando si desidera accedere l'utente dall'app, non è sufficiente cancellare i cookie dell'app o alla fine in caso contrario la sessione con l'utente.  È inoltre necessario reindirizzare all'utente di `end_session_endpoint` per Disconnetti.  Se non si riesce a tale scopo, l'utente potrà eseguire nuovamente l'autenticazione per l'app senza immettere nuovamente le proprie credenziali perché essi avranno una single sign-on sessione valido con l'endpoint di Azure Active Directory.

È possibile reindirizzare semplicemente all'utente di `end_session_endpoint` elencato nel documento di metadati OpenID connettersi:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | consigliati | L'URL che l'utente deve reindirizzato alla dopo la disconnessione ha esito positivo.  Se non è incluso, l'utente verrà visualizzato un messaggio di tipo generico.  |

## <a name="token-acquisition"></a>Acquisizione di immagini di token

Molte applicazioni web necessario non solo eseguire l'accesso utente in, ma anche accedere a un servizio web per conto di tale utente che usa OAuth. Questo scenario combina OpenID connessione per l'autenticazione utente durante l'acquisizione contemporaneamente un `authorization_code` che può essere utilizzato per ottenere `access_tokens` utilizzando il codice di autorizzazione OAuth flusso.

## <a name="get-access-tokens"></a>Ottenere i token di accesso

Per acquisire token di accesso, è necessario modificare la richiesta di accesso dall'alto:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

Inclusi gli ambiti di autorizzazione nella richiesta e usando `response_type=code+id_token`, il `authorize` endpoint garantisce che l'utente ha acconsentito alle autorizzazioni indicate nella `scope` parametro di query e restituire l'app un codice di autorizzazione di exchange per un token di accesso.

### <a name="successful-response"></a>Risposta corretta

Una risposta corretta utilizzando `response_mode=form_post` ha un aspetto analogo:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| id_token | Il `id_token` che ha richiesto l'app. È possibile utilizzare il `id_token` per verificare l'identità dell'utente e avviare una sessione con l'utente. |
| codice | Authorization_code che ha richiesto l'app. L'app è possibile utilizzare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. Authorization_codes sono molto temporanei, in genere che scadano dopo 10 minuti. |
| stato | Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. L'app è necessario verificare che i valori di stato nella richiesta e risposta sono identici. |

### <a name="error-response"></a>Risposta a un errore

Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che in grado di gestirle in modo appropriato l'app:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |

Per una descrizione dei codici di errore e la loro azione client consigliato, vedere [codici di errore per gli errori di endpoint di autorizzazione](#error-codes-for-authorization-endpoint-errors).

Dopo aver ricevuto l'autorizzazione `code` e un `id_token`, è possibile accedere all'utente e ottenere token di accesso per conto suo.  Per accedere all'utente in, è necessario convalidare il `id_token` esattamente come descritto sopra. Per ottenere i token di accesso, è possibile eseguire la procedura descritta nella [documentazione protocollo OAuth](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).
