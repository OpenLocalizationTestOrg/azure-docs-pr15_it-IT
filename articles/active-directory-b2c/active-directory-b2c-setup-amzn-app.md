<properties
    pageTitle="Azure B2C Directory attiva: Configurazione di Amazon | Microsoft Azure"
    description="Fornire per l'abbonamento e l'accesso agli utenti con un account di Amazon nelle applicazioni che sono protetti da Azure Active Directory B2C."
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure B2C Directory attiva: Fornire abbonamento e l'accesso agli utenti con un account di Amazon

## <a name="create-an-amazon-application"></a>Creare un'applicazione di Amazon

Per utilizzare Amazon come provider di identità in B2C Azure Active Directory (Azure Active Directory), è necessario creare un'applicazione di Amazon e fornire con i parametri di destro. È necessario un account di Amazon per eseguire questa operazione. Se non si dispone di uno, è possibile ottenere in [http://www.amazon.com/](http://www.amazon.com/).

1. Visitare il [Centro per sviluppatori di Amazon](https://login.amazon.com/) e accedere con le credenziali dell'account di Amazon.
2. Se non è già stato fatto, fare clic su **Iscrizione**, seguire la procedura di registrazione di sviluppo e accettare il criterio.
3. Fare clic su **Registra nuova applicazione**.

    ![Registrazione di una nuova applicazione al sito Web di Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Fornire le informazioni dell'applicazione (**nome**, **Descrizione**e **URL di avviso relativo all'informativa sulla Privacy**) e fare clic su **Salva**.

    ![Fornire informazioni sull'applicazione per la registrazione di una nuova applicazione di Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Nella sezione **Impostazioni Web** copiare i valori **dell'ID Client** e **Segreto del Client**. (È necessario fare clic sul pulsante **Mostra segreto** per verificarlo.) È necessario entrambi gli elementi per configurare Amazon come provider di identità nel tenant. Fare clic su **Modifica** nella parte inferiore della sezione. **Segreto del client** è una credenziale di sicurezza importanti.

    ![Fornire ID Client e segreto del Client per la nuova applicazione in Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Immettere `https://login.microsoftonline.com` nel campo **Origini diverse JavaScript consentita** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **Consentito URL restituito** . Sostituire **{tenant}** con il nome del tenant (ad esempio contoso.onmicrosoft.com). Fare clic su **Salva**. Il valore **{tenant}** corrisponde maiuscole e minuscole.

    ![Fornire JavaScript origini diverse e restituire URL per la nuova applicazione in Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configurare Amazon come provider di identità nel tenant

1. Seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Scegliere **i provider di identità**e il caratteristiche B2C.
3. Fare clic su **Add +** nella parte superiore e il.
4. Fornire un **nome** descrittivo per la configurazione del provider di identità. Ad esempio, immettere "Amzn".
5. Fare clic su **tipo di provider di identità**, selezionare **Amazon**e fare clic su **OK**.
6. Fare clic su **Configura il provider di identità** e immettere l'ID client e il segreto del client dell'applicazione Amazon creata in precedenza.
7. Fare clic su **OK** e quindi fare clic su **Crea** per salvare la configurazione di Amazon.
