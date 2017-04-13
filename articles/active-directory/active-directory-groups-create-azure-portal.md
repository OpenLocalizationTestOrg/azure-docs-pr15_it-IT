<properties
    pageTitle="Creare un nuovo gruppo in anteprima di Azure Active Directory | Microsoft Azure"
    description="Come creare un gruppo di Azure Active Directory e aggiungere utenti (membri) al gruppo"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Creare un nuovo gruppo in anteprima di Azure Active Directory

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-groups-create-azure-portal.md)
- [Portale classica Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

In questo articolo viene spiegato come creare e inserire un nuovo gruppo nel riquadro di anteprima di Azure Active Directory (Azure Active Directory). [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) Usare un gruppo per eseguire attività di gestione, ad esempio l'assegnazione di licenze o le autorizzazioni a un numero di utenti o dispositivi contemporaneamente.

## <a name="how-do-i-create-a-group"></a>Creazione di un gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2. Selezionare **altri servizi**, immettere **utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

  ![Gestione degli utenti di apertura](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. Scegliere **tutti i gruppi**e **l'utenti e gruppi** .

  ![Apertura e il gruppi](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. Scegliere il comando **Aggiungi** e **l'utenti e gruppi: tutti i gruppi** .

  ![Selezionare il comando Aggiungi](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. In e il **gruppo** , aggiungere un nome e una descrizione per il gruppo.

6. Per selezionare i membri che si desidera aggiungere al gruppo, selezionare **assegnato** nella casella **tipo di appartenenza** e quindi selezionare **i membri**. Per ulteriori informazioni su come gestire l'appartenenza a un gruppo in modo dinamico, vedere [attributi di utilizzo per creare regole avanzate per l'appartenenza ai gruppi](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Selezione membri da aggiungere](./media/active-directory-groups-create-azure-portal/select-members.png)

5. Nella e **membri** selezionare uno o più utenti o dispositivi per aggiungere al gruppo e selezionare il pulsante di **selezione** nella parte inferiore della stessa e per aggiungerli al gruppo. Casella **utente** filtra la visualizzazione in base alla corrispondenza la voce a qualsiasi parte di un nome utente o un dispositivo. In questa casella non vengono accettate caratteri jolly.

6. Dopo aver aggiunto i membri al gruppo, selezionare **Crea** e il **gruppo** .    

  ![Creare una conferma di gruppo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>Ulteriori informazioni

Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Visualizzare gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
