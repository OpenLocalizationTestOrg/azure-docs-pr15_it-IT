<properties
   pageTitle="Azure Active Directory B2B collaborazione | Microsoft Azure"
   description="Azure Active Directory B2B collaborazione consente ai partner aziendali di accedere alle applicazioni aziendali, con ogni degli utenti rappresentati da un singolo Azure AD account"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B collaborazione

Azure Active Directory (Azure Active Directory) B2B collaborazione consente di attivare l'accesso alle applicazioni aziendali da identità gestite partner. È possibile creare relazioni tra società da invitare e autorizzazione degli utenti di società partner per accedere alle risorse di. Complessità è ridotto, in quanto ogni società riunisce una volta con Azure Active Directory e ogni utente è rappresentato da un singolo account Azure Active Directory. Protezione viene aumentata se partner aziendali gestire il proprio account Azure Active Directory perché l'accesso è stato revocato quando gli utenti partner vengono terminati delle organizzazioni e impedisce l'accesso non autorizzato tramite l'appartenenza in directory interne. Per i partner commerciali che non dispongono di Azure Active Directory, collaborazione B2B ha un'esperienza semplificata per l'abbonamento a offrire account Azure Active Directory per i partner.

-   Partner commerciali usare le proprie credenziali di accesso, che si libera dalla gestione di una directory di partner esterni, scegliere la necessità di rimuovere l'accesso quando gli utenti lasciano l'organizzazione partner.

-   Gestire l'accesso alle App indipendente del ciclo di vita di business partner account. Ciò significa, ad esempio, che è possibile revocare l'accesso senza dover richiedere al reparto IT di partner commerciale fare nulla.

## <a name="capabilities"></a>Funzionalità

Collaborazione B2B semplifica la gestione e migliora la protezione di accesso dei partner ai risorse aziendali, tra cui SaaS App Office 365, Salesforce, servizi di Windows Azure e ogni mobile cloud e applicazione attestazioni locale. Partner consente di collaborazione B2B gestire account propri e aziende è possono applicare criteri di sicurezza di accesso dei partner.

Azure B2B Directory attiva collaborazione è facile da configurare con semplificato iscrizione per i partner di qualsiasi dimensione, anche se non dispone di Azure Active Directory tramite un processo di verifica al messaggio di posta elettronica. È anche facile manutenzione con alcuna directory esterni o per le configurazioni federazione partner.

## <a name="b2b-collaboration-process"></a>Processo di collaborazione B2B

1. Azure Active Directory B2B collaborazione consente a invitare e autorizzare un insieme di utenti esterni per caricare un file con valori delimitati da virgole (CSV) delle linee che non più di 2000 al portale di collaborazione B2B un amministratore della società.

  ![Finestra di dialogo Carica file CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. Il portale invierà inviti tramite posta elettronica agli utenti esterni.

3. L'utente invitato verrà accedere a un account di lavoro esistente con Microsoft (gestito in Azure Active Directory) o ottenere un nuovo account di lavoro in Azure Active Directory.

4. Dopo aver effettuato l'accesso, l'utente verrà reindirizzato all'app che è stata condivisa con loro.

Gli inviti agli indirizzi di posta elettronica consumer (ad esempio, Gmail o [*comcast.net*](http://comcast.net/)) non sono attualmente supportati.

Per ulteriori informazioni sul funzionamento di collaborazione B2B, guardare [questo video](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Passaggi successivi
Individuare gli altri articoli sulla collaborazione di Azure Active Directory B2B.

- [Che cos'è la collaborazione di Azure Active Directory B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Come funziona](active-directory-b2b-how-it-works.md)
- [Descrizione dettagliata](active-directory-b2b-detailed-walkthrough.md)
- [Informazioni sui formati file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato di token di utenti esterni](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche degli attributi oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni relative all'anteprima corrente](active-directory-b2b-current-preview-limitations.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
