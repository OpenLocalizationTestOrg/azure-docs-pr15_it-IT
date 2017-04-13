<properties
   pageTitle="Limitazioni relative all'anteprima corrente per la collaborazione di Azure Active Directory B2B | Microsoft Azure"
   description="Azure B2B Directory Active supporta le relazioni tra società abilitando business partner in modo selettivo accedere alle applicazioni aziendali"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Anteprima di collaborazione Azure Active Directory B2B: corrente anteprima limitazioni

- Autenticazione a più fattori (MFA) non è supportata per gli utenti esterni. Ad esempio, se Contoso ha MFA, ma non Partner organigramma, quindi organigramma Partner concesse agli utenti non è possibile MFA tramite la collaborazione B2B.
- Gli inviti sono possibili solo tramite CSV; gli inviti singoli e accesso alle API non sono supportate.
- Solo amministratori globali di Azure Active Directory è possibile caricare file con estensione csv.
- Gli inviti agli indirizzi di posta elettronica consumer (ad esempio hotmail.com, Gmail.com o comcast.net) non sono attualmente supportati.
- L'accesso degli utenti esterni per le applicazioni non verificate in locale.
- Gli utenti esterni non vengono automaticamente eliminati quando l'utente effettivo viene eliminato dalla propria directory.
- Gli inviti liste di distribuzione non sono supportati.
- Numero massimo di 2.000 record può essere caricato tramite CSV.

## <a name="related-articles"></a>Articoli correlati
Individuare gli altri articoli sulla collaborazione di Azure Active Directory B2B:

- [Che cos'è la collaborazione di Azure Active Directory B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Come funziona](active-directory-b2b-how-it-works.md)
- [Descrizione dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Informazioni sui formati file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato di token di utenti esterni](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche degli attributi oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
