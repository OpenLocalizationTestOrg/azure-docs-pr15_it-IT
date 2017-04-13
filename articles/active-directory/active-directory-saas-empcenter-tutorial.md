<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con EmpCenter | Microsoft Azure" 
    description="Ecco come utilizzare EmpCenter con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-empcenter"></a>Esercitazione: Integrazione di Azure Active Directory con EmpCenter
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure ed EmpCenter.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un EmpCenter single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a EmpCenter sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di EmpCenter (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per EmpCenter
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-empcenter-tutorial/IC802916.png "Scenario:")
##<a name="enabling-the-application-integration-for-empcenter"></a>Attivare l'integrazione delle applicazioni per EmpCenter
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per EmpCenter.

###<a name="to-enable-the-application-integration-for-empcenter-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per EmpCenter, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-empcenter-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-empcenter-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-empcenter-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **EmpCenter**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-empcenter-tutorial/IC802917.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **EmpCenter**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![EmpCentral] (./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a EmpCenter con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **EmpCenter** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-empcenter-tutorial/IC802919.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al EmpCenter** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-empcenter-tutorial/IC802920.png "Configurare Single Sign-On")

3.  Nella pagina **Configura impostazioni di App** , procedere come segue:

    ![Configurare le impostazioni dell'App] (./media/active-directory-saas-empcenter-tutorial/IC802921.png "Configurare le impostazioni dell'App")

    1.  Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione EmpCenter (ad esempio: *https://partner-authenticati.empcenter.com/workforce/SSO.do*).
    2.  Fare clic su **Avanti**

4.  Nella pagina **Configura il single sign-on in EmpCenter** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di metadati nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-empcenter-tutorial/IC802922.png "Configurare Single Sign-On")

5.  Inviare il file di metadati scaricati al team di supporto EmpCenter.

    >[AZURE.NOTE] Il team di supporto EmpCenter deve eseguire la configurazione di Single Sign-on effettiva.
Si riceve una notifica quando Single Sign-on è stata attivata per l'abbonamento.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-empcenter-tutorial/IC802923.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a EmpCenter, è necessario essere effettuato il provisioning in EmpCenter.  
Nel caso EmpCenter, è necessario che gli account utente creati dal team di supporto EmpCenter.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro EmpCenter utente account strumenti di creazione o API fornite dall'EmpCenter effettuare il provisioning di Azure Active Directory gli account utente.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-empcenter-perform-the-following-steps"></a>Per assegnare gli utenti a EmpCenter, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **EmpCenter **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-empcenter-tutorial/IC802924.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-empcenter-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).