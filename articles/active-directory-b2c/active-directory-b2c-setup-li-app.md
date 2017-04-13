<properties
    pageTitle="Azure B2C Directory attiva: Configurazione di LinkedIn | Microsoft Azure"
    description="Fornire per l'abbonamento e l'accesso agli utenti con un account di LinkedIn nelle applicazioni che sono protetti da Azure Active Directory B2C"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure B2C Directory attiva: Disponibili per l'abbonamento e l'accesso agli utenti con un account di LinkedIn

## <a name="create-a-linkedin-application"></a>Creare un'applicazione di LinkedIn

Per utilizzare LinkedIn come provider di identità in B2C Azure Active Directory (Azure Active Directory), è necessario creare un'applicazione di LinkedIn e fornire con i parametri di destro. È necessario un account di LinkedIn per eseguire questa operazione. Se non si dispone di uno, è possibile ottenere in [https://www.linkedin.com/](https://www.linkedin.com/).

1. Passare al [sito Web di LinkedIn sviluppatori](https://www.developer.linkedin.com/) e accedere con le credenziali dell'account LinkedIn.
2. Fare clic su **App personali** nella barra dei menu superiore e quindi fare clic su **Crea applicazione**.

    ![LinkedIn - nuova app](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Nel modulo **Crea una nuova applicazione di** inserire le informazioni pertinenti (**Il nome della società**, **nome**, **Descrizione**, **URL Logo dell'applicazione**, **Utilizzo dell'applicazione**, **URL del sito Web**, **Indirizzo di posta elettronica** e **Telefono ufficio**).
4. Accettare le **Condizioni per l'utilizzo dell'API di LinkedIn** e fare clic su **Invia**.

    ![LinkedIn - app registro](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copiare i valori **dell'ID Client** e **Segreto del Client**. (È possibile trovarle in **Chiavi di autenticazione**.) Sarà necessario entrambi gli elementi per configurare LinkedIn come provider di identità nel tenant.

    >[AZURE.NOTE] **Segreto del client** è una credenziale di sicurezza importanti.

6. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URL di reindirizzamento autorizzato** (sotto **OAuth 2.0**). Sostituire **{tenant}** con il nome del tenant (ad esempio contoso.onmicrosoft.com). Fare clic su **Aggiungi**e quindi fare clic su **Aggiorna**. Il valore **{tenant}** corrisponde maiuscole e minuscole.

    ![LinkedIn - app di configurazione](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configurare LinkedIn come provider di identità nel tenant

1. Seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Scegliere **i provider di identità**e il caratteristiche B2C.
3. Fare clic su **Add +** nella parte superiore e il.
4. Fornire un **nome** descrittivo per la configurazione del provider di identità. Ad esempio, immettere "LI".
5. Fare clic su **tipo di provider di identità**, selezionare **LinkedIn**e fare clic su **OK**.
6. Fare clic su **Configura il provider di identità** e immettere l'ID client e il segreto del client dell'applicazione LinkedIn creata in precedenza.
7. Fare clic su **OK** e quindi fare clic su **Crea** per salvare la configurazione di LinkedIn.
