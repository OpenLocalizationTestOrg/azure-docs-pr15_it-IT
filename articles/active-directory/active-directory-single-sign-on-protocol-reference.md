<properties
    pageTitle="Azure Single Sign-On SAML protocollo | Microsoft Azure"
    description="In questo articolo descrive il protocollo Single Sign in SAML di Azure Active Directory"
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

# <a name="single-sign-on-saml-protocol"></a>Singolo protocollo SAML Sign-On

In questo articolo vengono illustrate le richieste di autenticazione SAML 2.0 e le risposte che Azure Active Directory (Azure Active Directory) supporta per Single Sign-On.

Diagramma protocollo riportato di seguito vengono illustrati la sequenza. Il servizio cloud (il provider di servizi) utilizza un'associazione di reindirizzamento HTTP per passare un `AuthnRequest` elemento (richiesta di autenticazione) da Azure Active Directory (il provider di identità). Azure Active Directory quindi utilizza un post HTTP associazione per registrare un `Response` elemento al servizio cloud.

![Single Sign-On del flusso di lavoro](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Per richiedere un'autenticazione utente, cloud services invia un `AuthnRequest` elemento da Azure Active Directory. Un campione SAML 2.0 `AuthnRequest` potrebbe avere questo aspetto:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| Parametro | | Descrizione |
| ----------------------- | ------------------------------- | --------------- |
| ID | Obbligatorio | Azure Active Directory questo attributo viene utilizzato per popolare la `InResponseTo` attributo della risposta restituita. ID non deve iniziare con un numero in modo che una strategia comune consiste nel anteporre una stringa come "id" per la rappresentazione di un GUID. Ad esempio `id6c1c178c166d486687be4aaf5e482730` un ID valido. |
| Versione | Obbligatorio | Deve trattarsi di **2.0**.|
| IssueInstant | Obbligatorio | Si tratta di una stringa di data con un valore di ora UTC e [il formato di round trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure Active Directory prevede un valore DateTime di questo tipo, ma non vengono valutati o usare il valore. |
| AssertionConsumerServiceUrl | facoltativo | Se specificato, deve corrispondere la `RedirectUri` del servizio cloud di Azure Active Directory. |
| ForceAuthn | facoltativo | Se specificato, deve essere falsa. Qualsiasi altro valore genera un errore.|
| IsPassive | facoltativo | Se specificato, deve essere falsa. Qualsiasi altro valore genera un errore. |  

Tutti gli altri `AuthnRequest` attributi, ad esempio il consenso, di destinazione, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex e ProviderName vengono **ignorati**.

Azure Active Directory ignora anche il `Conditions` elemento `AuthnRequest`.

### <a name="issuer"></a>Emittente

Il `Issuer` elemento in un `AuthnRequest` deve corrispondere esattamente una **gli attributi SPN** nel servizio cloud in Azure Active Directory. In genere è impostata per l' **App ID URI** specificato durante la registrazione dell'applicazione.

Esempio SAML estratto contenente la `Issuer` elemento è simile alla seguente:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Questo elemento richiede un formato di ID nome specifico nella risposta e facoltativo in `AuthnRequest` elementi inviati a Azure Active Directory.

Un campione `NameIdPolicy` elemento è simile alla seguente:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Se `NameIDPolicy` viene fornito, è possibile includere il facoltativo `Format` attributo. Il `Format` attributo può avere solo uno dei seguenti valori. qualsiasi altro valore genera un errore.

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory problemi Richiedi NameID come identificatore coppie.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory problemi Richiedi NameID in formato dell'indirizzo di posta elettronica.
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Questo valore consente di Azure Active Directory per selezionare il formato di richiesta di rimborso. Azure Active Directory problemi il NameID come identificatore coppie.

Non includere nella selezione la `SPNameQualifer` attributo. Azure Active Directory ignora il `AllowCreate` attributo.

### <a name="requestauthncontext"></a>RequestAuthnContext

Il `RequestedAuthnContext` elemento specifica i metodi di autenticazione desiderato. Sia obbligatorio in `AuthnRequest` elementi inviati a Azure Active Directory. Azure Active Directory supporta solo una `AuthnContextClassRef` valore: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Definizione dell'ambito

Il `Scoping` elemento che include un elenco di provider di identità, è facoltativa in `AuthnRequest` elementi inviati a Azure Active Directory.

Se, non includere nella selezione la `ProxyCount` attributo, `IDPListOption` o `RequesterID` elemento, come non sono supportate.

### <a name="signature"></a>Firma

Non includere un `Signature` elemento `AuthnRequest` elementi, come Azure Active Directory non supporta l'accesso richieste di autenticazione.

### <a name="subject"></a>Oggetto

Azure Active Directory ignora il `Subject` elemento della `AuthnRequest` elementi.

## <a name="response"></a>Risposta

Quando un sign-on richieste viene completata correttamente, Azure Active Directory invia una risposta al servizio cloud. Una risposta di esempio per un tentativo di accesso ha esito positivo è simile alla seguente:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Risposta

Il `Response` elemento include il risultato della richiesta di autorizzazione. Set di Azure Active Directory di `ID`, `Version` e `IssueInstant` i valori nella `Response` elemento. Imposta, inoltre, gli attributi seguenti:

- `Destination`: Quando sign-on viene completata correttamente, è impostato sul `RedirectUri` del provider di servizi (servizio cloud).
- `InResponseTo`: Questa opzione è impostata sulla `ID` attributo del `AuthnRequest` elemento che ha avviato la risposta.

### <a name="issuer"></a>Emittente

Set di Azure Active Directory il `Issuer` elemento da `https://login.microsoftonline.com/<TenantIDGUID>/` in <TenantIDGUID> ID tenant del tenant di Azure Active Directory.

Ad esempio, una risposta di esempio con l'elemento emittente Impossibile simile al seguente:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Stato

Il `Status` elemento che trasferisce l'esito positivo o negativo del sign-on. Includa la `StatusCode` elemento, che contiene un codice o un insieme di codici annidati che rappresentano lo stato della richiesta. Include inoltre la `StatusMessage` elemento, che contiene messaggi di errore personalizzati che vengono generati durante il processo di iscrizione.

<!-- TODO: Add a authentication protocol error reference -->

Di seguito è una risposta SAML a un tentativo di accesso ha esito negativo.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Asserzione

Oltre alla `ID`, `IssueInstant` e `Version`, Azure Active Directory imposta gli elementi seguenti `Assertion` elemento della risposta.

#### <a name="issuer"></a>Emittente

Questo è impostato su `https://sts.windows.net/<TenantIDGUID>/`in <TenantIDGUID> ID Tenant del tenant di Azure Active Directory.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Firma

Azure Active Directory firma asserzione in risposta a un sign-on esito negativo. Il `Signature` elemento contiene una firma digitale che il servizio cloud consente di eseguire l'autenticazione all'origine per verificare l'integrità dell'asserzione.

Per generare la firma digitale, Azure Active Directory utilizza la chiave della firma nel `IDPSSODescriptor` elemento del documento di metadati.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Oggetto

Specifica il capitale è il soggetto delle istruzioni di asserzione. Contiene un `NameID` elemento, che rappresenta l'utente autenticato. Il `NameID` valore è un identificatore destinazione diretta solo al provider di servizi che è destinato il token. È persistente - può revocare, ma non è mai riassegnata. È inoltre opaco, in quanto non rivelare nulla sull'utente e non utilizzato come un identificatore per le query di attributi.

Il `Method` attributo del `SubjectConfirmation` elemento sempre è impostato su `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Condizioni

Questo elemento specifica le condizioni che definiscono l'utilizzo di asserzioni SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Il `NotBefore` e `NotOnOrAfter` attributi specificano l'intervallo in cui l'asserzione è valido.

- Il valore della `NotBefore` attributo è uguale a o leggermente (meno di un secondo) in un secondo momento rispetto al valore del `IssueInstant` attributo del `Assertion` elemento. Azure Active Directory non l'alcuna differenza di orario stesso e sul servizio cloud (provider di servizi) e non verrà aggiunto qualsiasi buffer a questo momento.
- Il valore della `NotOnOrAfter` l'attributo è 70 minuti di ritardo rispetto al valore della `NotBefore` attributo.

#### <a name="audience"></a>Gruppo di destinatari

Questa pagina contiene un URI che identifica un gruppo di destinatari desiderato. Azure Active Directory imposta il valore di questo elemento per il valore di `Issuer` elemento della `AuthnRequest` che ha avviato sign-on. Per valutare la `Audience` valore, utilizzare il valore della `App ID URI` che è stato specificato durante la registrazione dell'applicazione.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Ad esempio il `Issuer` valore, il `Audience` valore deve corrispondere esattamente uno dei nomi dell'entità servizio che rappresenta il servizio cloud in Azure Active Directory. Tuttavia, se il valore della `Issuer` elemento non è un valore URI la `Audience` la risposta è il `Issuer` value preceduto da `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Questa pagina contiene attestazioni sull'oggetto o utente. Estratto seguente contiene un campione `AttributeStatement` elemento. I puntini di sospensione indica che l'elemento può includere più attributi e valori degli attributi.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```     

- **Richiedi nome** : il valore di `Name` attributo (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) è il nome dell'entità utente dell'utente autenticato, ad esempio `testuser@managedtenant.com`.
- **Richiedi ObjectIdentifier** : il valore della `ObjectIdentifier` attributo (`http://schemas.microsoft.com/identity/claims/objectidentifier`) è la `ObjectId` dell'oggetto che rappresenta l'utente autenticato in Azure Active directory. `ObjectId`è un oggetto non modificabile, univoci e riutilizzare identificatore attendibili dell'utente autenticato.

#### <a name="authnstatement"></a>AuthnStatement

Questo elemento indica che l'oggetto asserzione è stato autenticato in modo particolare in un determinato momento.

- Il `AuthnInstant` attributo specifica l'ora in cui l'utente autenticato con Azure Active Directory.
- Il `AuthnContext` elemento specifica il contesto di autenticazione utilizzato per eseguire l'autenticazione dell'utente.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
