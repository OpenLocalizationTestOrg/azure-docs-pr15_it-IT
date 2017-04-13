<properties
    pageTitle="Riferimento di token di Azure Active Directory v 2.0 | Microsoft Azure"
    description="I tipi di token e sulle attestazioni emesse da endpoint v 2.0"
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

# <a name="v20-token-reference"></a>Guida di riferimento token v 2.0

Endpoint v 2.0 genera diversi tipi di token di sicurezza dell'elaborazione di ogni [flusso di autenticazione](active-directory-v2-flows.md). Questo documento descrive il formato, caratteristiche di protezione e il contenuto di ogni tipo di token.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Tipi di token

Endpoint v 2.0 supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-v2-protocols.md), che utilizza access_tokens e refresh_tokens.  Inoltre, supporta l'autenticazione e accesso tramite [OpenID connettersi](active-directory-v2-protocols.md#openid-connect-sign-in-flow), che introduce un terzo tipo di token, il id_token.  Ognuna di queste token viene rappresentato come "token del titolare".

Un token del titolare è un token di sicurezza leggera che concede l'accesso "titolare" a una risorsa protetta. In questo caso, "titolare" è tutte le parti che possono presentare il token. Se una festa necessario eseguire l'autenticazione con Azure Active Directory per ricevere il token del titolare, se non sono prese i passaggi necessari per proteggere il token in trasmissione e lo spazio di archiviazione, può essere intercettare e utilizzato da una parte indesiderata. Alcuni token di sicurezza dispone di un meccanismo incorporato per impedire che averli autorizzati, i token del titolare non dispone di questo meccanismo mentre devono avvenire in un canale sicuro, ad esempio protezione a livello di trasporto (HTTPS). Se un token del titolare viene trasmesso in chiaro, attacco di un uomo aggiuntivo può essere usato da una festa dannosa per acquisire il token e usarlo per un accesso non autorizzato a una risorsa protetta. Possono essere applicati sicurezza anche quando l'archiviazione o la memorizzazione nella cache token del titolare per successivi utilizzi. Sempre assicurarsi che l'app trasmette e archivia i token del titolare in modo sicuro. Per altre considerazioni sulla protezione sui token del titolare, vedere [RFC 6750 sezione 5](http://tools.ietf.org/html/rfc6750).

Molti dei token emessi endpoint v 2.0 implementate come Json Web token o JWTs.  Un JWT è una soluzione compatta, attendibili URL per il trasferimento di informazioni tra due parti.  Le informazioni contenute in JWTs sono noto come "reclami" o asserzioni delle informazioni sui oggetto del token del titolare.  Sulle attestazioni in JWTs sono oggetti JSON codificati e serializzati per la trasmissione.  Poiché JWTs rilasciato da endpoint v 2.0 è stato effettuato l'accesso, ma non crittografata, è possibile controllare facilmente il contenuto di un JWT per il debug. Per ulteriori informazioni su JWTs, è possibile fare riferimento alla [specifica JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens costituiscono una forma di token di sicurezza di accesso che l'app riceve quando si esegue l'autenticazione con [OpenID connessione](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Sono rappresentati come [JWTs](#types-of-tokens)e contengono delle attestazioni che è possibile usare per accedere all'utente l'app.  È possibile utilizzare le richieste in un id_token per adattarlo: in genere utilizzati per visualizzare informazioni sull'account o decisioni il controllo di accesso in un'app.  Endpoint v 2.0 problemi solo un tipo di id_token che dispone di una serie di attestazioni indipendentemente dal tipo di utente che ha effettuato l'accesso.  Che è indicato che il formato e il contenuto del id_tokens verrà essere la stessa operazione per entrambi gli utenti Account Microsoft personali e account aziendale o dell'istituto di istruzione.

Id_tokens è stato effettuato l'accesso, ma non crittografata al momento.  Quando l'app riceve un id_token, deve essere [convalidare la firma](#validating-tokens) per dimostrare di essere autenticità del token e convalidare alcune richieste nel token di provare la validità.  Reclami convalidati da un'app variano a seconda di requisiti di scenario, ma sono disponibili alcuni [comuni Richiedi convalida](#validating-tokens) che deve eseguire l'app in ogni scenario.

Dettagli completi sulle attestazioni in id_tokens vengono forniti di seguito, oltre al tipo id_token un campione.  Si noti che sulle attestazioni in id_tokens non vengono restituite in un ordine specifico.  Inoltre, nuove attestazioni possono essere stati introdotti id_tokens in qualsiasi punto nel tempo, l'app non dovrebbe interrompere come vengono introdotti nuovi sulle attestazioni.  Nell'elenco seguente include le attestazioni che l'app in modo affidabile può interpretare in fase di questo articolo.  Se necessario, maggiore dettaglio sono disponibili nella [specifica OpenID connettersi](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Esempio id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] Per l'esercitazione, provare il controllo delle attestazioni in id_token esempio incollandolo nella [calebb.net](https://calebb.net).

#### <a name="claims-in-idtokens"></a>Sulle attestazioni in id_tokens
| Nome | Richiesta di rimborso | Valore di esempio | Descrizione |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Gruppo di destinatari | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Consente di identificare il destinatario del token.  In id_tokens, i destinatari sono Id applicazione dell'app, come assegnata all'app nel portale di registrazione di app.  L'app deve convalidare questo valore e rifiutare il token se non corrisponde. |
| Emittente | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Consente di identificare il servizio token di sicurezza (servizio token di sicurezza) che crea e restituisce il token, come il tenant di Azure Active Directory in cui l'autenticazione dell'utente.  L'app dovrebbe convalidare la richiesta di emittente per assicurarsi che il token proviene da endpoint v 2.0.  Utilizza la parte guid del credito anche per limitare il numero di tenant autorizzati ad accedere all'app.  Guid che indica l'utente è un utente consumer dall'account Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Rilasciato a | `iat` | `1452285331` | L'ora in cui è stato inviato il token, rappresentato in fase di periodo. |
| Data di scadenza | `exp` | `1452289231` | L'ora in cui il token diventa non valido, rappresentato in fase di periodo.  L'app è necessario utilizzare la richiesta per verificare la validità della durata di token.  |
| Non prima del | `nbf` | `1452285331` |  L'ora in cui il token di validità, rappresentato in fase di periodo. È in genere stesso tempo del rilascio.  L'app è necessario utilizzare la richiesta per verificare la validità della durata di token.  |
| Versione | `ver` | `2.0` | La versione di id_token, come definito da Azure Active Directory.  Per l'endpoint v 2.0, il valore sarà `2.0`. |
| Id tenant | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Guid che rappresenta il Azure Active Directory del tenant che l'utente è stata inviata da.  Per gli account di lavoro e dell'istituto di istruzione, il guid sarà l'ID non modificabile tenant dell'organizzazione a cui appartiene l'utente.  Per gli account personali, il valore sarà `9188040d-6c67-4c5b-b112-36a304b66dad`.  Il `profile` ambito è richiesta per ricevere la richiesta. |
| Codice Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Hash codice è incluso in id_tokens solo quando il id_token viene rilasciato insieme a un codice di autorizzazione OAuth 2.0.  Può essere utilizzato per convalidare l'autenticità di un codice di autorizzazione.  Vedere la [specifica OpenID connettersi](http://openid.net/specs/openid-connect-core-1_0.html) per altri dettagli su come eseguire la convalida. |
| Hash Token di accesso | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | L'hash token di accesso è incluso in id_tokens solo quando il id_token viene rilasciato insieme a un token di accesso OAuth 2.0.  Può essere utilizzato per convalidare l'autenticità dei token di accesso.  Vedere la [specifica OpenID connettersi](http://openid.net/specs/openid-connect-core-1_0.html) per altri dettagli su come eseguire la convalida. |
| Nonce | `nonce` | `12345` | Il parametro nonce è una strategia per ridurre gli attacchi di riproduzione token.  L'app possibile specificare un nonce in una richiesta di autorizzazione utilizzando il `nonce` parametro della query.  Specificare nella richiesta viene emesso nel id_token `nonce` domanda, senza modificata.  In questo modo l'app verificare il valore rispetto al valore specificato nella richiesta, che associa un determinato id_token sessione dell'applicazione.  L'app deve eseguire la convalida durante il processo di convalida id_token. |
| Nome | `name` | `Babe Ruth` | Richiedi nome fornisce un valore leggibile umano che identifica l'oggetto del token. Questo valore non necessariamente essere univoco, è modificabile e progettato per essere utilizzato solo per la visualizzazione.  Il `profile` ambito è richiesta per ricevere la richiesta. |
| Posta elettronica | `email` | `thegreatbambino@nyy.onmicrosoft.com` | L'indirizzo di posta elettronica principale associato all'account utente, se disponibile.  Il valore è modificabile e può cambiare nel tempo per un utente specifico.  Il `email` ambito è richiesta per ricevere la richiesta. |
| Nome utente preferito | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | Il nome utente principale che viene utilizzato per rappresentare l'utente nell'endpoint v 2.0.  Può trattarsi di un indirizzo di posta elettronica, il numero di telefono o un nome utente generico senza un formato specificato.  Il valore è modificabile e può cambiare nel tempo per un utente specifico.  Il `profile` ambito è richiesta per ricevere la richiesta. |
| Oggetto | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Capitale intorno al quale il token indica informazioni, ad esempio l'utente di un'app. Questo valore è modificabile e non è possibile riassegnare o riutilizzati, pertanto può essere utilizzato per eseguire i controlli di autorizzazione in modo sicuro, ad esempio quando il token viene utilizzato per accedere a una risorsa. Poiché l'oggetto è sempre presenta in token i problemi di Azure Active Directory, è consigliabile usare questo valore in un sistema di autorizzazione generale. |
| ObjectId | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | L'oggetto Id dell'account aziendale o dell'istituto di istruzione nel sistema di Azure Active Directory.  Non verrà generata la richiesta per gli account Microsoft personali.  Il `profile` ambito è richiesta per ricevere la richiesta. |


## <a name="access-tokens"></a>Token di accesso

Token di accesso generati da endpoint v 2.0 sono utilizzabili da Microsoft Services solo in questo momento.  Le app non è necessario eseguire qualsiasi convalida o controllo token di accesso per gli scenari attualmente supportati.  È possibile considerare i token di accesso e completamente opaco - sono rappresentati da stringhe soltanto che l'app è possibile passare a Microsoft di richieste HTTP.

Nel prossimo futuro endpoint v 2.0 verrà illustrata la possibilità di eseguire l'app ricevere i token di accesso da altri client.  In quel momento, questa informazione verrà aggiornata con le informazioni che l'app è necessario eseguire la convalida token di accesso e altre operazioni.

Quando si richiede un token di accesso da endpoint v 2.0, l'endpoint v 2.0 restituisce inoltre alcuni metadati relativi token di accesso per il consumo dell'applicazione.  Queste informazioni includono l'ora di scadenza di token di accesso e gli ambiti per il quale è valido.  In questo modo l'app per eseguire intelligente memorizzazione nella cache dei token di accesso senza dover analizzare aperto il token di accesso.

## <a name="refresh-tokens"></a>Aggiornare i token

Aggiornare i token sono token di sicurezza che l'app è possibile utilizzare per acquisire nuove accedere token di un flusso di OAuth 2.0.  Consente l'app ottenere accesso a lungo termine alle risorse per conto di un utente senza interazione dall'utente.

Token di aggiornamento sono più risorse.  Vale a dire che un token di aggiornamento ricevuto durante una richiesta di token per una risorsa può essere utilizzato per i token di accesso a una risorsa completamente diverso.

Per ricevere un aggiornamento di una risposta token, l'app è necessario richiedere e concedere le `offline_acesss` ambito.   Per ulteriori informazioni sul `offline_access` definire l'ambito, fare riferimento all' [articolo consenso & ambiti](active-directory-v2-scopes.md).

Aggiornare i token sono e viene mantenuto, completamente opaco all'app.  Sono rilasciati da endpoint v 2.0 Azure Active Directory e possono solo essere esaminati e interpretati da endpoint v 2.0.  Sono lunga, ma l'app non deve essere scritte aspettarsi che un token di aggiornamento a lungo termine per un periodo di tempo.  I token di aggiornamento possono essere annullati in qualsiasi momento per diverse ragioni.  L'unico modo per l'app di sapere se un token di aggiornamento è valido consiste nel tentare di riscattare effettuando una richiesta di token all'endpoint v 2.0.

Quando si Riscatta un token di aggiornamento per un nuovo token di accesso (e, se l'app era stata concessa il `offline_access` ambito), si riceverà un nuovo token di aggiornamento in risposta token.  È consigliabile salvare token aggiornamento appena emesso sostituire quello usato nella richiesta.  Questo sarà garantisce che i token di aggiornamento rimangono valide per più a lungo possibile.

## <a name="validating-tokens"></a>Convalidare i token

In questo momento, la convalida solo token che le app dovrebbero devono eseguire convalida id_tokens.  Per convalidare un id_token, l'app di convalida della firma del id_token e le richieste di id_token.

<!-- TODO: Link -->
Sono disponibili le raccolte e gli esempi di codice che illustrano come gestire facilmente convalida token - le informazioni relative alla fornito soltanto per chi desidera comprendere il processo sottostante.  Sono disponibili anche varie 3 ° librerie di Apri origine di terze parti per la convalida JWT - almeno un'opzione per quasi ogni piattaforma & lingua disponibili.

#### <a name="validating-the-signature"></a>La convalida della firma
Un JWT contiene tre segmenti, separati dalla `.` carattere.  Il primo segmento è nota come **intestazione**, il secondo come **corpo**e la terza parte della **firma**.  Il segmento firma è utilizzabile per convalidare l'autenticità del id_token in modo che possono essere considerati attendibili per l'app.

Id_Tokens hanno eseguito l'accesso tramite algoritmi crittografia asimmetrici standard di settore, ad esempio RSA 256. L'intestazione della id_token contiene informazioni sul metodo di crittografia e chiave utilizzato per firmare il token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Il `alg` Richiedi indica l'algoritmo utilizzati per firmare i token, mentre il `kid` Richiedi indica la chiave pubblica particolare utilizzati per firmare il token.

In qualsiasi punto nel tempo, l'endpoint v 2.0 può accedere un id_token uno qualsiasi di un determinato insieme di coppie di parole chiave pubblica privata.  Endpoint v 2.0 ruota eventuali insiemi di chiavi periodicamente, in modo che l'app deve essere scritti per gestire automaticamente le modifiche apportate ai tasti.  Una frequenza accettabile per verificare la presenza di aggiornamenti per le chiavi pubbliche utilizzate dall'endpoint v 2.0 è circa 24 ore.

È possibile acquisire i dati della chiave firma necessari per convalidare la firma utilizzando il documento di metadati OpenID connessione che si trova in:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Provare questo URL in un browser.

In questo documento di metadati è un oggetto JSON contenente utile di varie informazioni, ad esempio la posizione dei vari endpoint necessari per eseguire l'autenticazione OpenID connettersi.  

Include inoltre una `jwks_uri`, che comporta la posizione del set di chiavi pubbliche utilizzato per firmare i token.  Il documento JSON che si trova nel `jwks_uri` contiene tutte le informazioni chiave pubbliche in uso in tale momento specifico.  L'app è possibile utilizzare il `kid` richiedere nell'intestazione di JWT per selezionare la chiave pubblica in questo documento è stata usata per firmare il token.  È quindi possibile eseguire la convalida della firma con la chiave pubblica corretta e algoritmo indicato.

Eseguire la convalida della firma non rientra nell'ambito di questo documento: sono disponibili molte librerie Apri origine consentono di eseguire questa operazione se necessario.

#### <a name="validating-the-claims"></a>Convalidare le attestazioni
Quando l'app riceve un id_token durante l'accesso degli utenti, è necessario eseguire anche alcuni controlli con i crediti nel id_token.  Questi includono, ma non sono limitati a:

- La richiesta di un **gruppo di destinatari** - per verificare che il id_token è stata utilizzata da assegnare all'app.
- **Non prima** e **l'Ora di scadenza** attestazioni - per verificare che il id_token è scaduto.
- Richiedi **emittente** - per verificare che il token è stato effettivamente rilasciato all'app da endpoint v 2.0.
- Il **parametro Nonce** - come un attenuazione attacco token di riproduzione.
- e altro ancora...

Per un elenco completo di convalida Richiedi che deve eseguire l'app, vedere [connettersi OpenID specifica](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

I dettagli dei valori previsti per le richieste sono riportati sopra nella [sezione id_token](#id_tokens).


## <a name="token-lifetimes"></a>Durata token

La durata di token seguente è disponibili esclusivamente per la collaborazione, come consentono di sviluppo e il debug di App.  Le applicazioni non devono essere scritte aspettarsi uno di questi durata rimanga costante - può e verrà modificato in qualsiasi punto nel tempo.

| Token | Durata | Descrizione |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens (account aziendale o dell'istituto di istruzione) | 1 ora | Id_Tokens sono in genere valide per un'ora.  App web possa utilizzare questo stesso ciclo di vita per la gestione di propria sessione con l'utente (scelta consigliata) o scegliere la durata di una sessione completamente diverso.  Se l'app deve iniziare una nuova id_token, è sufficiente deve eseguire una nuova richiesta accesso per la versione 2.0 autorizzare endpoint.  Se l'utente ha una sessione del browser valido con l'endpoint v 2.0, che potrebbe non necessari per immettere nuovamente le proprie credenziali. |
| Id_Tokens (account personali) | 24 ore | Id_Tokens per gli account personali sono in genere validi per 24 ore.  App web possa utilizzare questo stesso ciclo di vita per la gestione di propria sessione con l'utente (scelta consigliata) o scegliere la durata di una sessione completamente diverso.  Se l'app deve iniziare una nuova id_token, è sufficiente deve eseguire una nuova richiesta accesso per la versione 2.0 autorizzare endpoint.  Se l'utente ha una sessione del browser valido con l'endpoint v 2.0, che potrebbe non necessari per immettere nuovamente le proprie credenziali. |
| Token di accesso (account aziendale o dell'istituto di istruzione) | 1 ora | Indicato nelle risposte token come parte dei metadati token. |
| Token di accesso (account personali) | 1 ora | Indicato nelle risposte token come parte dei metadati token.  Access_tokens rilasciati per conto di account personali può essere configurato per la durata di una diversa, ma un'ora è in genere le maiuscole/minuscole. |
| Aggiornare i token (account aziendale o dell'istituto di istruzione) | Fino a 14 giorni | Un token di aggiornamento singolo è valido per un massimo di 14 giorni.  Tuttavia, il token di aggiornamento può diventare non valido in qualsiasi momento per diversi motivi, in modo che l'app deve continuare provare a utilizzare un token di aggiornamento finché non riesce o finché l'app sostituito con un nuovo token di aggiornamento.  Un token di aggiornamento verrà Invalidate anche se è stato 90 giorni dal momento che l'utente ha immesso le proprie credenziali. |
| Aggiornare i token (account personali) | Fino a 1 anno | Un token di aggiornamento singolo è valido per un massimo di 1 anno.  Tuttavia, il token di aggiornamento può diventare non valido in qualsiasi momento per diversi motivi, in modo che l'app deve continuare provare a utilizzare un token di aggiornamento finché non si verifica un errore. |
| Codici di autorizzazione (account aziendale o dell'istituto di istruzione) | 10 minuti | Codici di autorizzazione sono intenzionalmente breve e devono essere utilizzati immediatamente per access_tokens e refresh_tokens quando vengono ricevuti. |
| Codici di autorizzazione (account personali) | 5 minuti | Codici di autorizzazione sono intenzionalmente breve e devono essere utilizzati immediatamente per access_tokens e refresh_tokens quando vengono ricevuti.  Codici di autorizzazione rilasciati per conto di account personali sono anche uso occasionale. |
