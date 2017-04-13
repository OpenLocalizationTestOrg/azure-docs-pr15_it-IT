<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con TalentLMS | Microsoft Azure" 
    description="Ecco come utilizzare TalentLMS con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Esercitazione: Integrazione di Azure Active Directory con TalentLMS
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e TalentLMS.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant TalentLMS
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a TalentLMS sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di TalentLMS (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per TalentLMS
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenario:")

##<a name="enabling-the-application-integration-for-talentlms"></a>Attivare l'integrazione delle applicazioni per TalentLMS
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per TalentLMS.

###<a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per TalentLMS, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-talentlms-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-talentlms-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **TalentLMS**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-talentlms-tutorial/IC777290.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **TalentLMS**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![TalentLMS] (./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a TalentLMS con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML. .  
Per configurare single sign-on per TalentLMS è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **TalentLMS** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al TalentLMS** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **TalentLMS URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. TalentLMSapp.com*", quindi scegliere **Avanti**.

    ![URL di accesso] (./media/active-directory-saas-talentlms-tutorial/IC777294.png "URL di accesso")

4.  Nella pagina **Configura il single sign-on in TalentLMS** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\TalentLMS.cer**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società TalentLMS come amministratore.

6.  Nella sezione **Account e impostazioni** fare clic sulla scheda **utenti** .

    ![Account e impostazioni] (./media/active-directory-saas-talentlms-tutorial/IC777296.png "Account e impostazioni")

7.  Fare clic su **Single Sign-On (SSO)**,

8.  Nella sezione Single Sign-On, procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")

    1.  Nell'elenco **tipo di integrazione di Single Sign-on** , selezionare **SAML 2.0**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in TalentLMS** copiare il valore di **ID Provider di identità** e quindi incollarla nella casella di testo **il provider di identità (IdP)** .
    3.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale di certificato** .

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in TalentLMS** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso remoto** .
    5.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in TalentLMS** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione remoto** .
    6.  Nella casella di testo **TargetedID** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
    7.  Nella casella di testo **nome** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    8.  Nella casella di testo **Cognome** , digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    9.  Nella casella di testo **messaggio di posta elettronica** , digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
    10. Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a TalentLMS, è necessario essere effettuato il provisioning in TalentLMS.  
In caso di TalentLMS, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **TalentLMS** .

2.  Fare clic su **utenti**e quindi fare clic su **Aggiungi utente**.

3.  Nella pagina di finestra di dialogo **Aggiungi utente** , procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-talentlms-tutorial/IC777299.png "Aggiunta utente")

    1.  Digitare i valori correlati attributo dell'account utente di Azure Active Directory nelle caselle di testo seguenti: **nome**, **Cognome**, **indirizzo di posta elettronica**.
    2.  Fare clic su **Aggiungi utente**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro TalentLMS utente account strumenti di creazione o API fornite dal TalentLMS agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Per assegnare gli utenti a TalentLMS, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **TalentLMS **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-talentlms-tutorial/IC777300.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-talentlms-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).