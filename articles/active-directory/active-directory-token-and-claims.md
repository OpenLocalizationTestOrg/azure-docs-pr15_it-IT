 <properties
   pageTitle="Guida di riferimento Azure Active Directory Token | Microsoft Azure"
   description="Una Guida per la comprensione e la valutazione sulle attestazioni nei token SAML 2.0 e JSON Web token (JWT) emessi da Azure Active Directory (AAD)"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Riferimento token di Azure Active Directory

Azure Active Directory (Azure Active Directory) genera diversi tipi di token di sicurezza dell'elaborazione di ogni flusso di autenticazione. Questo documento descrive il formato, caratteristiche di protezione e il contenuto di ogni tipo di token.

## <a name="types-of-tokens"></a>Tipi di token

Azure Active Directory supporta il [protocollo di autorizzazione OAuth 2.0](active-directory-protocols-oauth-code.md), che utilizza access_tokens e refresh_tokens.  Inoltre, supporta l'autenticazione e accesso tramite [OpenID connettersi](active-directory-protocols-openid-connect-code.md), che introduce un terzo tipo di token, il id_token.  Ognuna di queste token viene rappresentato come "token del titolare".

Un token del titolare è un token di sicurezza leggera che concede l'accesso "titolare" a una risorsa protetta. In questo caso, "titolare" è tutte le parti che possono presentare il token. Anche se è necessaria l'autenticazione con Azure Active Directory per ricevere un token del titolare, è necessario eseguire i passaggi per proteggere il token, per evitare di intercettazione da una parte impreviste. Perché i token del titolare non dispone di un meccanismo incorporato per impedire a utenti non autorizzati dal loro utilizzo, deve essere trasporto in un canale sicuro, ad esempio protezione a livello di trasporto (HTTPS). Se un token del titolare viene trasmesso in chiaro, attacco di un uomo aggiuntivo può essere utilizzato per acquisire il token e ottenere accesso non autorizzato a una risorsa protetta. Possono essere applicati sicurezza anche quando l'archiviazione o la memorizzazione nella cache token del titolare per successivi utilizzi. Sempre assicurarsi che l'app trasmette e archivia i token del titolare in modo sicuro. Per altre considerazioni sulla protezione sui token del titolare, vedere [RFC 6750 sezione 5](http://tools.ietf.org/html/rfc6750).

Molti dei token emessi Azure Active Directory implementate come JSON Web token o JWTs.  Un JWT è una soluzione compatta, attendibili URL per il trasferimento di informazioni tra due parti.  Le informazioni contenute in JWTs sono noto come "reclami" o asserzioni delle informazioni sui oggetto del token del titolare.  Sulle attestazioni in JWTs sono oggetti JSON codificati e serializzati per la trasmissione.  Poiché JWTs rilasciato da Azure Active Directory effettuato l'accesso, ma non crittografati, è possibile controllare facilmente il contenuto di un JWT per il debug.  Sono disponibili diversi strumenti per eseguire questa operazione, ad esempio [jwt.calebb.net](http://jwt.calebb.net). Per ulteriori informazioni su JWTs, è possibile fare riferimento alla [specifica JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens costituiscono una forma di token di sicurezza di accesso che l'app riceve quando si esegue l'autenticazione con [OpenID connessione](active-directory-protocols-openid-connect-code.md).  Sono rappresentati come [JWTs](#types-of-tokens)e contengono delle attestazioni che è possibile usare per accedere all'utente l'app.  È possibile utilizzare le richieste in un id_token per adattarlo: in genere utilizzati per visualizzare informazioni sull'account o decisioni il controllo di accesso in un'app.

Id_tokens è stato effettuato l'accesso, ma non crittografata al momento.  Quando l'app riceve un id_token, deve essere [convalidare la firma](#validating-tokens) per dimostrare di essere autenticità del token e convalidare alcune richieste nel token di provare la validità.  Reclami convalidati da un'app variano a seconda di requisiti di scenario, ma sono disponibili alcuni [comuni Richiedi convalida](#validating-tokens) che deve eseguire l'app in ogni scenario.

Vedere la sezione seguente per informazioni su attestazioni id_tokens, come un id_token di esempio.  Si noti che sulle attestazioni in id_tokens non vengono restituite in un ordine specifico.  Inoltre, nuove attestazioni possono essere stati introdotti id_tokens in qualsiasi punto nel tempo, l'app non dovrebbe interrompere come vengono introdotti nuovi sulle attestazioni.  L'elenco seguente include le attestazioni che l'app in modo affidabile può interpretare in fase di questo articolo.  Se necessario, maggiore dettaglio sono disponibili nella [specifica OpenID connettersi](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Esempio id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Per l'esercitazione, provare il controllo delle attestazioni in id_token esempio incollandolo nella [calebb.net](http://jwt.calebb.net).

#### <a name="claims-in-idtokens"></a>Sulle attestazioni in id_tokens

| Richiedi JWT | Nome | Descrizione |
|-----------|------|-------------|
| `appid`| ID applicazione | Identifica l'applicazione che utilizza il token per accedere a una risorsa. L'applicazione può essere utilizzato in quanto tale o per conto di un utente. ID applicazione in genere rappresenta un oggetto applicazione, ma può rappresentare anche un oggetto principale del servizio in Azure Active Directory. <br><br> **Valore JWT di esempio**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Gruppo di destinatari | Il destinatario del token. L'applicazione che riceve il token necessario verificare che il valore di gruppo di destinatari sia corretto e rifiutare qualsiasi token destinato a un diverso gruppo di destinatari. <br><br> **Valore SAML di esempio**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Valore JWT di esempio**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Riferimento di classe di applicazione autenticazione contesto | Indica la modalità di autenticazione client. Per un pubblico client, il valore è uguale a 0. Se si utilizzano ID client e segreto del client, il valore è 1. <br><br> **Valore JWT di esempio**: <br> `"appidacr": "0"`|
| `acr`| Riferimento di classe di autenticazione contesto | Indica come l'oggetto è stato autenticato, anziché il client la richiesta di applicazione autenticazione contesto classe riferimento. Il valore "0" indica che l'autenticazione degli utenti finali non è soddisfatto i requisiti di ISO/IEC 29115. <br><br> **Valore JWT di esempio**: <br> `"acr": "0"`|
| | Autenticazione immediata | Registra la data e ora in cui si è verificato l'autenticazione. <br><br> **Valore SAML di esempio**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Metodo di autenticazione | Identifica la modalità di autenticazione l'oggetto del token. <br><br> **Valore SAML di esempio**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Valore JWT di esempio**:`“amr”: ["pwd"]` |
| `given_name`| Nome | Fornisce il primo o "assegnato" nome dell'utente, come impostato nell'oggetto utente di Azure Active Directory. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `"given_name": "Frank"` |
| `groups`| Gruppi | Fornisce gli ID di oggetto che rappresentano appartenenze ai gruppi dell'oggetto. Questi valori sono univoci (vedere ID oggetto) e possono essere utilizzati in modo sicuro per gestire l'accesso, ad esempio imposte autorizzazione per accedere a una risorsa. I gruppi inclusi in Richiedi gruppi sono configurati in base all'applicazione, tramite la proprietà di "groupMembershipClaims" del manifesto dell'applicazione. Un valore null verrà esclusi tutti i gruppi, il valore "SecurityGroup" includerà solo l'appartenenza al gruppo di sicurezza di Active Directory e value di "Tutti" includono i gruppi di sicurezza e gli elenchi di distribuzione di Office 365. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Provider di identità | Registra il provider di identità che l'oggetto del token autenticati. Questo valore è uguale al valore dell'attestazione emittente a meno che l'account utente sia in un tenant diverso rispetto a autorità. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Archivia l'ora in cui è stato inviato il token. Viene spesso utilizzato per misurare validità token. <br><br> **Valore SAML di esempio**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Valore JWT di esempio**: <br> `"iat": 1390234181` |
| `iss` | Emittente | Consente di identificare il servizio token di sicurezza (servizio token di sicurezza) che crea e restituisce il token. In token che restituisce Azure Active Directory, autorità è sts.windows.net. GUID nel valore Richiedi emittente corrisponde all'ID tenant della directory di Azure Active Directory. L'ID tenant è un identificatore non modificabile e affidabile della directory. <br><br> **Valore SAML di esempio**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Valore JWT di esempio**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Cognome | Fornisce l'ultimo nome, cognome oppure cognome dell'utente definito nell'oggetto utente Azure Active Directory. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `"family_name": "Miller"` |
| `unique_name`| Nome | Fornisce un valore leggibile umano che identifica l'oggetto del token. Questo valore non necessariamente essere univoco all'interno di un tenant e progettato per essere utilizzato solo per la visualizzazione. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | ID oggetto | Contiene un identificatore univoco dell'oggetto in Azure Active Directory. Questo valore è modificabile e non può essere riassegnata o riutilizzato. Utilizzare l'ID di oggetto per identificare un oggetto nelle query di Azure Active Directory. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Ruoli | Rappresenta tutti i ruoli di applicazione che l'oggetto è stata concessa sia direttamente o indirettamente tramite l'appartenenza ai gruppi e può essere utilizzato per applicare il controllo dell'accesso basato sui ruoli. Ruoli applicazione vengono definiti in base all'applicazione, tramite la `appRoles` proprietà del manifesto dell'applicazione. Il `value` proprietà di ogni ruolo applicazione è il valore visualizzato in Richiedi ruoli. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Ambito | Indica le autorizzazioni di rappresentazione concesse all'applicazione client. L'autorizzazione predefinita `user_impersonation`. Il proprietario della risorsa protetta può registrare altri valori in Azure Active Directory. <br><br> **Valore JWT di esempio**: <br> `"scp": "user_impersonation"`|
| `sub` |Oggetto| Consente di identificare il capitale intorno al quale il token indica informazioni, ad esempio l'utente di un'applicazione. Questo valore è modificabile e non è possibile riassegnare o riutilizzati, pertanto può essere utilizzato per eseguire i controlli di autorizzazione in modo sicuro. Poiché l'oggetto è sempre presenta in token i problemi di Azure Active Directory, è consigliabile usare questo valore in un sistema di autorizzazione generale. <br> `SubjectConfirmation`non è una richiesta di rimborso. Descrive come viene verificato l'oggetto del token. `Bearer`indica che l'oggetto è confermato dal loro possesso del token. <br><br> **Valore SAML di esempio**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Valore JWT di esempio**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | ID tenant | Un identificatore non modificabile, non riutilizzabile che identifica il tenant di directory che ha rilasciato il token. È possibile utilizzare questo valore per accedere alle risorse di directory tenant specifiche in un'applicazione multi-tenant. Ad esempio, è possibile utilizzare questo valore per identificare al tenant descritta in una chiamata all'API di grafico. <br><br> **Valore SAML di esempio**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Valore JWT di esempio**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Durata | Consente di definire l'intervallo di tempo entro il quale è valido un token. Il servizio che verifica il token necessario verificare che la data corrente è all'interno di tale durata, altra che deve rifiutare il token. Il servizio potrebbe consentire un massimo di cinque minuti oltre l'intervallo di durata a prendere in considerazione le differenze orario ("mancata sincronizzazione dell'ora") tra Azure Active Directory e il servizio. <br><br> **Valore SAML di esempio**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Valore JWT di esempio**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Nome dell'entità utente | Archivia il nome dell'entità utente.<br><br> **Valore JWT di esempio**: <br> `"upn": frankm@contoso.com`|
| `ver`| Versione | Archivia il numero di versione del token. <br><br> **Valore JWT di esempio**: <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>Token di accesso

Token di accesso sono utilizzabili da Microsoft Services solo in questo momento.  Le app non è necessario eseguire qualsiasi convalida o controllo token di accesso per gli scenari attualmente supportati.  È possibile considerare i token di accesso e completamente opaco - sono rappresentati da stringhe soltanto che l'app è possibile passare a Microsoft di richieste HTTP.

Quando si richiede un token di accesso, Azure Active Directory restituisce inoltre alcuni metadati relativi token di accesso per il consumo dell'applicazione.  Queste informazioni includono l'ora di scadenza di token di accesso e gli ambiti per il quale è valido.  In questo modo l'app per eseguire intelligente memorizzazione nella cache dei token di accesso senza dover analizzare aperto il token di accesso.

## <a name="refresh-tokens"></a>Aggiornare i token

Aggiornare i token sono token di sicurezza, l'applicazione consente di acquisire nuovi token di accesso in un flusso di OAuth 2.0.  Consente l'app ottenere accesso a lungo termine alle risorse per conto di un utente senza interazione dall'utente.

I token di aggiornamento sono più risorse, il che significa che possono essere ricevuti durante una richiesta di token per una risorsa, ma scegliendo tra i token di accesso a una risorsa completamente diverso. Per specificare più risorse, impostare il `resource` parametro nella richiesta per la risorsa di destinazione.

Token di aggiornamento sono completamente opaco all'app. Sono lunga, ma l'app non deve essere scritte aspettarsi che un token di aggiornamento a lungo termine per un periodo di tempo.  I token di aggiornamento possono essere annullati in qualsiasi momento per diverse ragioni.  L'unico modo per l'app di sapere se un token di aggiornamento è valido consiste nel tentare di riscattare effettuando una richiesta di token all'endpoint token di Azure Active Directory.

Quando si Riscatta un token di aggiornamento per un nuovo token di accesso, si riceverà un nuovo token di aggiornamento in risposta token.  È consigliabile salvare token aggiornamento appena emesso sostituire quello usato nella richiesta.  Questo sarà garantisce che i token di aggiornamento rimangono valide per più a lungo possibile.

## <a name="validating-tokens"></a>Convalidare i token

In questo momento, la convalida solo token che necessario eseguire l'applicazione client convalida id_tokens.  Per convalidare un id_token, l'app di convalida della firma del id_token e le richieste di id_token.

Sono disponibili le raccolte ed esempi di codice che illustrano come gestire facilmente la convalida token, se si desidera comprendere il processo sottostante.  Sono disponibili anche varie librerie di terze parti Apri origine per la convalida JWT, almeno un'opzione per quasi ogni piattaforma e della lingua. Per ulteriori informazioni sulle librerie di autenticazione di Azure Active Directory ed esempi di codice, vedere [le raccolte di autenticazione di Azure Active Directory](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>La convalida della firma

Un JWT contiene tre segmenti, separati dalla `.` carattere.  Il primo segmento è nota come **intestazione**, il secondo come **corpo**e la terza parte della **firma**.  Il segmento firma è utilizzabile per convalidare l'autenticità del id_token in modo che possono essere considerati attendibili per l'app.

Id_Tokens hanno eseguito l'accesso tramite algoritmi crittografia asimmetrici standard di settore, ad esempio RSA 256. L'intestazione della id_token contiene informazioni sul metodo di crittografia e chiave utilizzato per firmare il token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

Il `alg` Richiedi indica l'algoritmo utilizzati per firmare i token, mentre il `x5t` Richiedi indica la chiave pubblica particolare utilizzati per firmare il token.

In qualsiasi punto nel tempo, Azure Active Directory può accedere un id_token uno qualsiasi di un determinato insieme di coppie di parole chiave pubblica privata. Azure Active Directory ruota eventuali insiemi di chiavi periodicamente, in modo che l'app deve essere scritti per gestire automaticamente le modifiche apportate ai tasti.  Una frequenza accettabile per verificare la presenza di aggiornamenti per le chiavi pubbliche utilizzate da Azure Active Directory è ogni 24 ore.

È possibile acquisire i dati della chiave firma necessari per convalidare la firma utilizzando il documento di metadati OpenID connessione che si trova in:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Provare questo URL in un browser.

In questo documento di metadati è un oggetto JSON contenente utile di varie informazioni, ad esempio la posizione dei vari endpoint necessari per eseguire l'autenticazione OpenID connettersi.  

Include inoltre una `jwks_uri`, che comporta la posizione del set di chiavi pubbliche utilizzato per firmare i token.  Il documento JSON che si trova nel `jwks_uri` contiene tutte le informazioni chiave pubbliche in uso in tale momento specifico.  L'app è possibile utilizzare il `kid` richiedere nell'intestazione di JWT per selezionare la chiave pubblica in questo documento è stata usata per firmare il token.  È quindi possibile eseguire la convalida della firma con la chiave pubblica corretta e algoritmo indicato.

Eseguire la convalida della firma non rientra nell'ambito di questo documento: sono disponibili molte librerie Apri origine consentono di eseguire questa operazione se necessario.

#### <a name="validating-the-claims"></a>Convalidare le attestazioni

Quando l'app riceve un id_token durante l'accesso degli utenti, è necessario eseguire anche alcuni controlli con i crediti nel id_token.  Questi includono, ma non sono limitati a:

  - La richiesta di un **gruppo di destinatari** - per verificare che il id_token è stata utilizzata da assegnare all'app.
  - **Non prima** e **l'Ora di scadenza** attestazioni - per verificare che il id_token è scaduto.
  - Richiedi **emittente** - per verificare che il token è stato effettivamente rilasciato all'app da Azure Active Directory.
  - Il **parametro Nonce** - misure per limitarli tramite attacchi token.
  - e altro ancora...

Per un elenco completo di convalida Richiedi che deve eseguire l'app, fare riferimento alla [connessione OpenID specifica](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation).

Dettagli dei valori previsti per le richieste sono disponibili nella sezione [id_token sezione](#id-tokens) precedente.

## <a name="sample-tokens"></a>Token di esempio

In questa sezione consente di visualizzare esempi di token SAML e JWT che restituisce Azure Active Directory. In questi esempi consentono di visualizzare le richieste nel contesto.
Token SAML

Si tratta di un campione di un tipico token SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Token JWT - rappresentazione dell'utente

Si tratta di un campione di un tipico token di web JSON (JWT) utilizzato in un flusso di concedere l'autorizzazione codice.
Oltre a reclami, il token include un numero di versione nel **server** e **appidacr**, il riferimento alla classe contesto l'autenticazione, che indica la modalità di autenticazione il client. Per un pubblico client, il valore è uguale a 0. Se è stato usato un ID client o segreto del client, il valore è 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Contenuti correlati
- Vedere di Azure Active Directory grafico [operazioni di politica](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e l' [entità di criteri](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), per altre informazioni sulla gestione dei criteri di durata token tramite l'API di Azure Active Directory grafico.
- Per ulteriori informazioni ed esempi sulla gestione dei criteri tramite i cmdlet di PowerShell, inclusi esempi, vedere [configurabile token durata in Azure Active Directory](active-directory-configurable-token-lifetimes.md). 
