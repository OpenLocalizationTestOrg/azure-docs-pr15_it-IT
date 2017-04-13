<properties
    pageTitle="Azure Active B2C di Directory: Configurazione dell'account di Microsoft | Microsoft Azure"
    description="Fornire per l'abbonamento e l'accesso agli utenti con un account di Microsoft nelle applicazioni che sono protetti da Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure B2C Directory attiva: Fornire per l'abbonamento e l'accesso agli utenti con un account Microsoft

## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft

Per usare l'account Microsoft come provider di identità in B2C Azure Active Directory (Azure Active Directory), è necessario creare un'applicazione di account Microsoft e fornire con i parametri di destro. È necessario un account Microsoft per eseguire questa operazione. Se non si dispone di uno, è possibile ottenere in [https://www.live.com/](https://www.live.com/).

1. Accedere al [Portale di registrazione dell'applicazione di Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e accedere con le credenziali dell'account Microsoft.
2. Fare clic su **Aggiungi un'app**.

    ![Microsoft account: aggiunta di una nuova app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Specificare un **nome** per l'applicazione e fare clic su **Crea applicazione**.

    ![Account di Microsoft - nome dell'App](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Copiare il valore **dell'Id dell'applicazione**. Sarà necessario per configurare l'account Microsoft come provider di identità nel tenant.

    ![Account di Microsoft - Id dell'applicazione](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Fare clic su **Aggiungi piattaforma** e scegliere **Web**.

    ![Microsoft account: aggiunta di piattaforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Account di Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URI reindirizzare la chiamata** . Sostituire **{tenant}** con il nome del tenant (ad esempio contosob2c.onmicrosoft.com).

    ![Account di Microsoft - Reindirizzamento URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. Fare clic su **Genera nuova Password** nella sezione **Informazioni riservate dell'applicazione** . Copiare la nuova password visualizzata sullo schermo. Sarà necessario per configurare l'account Microsoft come provider di identità nel tenant. La password è una credenziale di sicurezza importanti.

    ![Microsoft account - generare nuova password](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Account di Microsoft - nuova password](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Selezionare la casella che indica che **supportano Live SDK** nella sezione **Opzioni avanzate** . Fare clic su **Salva**.

    ![Account di Microsoft - supporto Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurare l'account Microsoft come provider di identità nel tenant

1. Seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Scegliere **i provider di identità**e il caratteristiche B2C.
3. Fare clic su **Add +** nella parte superiore e il.
4. Fornire un **nome** descrittivo per la configurazione del provider di identità. Ad esempio, immettere "MSA".
5. Fare clic su **tipo di provider di identità**, selezionare **l'account Microsoft**e fare clic su **OK**.
6. Fare clic su **Configura il provider di identità** e immettere l'Id dell'applicazione e la password dell'applicazione Microsoft account creato in precedenza.
7. Fare clic su **OK** e quindi fare clic su **Crea** per salvare la configurazione dell'account Microsoft.
