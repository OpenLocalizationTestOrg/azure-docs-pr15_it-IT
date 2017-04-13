<properties
    pageTitle="Azure B2C Directory attiva: Configurazione Google + | Microsoft Azure"
    description="Fornire per l'abbonamento e l'accesso agli utenti con un account di Google + nelle applicazioni che sono protetti da Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure B2C Directory attiva: Disponibili per l'abbonamento e l'accesso agli utenti con un account di Google +

## <a name="create-a-google-application"></a>Creare un'applicazione di Google +

Per usare Google + come provider di identità in B2C Azure Active Directory (Azure Active Directory), è necessario creare un'applicazione di Google + e fornire con i parametri di destro. È necessario un account Google + per eseguire questa operazione. Se non si dispone di uno, è possibile ottenere in [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Passare alla [Console di Google gli sviluppatori](https://console.developers.google.com/) e accedere con le credenziali dell'account di Google +.
2. Fare clic su **Crea progetto**, immettere il **nome del progetto**e quindi fare clic su **Crea**.

    ![Google + - Guida introduttiva](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google + - nuovo progetto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Fare clic su **Gestione API** e fare clic su **credenziali** nel riquadro di spostamento sinistro.
4. Fare clic sulla scheda **OAuth consenso schermata** nella parte superiore.

    ![Google + - credenziali](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Selezionare o specificare un **indirizzo di posta elettronica**valido, specificare un **nome prodotto**e fare clic su **Salva**.

    ![Google + - OAuth consenso schermo](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Fare clic su **nuove credenziali** e quindi scegliere **ID client OAuth**.

    ![Google + - OAuth consenso schermo](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. In **tipo di applicazione**selezionare **applicazione Web**.

    ![Google + - OAuth consenso schermo](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Specificare un **nome** per l'applicazione, immettere `https://login.microsoftonline.com` nel campo **autorizzato JavaScript origini diverse** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **autorizzate reindirizzare URI** . Sostituire **{tenant}** con il nome del tenant (ad esempio contosob2c.onmicrosoft.com). Il valore **{tenant}** corrisponde maiuscole e minuscole. Fare clic su **Crea**.

    ![Google + - creare ID client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copiare i valori **dell'ID Client** e **segreto del Client**. Sarà necessario entrambi gli elementi per configurare Google + come provider di identità nel tenant. **Segreto del client** è una credenziale di sicurezza importanti.

    ![Google + - segreto del Client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurare Google + come provider di identità nel tenant

1. Seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Scegliere **i provider di identità**e il caratteristiche B2C.
3. Fare clic su **Add +** nella parte superiore e il.
4. Fornire un **nome** descrittivo per la configurazione del provider di identità. Ad esempio, immettere "C +".
5. Fare clic su **tipo di provider di identità**, selezionare **Google**e fare clic su **OK**.
6. Fare clic su **Configura il provider di identità** e immettere l'ID client e il segreto del client di Google + applicazione creata in precedenza.
7. Fare clic su **OK** e quindi fare clic su **Crea** per salvare la configurazione di Google +.
