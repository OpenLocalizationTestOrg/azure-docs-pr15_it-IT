<properties
   pageTitle="Come aggiungere o rimuovere un ruolo utente | Microsoft Azure"
   description="Informazioni su come aggiungere ruoli alle identità privilegi con l'applicazione di Azure Active Directory privilegi gestione delle identità."
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
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure Active Directory privilegi di gestione delle identità: Come aggiungere o rimuovere un ruolo utente

Con Azure Active Directory (Active Directory), un'amministratore globale o amministratore della società, aggiornarne quali utenti sono **definitivamente** assegnate ai ruoli Azure Active Directory. Questa operazione viene eseguita con i cmdlet di PowerShell come `Add-MsolRoleMember` e `Remove-MsolRoleMember`. O utilizzano il portale classico Azure come descritto in [assegnazione di ruoli di amministratore di Azure Active Directory](active-directory-assign-admin-roles.md).

L'applicazione di Azure Active Directory privilegi di gestione delle identità consente agli amministratori di ruolo privilegi effettuare un'assegnazione di ruolo permanente, nonché. Inoltre, gli amministratori di ruolo privilegi inserirvi gli utenti **idoneo** per i ruoli di amministratore. Un amministratore idoneo possibile attivare il ruolo quando hanno bisogno e quindi le relative autorizzazioni scadono una volta completati.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Gestire i ruoli con PIM nel portale di Azure

All'interno dell'organizzazione, è possibile assegnare utenti ai ruoli amministrativi diversi in Azure Active Directory, applicazioni e servizi di Microsoft Office 365 e altri.  Sono disponibili ulteriori informazioni sui ruoli disponibili ruoli di [Azure Active Directory PIM](active-directory-privileged-identity-management-roles.md).

Per aggiungere o rimuovere un utente in un ruolo con privilegi di gestione delle identità, visualizzare il dashboard PIM. Quindi fare clic sul pulsante **gli utenti di ruoli di amministratore** o scegliere un ruolo specifico (ad esempio amministratore globale) dalla tabella ruoli.

> [AZURE.NOTE] Se è stata ancora abilitata PIM nel portale di Azure, passare alla [Guida introduttiva di Azure Active Directory PIM](active-directory-privileged-identity-management-getting-started.md) per informazioni dettagliate.

Se si desidera concedere a un altro utente l'accesso a PIM stesso, i ruoli che PIM richiede all'utente ulteriori informazioni sono vedere [come concedere l'accesso a PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Aggiungere un utente a un ruolo

1. Nel [portale di Azure](https://portal.azure.com/), selezionare il riquadro di **Azure Active Directory privilegi di gestione delle identità** nel dashboard.
2. Selezionare **Gestisci ruoli privilegi**.
3. Nella tabella **ruolo riepilogo** , selezionare il ruolo che si desidera gestire.
4. Nella finestra e ruolo, selezionare **Aggiungi**.
5. Fare clic su **selezionare utenti** e cercare l'utente e il **Selezionare gli utenti** .  
6. Selezionare l'utente dall'elenco dei risultati di ricerca e fare clic su **Chiudi**.
4. Fare clic su **OK** per salvare la selezione. L'utente che è stato selezionato verrà visualizzato nell'elenco come idoneo per il ruolo.

> [AZURE.NOTE]
>Nuovi utenti in un ruolo sono solo idonei per il ruolo per impostazione predefinita. Se si desidera rendere permanente il ruolo, fare clic sull'utente nell'elenco. Informazioni relative all'utente verranno visualizzato in una nuova pala. Selezionare **autorizzazioni** nel menu informazioni utente.  
>Se un utente non è possibile eseguire la registrazione per l'autenticazione a più fattori Azure (MFA) o usando un account Microsoft (in genere @outlook.com), è necessario per renderli permanente in tutti i ruoli. Gli amministratori idonei chiesto di eseguire la registrazione per MFA durante l'attivazione.

Ora che l'utente è idoneo per un ruolo, è consigliabile avvisarlo che è possibile attivare seguendo le istruzioni su [come attivare o disattivare un ruolo](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Rimuovere un utente da un ruolo

È possibile rimuovere utenti da assegnazioni di ruolo idoneo ma assicurarsi che sia sempre almeno un utente che un amministratore globale permanente.

Seguire questa procedura per rimuovere un utente specifico da un ruolo:

1. Passare al ruolo nell'elenco ruolo selezionando un ruolo nel dashboard di Azure Active Directory PIM o facendo clic sul pulsante **gli utenti di ruoli di amministratore** .
2. Fare clic su utente nell'elenco degli utenti.
3. Fare clic su **Rimuovi**. Un messaggio verrà chiesto di confermare.
4. Fare clic su **Sì** per rimuovere il ruolo dell'utente.

Se non si sa quali gli utenti devono ancora le assegnazioni di ruolo, è possibile [avviare una revisione di accesso per il ruolo](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
