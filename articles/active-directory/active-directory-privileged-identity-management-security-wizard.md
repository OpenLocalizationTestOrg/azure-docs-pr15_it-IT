<properties
   pageTitle="L'impostazione guidata sicurezza di Azure Active Directory privilegi di gestione delle identità"
   description="La prima volta di utilizzare l'estensione di Azure Active Directory privilegi gestione delle identità, verrà visualizzata con una procedura guidata sicurezza. In questo articolo vengono illustrati i passaggi per la creazione guidata."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>L'impostazione guidata sicurezza di Azure Active Directory privilegi di gestione delle identità

Se si è la prima persona per l'esecuzione di Azure privilegi identità PIM (Management) per l'organizzazione, verrà visualizzata una procedura guidata. La procedura guidata consente di considerare i rischi di sicurezza di identità privilegi e su come usare PIM per ridurre i rischi. Non è necessario apportare le modifiche alle assegnazioni di ruolo esistente nella creazione guidata, se si preferisce eseguire in un secondo momento.

## <a name="what-to-expect"></a>Cosa aspettarsi

Prima che l'organizzazione si avvia con PIM, tutte le assegnazioni di ruolo sono permanente: gli utenti sono sempre questi ruoli anche se attualmente non è necessario i privilegi.  Il primo passaggio della procedura guidata viene visualizzato un elenco dei ruoli privilegi elevati e numero di utenti che appartengono a tali ruoli. È possibile eseguire il drill-a un particolare ruolo per ulteriori informazioni sugli utenti se uno o più delle loro si ha familiarità.

Il secondo passaggio della procedura guidata offre la possibilità di modificare le assegnazioni di ruolo dell'amministratore.  

> [AZURE.WARNING]È importante avere almeno un amministratore globale e più di un amministratore di ruoli privilegi con un account aziendale (non un account Microsoft). Se c'è solo un amministratore di ruoli privilegi, non sarà possibile gestire PIM se tale account viene eliminato l'organizzazione.
> Tenere inoltre assegnazioni di ruolo permanente se un utente ha un account Microsoft (un account che consente di accedere a servizi come Skype e Outlook.com). Se si prevede di richiedere MFA per l'attivazione per tale ruolo, tale utente verrà bloccato.


Dopo avere apportato modifiche, non verrà visualizzata non è più. La volta successiva che l'utente o un altro amministratore ruolo privilegi usare PIM, verrà visualizzato nel dashboard PIM.  

- Se si desidera aggiungere o rimuovere utenti dai ruoli o modificare le assegnazioni da permanente a idoneo, per ulteriori come [aggiungere o rimuovere un ruolo utente](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Se si desidera concedere l'accesso per gestire PIM altri utenti, per saperne di più su [come concedere l'accesso per gestire in PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
