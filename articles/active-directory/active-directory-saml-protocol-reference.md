<properties
    pageTitle="Riferimento al protocollo Azure Active Directory SAML | Microsoft Azure"
    description="In questo articolo viene fornita una panoramica dei profili Single Sign-On e SAML Sign-Out singola di Azure Active Directory."
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
    ms.date="06/23/2016"
    ms.author="priyamo"/>


# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Modalità di utilizzo del protocollo SAML Azure Active Directory

Azure Active Directory (Azure Active Directory) utilizza SAML 2.0 protocollo consentono alle applicazioni assicurare un'esperienza single sign-on agli utenti. I profili [Single Sign-On](active-directory-single-sign-on-protocol-reference.md) e SAML [singola disconnessione](active-directory-single-sign-out-protocol-reference.md) di Azure Active Directory illustrano come SAML asserzioni, associazioni e protocolli utilizzate nel servizio di provider di identità.

Protocollo SAML richiede il provider di identità (Azure Active Directory) e il provider di servizi, l'applicazione, per lo scambio di informazioni su se stessi.

Quando viene registrata un'applicazione con Azure Active Directory, dello sviluppatore dell'app registra informazioni relative alla federazione con Azure Active Directory. Include il **Reindirizzamento URI** e **Metadati URI** dell'applicazione.

Azure Active Directory utilizza l' **URI dei metadati** del servizio cloud per recuperare la chiave di firma e la disconnessione URI del servizio cloud. Se l'applicazione non supporta un URI dei metadati, lo sviluppatore necessario contattare il supporto tecnico Microsoft per fornire la disconnessione URI e chiave di firma.

Azure Active Directory espone specifiche del tenant e comuni (tenant indipendente) single sign-on e singolo disconnessione endpoint. Questi URL rappresentano posizioni utilizzabili: non sono un identificatori, in modo che è possibile accedere al punto finale per leggere i metadati.

 - Endpoint specifiche Tenant si trova in `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  Il <TenantDomainName> segnaposto rappresenta un nome di dominio registrato o TenantID GUID di un tenant di Azure Active Directory. Ad esempio, i metadati di federazione del tenant contoso.com si trova: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

- Endpoint indipendente Tenant si trova in `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. In questo indirizzo endpoint **comuni** viene visualizzata, invece che tramite un nome di dominio tenant o ID.

Per informazioni sui documenti metadati federazione che pubblica Azure Active Directory, vedere [Federazione metadati](active-directory-federation-metadata.md).
