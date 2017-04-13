<properties
    pageTitle="Azure Active Directory Federation metadati | Microsoft Azure"
    description="Questo articolo descrive il documento di metadati federazione Azure Active Directory pubblica per i servizi che accettano i token di Azure Active Directory."
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


# <a name="federation-metadata"></a>Federazione dei metadati

Azure Active Directory (Azure Active Directory) pubblica un documento di metadati federazione per servizi configurati per accettare i token di sicurezza che problemi di Azure Active Directory. Il formato di documento basato su metadati federazione viene descritta in [Web Services Federation Language (WS-Federation) versione 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), che si estende [metadati per la versione 2.0 OASIS protezione SAML Assertion Markup Language ()](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Specifiche del tenant e gli endpoint metadati indipendente Tenant

Azure Active Directory pubblica endpoint specifiche del tenant e tenant indipendente.

Specifiche del tenant endpoint sono progettate per un particolare tenant. I metadati di federazione specifiche del tenant includono informazioni su tenant, incluse le informazioni di endpoint di autorità che emette tenant specifiche. Le applicazioni di limitano l'accesso a un singolo tenant utilizzano i punti finali specifiche del tenant.

Gli endpoint tenant indipendente forniscono informazioni comuni ai tenant tutti Azure Active Directory. Queste informazioni si applica ai tenant ospitato presso *login.microsoftonline.com* e condivisi tra tenant. I punti finali tenant indipendente sono consigliati per applicazioni multi-tenant, poiché non sono associati a un determinato tenant.

## <a name="federation-metadata-endpoints"></a>Endpoint metadati federazione

Azure Active Directory pubblica metadati federazione in `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Per gli **endpoint specifiche del tenant**, il `TenantDomainName` può essere uno dei seguenti tipi:

- Un nome di dominio registrato di un annuncio Azure tenant, ad esempio: `contoso.onmicrosoft.com`.
- L'immutabile tenant ID del dominio, ad esempio `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Per gli **endpoint indipendente tenant**, il `TenantDomainName` è `common`. In questo documento sono elencati solo gli elementi dei metadati di federazione comuni a tutti i tenant di Azure Active Directory ospitati presso login.microsoftonline.com.

Ad esempio, potrebbe essere un endpoint specifiche del tenant `https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. L'endpoint tenant indipendente è [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). È possibile visualizzare il documento di metadati federazione digitando questo URL in un browser.

## <a name="contents-of-federation-metadata"></a>Contenuto della federazione dei metadati

La sezione seguente vengono fornite informazioni necessari per i servizi che utilizzano i token emessi Azure Active Directory.

### <a name="entity-id"></a>ID entità

Il `EntityDescriptor` elemento contiene un `EntityID` attributo. Il valore di `EntityID` attributo rappresenta autorità, vale a dire servizio token di sicurezza (servizio token di sicurezza) che ha rilasciato il token. È importante convalidare autorità quando si riceve un token.

I metadati seguenti viene illustrato un esempio specifiche del tenant `EntityDescriptor` elemento con un `EntityID` elemento.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
È possibile sostituire l'ID tenant nell'endpoint del tenant indipendente con il proprio ID tenant per creare uno specifico tenant `EntityID` valore. Il valore risultante sarà uguale autorità token. La strategia consente a un'applicazione multi-tenant convalidare autorità per un determinato tenant.

I metadati seguenti viene illustrato un esempio tenant indipendente `EntityID` elemento. Si noti che che la `{tenant}` è un valore letterale, non un segnaposto.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Certificati di firma del token

Quando un servizio riceve un token emesso da un tenant di Azure Active Directory, la firma di token deve essere convalidata con una chiave di firma che viene pubblicata in un documento di metadati federazione. I metadati di federazione includono la parte pubblica dei certificati che utilizzano il tenant per la firma di token. Byte non elaborati certificato vengono visualizzate nel `KeyDescriptor` elemento. Il certificato di firma di token è valido per l'accesso solo quando il valore della `use` attributo `signing`.

Un documento di metadati federazione pubblicato da Azure Active Directory può avere più chiavi firma, ad esempio quando di preparazione di Azure Active Directory per aggiornare il certificato di firma. Quando un documento di metadati federazione include più di un certificato, un servizio che convalida i token deve supportare tutti i certificati nel documento.

I metadati seguenti viene illustrato un esempio `KeyDescriptor` elemento con una chiave di firma.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Il `KeyDescriptor` elemento verrà visualizzato in due posizioni nel documento di metadati federazione. Nella sezione WS federazione specifici e la sezione SAML specifica. I certificati pubblicati in entrambe le sezioni saranno gli stessi.

Nella sezione WS federazione specifica un lettore di metadati WS-Federation legge i certificati da un `RoleDescriptor` elemento con il `SecurityTokenServiceType` tipo.

I metadati seguenti viene illustrato un esempio `RoleDescriptor` elemento.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

Nella sezione SAML specifica un lettore di metadati WS-Federation legge i certificati da un `IDPSSODescriptor` elemento.

I metadati seguenti viene illustrato un esempio `IDPSSODescriptor` elemento.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Non esistono differenze in formato dei certificati specifiche del tenant e tenant indipendente.

### <a name="ws-federation-endpoint-url"></a>URL endpoint WS-Federation

I metadati di federazione includono l'URL per accesso singolo e singolo disconnessione protocollo WS-Federation l'uso di Azure Active Directory. Questo endpoint viene visualizzato nel `PassiveRequestorEndpoint` elemento.

I metadati seguenti viene illustrato un esempio `PassiveRequestorEndpoint` elemento per un endpoint specifiche del tenant.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Per l'endpoint tenant indipendente URL WS-Federation verrà visualizzato endpoint WS-Federation, come illustrato nell'esempio seguente.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL endpoint protocollo SAML

I metadati di federazione includono l'URL che utilizza Azure Active Directory per single sign-in di e singolo disconnessione protocollo SAML 2.0. Questi punti finali assumono nel `IDPSSODescriptor` elemento.

URL di accesso e disconnessione vengono visualizzati nel `SingleSignOnService` e `SingleLogoutService` elementi.

I metadati seguenti viene illustrato un esempio `PassiveResistorEndpoint` per un endpoint specifiche del tenant.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Allo stesso modo endpoint per gli endpoint di protocollo SAML 2.0 comuni pubblicate nei metadati federazione indipendente tenant, come illustrato nell'esempio seguente.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
