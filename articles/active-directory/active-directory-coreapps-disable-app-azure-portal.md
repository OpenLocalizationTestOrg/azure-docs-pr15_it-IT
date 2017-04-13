<properties
    pageTitle="Disattivare gli accessi utente per un'applicazione enterprise in anteprima di Azure Active Directory | Microsoft Azure"
    description="Come disattivare un'applicazione aziendale in modo che nessun utente possono accedere a tale di Azure Active Directory"
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
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Disattivare gli accessi utente per un'applicazione enterprise in anteprima di Azure Active Directory

È facile disattivare un'applicazione aziendale in modo che nessun utente possono accedere a tale in anteprima di Azure Active Directory (Azure Active Directory). [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) È necessario disporre le autorizzazioni appropriate per gestire l'applicazione enterprise. Nel riquadro di anteprima corrente, è necessario essere un amministratore globale per la directory.

## <a name="how-do-i-disable-user-sign-ins"></a>Come è possibile disattivare gli accessi utente?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2. Selezionare **altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.

3. Di * *Azure Active Directory - *directoryname* ** blade (vale a dire Azure Active Directory pala della directory vengono gestiti), selezionare **Enterprise applicazioni * *.

    ![App aziendali di apertura](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. Scegliere **tutte le applicazioni**e **l'applicazioni aziendali** . Verrà visualizzato un elenco delle App che è possibile gestire.

5. Scegliere un'app e **l'applicazioni aziendali - tutte le applicazioni** .

6. Scegliere **proprietà**e il ***NomeApplicazione*** (ovvero, e il con il nome dell'app selezionato nel titolo).

    ![Selezionare il comando tutte le applicazioni](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. In ***NomeApplicazione*** **-proprietà** blade, selezionare **No** per **abilitate per gli utenti effettuare l'accesso?**.

8. Selezionare il comando **Salva** .

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare tutti i gruppi](active-directory-groups-view-azure-portal.md)
- [Assegnare un utente o gruppo a un'applicazione enterprise](active-directory-coreapps-assign-user-azure-portal.md)
- [Rimuovere l'assegnazione di un utente o gruppo da un'applicazione enterprise](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Modificare il nome o il logo di un'applicazione enterprise](active-directory-coreapps-change-app-logo-user-azure-portal.md)
