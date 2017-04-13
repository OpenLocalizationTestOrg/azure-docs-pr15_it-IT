<properties
    pageTitle="Azure B2C Directory attiva: Creare un tenant di Azure Active Directory B2C | Microsoft Azure"
    description="Un argomento su come creare un tenant di Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure B2C Directory attiva: Creare un tenant di Azure Active Directory B2C

Per iniziare a utilizzare Microsoft Azure Active Directory (Azure Active Directory) B2C, eseguire i tre passaggi descritti in questo articolo.

## <a name="step-1-sign-up-for-an-azure-subscription"></a>Passaggio 1: Iscriversi a un abbonamento a Azure

Se si dispone già di un abbonamento Azure, ignorare questo passaggio. In caso contrario, effettuare l'iscrizione per un [abbonamento Azure](../active-directory/sign-up-organization.md) e ottenere accesso a B2C di Azure Active Directory.

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>Passaggio 2: Creare un tenant di Azure Active Directory B2C

Utilizzare la procedura seguente per creare un nuovo tenant di Azure Active Directory B2C. Attualmente B2C caratteristiche non possono essere attivate nel tenant esistente.

1. Accedere al [portale classica Azure](https://manage.windowsazure.com/) come amministratore della sottoscrizione. Questo è lo stesso lavoro o dell'istituto di istruzione account o lo stesso account Microsoft usato per effettuare l'iscrizione per Azure.
2. Fare clic su **Nuovo** > **servizi App** > **Active Directory** > **Directory** > **creare personalizzata**.

    ![Schermata di avvio creare un tenant](./media/active-directory-b2c-get-started/new-directory.png)

3. Scegliere il **nome**, **Il nome di dominio** e **paese o area geografica** per il tenant.
4. Selezionare l'opzione che indica che **si tratta di una directory B2C**.
5. Fare clic sul segno di spunta per completare l'operazione.

    ![Schermata del segno di spunta per creare una directory B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Il tenant è stato creato e verrà visualizzata l'estensione di Active Directory. Si possono essere utilizzate anche un amministratore globale del tenant. È possibile aggiungere altri amministratori globale in base alle esigenze.

    > [AZURE.IMPORTANT]
    Se si prevede di utilizzare un tenant B2C su un'applicazione di produzione, leggere l'articolo sulla [scala di produzione e tenant anteprima B2C](active-directory-b2c-reference-tenant-type.md). Si noti che esistono noti problemi quando si elimina un tenant B2C esistente e ricrearlo con lo stesso nome di dominio. È necessario creare un tenant B2C con un nome di dominio diverso.

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>Passaggio 3: Passare a e il caratteristiche B2C nel portale di Azure

1. Individuare l'estensione di Active Directory sulla barra di spostamento sul lato sinistro.
2. Individuare il tenant nella scheda **Directory** e fare clic su esso.
3. Fare clic sulla scheda **Configura** .
4. Fare clic sul collegamento di **B2C gestire le impostazioni** nella sezione **amministrazione B2C** .

    ![Schermata della configurazione di directory per B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Portale di Azure con e il caratteristiche B2C con verrà aperto in una finestra o una nuova scheda del browser.

    > [AZURE.IMPORTANT]
    Può richiedere fino a 2-3 minuti per il tenant essere accessibili nel portale di Azure. Inviare di nuovo la procedura seguente dopo alcuni minuti consente di risolvere questo. In caso contrario, contattare il supporto.

6. Aggiungere questo blade per il Startboard per facilitare l'accesso. (Lo strumento Pin è contrassegnato in rosso nell'angolo superiore destro della stessa e funzionalità.)

    ![Schermata della stessa e caratteristiche B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    È possibile gestire utenti e gruppi, configurazione di reimpostazione password self-service e caratteristiche di personalizzazione della società di tenant nel [portale classica Azure](https://manage.windowsazure.com/).

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Accedere facilmente e il caratteristiche B2C nel portale di Azure

Per migliorare l'individuazione, abbiamo creato un collegamento a e il caratteristiche B2C nel portale di Azure.

1. Accedere al portale di Azure come amministratore globale del tenant B2C. Se sono già eseguito l'accesso a un tenant diverso, passare tenant (nell'angolo superiore destro).
2. Fare clic su **Sfoglia** nella barra di spostamento a sinistra.
3. Fare clic su **Azure Active Directory B2C** per accedere e il caratteristiche B2C.

    ![Schermata della finestra di dialogo Sfoglia per blade caratteristiche B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come registrare un'applicazione di Azure Active Directory B2C e per creare un'applicazione di avvio rapido, leggere [Azure Active Directory B2C: registrare l'applicazione](active-directory-b2c-app-registration.md).
