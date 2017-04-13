<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con LogicMonitor | Microsoft Azure" 
    description="Ecco come utilizzare LogicMonitor con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Esercitazione: Integrazione di Azure Active Directory con LogicMonitor
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e LogicMonitor.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant LogicMonitor
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per LogicMonitor
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario:")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>Attivare l'integrazione delle applicazioni per LogicMonitor
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per LogicMonitor.

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per LogicMonitor, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **logicmonitor**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **LogicMonitor**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![LogicMonitor] (./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a LogicMonitor con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **LogicMonitor **, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al LogicMonitor** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per effettuare l'accesso a LogicMonitor \(e, g,: "*http://company.logicmonitor.com*"\)e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in LogicMonitor** fare clic su **Scarica metadati**e quindi salvarlo nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configurare Single Sign-On")

5.  Accedere al sito aziendale **LogicMonitor** come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Impostazioni")

7.  All'inizio della struttura di spostamento sul lato sinistro, fare clic su **Single Sign-On**

    ![Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")

8.  Nella sezione **impostazioni Single Sign-on (SSO)** , procedere come segue:

    ![Impostazioni servizio Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Impostazioni servizio Single Sign-On")

    1.  Selezionare **Attiva Single Sign-on**.
    2.  Come **Predefinito assegnazione di ruolo**, selezionare **sola lettura**.
    3.  Aprire il file di metadati scaricati nel blocco note e quindi incollare il contenuto del file nella casella di testo **Metadati del Provider di identità** .
    4.  Fare clic su **Salva modifiche**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per gli utenti AAD in grado di accedere, è necessario essere effettuato il provisioning all'applicazione LogicMonitor utilizzando i relativi nomi utente di Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale LogicMonitor come amministratore.

2.  Nel menu nella parte superiore, fare clic su **Impostazioni**e quindi fare clic su **utenti e ruoli**.

    ![Ruoli e utenti] (./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Ruoli e utenti")

3.  Fare clic su **Aggiungi**.

4.  Nella sezione **Aggiungi un account** , eseguire i passaggi seguenti:

    ![Aggiungere un account] (./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Aggiungere un account")

    1.  Digitare i valori di **nome utente**, **messaggio di posta elettronica**, **Password** e **Conferma password** dell'utente Azure Active Directory a disposizione nelle caselle di testo correlati.
    2.  Selezionare **i ruoli**, **visualizzare le autorizzazioni** e lo **stato**.
    3.  Fare clic su **Invia**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro LogicMonitor utente account strumenti di creazione o API fornite dal LogicMonitor effettuare il provisioning di Azure Active Directory gli account utente.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Per assegnare gli utenti a LogicMonitor, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **LogicMonitor** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).




