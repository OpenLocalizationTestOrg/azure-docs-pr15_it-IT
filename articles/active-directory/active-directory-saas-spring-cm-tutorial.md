<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con primavera CM | Microsoft Azure" 
    description="Informazioni su come utilizzare primavera CM con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>Esercitazione: Integrazione di Azure Active Directory con primavera CM
  
L'obiettivo di questa esercitazione è sufficiente visualizzare come configurare il single sign-on tra Azure Active Directory e SpringCM.
  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un SpringCM single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a SpringCM sarà possibile il single sign-on mediante il pannello di accesso AAD.

1.  Attivare l'integrazione delle applicazioni per SpringCM
2.  Configurazione di Single Sign-On
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scenario:")

##<a name="enabling-the-application-integration-for-springcm"></a>Attivare l'integrazione delle applicazioni per SpringCM
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per SpringCM.

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per SpringCM, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **SpringCM**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **SpringCM**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![SpringCM] (./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>Configurazione di Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a SpringCM con il proprio account Azure Active Directory mediante la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **SpringCM** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configura single Sign-On] (./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configura single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al SpringCM** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configura single Sign-On] (./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configura single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **SpringCM su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione SpringCM e quindi fare clic su **Avanti**. 

    L'app URL è il SpringCM tenant (ad esempio: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![Configurare URL di App] (./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in SpringCM** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare l'accesso singolo] (./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurare l'accesso singolo")

5.  In una finestra del browser web diverso, accedere al sito aziendale **SpringCM** come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Vai a**, fare clic su **Preferenze**e quindi, nella sezione **Preferenze Account** fare clic su **SAML Single Sign-on**.

    ![SAML Single Sign-on] (./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML Single Sign-on")

7.  Nella sezione Configurazione del Provider di identità, procedere come segue:

    ![Configurazione del Provider di identità] (./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configurazione del Provider di identità")

    1.  Per caricare il certificato di Azure Active Directory scaricato, fare clic su **Selezionare certificazione** o **Certificazione di modifica**.
    2.  Nel portale di classica Azure, nella pagina **Configura il single sign-on in SpringCM** , copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente** .
    3.  Nel portale di classica Azure, nella pagina **Configura il single sign-on in SpringCM** , copiare il valore di **Singel Sign-On URL del servizio** e quindi incollarla nella casella di testo **Endpoint avviata Provider di servizi (SP)** .
    4.  Come **SAML abilitato**, selezionare **Attiva**.
    5.  Fare clic su **Salva**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare l'accesso singolo] (./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurare l'accesso singolo")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a SpringCM, è necessario essere effettuato il provisioning in SpringCM.  
In caso di SpringCM, il provisioning è un'attività manuale.

>[AZURE.NOTE] Per ulteriori informazioni, vedere [creare e modificare un utente SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente per SpringCM, procedere come segue:

1.  Accedere al sito aziendale **SpringCM** come amministratore.

2.  Fare clic su **Vai**e quindi fare clic su **Rubrica**.

    ![Creare utente] (./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Creare utente")

3.  Fare clic su **Crea utente**.

4.  Selezionare un **ruolo utente**.

5.  Selezionare **Invia messaggio di posta elettronica l'attivazione**.

6.  Digitare il nome, cognome e indirizzo di posta elettronica di un account utente di Azure Active Directory valido, che si desidera eseguire il provisioning nelle caselle di testo correlati.

7.  Aggiungere l'utente a un **gruppo di sicurezza**.

8.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro SpringCM utente account strumenti di creazione o API fornite dal SpringCM agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Per assegnare gli utenti a SpringCM, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **SpringCM** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).




