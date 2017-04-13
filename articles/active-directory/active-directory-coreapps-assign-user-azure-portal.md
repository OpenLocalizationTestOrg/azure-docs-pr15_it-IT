<properties
    pageTitle="Assegnare un utente o gruppo a un'applicazione enterprise in anteprima di Azure Active Directory | Microsoft Azure"
    description="Come selezionare un'applicazione enterprise da assegnarle un utente o gruppo di Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Assegnare un utente o gruppo a un'applicazione enterprise in anteprima di Azure Active Directory

È facile assegnare un utente o un gruppo di applicazioni aziendali in anteprima di Azure Active Directory (Azure Active Directory). [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) È necessario disporre le autorizzazioni appropriate per gestire l'applicazione enterprise. Nel riquadro di anteprima corrente, è necessario essere un amministratore globale per la directory.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Come assegnare l'accesso degli utenti a un'applicazione enterprise?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2. Selezionare **altri servizi**, immettere Azure Active Directory nella casella di testo e quindi premere **INVIO**.

3. Di * *Azure Active Directory - *directoryname* ** blade (vale a dire Azure Active Directory pala della directory vengono gestiti), selezionare **Enterprise applicazioni * *.

    ![App aziendali di apertura](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. Scegliere **tutte le applicazioni**e **l'applicazioni aziendali** . Verrà visualizzato un elenco delle App che è possibile gestire.

5. Scegliere un'app e **l'applicazioni aziendali - tutte le applicazioni** .

6. Scegliere **utenti e gruppi**e il ***NomeApplicazione*** (ovvero, e il con il nome dell'app selezionato nel titolo).

    ![Selezionare il comando tutte le applicazioni](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. In ***NomeApplicazione*** **-utente & assegnazione al gruppo** blade, selezionare il comando **Aggiungi** .

8. Scegliere **utenti e gruppi**e **l'Aggiungi assegnazione** .

    ![Assegnare un utente o gruppo all'app](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. In e **l'utenti e gruppi** , selezionare uno o più utenti o gruppi dall'elenco e quindi selezionare il pulsante di **selezione** nella parte inferiore della stessa e.

10. Scegliere **ruolo**e **l'Aggiungi assegnazione** . Quindi, scegliere un ruolo da applicare agli utenti selezionati o ai gruppi, e il **Ruolo di selezionare** e quindi selezionare il pulsante **OK** nella parte inferiore della stessa e.

11. Scegliere il pulsante **Assegna** nella parte inferiore della stessa e e **l'Aggiungi assegnazione** . Gli utenti assegnati o i gruppi avrà le autorizzazioni definite dal ruolo selezionato per questa applicazione enterprise.

## <a name="next-steps"></a>Passaggi successivi

- [Vedere tutti i gruppi](active-directory-groups-view-azure-portal.md)
- [Rimuovere l'assegnazione di un utente o gruppo da un'applicazione enterprise](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Disattivare gli accessi utente per un'applicazione enterprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Modificare il nome o il logo di un'applicazione enterprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
