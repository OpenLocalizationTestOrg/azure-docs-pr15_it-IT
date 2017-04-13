<properties
    pageTitle="Condividere un unico tenant di Azure AD abbonamenti a Office 365 e Azure | Microsoft Azure"
    description="Informazioni su come condividere il tenant di Azure Active Directory di Office 365 e i relativi utenti con l'abbonamento Azure o viceversa"
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
    ms.date="08/17/2016"
    ms.author="cjiang"/>

# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Usare un account Office 365 esistente con l'abbonamento Azure o viceversa
Scenario: È già ha un abbonamento a Office 365 e pronti per un abbonamento a Azure, ma si desidera utilizzare gli account utente di Office 365 esistenti per l'abbonamento Azure. In alternativa, si è abbonati Azure e si desidera ottenere un abbonamento a Office 365 per gli utenti di Azure Active Directory esistente. In questo articolo illustra quanto sia semplice raggiungere entrambi.

> [AZURE.NOTE] In questo articolo non è applicabile ai clienti contratto Enterprise (EA). Se sono necessarie altre in qualsiasi momento in questo articolo, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere il problema risolto rapidamente.


## <a name="quick-guidance"></a>Guida rapida

- Se è già ha un abbonamento a Office 365 e vuoi iscriverti per Azure, utilizzare l'opzione **accedere con l'account dell'organizzazione** . Quindi continuare il processo di iscrizione Azure con l'account di Office 365. Vedere [la procedura dettagliata più avanti in questo articolo](#s1).

- Se è già disponibile un abbonamento a pagamento Azure e per ottenere un abbonamento a Office 365, accedere a Office 365 con il proprio account Azure. Procedere con la procedura di iscrizione. Dopo aver completato l'abbonamento, l'abbonamento a Office 365 viene aggiunto alla stessa istanza di Azure Active Directory l'abbonamento Azure a cui appartiene. Per ulteriori informazioni, vedere la sezione [procedura dettagliata più avanti in questo articolo](#s2).

>[AZURE.NOTE] Per ottenere un abbonamento a Office 365, l'account usare per abbonamento deve essere membro del ruolo di directory amministratore globale o amministratore fatturazione nel tenant di Azure Active Directory. [Informazioni su come determinare il ruolo di Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

Per capire cosa accade quando si aggiunge una sottoscrizione a un account, vedere le [informazioni di base avanti in questo articolo](#background-information).

## <a name="detailed-steps"></a>Procedura dettagliata
<a id="s1"></a>
### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>Scenario 1: Utenti di Office 365 che prevede di acquistano Azure
In questo scenario si presuppone che Kelley Wall è un utente che ha un abbonamento a Office 365 e pianificazione sottoscrivere Azure. Sono disponibili due utenti attivi aggiuntivi, Olga e Marco. Si tratta di Kelley admin@contoso.onmicrosoft.com.

![Amministrazione utente di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Per iscriversi a Azure, procedere come segue:

1. Iscriversi a Azure in [Azure.com](https://azure.microsoft.com/). Fare clic su **prova gratuitamente**. Nella pagina successiva, fare clic su **Inizia ora**.

    ![Prova gratuitamente Azure.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. Fare clic su **accedere con l'account dell'organizzazione**.

    ![Accedere a Azure.](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Accedere con l'account di Office 365. In questo caso è account Office 365 di Kelley.

    ![Accedere con l'account di Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. Inserire le informazioni e completare il processo di iscrizione.

    ![Immettere le informazioni e completamento dell'iscrizione.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Fare clic su Avvia il servizio di gestione.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

A questo punto è possibile iniziare. Nel portale di Azure, vedere gli stessi utenti visualizzati. Per verificare questa impostazione, procedere come segue:

1. Fare clic su **Avvia il servizio di gestione** nella schermata precedente.
2. Fare clic su **Sfoglia**e quindi fare clic su **Active Directory**.

    ![Fare clic su Sfoglia e quindi fare clic su Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Fare clic su **utenti**.

    ![Scheda utenti](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Tutti gli utenti, inclusi Kelley, sono elencati come previsto.

    ![Elenco di utenti](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>Scenario 2: Azure utenti prevede di acquistare Office 365

In questo scenario Kelley Wall è un utente che dispone di una sottoscrizione con l'account Azure admin@contoso.onmicrosoft.com. Kelley desidera effettuare la sottoscrizione a Office 365 e utilizzare la stessa directory che ha già con Azure.

>[AZURE.NOTE] Per ottenere un abbonamento a Office 365, l'account usato per l'accesso deve essere membro del ruolo di directory amministratore globale o amministratore fatturazione nel tenant di Azure Active Directory. [Imparare a conoscere il ruolo di Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).

![Impostazioni di sottoscrizione portale Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Azure agli utenti di Active Directory del portale](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Per eseguire la sottoscrizione a Office 365, procedere come segue:

1. Passare alla [pagina di prodotto di Office 365](https://products.office.com/business)e quindi selezionare il piano a cui è adatto alle proprie esigenze.
2. Dopo aver selezionato il piano, verrà visualizzata la pagina seguente. Compilare il modulo. Fare clic su **Accedi** nell'angolo superiore destro della pagina.

    ![Pagina di valutazione di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. Accedere con le credenziali dell'account. In questo esempio è account di Kelley.

    ![Accesso di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. Fare clic su **provare ora**.

    ![Confermare l'ordine di Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. Nella pagina Conferma ordine, fare clic su **Continua**.

    ![Conferma ordine di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

A questo punto è possibile iniziare. Nell'interfaccia di amministrazione di Office 365, verrà visualizzato agli utenti della directory di Contoso appaiono utenti attivi. Per verificare questa impostazione, procedere come segue:

1. Aprire l'interfaccia di amministrazione di Office 365.
2. Espandere **utenti**e quindi fare clic su **Utenti attivi**.

    ![Utenti di Office 365 Amministrazione centrale](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Come sapere proprio ruolo all'interno di Azure Active Directory

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Sfoglia**e quindi fare clic su **Active Directory**.

    ![Active Directory nel portale di Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. Fare clic su **utenti**.

    ![Utenti di Active Directory predefinito portale Azure](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. Fare clic sull'utente. In questo esempio, l'utente è Kelley Wall.

    Si noti che il campo del **Ruolo dell'organizzazione**.

    ![Identità utente portale Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Informazioni sugli abbonamenti Azure e in Office 365
Office 365 e Azure usare il servizio di Azure Active Directory per gestire utenti e le sottoscrizioni. Valutare la possibilità di una directory Azure come contenitore in cui è possibile raggruppare gli utenti e le sottoscrizioni. Per usare lo stesso account utente per le sottoscrizioni di Azure e in Office 365, è necessario assicurarsi che gli abbonamenti vengono creati nella stessa directory. Tenere presenti i seguenti punti:

- Una sottoscrizione viene creata in una directory non viceversa.
- Gli utenti appartengono a directory e non viceversa.
- Una sottoscrizione recapitata nella directory dell'utente che ha creato l'abbonamento. Di conseguenza, l'abbonamento a Office 365 è collegato allo stesso account come abbonamento Azure quando si utilizza tale account per creare l'abbonamento a Office 365.

![Informazioni di base](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Per ulteriori informazioni, vedere [come Azure sottoscrizioni associate Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

>[AZURE.NOTE] Sottoscrizioni di Azure appartengono ai singoli utenti nella directory.

>[AZURE.NOTE] Sottoscrizioni di Office 365 appartengono a directory stessa. Se gli utenti all'interno della directory dispongano delle autorizzazioni necessarie, può operare su sottoscrizioni.

## <a name="next-steps"></a>Passaggi successivi
Se si desidera essere in grado di accedere al tenant di Office 365 dalla sottoscrizione Azure abbonamenti il Azure e di Office 365 è stato acquistato separatamente in passato, vedere [associazione di un tenant di Office 365 con un abbonamento a Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [AZURE.NOTE] Se si riscontrano ancora domande, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere il problema risolto rapidamente.
