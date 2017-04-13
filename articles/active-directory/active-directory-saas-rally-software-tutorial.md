<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con un Software per manifestazioni da organizzare | Microsoft Azure" 
    description="Informazioni su come utilizzare Rally Software con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Esercitazione: Integrazione di Azure Active Directory con un Software per manifestazioni da organizzare
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Rally Software.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Rally Software
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni Software Rally
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario:")
##<a name="enabling-the-application-integration-for-rally-software"></a>Attivare l'integrazione di applicazioni Software Rally
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni Software Rally.

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni Software Rally, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-rally-software-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-rally-software-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **manifestazioni da organizzare**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-rally-software-tutorial/IC769526.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Rally Software**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Rally Software] (./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Rally Software con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato a Rally Software.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Rally Software **, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurare il single sign-on")

2.  Nella pagina **da come si desidera agli utenti di effettuare l'accesso a manifestazioni da organizzare** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Microsoft Azure Active Directory Single Sign-On] (./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure Active Directory Single Sign-On")

3.  Nella pagina **Configura URL App** nella casella di testo **Rally Software Tenant URL** digitare l'URL utilizzando il modello seguente "*https://\<nome tenant\>. rally.com*", quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in manifestazioni da organizzare** , fare clic su Download metadati e quindi salvarlo nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurare il single sign-on")

5.  Accedere al tenant di **Rally Software** .

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Impostazioni**e quindi selezionare **abbonamento**.

    ![Abbonamento] (./media/active-directory-saas-rally-software-tutorial/IC769531.png "Abbonamento")

7.  Fare clic sul pulsante di **azione** nella barra degli strumenti in alto a destra e quindi selezionare **Modifica sottoscrizione**.

8.  Nella pagina di finestra di dialogo **sottoscrizione** , eseguire le operazioni seguenti e quindi fare clic su **Salva e Chiudi**:

    ![Autenticazione] (./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticazione")

    1.  Selezionare **autenticazione manifestazioni da organizzare o Single Sign-on** dall'elenco a discesa autenticazione
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Rally software** copiare il valore di **ID Provider di identità** e quindi incollarla nella casella di testo **URL del Provider di identità**
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Rally software** copiare il valore di **URL disconnessione remoto** .

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per gli utenti AAD in grado di accedere, è necessario essere effettuato il provisioning all'applicazione Software Rally utilizzando i relativi nomi utente di Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al tenant di Rally Software.

2.  Passare a **configurazione \> utenti**e quindi fare clic su **+ Add New**.

    ![Utenti] (./media/active-directory-saas-rally-software-tutorial/IC781039.png "Utenti")

3.  Digitare il nome nella casella di testo nuovo utente e quindi fare clic su **Aggiungi con dettagli**.

4.  Nella sezione **Crea utente** , procedere come segue:

    ![Creare utente] (./media/active-directory-saas-rally-software-tutorial/IC781040.png "Creare utente")

    1.  Nella casella di testo **Nome utente** digitare il nome dell'utente effettuare il provisioning di Azure Active Directory.
    2.  Nella casella di testo **Indirizzo di posta elettronica** digitare l'indirizzo di posta elettronica dell'utente effettuare il provisioning di Azure Active Directory.
    3.  Fare clic su **Salva e Chiudi**.

>[AZURE.NOTE]È possibile utilizzare altri Software Rally utente account creazione strumenti o API fornite da parte di Software Rally agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Per assegnare gli utenti a Software Rally, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Rally Software** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).




