<properties
    pageTitle="Azure singola Disconnetti SAML protocollo | Microsoft Azure"
    description="In questo articolo descrive il protocollo SAML Sign-Out singola di Azure Active Directory"
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


# <a name="single-sign-out-saml-protocol"></a>Protocollo singola SAML disconnessione

Supporta Active Directory (Azure Active Directory) Azure SAML 2.0 web profilo disconnessione singola del browser. Per singolo uscita per il corretto funzionamento di Azure Active Directory necessario registrare l'URL dei metadati durante la registrazione dell'applicazione. Azure Active Directory Ottiene l'URL di disconnessione e la chiave di firma del servizio cloud dai metadati. Azure Active Directory utilizza la chiave di firma per verificare la firma nella posta in arrivo LogoutRequest e utilizza la LogoutURL per reindirizzare gli utenti eseguito.

Se il servizio cloud non supporta un endpoint metadati, una volta registrata l'applicazione, lo sviluppatore dovrà contattare il supporto Microsoft per fornire l'URL di disconnessione e chiave di firma.

Questo diagramma mostra il flusso di lavoro del processo di disconnessione singolo di Azure Active Directory.

![Disconnetti singolo flusso di lavoro](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest

Invia il servizio cloud un `LogoutRequest` messaggio per indicare che è stata terminata una sessione di Azure Active Directory. Porzione riportata di seguito viene illustrato un esempio `LogoutRequest` elemento.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest

Il `LogoutRequest` elemento inviato per Azure Active Directory richiede gli attributi seguenti:

- `ID`: Identifica la richiesta di disconnessione. Il valore di `ID` non devono iniziare con un numero. La procedura tipica viene aggiunta **id** della rappresentazione di un GUID.

- `Version`: Impostare il valore di questo elemento a **2.0**. Questo valore è obbligatorio.

- `IssueInstant`: Si tratta di un `DateTime` stringa con un valore di ora UTC (Coordinate) e [il formato di round trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure Active Directory prevede un valore di questo tipo, ma non vengono applicate.

- Il `Consent`, `Destination`, `NotOnOrAfter` e `Reason` gli attributi vengono ignorati se sono incluse un `LogoutRequest` elemento.

### <a name="issuer"></a>Emittente

Il `Issuer` elemento in una `LogoutRequest` deve corrispondere esattamente una **gli attributi SPN** nel servizio cloud in Azure Active Directory. In genere è impostata per l' **App ID URI** specificato durante la registrazione dell'applicazione.

### <a name="nameid"></a>NameID

Il valore della `NameID` elemento deve corrispondere esattamente la `NameID` dell'utente che è stato effettuato l'accesso a indietro.
## <a name="logoutresponse"></a>LogoutResponse

Azure Active Directory invia una `LogoutResponse` in risposta a un `LogoutRequest` elemento. Porzione riportata di seguito viene illustrato un esempio `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse

Set di Azure Active Directory di `ID`, `Version` e `IssueInstant` i valori nella `LogoutResponse` elemento. Imposta anche la `InResponseTo` elemento per il valore della `ID` attributo del `LogoutRequest` che manifesta la risposta.

### <a name="issuer"></a>Emittente

Azure Active Directory imposta questo valore su `https://login.microsoftonline.com/<TenantIdGUID>/` in <TenantIdGUID> ID tenant del tenant di Azure Active Directory.

Calcolare il valore di `Issuer` elemento, usare il valore dell' **App ID URI** fornito durante la registrazione dell'applicazione.

### <a name="status"></a>Stato

Azure Active Directory utilizza il `StatusCode` elemento il `Status` elemento per indicare l'esito positivo o negativo di disconnessione. Quando il tentativo di disconnessione non riesce, il `StatusCode` elemento può inoltre contenere messaggi di errore personalizzati.
