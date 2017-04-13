<properties
    pageTitle="Panoramica di Azure Active Directory .NET protocollo | Microsoft Azure"
    description="In questo articolo viene descritto come utilizzare messaggi HTTP per autorizzare l'accesso alle applicazioni web e web API nel tenant di utilizzo di Azure Active Directory e OAuth 2.0."
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


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Autorizzare l'accesso alle applicazioni web mediante OAuth 2.0 e Azure Active Directory

Azure Active Directory (Azure Active Directory) Usa OAuth 2.0 che consentono di autorizzare l'accesso al web API nel tenant di Azure Active Directory e le applicazioni web. Questa guida è indipendente dal linguaggio e viene descritto come inviare e ricevere messaggi HTTP senza utilizzare uno dei nostro raccolte Apri origine.

Il flusso di codice autorizzazione OAuth 2.0 descritto nella [sezione 4.1 della specifica OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1) . Viene utilizzato per eseguire l'autenticazione e l'autorizzazione nella maggior parte dei tipi di app, incluse le applicazioni web e a livello nativo App installate.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>Flusso di autorizzazione OAuth 2.0

Un alto livello, il flusso di intero insieme delle autorizzazioni per un'applicazione di un po' analoga alla seguente:

![Flusso di codice Auth OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>Richiesta di un codice di autorizzazione

Il flusso di codice autorizzazione inizia con il client indirizza all'utente di `/authorize` endpoint. Nella convocazione, il client indica le autorizzazioni che necessarie per acquisire dall'utente. È possibile ottenere i punti finali OAuth 2.0 dalla pagina dell'applicazione nel portale classica Azure, il pulsante di **Visualizzazione endpoint** nel cassetto inferiore.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| tenant | Obbligatorio | Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti sono identificatori tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` o `common` per i token indipendente tenant |
| client_id | Obbligatorio | Id applicazione assegnata all'app quando è registrato con Azure Active Directory. È possibile trovare nel portale di gestione di Azure. Fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura** |
| response_type | Obbligatorio | Deve includere `code` per il flusso di codice di autorizzazione. |
| redirect_uri | consigliati | Redirect_uri dell'app, in cui risposte autenticazione possono essere inviate e ricevute tramite l'app.  Deve corrispondere esattamente una redirect_uris registrato nel portale, ad eccezione del fatto che deve essere url codificata.  Per App nativi e dispositivi mobili, è necessario utilizzare il valore predefinito di `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode | consigliati | Specifica il metodo che deve essere utilizzato per restituire il risultante token l'app.  Può essere rappresentato da `query` o `form_post`.  |
| stato | consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token. Un valore univoco generato in modo casuale viene usato in genere per [prevenire attacchi FALSO intersito richiesta](http://tools.ietf.org/html/rfc6749#section-10.12).  Lo stato viene usato anche per codificare le informazioni relative allo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |
| risorsa | facoltativo | App ID URI del web API (protetta delle risorse). Per trovare l'URI ID App del web API, nel portale di gestione di Azure, fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura**. |
| prompt dei comandi | facoltativo |  Indicare il tipo di interazione dell'utente che è necessario.<p> Valori validi sono: <p> *accesso*: l'utente deve dovrà eseguire nuovamente l'autenticazione. <p> *consenso*: il consenso dell'utente è stato concesso, ma è necessario aggiornare. L'utente verrà richiesto di confermare. <p> *admin_consent*: un amministratore deve essere richiesto di confermare per conto di tutti gli utenti dell'organizzazione |
| login_hint | facoltativo | Può essere utilizzato per pre-campo username/posta elettronica indirizzo della pagina di accesso dell'utente, se si conosce il proprio nome utente anticipo.  Spesso App utilizzerà questo parametro durante la nuova autenticazione, visto già estratti il nome utente da un precedente accesso tramite il `preferred_username` richiedere. |
| domain_hint | facoltativo | Fornisce un suggerimento per il tenant o il dominio che l'utente deve usare per accedere. Il valore del domain_hint è un dominio registrato per il tenant. Se il tenant è federato a una directory locale, AAD Reindirizza al server federativo tenant specificato. |

> [AZURE.NOTE] Se l'utente fa parte di un'organizzazione, un amministratore dell'organizzazione possa consenso o rifiutare per conto dell'utente oppure consentire all'utente di consenso. L'utente ha la possibilità di consenso solo quando l'amministratore lo consente.

A questo punto, l'utente verrà chiesto di immettere le credenziali e consenso per le autorizzazioni indicate nella `scope` parametro della query. Dopo l'utente viene autenticato e concede di consenso, Azure Active Directory invia una risposta per l'app nel `redirect_uri` indirizzo nella convocazione.

### <a name="successful-response"></a>Risposta corretta

Una risposta corretta Impossibile simile al seguente:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametro | Descrizione |
| -----------------------| --------------- |
| admin_consent | Il valore è True se un amministratore acconsentito a una richiesta di consenso.|
| codice | Il codice di autorizzazione richiesta dall'applicazione. L'applicazione è possibile utilizzare il codice di autorizzazione per richiedere un token di accesso per la risorsa di destinazione. |
| session_state | Valore univoco che identifica la sessione utente corrente. Questo valore è un GUID, ma deve essere considerato un valore opaco passato senza esame. |
| stato | Se un parametro di stato è incluso nella richiesta, lo stesso valore deve essere visualizzato nella risposta. È buona norma per l'applicazione per verificare che i valori di stato nella richiesta e risposta sono identici prima di utilizzare la risposta. In questo modo per rilevare [attacchi (intersito Request FORGERY)](https://tools.ietf.org/html/rfc6749#section-10.12) contro il client.  

### <a name="error-response"></a>Risposta a un errore

Risposte di errore possono essere inviate anche alla `redirect_uri` in modo che l'applicazione possibile gestirli in modo appropriato.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametro | Descrizione |
|-----------|-------------|
| Errore | Un valore di codice di errore definito nella sezione 5.2 di [OAuth 2.0 autorizzazione Framework](http://tools.ietf.org/html/rfc6749). La tabella seguente descrive i codici di errore che restituisce Azure Active Directory. |
| error_description | Una descrizione più dettagliata dell'errore. Questo messaggio non deve essere descrittivo degli utenti finali. |
| stato | Il valore di stato è un valore non riutilizzati casuale inviato nella richiesta e restituito in risposta a attacchi FALSO (CSRF) intersito richiesta. |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Utilizzare il codice di autorizzazione per richiedere un token di accesso

Ora che è stato acquistato un codice di autorizzazione ed è stata concessa l'autorizzazione dall'utente, si può riscattare il codice per un token di accesso alla risorsa desiderata mediante l'invio di una richiesta di POST per il `/token` endpoint:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | Obbligatorio |  Il `{tenant}` valore nel percorso della richiesta può essere utilizzato per controllare chi può accedere all'applicazione.  I valori consentiti sono identificatori tenant, ad esempio `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` o `contoso.onmicrosoft.com` o `common` per i token indipendente tenant |
| client_id | Obbligatorio | Id applicazione assegnata all'app quando è registrato con Azure Active Directory. È possibile trovare nel portale classica di Azure. Fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura** |
| grant_type | Obbligatorio | Deve essere `authorization_code` per il flusso di codice di autorizzazione. |
| codice | Obbligatorio | Il `authorization_code` che è stato acquistato nella sezione precedente   |
| redirect_uri | Obbligatorio | Lo stesso `redirect_uri` valore utilizzato per acquisire il `authorization_code`. |
| client_secret | necessari per web apps | Il segreto applicazione creata nel portale di registrazione di app per l'app.  Essa non utilizzare in un'applicazione nativa, perché client_secrets non possono essere archiviate in modo affidabile su dispositivi.  È necessario per web apps e web API, che hanno la possibilità di archiviare il `client_secret` in modo sicuro sul lato server. |
| risorsa | obbligatorio se specificato nella richiesta di codice autorizzazione, altrimenti facoltativo | App ID URI del web API (protetta delle risorse).
Per trovare l'URI ID App, nel portale di gestione di Azure, fare clic su **Active Directory**, fare clic sulla cartella, fare clic sull'applicazione e quindi fare clic su **Configura**.

### <a name="successful-response"></a>Risposta corretta

Azure Active Directory restituisce un token di accesso a una risposta corretta. Per ridurre al minimo le chiamate di rete dall'applicazione client e la relativa latenza associato, l'applicazione client deve memorizzare nella cache di token di accesso per la durata token specificata nella risposta OAuth 2.0. Per determinare la durata, utilizzare la `expires_in` o `expires_on` i valori dei parametri.

Se una risorsa API web restituisce un `invalid_token` codice di errore, è possibile che la risorsa ha stabilito che il token è scaduto. Se i tempi di orologio client e delle risorse sono diversi (noto come "tempi asimmetria"), la risorsa può valutare la possibilità di token è scaduto prima che il token sia deselezionato dalla cache del client. In questo caso, deselezionare il token dalla cache, anche se è all'interno di durata calcolata.

Una risposta corretta Impossibile simile al seguente:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Access | Token di accesso. L'app è possibile utilizzare questo token per eseguire l'autenticazione per la risorsa protetta, ad esempio dell'API web. |
| token_type | Indica il valore di tipo di token. L'unico tipo che supporta Azure Active Directory è titolare. Per ulteriori informazioni sui token del titolare, vedere [OAuth2.0 autorizzazione Framework: utilizzo di Token del titolare (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | Per quanto tempo il token di accesso è valido (secondi). |
| expires_on | La data di scadenza token di accesso. La data viene rappresentata come il numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene utilizzato per determinare la durata del token memorizzati nella cache. |
| risorsa | App ID URI del web API (protetta delle risorse).|
| ambito | Autorizzazioni di rappresentazione all'applicazione client. L'autorizzazione predefinita `user_impersonation`. Il proprietario della risorsa protetta può registrare altri valori in Azure Active Directory.|
| refresh_token |  Un token di aggiornamento OAuth 2.0. L'app è possibile utilizzare questo token per acquisire i token di accesso aggiuntive dopo la scadenza del token di accesso corrente.  Aggiornare i token sono permanenti e può essere utilizzati per mantenere l'accesso alle risorse per lunghi periodi di tempo. |
| id_token | Un unsigned JSON Web Token (JWT). Base64Url di può app decodificare i segmenti del token per richiedere informazioni relative all'utente che ha effettuato l'accesso. L'app è possibile memorizzare nella cache i valori e visualizzarli, ma non deve basarsi su di essi per qualsiasi autorizzazione o i limiti di sicurezza. |

### <a name="jwt-token-claims"></a>Reclami Token JWT
Token JWT il valore della `id_token` parametro può decodificare in attestazioni seguenti:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Il `id_token` parametro include i tipi seguenti di richiesta di rimborso. Per ulteriori informazioni sui token web JSON, vedere la [specifica della bozza IETF JWT](http://go.microsoft.com/fwlink/?LinkId=392344). Per ulteriori informazioni sui tipi di token e sulle attestazioni, leggere [supportati Token e tipi di attestazione](active-directory-token-and-claims.md)

| Tipo di attestazione | Descrizione |
|------------|-------------|
| AUD | Gruppo di destinatari del token. Quando il token viene eseguito a un'applicazione client, i destinatari sono la `client_id` del client.
| EXP | Data di scadenza. La data di scadenza il token. Per il token di essere valida, data/ora corrente deve essere minore o uguale alla `exp` valore. Il tempo viene rappresentato come il numero di secondi da 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino al momento in cui è stato rilasciato il token. |
| family_name | Utente ultimo nome o un cognome. L'applicazione può visualizzare questo valore. |
| given_name | Nome dell'utente. L'applicazione può visualizzare questo valore. |
| IAT | Rilasciato momento. Ora di emissione la JWT. Il tempo viene rappresentato come il numero di secondi da 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino al momento in cui è stato rilasciato il token. |
| ISS | Identifica autorità token |
| NBF | Non prima del tempo. L'ora in cui il token diventa efficace. Per il token di essere valida, data/ora corrente deve essere maggiore o uguale al valore Nbf. Il tempo viene rappresentato come il numero di secondi da 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino al momento in cui è stato rilasciato il token. |
| OID | Identificatore di oggetto (ID) dell'oggetto utente in Active Directory Azure. |
| Sub | Identificatore oggetto token. Si tratta di un identificatore permanente e non modificabile per l'utente che descrive il token. Utilizzare questo valore in logica di memorizzazione nella cache. |
| TID | Identificatore (ID) del tenant di Azure Active Directory che ha rilasciato il token del tenant. |
| unique_name | Un identificatore univoco che può essere visualizzato all'utente. Si tratta in genere un nome dell'entità utente (UPN). |
| UPN | Nome dell'entità utente dell'utente. |
| versione | Versione. La versione del token JWT, in genere 1.0. |

### <a name="error-response"></a>Risposta a un errore

Gli errori di endpoint del rilascio token sono i codici di errore HTTP, perché il client chiama l'endpoint del rilascio token direttamente. Oltre al codice di stato HTTP l'endpoint del rilascio token di Azure Active Directory restituisce anche un documento JSON con oggetti che descrive l'errore.

Una risposta di errore di esempio Impossibile simile al seguente:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |
| error_codes | Un elenco dei codici di errore specifico servizio token di sicurezza che possono risultare utili nella diagnostica. |
| timestamp | L'ora dell'errore. |
| trace_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica.  |
| correlation_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica vari componenti.|

#### <a name="http-status-codes"></a>Codici di stato HTTP

Nella tabella seguente sono elencati i codici di stato HTTP che restituisce l'endpoint del rilascio token. In alcuni casi, il codice di errore non è sufficiente per descrivere la risposta, ma in caso di errori, è necessario analizzare il documento JSON ed esaminare il codice di errore.

| Codice HTTP | Descrizione |
|-----------|-------------|
| 400       | Codice HTTP predefinito. Nella maggior parte dei casi viene in genere a causa di una richiesta non valida. Correggere e inviare la richiesta. |
| 401       | Autenticazione non riuscita. Ad esempio, la richiesta non include il parametro client_secret.|
| 403       | Autorizzazione non riuscita. Ad esempio, l'utente non dispone dell'autorizzazione per accedere alla risorsa. |
| 500       | Si è verificato un errore interno nel servizio. Ripetere la richiesta. |

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

## <a name="use-the-access-token-to-access-the-resource"></a>Utilizzare il token di accesso per accedere alla risorsa

Ora che è stata acquisito un `access_token`, è possibile utilizzare il token nelle convocazioni alle API Web, includendo con la `Authorization` intestazione. Specifica [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) viene illustrato come utilizzare i token del titolare in richieste HTTP per accedere alle risorse protette.

### <a name="sample-request"></a>Richiesta di esempio

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Risposta a un errore

Risorse protette che implementano codici di stato HTTP RFC 6750 problema. Se la richiesta non include le credenziali di autenticazione o mancante il token, la risposta include un `WWW-Authenticate` intestazione. Quando una richiesta non riesce, il server delle risorse risponde con il codice di errore e un codice di stato HTTP.

Di seguito è illustrato un esempio di una risposta di non riuscire quando la richiesta del client non include il token del titolare:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parametri di errore

| Parametro | Descrizione |
|-----------|-------------|
| authorization_uri | URI (endpoint fisico) del server di autorizzazione. Questo valore viene usato anche come chiave di ricerca per ottenere ulteriori informazioni sul server da un endpoint di individuazione. <p><p> Il cliente deve confermare che il server di autorizzazione sia attendibile. Quando la risorsa è protetto da Azure Active Directory, è sufficiente verificare che l'URL inizia con https://login.windows.net o un altro nome host che supporta Azure Active Directory. Una risorsa specifiche del tenant deve restituire sempre un'autorizzazione specifiche del tenant URI. |
| Errore | Un valore di codice di errore definito nella sezione 5.2 di [OAuth 2.0 autorizzazione Framework](http://tools.ietf.org/html/rfc6749).|
| error_description | Una descrizione più dettagliata dell'errore. Questo messaggio non deve essere descrittivo degli utenti finali.|
| resource_id | Restituisce l'identificatore univoco della risorsa. L'applicazione client può utilizzare l'identificatore come il valore di `resource` parametro quando richiede un token per la risorsa. <p><p> È molto importante per l'applicazione client per verificare questo valore, in caso contrario un servizio dannoso potrebbe essere possibile forzare attacco **elevazione dei privilegi** <p><p> La strategia consigliata per impedire un attacco consiste nel verificare che la `resource_id` soddisfa la base del web API URL che si accede. Se si accede https://service.contoso.com/data, ad esempio il `resource_id` può essere rappresentato da htttps://service.contoso.com/. L'applicazione client deve rifiutare un `resource_id` che non inizia con l'URL di base a meno che non esiste un modo affidabile alternativo per verificare l'id. |

#### <a name="bearer-scheme-error-codes"></a>Codici di errore combinazione titolare

La specifica RFC 6750 definisce i seguenti errori di risorse che utilizzano usando l'autenticazione WWW intestazione e combinazione titolare nella risposta.

| Codice di stato HTTP | Codice di errore | Descrizione | Azione di client |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | La richiesta non è corretta. Ad esempio, potrebbe essere parametro mancante o con lo stesso parametro due volte. | Correggere l'errore e riprovare. Questo tipo di errore dovrebbe essere eseguita solo durante lo sviluppo e rilevato durante i test iniziale. |
| 401 | invalid_token   | Il token di accesso è mancante, non valida o è stato revocato. Il valore del parametro error_description fornisce ulteriori dettagli. |  Richiedere un nuovo token dal server di autorizzazione. Se il nuovo token non riesce, si è verificato un errore imprevisto. Inviare un messaggio di errore all'utente e Riprova dopo ritardi casuali. |
| 403 | insufficient_scope | Il token di accesso non contiene le autorizzazioni di rappresentazione necessarie per accedere alla risorsa. | Inviare una nuova richiesta di autorizzazione per l'endpoint di autorizzazione. Se la risposta contiene il parametro scope, usare il valore di ambito nella richiesta per la risorsa. |
| 403 | insufficient_access | L'oggetto del token non dispone delle autorizzazioni necessarie per accedere alla risorsa. | Richiedere all'utente per usare un altro account o per richiedere le autorizzazioni per la risorsa specificata. |

## <a name="refreshing-the-access-tokens"></a>Aggiornamento dei token di accesso

Token di accesso sono temporanei e devono essere aggiornati dopo la scadenza per continuare l'accesso alle risorse. Che è possibile aggiornare il `access_token` inviando un altro `POST` richiesta del `/token` endpoint, ma questa volta fornire la `refresh_token` anziché il `code`.

Aggiornare i token non dispone di durata specificata. In genere, la durata di token di aggiornamento è relativamente lunga. Tuttavia, in alcuni casi, i token di aggiornamento scadono è stati revocati o dispongono di privilegi sufficienti per l'azione desiderata. L'applicazione deve aspettarsi e gestire errori restituiti da endpoint del rilascio token correttamente.

Quando si riceve una risposta con un errore di token di aggiornamento, eliminare il token di aggiornamento corrente e richiedere un nuovo codice di autorizzazione o token accesso. In particolare, quando tramite un aggiornamento token nel flusso di concessione di codice di autorizzazione, se si riceve una risposta con il `interaction_required` o `invalid_grant` codici di errore, eliminare il token di aggiornamento e chiedere a un nuovo codice di autorizzazione.

Un esempio di richiedere all'endpoint **specifiche del tenant** (è anche possibile usare l'endpoint **comuni** ) per ottenere un nuovo accesso token utilizzando un token di aggiornamento è simile alla seguente:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parametro | Descrizione |
|-----------|-------------|
| Access | Il nuovo token di accesso che è stata richiesta.|
| expires_in   | Durata rimanente del token in secondi. Un valore tipico è 3600 (un'ora). |
| expires_on   | Data e ora in cui il token di scadenza. La data viene rappresentata come il numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. |
| refresh_token | Nuovo refresh_token OAuth 2.0 che può essere utilizzato per richiedere nuovi token di accesso quando scade quella in questa risposta. |
| risorsa     | Identifica la risorsa protetta token di accesso può essere usata per l'accesso. |
| ambito        | Autorizzazioni di rappresentazione all'applicazione client nativi. L'autorizzazione predefinita è **user_impersonation**. Il proprietario della risorsa di destinazione può registrare valori alternativi in Azure Active Directory. |
| token_type   | Il tipo di token. L'unico valore supportato è **titolare**. |

### <a name="successful-response"></a>Risposta corretta

Una risposta corretta token si presenterà:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>Risposta a un errore

Una risposta di errore di esempio Impossibile simile al seguente:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parametro | Descrizione |
| ----------------------- | ------------------------------- |
| Errore | Una stringa di codice di errore che può essere utilizzata per classificare i tipi di errori che si verificano e può essere usata per rispondere agli errori. |
| error_description | Messaggio di errore specifici che consente di identificare uno sviluppatore causa dell'errore di autenticazione.  |
| error_codes | Un elenco dei codici di errore specifico servizio token di sicurezza che possono risultare utili nella diagnostica. |
| timestamp | L'ora dell'errore. |
| trace_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica.  |
| correlation_id | Identificatore univoco per la richiesta che può risultare utili nella diagnostica vari componenti.|

Per una descrizione dei codici di errore e l'azione client consigliato, vedere [codici di errore per gli errori di token endpoint](#error-codes-for-token-endpoint-errors).
