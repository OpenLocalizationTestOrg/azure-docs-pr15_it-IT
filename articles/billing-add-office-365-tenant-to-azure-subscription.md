<properties
    pageTitle="Usare un tenant di Office 365 con un abbonamento a Azure | Microsoft Azure"
    description="Informazioni su come aggiungere una directory di Office 365 (tenant) a un abbonamento Azure per definire l'associazione."
    services=""
    documentationCenter=""
    authors="JiangChen79"
    manager="mbaldwin"
    editor=""
    tags="billing,top-support-issue"/>

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="cjiang"/>

# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Associare un tenant di Office 365 con un abbonamento a Azure
Se le sottoscrizioni di Azure e Office 365 è stato acquistato separatamente in passato e ora si desidera essere in grado di accedere al tenant di Office 365 dalla sottoscrizione Azure, è facile per farlo. In questo articolo illustra come.

> [AZURE.NOTE] In questo articolo non è applicabile ai clienti contratto Enterprise (EA).

## <a name="quick-guidance"></a>Guida rapida
Per associare il tenant di Office 365 con l'abbonamento Azure, usare l'account Azure per aggiungere il tenant di Office 365 e quindi associare la sottoscrizione Azure tenant di Office 365.

## <a name="detailed-steps"></a>Procedura dettagliata
In questo scenario Kelley Wall è un utente che dispone di una sottoscrizione con l'account Azure kelley.wall@outlook.com. Kelley è installato anche un abbonamento a Office 365 con l'account kelley.wall@contoso.onmicrosoft.com. Kelley desidera accedere tenant di Office 365 con l'abbonamento Azure.

![Stato di schermata di Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Utenti attivi schermata di Office 365 Amministrazione centrale](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>Prerequisiti
Per l'associazione per il corretto funzionamento, i prerequisiti seguenti sono necessari:

- È necessario le credenziali dell'amministratore del servizio di abbonamento Azure. Gli CO-amministratori di non è possibile eseguire un sottoinsieme dei passaggi.
- È necessario disporre delle credenziali di un amministratore globale del tenant di Office 365.
- L'indirizzo di posta elettronica dell'amministratore del servizio deve essere incluso nel tenant di Office 365.
- L'indirizzo di posta elettronica dell'amministratore del servizio non deve corrispondere che un amministratore globale del tenant di Office 365.
- Se al momento si usano un indirizzo di posta elettronica è un account Microsoft e un account dell'organizzazione, modificare temporaneamente l'amministratore del servizio di abbonamento Azure per usare un altro account Microsoft. È possibile creare un nuovo account di Microsoft nella [pagina account Microsoft](https://signup.live.com/).


Per modificare l'amministratore del servizio, procedere come segue:

1. Accedere al [portale di gestione degli Account](https://account.windowsazure.com/subscriptions).
2. Selezionare l'abbonamento che si desidera modificare.
3. Selezionare **Modifica dettagli abbonamento**.

    ![Informazioni sull'abbonamento schermata di Azure, con "Modifica dettagli abbonamento" evidenziato](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

4. Nella casella **Amministratore del servizio** immettere l'indirizzo di posta elettronica dell'amministratore del servizio nuovo.

    ![Schermata della finestra di dialogo "Modifica l'abbonamento"](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Associare il tenant di Office 365 con l'abbonamento Azure
Per associare il tenant di Office 365 con l'abbonamento Azure, procedere come segue:

1.  Accedere al [portale di gestione degli Account](https://account.windowsazure.com/subscriptions) con le credenziali di amministratore di servizio.
2.  Nel riquadro sinistro selezionare **ACTIVE DIRECTORY**.

    ![Voce di schermata di Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

    > [AZURE.NOTE] Non è visualizzato il tenant di Office 365. Se è presente, ignorare il passaggio successivo.

    ![Schermata della directory predefinita di Azure Active Directory](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. Aggiungere il tenant di Office 365 per l'abbonamento Azure.

    un. Selezionare **Nuovo** > **DIRECTORY** > **Crea personalizzato**.

    ![Schermata di Azure Active Directory personalizzato creare](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

    b. Nella pagina **Aggiungi directory** nella **DIRECTORY**, selezionare **utilizza directory esistente**. Quindi selezionare **Pronto essere disconnessi ora**e **completamento** ![icona completare](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Schermata di "Utilizzare directory esistente"](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

    c. Dopo che si è disconnessi, accedere con le credenziali di amministratore globale del tenant di Office 365.

    ![Accesso aggiuntivo amministratore globale di schermata di Office 365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

    d. Selezionare **Continua**.

    ![Schermata di verifica](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

    e. Selezionare **Esci**.

    ![Schermata della disconnessione](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

    f. Accedere al [portale di gestione degli Account](https://account.windowsazure.com/subscriptions) con le credenziali di amministratore di servizio.

    ![Accesso nella schermata di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

    g. Verrà visualizzato il tenant di Office 365 nel dashboard.

    ![Schermata del dashboard](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. Modificare la directory associata alla sottoscrizione Azure.

    un. Selezionare **Impostazioni**.

    ![Icona di impostazioni del portale classica schermata di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

    b. Selezionare l'abbonamento Azure e quindi selezionare **Modifica DIRECTORY**.
    ![Directory di modifica abbonamento schermata di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

    c. Selezionare **Avanti** ![Avanti icona](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png).

    ![Schermata di "Modifica directory associata"](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

    > [AZURE.WARNING] Si riceverà un messaggio di avviso che verranno rimossi tutti gli amministratori condivisa.

    ![Azure confermare-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

    >[AZURE.WARNING] Inoltre, tutti gli utenti di [controllo dell'accesso basato sui ruoli (RBAC)](./active-directory/role-based-access-control-configure.md) con access assegnate in gruppi di risorse esistenti vengono rimosse anche. Tuttavia, si riceve il messaggio di avviso menzioni solo la rimozione di coamministratori.

    ![assegnato a utenti-rimosso--gruppi di risorse](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

    d. Selezionare **completa** ![icona completare](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

5. A questo punto è possibile aggiungere l'account aziendale di Office 365 come coamministratori nel tenant di Azure Active Directory.

    un. Selezionare la scheda **ADMINISTRATORS** e quindi selezionare **Aggiungi**.

    ![Scheda gli amministratori di impostazioni del portale classica schermata di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

    b. Immettere un account dell'organizzazione del tenant di Office 365, selezionare l'abbonamento Azure e quindi selezionare **completa** ![icona completare](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png).

    ![Schermata di Azure aggiungere CO-amministratore finestra di dialogo](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

    c. Tornare alla scheda **amministratori** . Verrà visualizzato l'account dell'organizzazione visualizzato come amministratore condivisa.

    ![Schermata della scheda gli amministratori](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. Avanti è possibile verificare l'accesso con l'amministratore di creazione.

    un. Disconnettersi da portale di gestione degli Account.

    b. Aprire il [portale di gestione degli Account](https://account.windowsazure.com/subscriptions) o un [portale di Azure](https://portal.azure.com/).

    c. Se la pagina di accesso Azure include un collegamento di **accedere con l'account dell'organizzazione**, selezionare il collegamento. In caso contrario, ignorare questo passaggio.

    ![Pagina di accesso schermata di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

    d. Immettere le credenziali di amministratore condivisa e quindi selezionare **Accedi**.

    ![Pagina di accesso schermata di Azure](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>Passaggi successivi
Scenari correlati includono:

- Già ha un abbonamento a Office 365 e si è pronti per un abbonamento a Azure, ma si desidera utilizzare gli account utente di Office 365 esistenti per l'abbonamento Azure.
- Si è abbonati Azure e si desidera ottenere un abbonamento a Office 365 per gli utenti nell'istanza di Azure Active Directory esistente.

Per informazioni su come eseguire queste operazioni, vedere [usare l'esistente di Office 365 account con l'abbonamento Azure o viceversa](billing-use-existing-office-365-account-azure-subscription.md).
