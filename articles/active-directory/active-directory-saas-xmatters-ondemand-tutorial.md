<properties 
    pageTitle="Esercitazione: Azure Active Directory integrazione con xMatters su richiesta | Microsoft Azure"
    description="Informazioni su come usare xMatters su richiesta con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Esercitazione: Azure Active Directory integrazione con xMatters su richiesta
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e xMatters su richiesta. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di xMatters su richiesta
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a xMatters su richiesta sarà possibile accesso singolo nell'applicazione di sito della società xMatters su richiesta (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per xMatters su richiesta
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario:")

##<a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Attivare l'integrazione delle applicazioni per xMatters su richiesta
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per xMatters su richiesta.

###<a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per xMatters su richiesta, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **xMatters su richiesta**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **XMatters su richiesta**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![xMatters su richiesta] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters su richiesta")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a XMatters OnDemand con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Su richiesta XMatters** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere su richiesta XMatters** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di app] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurare URL di app")

    un. Nella casella di testo **XMatters su richiesta URL di accesso** , digitare l'URL utilizzando il modello seguente:`https://<tenant-name>.XMattersOnDemandapp.com`

    b. Fare clic su **Avanti**.


4.  Nella pagina **Configura il single sign-on in XMatters su richiesta** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] È necessario inoltrare il certificato per il team di supporto xMatters. Il certificato deve essere caricata dal team di supporto xMatters prima possibile completare la configurazione single sign-on.

    ![Configura single accesso dall'] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configura single accesso dall'")

5.  In una finestra del browser web diverso, accedere a sito della società XMatters OnDemand come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **amministratore**e quindi fare clic su **Dettagli società** nella barra di spostamento sul lato sinistro.

    ![Amministratore] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Amministratore")

7.  Nella pagina **Configurazione SAML** , procedere come segue:

    ![Configurazione di SAML] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configurazione di SAML")

    1.  Selezionare **Abilita SAML**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in su richiesta XMatters** copiare il valore di **ID Provider di identità** e quindi incollarla nella casella di testo **ID Provider di identità** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in su richiesta XMatters** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **Singola accesso su URL** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in su richiesta XMatters** copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **Singola disconnessione URL** .
    5.  Nella pagina dettagli della società, nella parte superiore, fare clic su **Salva modifiche**.
        ![Informazioni sulla società] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Informazioni sulla società")

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configura single accesso dall'] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configura single accesso dall'")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a XMatters OnDemand, è necessario essere effettuato il provisioning in XMatters OnDemand.  
Nel caso dei XMatters OnDemand, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **XMatters su richiesta** .

2.  Fare clic sulla scheda **utenti** .

3.  Fare clic su **Aggiungi utente**.

    ![Utenti] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Utenti")

4.  Selezionare **Attiva**.

5.  Nella sezione **aggiungere un utente** , procedere come segue:

    ![Aggiungere un utente] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Aggiungere un utente")

    1.  Immettere **l'ID utente**, **nome**, **Cognome**, **siti** di un account valido AAD che si desidera eseguire il provisioning.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro XMatters OnDemand utente account strumenti di creazione o API fornite dal XMatters OnDemand agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Per assegnare gli utenti a XMatters OnDemand, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Su richiesta XMatters **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).