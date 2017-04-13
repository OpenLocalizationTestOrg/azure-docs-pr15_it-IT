<properties
    pageTitle="Azure B2C Directory attiva: Configurazione di Facebook | Microsoft Azure"
    description="Fornire per l'abbonamento e l'accesso agli utenti con un account di Facebook nelle applicazioni che sono protetti da Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure B2C Directory attiva: Disponibili per l'abbonamento e l'accesso agli utenti con un account di Facebook

## <a name="create-a-facebook-application"></a>Creare un'applicazione di Facebook

Per utilizzare Facebook come provider di identità in B2C Azure Active Directory (Azure Active Directory), è necessario creare un'applicazione di Facebook e fornire con i parametri di destro. È necessario un account di Facebook per eseguire questa operazione. Se non si dispone di uno, è possibile ottenere in [https://www.facebook.com/](https://www.facebook.com/).

1. Passare al sito Web di [Facebook per gli sviluppatori](https://developers.facebook.com/) e accedere con le credenziali dell'account di Facebook.
2. Se non è già stato fatto, è necessario eseguire la registrazione come uno sviluppatore di Facebook. Per eseguire questa operazione, fare clic su **Registra** (nell'angolo superiore destro della pagina), accettare i criteri di Facebook e completare la procedura di registrazione.
3. Fare clic su **App personali** e quindi fare clic su **Aggiungi una nuova App**. Scegliere **sito Web** come la piattaforma e quindi fare clic su **Ignora e creare ID App**.

    ![Facebook - aggiungere una nuova App](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - aggiungere una nuova App - sito Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - creare App ID](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. Nella maschera, specificare un **Nome visualizzato**, un **Messaggio di posta elettronica contatto**valido, una **categoria**appropriata e fare clic su **Crea ID App**. Questa operazione è necessario accettare i criteri di piattaforma Facebook e completare un controllo di protezione in linea.

    ![Facebook - creare un nuovo ID di App](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Fare clic su **Impostazioni** nella barra di spostamento sinistra.
6. Fare clic su **+ Aggiungi piattaforma** e quindi selezionare **il sito Web**.

    ![Facebook - impostazioni](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Sito Web di Facebook - impostazioni-](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Immettere [https://login.microsoftonline.com/](https://login.microsoftonline.com/) nel campo **URL del sito** e quindi fare clic su **Salva modifiche**.

    ![Facebook - URL del sito](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. Copiare il valore **dell'ID di App**. Fare clic su **Mostra** e copiare il valore di **Segreto App**. Sarà necessario entrambi gli elementi per configurare Facebook come provider di identità nel tenant. **Segreto App** è una credenziali di sicurezza importanti.

    ![Facebook - App ID e App segreto](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. Fare clic su **+ Aggiungi prodotto** nella barra di spostamento sinistra, quindi sul pulsante **Guida introduttiva** accanto a **Facebook Login**.

    ![Facebook - accesso Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **valido OAuth reindirizzare URI** nella sezione **Impostazioni OAuth Client** . Sostituire **{tenant}** con il nome del tenant (ad esempio contosob2c.onmicrosoft.com). Fare clic su **Salva modifiche** nella parte inferiore della pagina.

    ![Facebook - reindirizzamento OAuth URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. Per rendere l'applicazione di Facebook utilizzabile da Azure Active Directory B2C, è necessario renderla disponibile al pubblico. È possibile eseguire questa operazione, fare clic su **App revisione** sulla barra di spostamento sinistra e attivare l'opzione nella parte superiore della pagina su **Sì** e facendo clic su **Conferma**.

    ![Facebook - App pubblico](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurare Facebook come provider di identità nel tenant

1. Seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Scegliere **i provider di identità**e il caratteristiche B2C.
3. Fare clic su **Add +** nella parte superiore e il.
4. Fornire un **nome** descrittivo per la configurazione del provider di identità. Ad esempio, immettere "FB".
5. Fare clic su **tipo di provider di identità**, selezionare **Facebook**e fare clic su **OK**.
6. Fare clic su **Configura il provider di identità** e immettere l'ID di app e segreto app (dell'applicazione Facebook creata in precedenza) nel **segreto del Client** e **l'ID Client** campi rispettivamente.
7. Fare clic su **OK**e quindi fare clic su **Crea** per salvare la configurazione di Facebook.
