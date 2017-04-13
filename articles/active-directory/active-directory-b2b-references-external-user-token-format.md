<properties
   pageTitle="Formato di token di utenti esterni per l'anteprima di collaborazione di Azure Active Directory B2B | Microsoft Azure"
   description="Azure B2B Directory attiva supporta le relazioni tra società abilitando business partner in modo selettivo accedere alle applicazioni aziendali"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Anteprima di collaborazione Azure Active Directory B2B: formato token di utenti esterni

I reclami per un annuncio di Azure standard token sono descritti nell'articolo [supportati Token e tipi di attestazione](active-directory-token-and-claims.md) in azure.microsoft.com.

Le attestazioni sono diverse per un utente esterno collaborazione B2B autenticato sono i seguenti:<br/>
- **OID:** l'ID di oggetto del tenant di risorse<br/>
- **TID**: tenant ID del tenant di risorse<br/>
- **Emittente**: si tratta di tenant delle risorse<br/>
- **IDP**: si tratta di tenant iniziale dell'utente<br/>
- **AltSecId**: l'ID di sicurezza alternativi, opaco all'utente<br/>

## <a name="related-articles"></a>Articoli correlati
Individuare gli altri articoli sulla collaborazione di Azure Active Directory B2B:

- [Che cos'è la collaborazione di Azure Active Directory B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Come funziona](active-directory-b2b-how-it-works.md)
- [Descrizione dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Informazioni sui formati file CSV](active-directory-b2b-references-csv-file-format.md)
- [Modifiche degli attributi oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni relative all'anteprima corrente](active-directory-b2b-current-preview-limitations.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
