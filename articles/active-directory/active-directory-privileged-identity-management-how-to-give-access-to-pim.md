<properties
   pageTitle="Come concedere l'accesso a PIM | Microsoft Azure"
   description="Informazioni su come aggiungere i ruoli degli utenti con l'estensione di Azure Active Directory privilegi gestione delle identità in modo che gestiscono PIM."
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
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>Come concedere l'accesso per gestire i privilegi di Azure Active Directory di gestione delle identità

È possibile ottenere l'amministratore globale che consente di Azure Active Directory privilegi di gestione delle identità (PIM) per un'organizzazione automaticamente l'accesso a PIM o le assegnazioni di ruolo. Nessun utente ottiene accesso scrittura per impostazione predefinita, tuttavia, inclusi altri amministratori globali. Altri amministratori globali, amministratori della protezione e ai lettori di sicurezza dispongono di accesso in sola lettura per PIM di Azure Active Directory. Per concedere l'accesso a PIM, il primo utente possa assegnare ad altri utenti al ruolo **amministratore ruoli privilegi** . L'assegnazione deve essere eseguita dall'interno PIM stesso e non può essere modificato tramite PowerShell o altri portali.

> [AZURE.NOTE] La gestione di Azure Active Directory PIM richiede Azure MFA. Poiché account di Microsoft non è possibile eseguire la registrazione per MFA Azure, un utente che accede con un account Microsoft non è possibile accedere PIM di Azure Active Directory.

Verificare che ci sono sempre almeno due utenti in un ruolo di amministratore ruolo privilegi, nel caso in cui un utente è bloccato o viene eliminato il proprio account.

## <a name="give-another-user-access-to-manage-pim"></a>Assegnare un altro utente l'accesso a gestire PIM

1. Accedere al [portale di Azure](https://portal.azure.com/) e selezionare l'app di **Azure Active Directory privilegi di gestione delle identità** nel dashboard.
2. Selezionare **Gestisci ruoli privilegi** > **amministratore ruoli privilegi** > **Aggiungi**.

    ![Aggiungere amministratori ruolo privilegi - cattura di schermata][1]

4. In e l'utenti gestiti Aggiungi passaggio 1 è già completo. Selezionare il passaggio 2, **selezionare utenti** e cercare l'utente che si desidera aggiungere.

    ![Selezionare gli utenti - schermata][2]

6. Selezionare l'utente nei risultati della ricerca e fare clic su **Chiudi**.
7. Fare clic su **OK** per salvare la selezione. L'utente che è stato selezionato verrà visualizzato nell'elenco di amministratori privilegi ruolo.

    - Ogni volta che si assegna un nuovo ruolo a un utente, vengono automaticamente impostati come idoneo per attivare il ruolo. Se si vuole renderle effettive del ruolo, fare clic sull'utente nell'elenco. Selezionare **autorizzazioni** nel menu informazioni utente.

8. Inviare l'utente un collegamento alla [Guida introduttiva di Azure Active Directory privilegi di gestione delle identità](active-directory-privileged-identity-management-getting-started.md).


## <a name="remove-another-users-access-rights-for-managing-pim"></a>Rimuovere i diritti di accesso di un altro utente per la gestione dei PIM

Prima di rimuovere un utente dal ruolo di amministratore ruolo privilegi, verificare sempre sarà ancora presente due utenti assegnati a essa.

1. Nel dashboard PIM fare clic sul ruolo di **amministratore ruoli privilegi**.  Verrà visualizzato l'elenco di utenti attualmente in tale ruolo.
2. Fare clic su utente nell'elenco degli utenti.
3. Fare clic su **Rimuovi**.  Viene visualizzata con un messaggio di conferma.
4. Fare clic su **Sì** per rimuovere l'utente dal ruolo.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
