<properties
    pageTitle="Rimuovere l'assegnazione di un utente o gruppo da un'app di enterprise in anteprima di Azure Active Directory | Microsoft Azure"
    description="Come rimuovere l'assegnazione di accesso di un utente o gruppo da un'applicazione enterprise di Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Rimuovere un utente o assegnazione al gruppo da un'app enterprise in anteprima di Azure Active Directory

È facile rimuovere un utente o un gruppo l'accesso a una delle applicazioni aziendali in anteprima di Azure Active Directory (Azure Active Directory) venga assegnato. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) È necessario disporre le autorizzazioni appropriate per gestire l'applicazione enterprise. Nel riquadro di anteprima corrente, è necessario essere un amministratore globale per la directory.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Come rimuovere un utente o assegnazione al gruppo?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2. Selezionare **altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.

3. Di * *Azure Active Directory - *directoryname* ** blade (vale a dire Azure Active Directory pala della directory vengono gestiti), selezionare **Enterprise applicazioni * *.

    ![App aziendali di apertura](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. Scegliere **tutte le applicazioni**e **l'applicazioni aziendali** . Verrà visualizzato un elenco delle App che è possibile gestire.

5. Scegliere un'app e **l'applicazioni aziendali - tutte le applicazioni** .

6. Scegliere **utenti e gruppi**e il ***NomeApplicazione*** (ovvero, e il con il nome dell'app selezionato nel titolo).

    ![Selezionare gli utenti o gruppi](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. In ***NomeApplicazione*** **-utente & assegnazione al gruppo** blade, selezionare uno degli altri utenti o gruppi e quindi selezionare il comando **Rimuovi** . Confermare la scelta quando viene richiesto.

    ![Selezionare il comando Rimuovi](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Passaggi successivi

- [Vedere tutti i gruppi](active-directory-groups-view-azure-portal.md)
- [Assegnare un utente o gruppo a un'applicazione enterprise](active-directory-coreapps-assign-user-azure-portal.md)
- [Disattivare gli accessi utente per un'applicazione enterprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Modificare il nome o il logo di un'applicazione enterprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
