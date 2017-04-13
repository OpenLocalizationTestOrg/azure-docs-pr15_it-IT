<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ScreenSteps | Microsoft Azure" 
    description="Ecco come utilizzare ScreenSteps con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Esercitazione: Integrazione di Azure Active Directory con ScreenSteps
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e ScreenSteps.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant ScreenSteps
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a ScreenSteps sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di ScreenSteps (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per ScreenSteps
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario:")
##<a name="enabling-the-application-integration-for-screensteps"></a>Attivare l'integrazione delle applicazioni per ScreenSteps
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per ScreenSteps.

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per ScreenSteps, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-screensteps-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-screensteps-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **ScreenSteps**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-screensteps-tutorial/IC778517.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **ScreenSteps**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![ScreenSteps] (./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a ScreenSteps con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **ScreenSteps** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al ScreenSteps** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **ScreenSteps URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. ScreenSteps.com*", quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in ScreenSteps** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società ScreenSteps come amministratore.

6.  Fare clic su **gestione degli Account**.

    ![Gestione degli account] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestione degli account")

7.  Fare clic su **autenticazione remota**.

    ![Autenticazione remota] (./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticazione remota")

8.  Fare clic su **Crea endpoint di autenticazione**.

    ![Autenticazione remota] (./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticazione remota")

9.  Nella sezione **Crea un Endpoint di autenticazione** , procedere come segue:

    ![Crea un endpoint di autenticazione] (./media/active-directory-saas-screensteps-tutorial/IC778526.png "Crea un endpoint di autenticazione")

    1.  Nella casella di testo **titolo** digitare un titolo.
    2.  Dall'elenco **modalità** selezionare **SAML**.
    3.  Fare clic su **Crea**.

10. Nella sezione **Endpoint autenticazione remoto** , procedere come segue:

    ![Punto finale di autenticazione remota] (./media/active-directory-saas-screensteps-tutorial/IC778527.png "Punto finale di autenticazione remota")

    1.  Nel portale di classica Azure, nella pagina **Configura il single sign-on in ScreenSteps** , copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso remoto** .
    2.  Nel portale di classica Azure, nella pagina **Configura il single sign-on in ScreenSteps** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **disconnettersi URL** .
    3.  Fare clic su **Scegli un file**e quindi caricare il certificato scaricato.
    4.  Fare clic su **Aggiorna**.

11. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a **ScreenSteps**, è necessario essere effettuato il provisioning in **ScreenSteps**.  
In caso di **ScreenSteps**, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente per ScreenSteps, procedere come segue:

1.  Accedere al tenant di **ScreenSteps** .

2.  Fare clic su **gestione degli Account**.

    ![Gestione degli account] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestione degli account")

3.  Fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-screensteps-tutorial/IC778544.png "Utenti")

4.  Fare clic su **Crea un utente**.

    ![Tutti gli utenti] (./media/active-directory-saas-screensteps-tutorial/IC778545.png "Tutti gli utenti")

5.  Nell'elenco **Ruolo utente** , selezionare un ruolo per l'utente.

6.  Nella sezione ruolo utente digitare**"nome**, **Cognome**, **posta elettronica**, **accesso**, **Password** e **Conferma Password"**di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.

    ![I nuovi utenti] (./media/active-directory-saas-screensteps-tutorial/IC778546.png "I nuovi utenti")

7.  Nella sezione gruppi, selezionare **L'autenticazione gruppo SAML ()**e quindi fare clic su **Crea utente**.

    ![Gruppi] (./media/active-directory-saas-screensteps-tutorial/IC778547.png "Gruppi")

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro ScreenSteps utente account strumenti di creazione o API fornite dal ScreenSteps agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Per assegnare gli utenti a ScreenSteps, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **ScreenSteps **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-screensteps-tutorial/IC773094.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Assegnare agli utenti] (./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assegnare agli utenti")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).