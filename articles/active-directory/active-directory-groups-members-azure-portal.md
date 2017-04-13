<properties
    pageTitle="Gestire i membri per un gruppo in anteprima di Azure Active Directory | Microsoft Azure"
    description="Come gli utenti e dispositivi che fanno parte di un gruppo di Azure Active Directory"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Gestire i membri per un gruppo in anteprima di Azure Active Directory

In questo articolo viene illustrato come gestire i membri per un gruppo in anteprima di Azure Active Directory (Azure Active Directory). [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Come trovare i membri e gestirli

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **altri servizi**, immettere **gli utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

  ![Gestione degli utenti di apertura](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  Scegliere **tutti i gruppi**e **l'utenti e gruppi** .

  ![Apertura e il gruppi](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Scegliere un gruppo e **l'utenti e gruppi: tutti i gruppi** .

5. Nel * *gruppo - *nomegruppo* ** blade, selezionare **i membri * *.

  ![Apertura e il membri](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Per aggiungere membri al gruppo, e il **gruppo - membri** , selezionare **Aggiungi membri**.

  ![Aggiungere membri comando](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. Nella e **membri** selezionare uno o più utenti o dispositivi per aggiungere al gruppo e selezionare il pulsante di **selezione** nella parte inferiore della stessa e per aggiungerli al gruppo. Casella **utente** filtra la visualizzazione in base alla corrispondenza la voce a qualsiasi parte di un nome utente o un dispositivo. In questa casella non vengono accettate caratteri jolly.

8. Per rimuovere membri dal gruppo, e il **gruppo - membri** , selezionare un membro.

9. Scegliere e il ***nome di membro*** , selezionare il comando **Rimuovi** e confermare la scelta quando viene richiesto.

  ![rimuovere il comando membri](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Al termine della modifica i membri del gruppo, selezionare **Salva**.


## <a name="additional-information"></a>Ulteriori informazioni

Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Visualizzare gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
