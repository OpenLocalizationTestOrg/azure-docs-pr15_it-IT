<properties
    pageTitle="Gestire i gruppi il gruppo è un membro di in anteprima di Azure Active Directory | Microsoft Azure"
    description="I gruppi possono contenere altri gruppi di Azure Active Directory. Di seguito viene illustrato come gestire le appartenenze."
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
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Gestire i gruppi che il gruppo è un membro di in anteprima di Azure Active Directory

I gruppi possono contenere altri gruppi in anteprima di Azure Active Directory. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) Di seguito viene illustrato come gestire le appartenenze.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Come è possibile trovare i gruppi di un membro del gruppo personale?

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **altri servizi**, immettere **gli utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

  ![Gestione degli utenti di apertura](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  Scegliere **tutti i gruppi**e **l'utenti e gruppi** .

  ![Apertura e il gruppi](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. Scegliere un gruppo e **l'utenti e gruppi: tutti i gruppi** .

5. Nel * *gruppo - *nomegruppo* ** blade selezionare **raggruppare le appartenenze * *.

  ![Apertura e l'appartenenza al gruppo](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Per aggiungere il gruppo come membro del gruppo un'altra e il **gruppo - appartenenza ai gruppi** , selezionare il comando **Aggiungi** .

7. Selezionare un gruppo e il **Gruppo selezionare** e quindi selezionare il pulsante di **selezione** nella parte inferiore della stessa e. È possibile aggiungere il gruppo a un solo gruppo alla volta. Casella **utente** filtra la visualizzazione in base alla corrispondenza la voce a qualsiasi parte di un nome utente o un dispositivo. In questa casella non vengono accettate caratteri jolly.

  ![Aggiungere un appartenenza ai gruppi](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Per rimuovere il gruppo come membro del gruppo un'altra e il **gruppo - appartenenze ai gruppi** , selezionare un gruppo.

9. Scegliere e il ***nome*** , selezionare il comando **Rimuovi** e confermare la scelta quando viene richiesto.

  ![rimuovere il comando di appartenenza](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Al termine della modifica appartenenze ai gruppi per il gruppo, selezionare **Salva**.


## <a name="additional-information"></a>Ulteriori informazioni

Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Visualizzare gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
