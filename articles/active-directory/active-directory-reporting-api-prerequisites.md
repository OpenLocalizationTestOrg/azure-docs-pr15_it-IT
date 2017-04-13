<properties
    pageTitle="Prerequisiti per accedere a Azure AD API di segnalazione. | Microsoft Azure"
    description="Informazioni sui prerequisiti per accedere a Azure AD API di segnalazione"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Prerequisiti per accedere a Azure AD API di segnalazione 

[Azure Active Directory reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornirgli l'accesso a dati tramite un insieme di API REST. È possibile chiamare queste API da una varietà di strumenti di modifica e lingue di programmazione.

L'API dei report utilizza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) per autorizzare l'accesso al web API. 

Per preparare l'accesso all'API dei report, è necessario:

1. Creare un'applicazione nel tenant di Azure Active Directory 

2. Concedere le autorizzazioni appropriate applicazione per accedere ai dati di Azure Active Directory

3. Raccogliere le impostazioni di configurazione dalla directory

Per eventuali domande, problemi o commenti e suggerimenti, contattare il [Report consente AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Creare un'applicazione di Azure Active Directory

Per configurare la directory per accedere a Azure AD API di segnalazione, è necessario accedere al portale di classica Azure con un account di amministratore di Azure abbonamento che è anche un membro del ruolo amministratore globale directory nel tenant di Azure Active Directory.

> [AZURE.IMPORTANT] Le applicazioni in esecuzione con credenziali "privilegi di"amministratore … possono essere molto potenti, pertanto assicurarsi di proteggere le credenziali dell'ID/segreto dell'applicazione.


1. Nel [portale classica Azure](https://manage.windowsazure.com), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Nell'elenco di **active directory** , selezionare la directory.

3. Nel menu nella parte superiore, fare clic su **applicazioni**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Nella barra inferiore fare clic su **Aggiungi**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/03.png) 

5. Nel **cosa si desidera eseguire?** finestra di dialogo, fare clic su **Aggiungi un'applicazione di sviluppo di organizzazione**. 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/04.png) 


6. Nella finestra di dialogo **inviare commenti e suggerimenti sull'applicazione** , procedere come segue: 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/05.png) 

    un. Nella casella di testo **nome** digitare un nome (ad esempio: applicazione API segnalazione).

    b. Selezionare **applicazione Web e/o API web**.

    c. Fare clic su **Avanti**.


7. Nella finestra di dialogo **proprietà di App** , procedere come segue: 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/06.png) 

    un. Nella casella di testo **URL di accesso** , digitare `https://localhost`.

    b. Nella casella di testo **App ID URI** digitare ```https://localhost/ReportingApiApp```.

    c. Fare clic su **completa**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Concedere l'autorizzazione di applicazione per utilizzare l'API

1. Nel [portale classica Azure](https://manage.windowsazure.com/), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Nell'elenco di **active directory** , selezionare la directory.

3. Nel menu nella parte superiore, fare clic su **applicazioni**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png)


3. Nell'elenco delle applicazioni, selezionare l'applicazione appena creata.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/07.png)

4. Nel menu nella parte superiore, fare clic su **Configura**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/08.png)


5. Nella sezione **autorizzazioni ad altre applicazioni** per la risorsa di **Azure Active Directory** , fare clic sull'elenco a discesa di **Autorizzazioni per l'applicazione** e quindi selezionare **i dati di directory lettura**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/09.png)


5. Nella barra inferiore fare clic su **Salva**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Raccogliere le impostazioni di configurazione dalla directory

In questa sezione viene illustrato come ottenere le impostazioni seguenti dalla directory:

- Nome di dominio
- ID client
- Segreto del client

È necessario questi valori quando si configura chiamate all'API dei report. 


### <a name="get-your-domain-name"></a>Ottenere il nome di dominio

1. Nel [portale classica Azure](https://manage.windowsazure.com), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Nell'elenco di **active directory** , selezionare la directory.

3. Nel menu nella parte superiore, fare clic su **domini**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/11.png) 

4. Nella colonna **Nome di dominio** , copiare il nome di dominio.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>Ottenere ID client dell'applicazione

1. Nel [portale classica Azure](https://manage.windowsazure.com), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Nell'elenco di **active directory** , selezionare la directory.

3. Nel menu nella parte superiore, fare clic su **applicazioni**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Nell'elenco delle applicazioni, selezionare l'applicazione appena creata.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/07.png)

4. Nel menu nella parte superiore, fare clic su **Configura**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/08.png)

4. Copiare l' **ID Client**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>Ottenere segreto del client dell'applicazione

Per ottenere segreto del client dell'applicazione, è necessario creare una nuova chiave e salvare il relativo valore durante il salvataggio la nuova chiave perché non è possibile recuperare questo valore in un secondo momento più.

1. Nel [portale classica Azure](https://manage.windowsazure.com), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Nell'elenco di **active directory** , selezionare la directory.

3. Nel menu nella parte superiore, fare clic su **applicazioni**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Nell'elenco delle applicazioni, selezionare l'applicazione appena creata.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/07.png)

4. Nel menu nella parte superiore, fare clic su **Configura**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/08.png)

5. Nella sezione **tasti** , procedere come segue: 

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/14.png)

    un. Nell'elenco durata, selezionare la durata

    b. Nella barra inferiore fare clic su **Salva**.

    ![Registrare l'applicazione](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Copiare il valore di chiave.

## <a name="next-steps"></a>Passaggi successivi

- Si desidera accedere ai dati di Azure AD API di segnalazione in modo livello di programmazione? Vedere [Guida introduttiva di Azure Active Directory Reporting API di](active-directory-reporting-api-getting-started.md).

- Per saperne di più sui report di Azure Active Directory, vedere [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  
