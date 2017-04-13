<properties
    pageTitle="Azure Active Directory v 2.0 ambiti, autorizzazioni e consenso | Microsoft Azure"
    description="Descrizione dell'autorizzazione nell'endpoint v 2.0 Azure Active Directory, inclusi gli ambiti, autorizzazioni e consenso dell'utente."
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>Ambiti, autorizzazioni e consenso endpoint v 2.0

App che si integrano con Azure Active Directory seguono un modello di autorizzazione che consente agli utenti controllare come un'app può accedere ai dati.  È stato aggiornato l'implementazione v 2.0 di questo modello di autorizzazione Modifica come un'app deve interagire con Azure Active Directory.  In questo argomento illustra i concetti di base di questo modello di autorizzazione, inclusi gli ambiti, autorizzazioni e consenso dell'utente.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="scopes--permissions"></a>Ambiti e autorizzazioni

Azure Active Directory implementato il protocollo di autorizzazione [OAuth 2.0](active-directory-v2-protocols.md) , un metodo per consentire a un'app di terze parti 3 ° accedere alle risorse web ospitato per conto di un utente.  Tutte le risorse ospitate sul web che si integrano con Azure Active Directory avrà un identificatore di risorsa o **App ID URI**.  Ad esempio, alcune delle risorse web ospitato Microsoft includono:

- Office 365 unificata posta API:`https://outlook.office.com`
- API di Azure Active Directory grafico:`https://graph.windows.net`
- Microsoft Graph:`https://graph.microsoft.com`

Lo stesso avviene per le risorse di terze parti 3 ° che è integrato con Azure Active Directory.  Una di queste risorse inoltre possibile definire un set di autorizzazioni che può essere utilizzato per suddividere la funzionalità di quella risorsa in sezioni più piccole.  Se ad esempio Microsoft Graph è definita alcune autorizzazioni:

- Leggere il calendario di un utente
- Scrivere al calendario di un utente
- Inviare posta elettronica come un utente
- [e altre informazioni](https://graph.microsoft.io)

Definendo queste autorizzazioni, la risorsa può avere un controllo preciso sui dati e come è esposto all'esterno.  Un'app di terze parti 3 ° può richiedere quindi queste autorizzazioni a un utente finale - e l'utente finale deve approvare le autorizzazioni prima che l'app può essere utilizzato per suo conto.  Per la suddivisione in blocchi funzionalità della risorsa nel set di autorizzazioni più piccole, è possibile compilate 3 ° app di terze parti per richiedere solo le autorizzazioni necessarie per eseguire l'applicazione prevista.  Consente inoltre agli utenti finali di sapere esattamente come un'app utilizzerà i dati, in modo che siano più sicuri che l'app non funziona con azione di malintenzionati.

In Azure Active Directory e OAuth, queste autorizzazioni sono noti come **ambiti**.  Potrebbe essere anche visualizzata loro denominato **oAuth2Permissions**.  Un ambito è rappresentato in Azure Active Directory come valore stringa.  Continuare con l'esempio di Microsoft Graph, il valore dell'ambito per ogni autorizzazione è:

- Leggere il calendario dell'utente:`Calendar.Read`
- Scrivere al calendario di un utente:`Mail.ReadWrite`
- Inviare posta elettronica come un utente:`Mail.Send`

Un'app possibile richiedere il queste autorizzazioni, specificare gli ambiti richieste all'endpoint v 2.0, come descritto di seguito.

## <a name="openid-connect-scopes"></a>Connettere OpenId ambiti

L'implementazione v 2.0 di OpenID Connect è pochi ambiti ben definiti che non si applicano a qualsiasi risorsa particolare - `openid`, `email`, `profile`, e `offline_access`.

#### <a name="openid"></a>OpenId

Se un'app esegue accesso con [OpenID connessione](active-directory-v2-protocols.md#openid-connect-sign-in-flow), è necessario richiedere la `openid` ambito.  Il `openid` ambito verrà visualizzato nella finestra di consenso account di lavoro come l'autorizzazione "Di accesso" e nella schermata di consenso personale dell'account Microsoft come l'autorizzazione "Visualizzare il profilo personale e connettersi all'App e servizi con il proprio account Microsoft".  Questa autorizzazione consente di ricevere un identificatore univoco per l'utente in forma di un'app di `sub` richiedere.  Ha anche l'app di accedere all'endpoint informazioni utente.  Il `openid` ambito può essere utilizzata anche all'endpoint token v 2.0 per acquisire id_tokens, che possono essere utilizzati per proteggere le chiamate HTTP tra i diversi componenti di un'app.

#### <a name="email"></a>Posta elettronica

Il `email` ambito può essere incluso lungo il `openid` limitare l'ambito e tutti gli altri.  Consente l'accesso app all'indirizzo di posta elettronica principale dell'utente in forma del `email` richiedere.  Il `email` Richiedi solo da includere nel token se un indirizzo di posta elettronica è associato l'account utente, non è sempre le maiuscole/minuscole.  Se Usa il `email` ambito, l'app deve essere preparato per gestire il caso in cui il `email` richiesta non esiste nel token.

#### <a name="profile"></a>Profilo

Il `profile` ambito può essere incluso lungo il `openid` limitare l'ambito e tutti gli altri.  Consente l'app di accedere a un'ampia varietà di informazioni relative all'utente.  Include, ma non è limitato al nome dell'utente, cognome, nome utente preferito, ID oggetto e così via.  Per un elenco completo dei crediti profilo disponibili in id_tokens per un utente specifico, fare riferimento alla [Guida di riferimento token v 2.0](active-directory-v2-tokens.md).

#### <a name="offlineaccess"></a>Offline_access

Il [ `offline_access` ambito](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) consente l'app per accedere alle risorse per conto dell'utente per un periodo di tempo.  Nella schermata di consenso account di lavoro, l'ambito verrà visualizzato come l'autorizzazione "Accesso ai dati in qualsiasi momento".  Nella schermata account consenso del personale Microsoft, verrà visualizzato come l'autorizzazione "Accesso in qualsiasi momento le informazioni personali".  Quando un utente approva la `offline_access` ambito, l'app viene abilitato per la ricezione di token di aggiornamento dal punto finale di token v 2.0.  Aggiornare i token sono permanenti e per consentire l'app acquisire nuovi token di accesso, come quelli meno recenti scadono.

Se l'app non viene richiesta la `offline_access` ambito, non verranno ricevuti refresh_tokens.  Questo errore indica che quando si Riscatta un authorization_code nel [flusso di codice autorizzazione OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), si riceveranno solo nuovamente un Access dalla `/token` endpoint.  Tale Access continuerà a essere valido per un breve periodo di tempo (in genere un'ora), ma scadrà alla fine.  Eseguire il backup in tale momento, l'app sarà necessario reindirizzare l'utente per il `/authorize` endpoint per recuperare un nuovo authorization_code.  Durante il reindirizzamento, l'utente può o non è necessario immettere nuovamente le credenziali o un nuovo consenso autorizzazioni, a seconda del tipo il tipo di app.

Per ulteriori informazioni su come accedere e utilizzare i token l'aggiornamento, fare riferimento al [riferimento al protocollo v 2.0](active-directory-v2-protocols.md).


## <a name="requesting-individual-user-consent"></a>Richiesta di consenso dell'utente singolo

In una richiesta di autorizzazione [OpenID connettersi o 2.0 OAuth](active-directory-v2-protocols.md) un'app può richiedere le autorizzazioni necessarie utilizzando la `scope` parametro della query.  Ad esempio quando un utente accede a un'app, l'app da inviare una richiesta simile alla seguente (con interruzioni di riga per migliorare la leggibilità):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Il `scope` parametro è un elenco delimitato da spazio degli ambiti che richiede l'app.  Ogni singolo ambito è indicato aggiungendo il valore dell'ambito per l'identificatore della risorsa (App ID URI).  La richiesta sopra indica che l'app necessaria l'autorizzazione per il calendario dell'utente di leggere e inviare posta elettronica come l'utente.

Dopo l'immissione delle credenziali, l'endpoint v 2.0 controllerà per un record corrispondente di **consenso all'utente**.  Se l'utente non ha acconsentito per le autorizzazioni richieste in passato, l'endpoint v 2.0 chiederà all'utente di concedere le autorizzazioni necessarie.  

![Schermata di consenso Account di lavoro](../media/active-directory-v2-flows/work_account_consent.png)

Quando l'utente approva l'autorizzazione, il consenso verrà registrato in modo che l'utente non è necessario consenso nuovamente su componenti aggiuntivi di accesso successivi.

## <a name="requesting-consent-for-an-entire-tenant"></a>Richiesta di consenso per un intero tenant

Spesso quando un'organizzazione acquista una licenza o un abbonamento a un'applicazione, desiderano completamente provisioning per tutti i dipendenti.  Come parte del processo, un amministratore aziendale può concedere il consenso dell'applicazione agire per conto di un dipendente.  I dipendenti di tale organizzazione concedendo consenso per un intero tenant, non saranno l'esperienza alla schermata di consenso per tale applicazione.

Per richiedere consenso per tutti gli utenti in un tenant, l'applicazione consente l' **amministratore di consenso all'endpoint**, descritto di seguito.

## <a name="admin-restricted-scopes"></a>Amministrazione limitata ambiti

Alcune autorizzazioni ad alta privilegi ecosistema Microsoft possono essere contrassegnati come **amministrazione limitata**.  Esempi di tali ambiti includono:

- Si leggono dati directory di organizaion:`Directory.Read`
- Dati di scrittura alla directory dell'organizzazione:`Directory.ReadWrite`
- Lettura dei gruppi di sicurezza nella directory dell'organizzazione:`Groups.Read.All`

Quando un utente consumer può concedere un accesso di applicazione a tali dati, la concessione dell'accesso allo stesso set di dati aziendali importanti sono limitati per gli utenti dell'organizzazione.  Se l'applicazione richiede l'accesso a una di queste autorizzazioni da un utente dell'organizzazione, l'utente verrà visualizzato un messaggio di errore che indica che non siano autorizzati a consenso alle autorizzazioni dell'app.

Se l'app richiede l'accesso a questi ambiti con restrizioni amministratore per le organizzazioni, è consigliabile richiedere direttamente da un amministratore di azienda che utilizza l' **amministratore di consenso all'endpoint**, descritto di seguito.

Quando un amministratore concede queste autorizzazioni tramite l'endpoint di consenso dell'utente di amministratore, consenso verrà concesse per tutti gli utenti nel tenant, come descritto in precedenza.

## <a name="using-the-admin-consent-endpoint"></a>Utilizzo di un endpoint di consenso dell'utente di amministratore

Eseguendo la procedura seguente, l'app sarà possibile raccogliere le autorizzazioni per tutti gli utenti in un tenant specificato, inclusi gli ambiti amministrazione limitata.  Per visualizzare un esempio di codice che implementa verrà procedura riportata di seguito, fare riferimento ad [esempio ambiti limitate amministratore](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Richiedere le autorizzazioni nel portale di registrazione di app

- Passare all'applicazione in [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o [creare un'app](active-directory-v2-app-registration.md) se non è già.
- Individuare la sezione **Autorizzazioni di Microsoft Graph** e aggiungere le autorizzazioni che richiede l'app.
- Assicurarsi che per **salvare** la registrazione di app

#### <a name="recommended-sign-the-user-into-your-app"></a>Consigliata: accede all'utente l'app

In genere durante la creazione di un'applicazione che utilizza l'amministratore di consenso all'endpoint, l'app sarà necessario disporre di una pagina/visualizzazione che consente all'amministratore di approvare le autorizzazioni dell'app.  Questa pagina può far parte del flusso di iscrizione dell'app, parte delle impostazioni dell'applicazione o un flusso di "connettersi" dedicato.  In molti casi, è opportuno per l'app visualizzare le informazioni "connettersi" visualizzazione solo dopo che un utente ha eseguito l'accesso con un aziendale o dell'istituto di istruzione account Microsoft.

L'utente l'accesso all'App consente di identificare organziation a cui appartiene l'amministratore prima di chiedere loro di approvare le autorizzazioni necessarie.  Benché non sia strettamente necessario, è possibile consentono di creare un'esperienza più intuitiva per gli utenti dell'organizzazione.  Per accedere all'utente in, eseguire le [esercitazioni su protocollo v 2.0](active-directory-v2-protocols.md).

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
| tenant | Obbligatorio | Tenant directory che si desidera richiedere l'autorizzazione da.  Può essere fornito in formato nome descrittivo o guid. |
| client_id | Obbligatorio | Id applicazione assegnata l'app che il portale di registrazione ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)). |
| redirect_uri | Obbligatorio | Redirect_uri nel punto in cui si desidera la risposta da inviare per l'app gestire.  Deve corrispondere esattamente una redirect_uris registrato nel portale. |
| stato | consigliati | Valore incluso nella richiesta che vengano restituita anche nella risposta token.  Può essere una stringa di tutto il contenuto che si desidera.  Lo stato viene utilizzato per codificare informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la visualizzazione in. |

A questo punto, Azure Active Directory impone che può accedere solo un amministratore tenant per completare la richiesta.  L'amministratore verrà chiesto di approvare tutte le autorizzazioni richieste per l'app nel portale di registrazione. 

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

Dopo aver ricevuto una risposta corretta dal punto finale di consenso amministrazione, l'app in possesso delle autorizzazioni che sono richieste.  È possibile procedere in richiedendo un token per la risorsa desiderata, come descritto di seguito.

## <a name="using-permissions"></a>Utilizzo delle autorizzazioni

Dopo l'utente acconsente alle autorizzazioni per l'app, l'app può acquisire token di accesso che rappresentano autorizzazioni dell'app per accedere a una risorsa in alcune capacità.  Un token di accesso specificato può essere usato solo per un singolo resorce, ma con codifica all'interno sarà ogni autorizzazione che è stata concessa l'app per tale risorsa.  Per acquisire un token di accesso, l'app è possibile apportare una richiesta all'endpoint token v 2.0:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Token di accesso risultante può quindi essere usato in richieste alla risorsa, esso verrà indica in modo affidabile la risorsa che l'app disponga delle autorizzazioni appropriate per eseguire una determinata attività.  

Per ulteriori informazioni su come acquistare token di accesso e il protocollo OAuth 2.0, vedere il [riferimento al protocollo endpoint v 2.0](active-directory-v2-protocols.md).
