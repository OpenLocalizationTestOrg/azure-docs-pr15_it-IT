<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Tipi di token emessi il B2C di Azure Active Directory."
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


# <a name="azure-ad-b2c-token-reference"></a>Azure Active Directory B2C: Guida di riferimento Token

Azure Active Directory (Azure Active Directory) B2C genera diversi tipi di token di sicurezza durante l'elaborazione di ogni [flusso di autenticazione](active-directory-b2c-apps.md). Questo documento descrive il formato, caratteristiche di protezione e il contenuto di ogni tipo di token.

## <a name="types-of-tokens"></a>Tipi di token

Azure Active Directory B2C supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-b2c-reference-protocols.md), che utilizza token di accesso e i token di aggiornamento. Supporta anche l'autenticazione e accesso tramite [OpenID connettersi](active-directory-b2c-reference-protocols.md), che introduce un terzo tipo di token: token ID. Ognuna di queste token viene rappresentato come token del titolare.

Un token del titolare è un token di sicurezza leggera che concede l'accesso "titolare" a una risorsa protetta. Il titolare è tutte le parti che possono presentare il token. Azure Active Directory necessario eseguire l'autenticazione una festa prima che possa ricevere un token del titolare. Ma se i passaggi necessari non sono prese per proteggere il token in trasmissione e lo spazio di archiviazione, può essere intercettare e utilizzato da una parte indesiderata. Alcuni token di sicurezza disporre di un meccanismo incorporato per impedire che averli autorizzati, ma i token del titolare non questo meccanismo. Si devono avvenire in un canale sicuro, ad esempio di protezione a livello di trasporto (HTTPS).

Se un token del titolare viene trasmesso all'esterno di un canale sicuro, una festa dannosa possa utilizzare un attacco uomo secondo per acquisire il token e usarlo per ottenere accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza applicano quando i token del titolare vengono archiviati o memorizzati nella cache per successivi utilizzi. Sempre assicurarsi che l'app trasmette e archivia i token del titolare in modo sicuro.

Per ulteriori considerazioni sulla protezione sui token del titolare, vedere [RFC 6750 sezione 5](http://tools.ietf.org/html/rfc6750).

Molte di token che problemi di Azure Active Directory B2C implementate come token web JSON (JWTs). Un JWT è una soluzione compatta, attendibili URL per il trasferimento di informazioni tra due parti. JWTs contengono informazioni sulle attestazioni anche detta. Si tratta asserzioni informazioni relative al titolare e l'oggetto del token. Sulle attestazioni in JWTs sono oggetti JSON che codificati e serializzati per la trasmissione. Poiché JWTs rilasciato da Azure Active Directory B2C sono stato effettuato l'accesso ma non crittografati, è possibile controllare facilmente il contenuto di un JWT per eseguirne il debug. Sono disponibili diversi strumenti che può eseguire questa operazione, incluse [calebb.net](http://calebb.net). Per ulteriori informazioni su JWTs, fare riferimento alle [specifiche JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Token ID

Token ID è una forma di token di sicurezza che riceve l'app da B2C di Active Directory Azure `authorize` e `token` i punti finali. Token ID sono rappresentati come [JWTs](#types-of-tokens)e contengono delle attestazioni che è possibile utilizzare per identificare gli utenti nell'app. Quando i token ID vengono acquisiti dal `authorize` endpoint, spesso utilizzati per accedere agli utenti di applicazioni web. Quando i token ID vengono acquisiti dal `token` endpoint, e possono essere inviati in richieste HTTP durante le comunicazioni tra due componenti della stessa applicazione o servizio. È possibile utilizzare le richieste in un token di ID per adattarlo. Vengono in genere utilizzati per visualizzare informazioni sull'account o per prendere decisioni di controllo di accesso in un'app.  

Token ID hanno eseguito l'accesso, ma non crittografati al momento. Quando l'app o API riceve un token di ID, è necessario [convalidare la firma](#token-validation) per verificare che il token è autentico. L'app o API inoltre necessario convalidare alcune richieste nel token a provare valido. In base ai requisiti di scenario, sulle attestazioni convalidate da un'app possono variare, ma l'app è necessario eseguire alcune [comuni Richiedi convalida](#token-validation) in ogni scenario.

#### <a name="sample-id-token"></a>Token ID di esempio
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Token di accesso

Token di accesso è anche un modulo di token di sicurezza che riceve l'app da B2C di Active Directory Azure `authorize` e `token` i punti finali. Token di accesso sono rappresentati anche come [JWTs](#types-of-tokens)e contengono delle attestazioni che è possibile utilizzare per identificare gli utenti i servizi web e le API.

Token di accesso sono stato effettuato l'accesso, ma non sono molto simili a token id e crittografata al momento.  Token di accesso dovrebbero essere usati per accedere ai servizi web e le API e per identificare ed eseguire l'autenticazione utente in tali servizi.  Tuttavia, non forniscono qualsiasi asserzione dell'autorizzazione in tali servizi.  Vale a dire che la `scp` Richiedi in token di accesso non limitare o in caso contrario rappresentano l'accesso di cui è stata concessa l'oggetto del token.

Quando l'API riceve un token di accesso, è necessario [convalidare la firma](#token-validation) per verificare che il token è autentico. L'API inoltre necessario convalidare alcune richieste nel token a provare valido. In base ai requisiti di scenario, sulle attestazioni convalidate da un'app possono variare, ma l'app è necessario eseguire alcune [comuni Richiedi convalida](#token-validation) in ogni scenario.

### <a name="claims-in-id--access-tokens"></a>Sulle attestazioni in ID e token di accesso

Quando si usa B2C di Azure Active Directory, è necessario un controllo preciso sul contenuto dei token. È possibile configurare [i criteri](active-directory-b2c-reference-policies.md) per l'invio di determinati set di dati utente in attestazioni che richiede l'app per le operazioni. Queste domande possono includere le proprietà standard, ad esempio l'utente `displayName` e `emailAddress`. Possono inoltre includere [attributi utente personalizzati](active-directory-b2c-reference-custom-attr.md) che è possibile definire nella directory B2C. Ogni ID e accesso token ricevuti conterrà un determinato insieme ai reclami relativi alla protezione. Le applicazioni è possono utilizzare le richieste di autenticarsi utenti e le richieste.

Si noti che le richieste nel token ID non vengono restituite in un ordine specifico. Inoltre, è possono inserire nuove attestazioni nei token di ID in qualsiasi momento. L'app non dovrebbe interrompere come vengono introdotti nuovi sulle attestazioni. Ecco le attestazioni che si prevedono l'esistenza di ID e accesso token emessi Azure Active Directory B2C. Reclami aggiuntive verranno determinati dai criteri. Per l'esercitazione, provare il controllo delle attestazioni nel token di ID di esempio incollandolo nella [calebb.net](http://calebb.net). Sono disponibili ulteriori dettagli nella [specifica OpenID connettersi](http://openid.net/specs/openid-connect-core-1_0.html).

| Nome | Richiesta di rimborso | Valore di esempio | Descrizione |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Gruppo di destinatari | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Richiedi un gruppo di destinatari identificano il destinatario del token. Per Azure Active Directory B2C, gruppo di destinatari è ID applicazione dell'app, come assegnata all'app nel portale di registrazione di app. L'app deve convalidare questo valore e rifiutare il token se non corrisponde. |
| Emittente | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | La richiesta consente di identificare il servizio token di sicurezza (servizio token di sicurezza) che crea e restituisce il token. Vengono inoltre individuati nella directory di Azure Active Directory in cui l'autenticazione dell'utente. L'app dovrebbe convalidare la richiesta di emittente per assicurarsi che il token proviene da endpoint v 2.0. |
| Rilasciato a | `iat` | `1438535543` | La richiesta è ora in cui il token è stato inviato, rappresentato in fase di periodo. |
| Data di scadenza | `exp` | `1438539443` | Ora di scadenza attestazione è l'ora in cui il token diventa non valido, nel tempo periodo. L'app è necessario utilizzare la richiesta per verificare la validità della durata di token.  |
| Non prima del | `nbf` | `1438535543` | La richiesta sia l'ora in cui il token diventa valido, rappresentato in fase di periodo. Si tratta in genere diverso da quello di ora che il token è stato inviato. L'app è necessario utilizzare la richiesta per verificare la validità della durata di token.  |
| Versione | `ver` | `1.0` | Questa è la versione del token ID come definito da Azure Active Directory. |
| Codice hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hash codice è incluso in un token di ID solo quando il token rilasciato insieme a un codice di autorizzazione OAuth 2.0. Utilizzare un hash di codice per convalidare l'autenticità di un codice di autorizzazione. Vedere la [specifica OpenID connettersi](http://openid.net/specs/openid-connect-core-1_0.html) per altri dettagli su come eseguire la convalida. |
| Hash token di accesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Un hash token di accesso è incluso in un token di ID solo quando il token rilasciato insieme a un token di accesso OAuth 2.0. Un hash token di accesso può essere utilizzato per convalidare l'autenticità dei token di accesso. Vedere la [specifica OpenID connettersi](http://openid.net/specs/openid-connect-core-1_0.html) per altri dettagli su come eseguire la convalida. |
| Nonce | `nonce` | `12345` | Un nonce è una strategia utilizzata per limitare gli attacchi di riproduzione token. L'app possibile specificare un nonce in una richiesta di autorizzazione utilizzando il `nonce` parametro della query. Viene emesso di specificare nella richiesta non modificata nel `nonce` richiedere di solo un token di ID. In questo modo l'app verificare il valore rispetto al valore specificato nella richiesta, che associa un determinato token ID sessione dell'applicazione. L'app deve eseguire la convalida durante il processo di convalida token ID. |
| Oggetto | `sub` | `Not supported currently. Use oid claim.` | Si tratta di un capitale intorno al quale il token indica informazioni, ad esempio l'utente di un'app. Questo valore è modificabile e non può essere riassegnata o riutilizzato. Può essere utilizzato per eseguire i controlli di autorizzazione in modo sicuro, ad esempio quando il token viene utilizzato per accedere a una risorsa. Tuttavia, la richiesta di oggetto non è ancora implementata in B2C di Active Directory Azure. È necessario configurare i criteri per includere l'ID di oggetto `oid` richiedere e usare il valore per identificare gli utenti, anziché utilizzare la domanda oggetto di autorizzazione. |
| Riferimento di classe di autenticazione contesto | `acr` | `b2c_1_sign_in` | Questo è il nome del criterio utilizzato per acquisire token ID.  |
| Ora di autenticazione | `auth_time` | `1438535543` | La richiesta sia l'ora in cui un'ultima immesso le credenziali utente, in forma di ora periodo. |


### <a name="refresh-tokens"></a>Aggiornare i token

Aggiornare i token sono token di sicurezza che l'app è possibile utilizzare per acquisire nuovi token ID e accedere token di un flusso di OAuth 2.0. Forniscono l'app di accedere a lungo termine alle risorse per conto di utenti senza interazione con tali utenti.

Per ricevere un aggiornamento token in una risposta token l'app è necessario richiedere la `offline_acesss` ambito. Per ulteriori informazioni sul `offline_access` definire l'ambito, fare riferimento al [riferimento al protocollo B2C di Azure Active Directory](active-directory-b2c-reference-protocols.md).

Aggiornare i token sono e viene mantenuto, completamente opaco all'app. Rilasciato da Azure Active Directory e possono essere esaminati e interpretati solo da Azure Active Directory. Sono lunga, ma l'app non deve essere scritte con la previsione che un token di aggiornamento a lungo termine per un periodo di tempo specifico. I token di aggiornamento possono essere annullati in qualsiasi momento per diverse ragioni. L'unico modo per l'app di sapere se un token di aggiornamento è valido consiste nel tentare di riscattare effettuando una richiesta di token di Azure Active Directory.

Quando si Riscatta un token di aggiornamento per un nuovo token (e, se l'app è stata concessa il `offline_access` ambito), si riceverà un nuovo token di aggiornamento in risposta token. È consigliabile salvare il token di aggiornamento appena emesso. È necessario sostituire il token di aggiornamento che precedentemente utilizzati per la richiesta. In questo modo garantire che i token di aggiornamento rimangono valide per più a lungo possibile.

## <a name="token-validation"></a>Convalida dei token

Per convalidare un token, l'app di verificare la firma e reclami del token.

Molte librerie Apri origine sono disponibili per la convalida dei JWTs, a seconda lingua preferita. È consigliabile esplorare queste opzioni, anziché implementare la logica di convalida. Le informazioni contenute in questa guida consentono di imparare a utilizzare queste raccolte in modo appropriato.

### <a name="validate-the-signature"></a>Convalidare la firma
Un JWT contiene tre segmenti, separati dalla `.` carattere. Il primo segmento è l' **intestazione**, il secondo è il **corpo**e il terzo è la **firma**. Il segmento firma è utilizzabile per convalidare l'autenticità del token in modo che possono essere considerati attendibili per l'app.

Azure Active Directory B2C token firmati con algoritmi di crittografia asimmetrici standard di settore, ad esempio RSA 256. L'intestazione del token contiene informazioni sul metodo di crittografia e chiave utilizzato per firmare il token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Il `alg` Richiedi indica l'algoritmo utilizzati per firmare il token. Il `kid` Richiedi indica la chiave pubblica particolare utilizzati per firmare il token.

In qualsiasi momento, Azure Active Directory può accedere un token utilizzando uno qualsiasi di un determinato insieme di coppie di parole chiave pubblica privata. Azure AD eventuali insiemi di tasti viene ruotata periodicamente, in modo che l'app deve essere scritti per gestire automaticamente le modifiche apportate ai tasti. Una frequenza accettabile per verificare la presenza di aggiornamenti per le chiavi pubbliche utilizzate da Azure Active Directory è ogni 24 ore.

Azure Active Directory B2C dispone di un endpoint di metadati OpenID connettersi. In questo modo App recuperare informazioni su Azure Active Directory B2C in fase di esecuzione. Queste informazioni includono i punti finali, contenuto token e token di chiavi per la firma. Directory B2C contiene un documento di metadati JSON per ogni criterio. Ad esempio, il documento di metadati per il `b2c_1_sign_in` criteri nel `fabrikamb2c.onmicrosoft.com` si trova in:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`è la directory B2C utilizzata per eseguire l'autenticazione utente, e `b2c_1_sign_in` il criterio utilizzato per acquisire il token. Per determinare il tipo di criteri è stato utilizzato per firmare un token e dove è possibile per recuperare i metadati, sono disponibili due opzioni. Prima di tutto, il nome del criterio è incluso nel `acr` richiedere nel token. È possibile analizzare sulle attestazioni dal corpo della JWT da base 64 decodificare corpo e deserializzazione della stringa JSON risultante. Il `acr` Richiedi sarà il nome del criterio utilizzato per rilasciare il token.  L'altra opzione consiste nel codificare i criteri del valore di `state` parametro quando si invia la richiesta e decodifica per determinare il tipo di criteri è stata utilizzata. Dei metodi descritti è valido.

Il documento di metadati è un oggetto JSON che contiene numerose utili delle informazioni. Sono incluse la posizione dell'endpoint necessari per eseguire l'autenticazione OpenID connettersi. Includono anche `jwks_uri`, che comporta la posizione del set di chiavi pubbliche vengono utilizzati per firmare i token. Percorso di seguito, ma è consigliabile recuperare la posizione in modo dinamico utilizzando il documento di metadati e analisi fuori `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

Il documento JSON che si trova in questo URL contiene tutte le informazioni chiave pubbliche in uso in un determinato momento. L'app è possibile utilizzare il `kid` richiedere nell'intestazione di JWT per selezionare la chiave pubblica nel documento JSON utilizzati per firmare il token. Quindi possibile eseguire la convalida della firma utilizzando la chiave pubblica corretta e algoritmo indicata.

Una descrizione di come eseguire la convalida della firma non è compreso l'ambito di questo documento. Molte librerie Apri origine sono disponibili per semplificare questa operazione se è necessario.

### <a name="validate-the-claims"></a>Convalidare le attestazioni
Quando l'app o API riceve un token di ID, è necessario eseguire anche diversi controlli con i crediti nel token di ID. Questi includono, ma non sono limitati a:

- Richiedi **pubblico** : verifica che il token ID è stato utilizzato da assegnare all'app.
- Sulle attestazioni **non prima** e **l'ora di scadenza** : questi verificare che il token ID è scaduto.
- Richiedi **emittente** : verifica che il token è stato rilasciato all'app da Azure Active Directory.
- **Nonce**: si tratta di una strategia di attenuazione attacco token di riproduzione.

Per un elenco completo di convalida che deve eseguire l'app, fare riferimento alla [connessione OpenID specifica](https://openid.net). Dettagli dei valori previsti per le richieste sono inclusi nella precedente [sezione token](#types-of-tokens).  

## <a name="token-lifetimes"></a>Durata token

La durata di token seguente è disponibili per ampliare la conoscenza. Consentono quando si sviluppa ed eseguire il debug app. Si noti che le applicazioni non devono essere scritte aspettarsi uno di questi durata rimanga costante. Possono e verrà modificato.  È possibile leggere informazioni sulla personalizzazione della durata token di Azure Active Directory B2C [qui](active-directory-b2c-token-session-sso.md).

| Token | Durata | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| Token ID | Un'ora | Token ID sono in genere valide per un'ora. App web è possibile usare questo caso la durata per mantenere i proprio sessioni con gli utenti (scelta consigliati). È anche possibile scegliere la durata di una sessione diversa. Se l'app deve iniziare un nuovo ID token, è sufficiente deve verificare una nuova richiesta di Azure Active Directory. Se un utente ha una sessione del browser valido con Azure Active Directory, l'utente potrebbe non essere necessario immettere nuovamente le credenziali. |
| Aggiornare i token | Fino a 14 giorni | Un token di aggiornamento singolo è valido per un massimo di 14 giorni. Tuttavia, un token di aggiornamento può diventare non valido in qualsiasi momento per diversi motivi. L'app è necessario continuare a provare a utilizzare un token di aggiornamento finché la richiesta non riesce o finché l'app sostituisce il token di aggiornamento con uno nuovo.  Un token di aggiornamento può anche più valido se è già trascorsa 90 giorni dal momento che le credenziali dell'ultimo specificato dall'utente. |
| Codici di autorizzazione | Cinque minuti | Codici di autorizzazione sono intenzionalmente breve. Deve recuperati immediatamente per token di accesso, token ID o i token di aggiornamento quando vengono ricevuti. |
