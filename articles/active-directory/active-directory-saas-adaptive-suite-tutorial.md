<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Suite adattata | Microsoft Azure"
    description="Informazioni su come utilizzare adattata famiglia di prodotti con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Esercitazione: Integrazione di Azure Active Directory con adattamento famiglia di prodotti

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e adattata famiglia di prodotti.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant adattata famiglia di prodotti

Dopo aver completato questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Suite adattata sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di famiglia di prodotti adattata (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per adattata famiglia di prodotti
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scenario:")
##<a name="enabling-the-application-integration-for-adaptive-suite"></a>Attivare l'integrazione delle applicazioni per adattata famiglia di prodotti

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per adattata famiglia di prodotti.

###<a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni famiglia adattata, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Adattata famiglia di prodotti**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Adattata famiglia di prodotti**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Adattamento famiglia di prodotti] (./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adattamento famiglia di prodotti")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione alla famiglia di prodotti adattata con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Suite adattata è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Adattata famiglia di prodotti** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come agli utenti di eseguire l'accesso alla famiglia di prodotti adattata** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurare Single Sign-On")

3.  Nella pagina **Configura impostazioni di App** nella casella di testo **URL di risposta** , digitare l'URL utilizzando il modello seguente "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE =*", quindi scegliere **Avanti**.

    >[AZURE.NOTE] È possibile ottenere questo valore dalla pagina di **Impostazioni di Single Sign-on SAML** della famiglia di prodotti adattata.

    ![Configurare le impostazioni dell'App] (./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configurare le impostazioni dell'App")

4.  Nella pagina **Configura il single sign-on Suite adattata** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito della società adattata famiglia di prodotti.

6.  Passare a **amministratore**.

    ![Amministratore] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Amministratore")

7.  Nella sezione **utenti e ruoli** fare clic su **Gestisci impostazioni Single Sign-on SAML**.

    ![Gestire le impostazioni di SAML Single Sign-on] (./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Gestire le impostazioni di SAML Single Sign-on")

8.  Nella pagina **Impostazioni di Single Sign-on SAML** , procedere come segue:

    ![Impostazioni di SAML Single Sign-on] (./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Impostazioni di SAML Single Sign-on")

    1.  Nella casella di testo **nome del provider di identità** , digitare un nome per la configurazione.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Suite adattata** copiare il valore di **ID entità** e quindi incollarla nella casella di testo **il provider di identità ID entità** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Suite adattata** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **il provider di identità URL Single Sign-on** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Suite adattata** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **URL disconnessione personalizzato** .
    5.  Per caricare il certificato scaricato, fare clic su **Scegli file**.
    6.  Come **id utente SAML**, selezionare **adattata approfondimenti nome utente**.
    7.  Come **percorso di id utente di SAML**, selezionare **id utente di NameID dell'oggetto**.
    8.  Come **formato SAML NameID**, selezionare **l'indirizzo di posta elettronica**.
    9.  Come **Abilitare SAML**, selezionare **Consenti Single Sign-on SAML e diretto approfondimenti adattata login**.
    10. Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a adattata famiglia di prodotti, è necessario essere effettuato il provisioning in adattata famiglia di prodotti.  
In caso di famiglia di prodotti adattata il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Suite adattata** come amministratore.

2.  Passare a **amministratore**.

    ![Amministratore] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Amministratore")

3.  Nella sezione **utenti e ruoli** fare clic su **Aggiungi utente**.

    ![Aggiunta utente] (./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Aggiunta utente")

4.  Nella sezione **Nuovo utente** , procedere come segue:

    ![Inviare] (./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Inviare")

    1.  Digitare il **nome**, **Login**, **messaggio di posta elettronica**e **la Password** di un utente di Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Selezionare un **ruolo**.
    3.  Fare clic su **Invia**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Suite adattata utente account creazione strumenti o API fornite dal Suite adattata agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Per assegnare gli utenti alla famiglia di prodotti adattata, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Adattata famiglia di prodotti **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
