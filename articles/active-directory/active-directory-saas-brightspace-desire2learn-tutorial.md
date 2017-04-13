<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Brightspace da Desire2Learn | Microsoft Azure" 
    description="Informazioni su come utilizzare Brightspace da Desire2Learn con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Esercitazione: Integrazione di Azure Active Directory con Brightspace da Desire2Learn

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Brightspace da Desire2Learn.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Brightspace da Desire2Learn il single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Brightspace da Desire2Learn sarà possibile eseguire l'accesso singolo all'applicazione il Brightspace sito aziendale Desire2Learn (servizio provider avviato accesso) o utilizzando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Brightspace da Desire2Learn
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenario:")
##<a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>Attivare l'integrazione delle applicazioni per Brightspace da Desire2Learn

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Brightspace da Desire2Learn.

###<a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Brightspace da Desire2Learn, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Brightspace da Desire2Learn**.

    ![Raccolta Apllication] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Raccolta Apllication")

7.  Nel riquadro dei risultati, selezionare **Brightspace da Desire2Learn**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Brightspace da Desire2Learn] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace da Desire2Learn")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Brightspace da Desire2Learn con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Brightspace da Desire2Learn** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Brightspace da Desire2Learn** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurare URL di App")

    1.  Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere al **Brightspace da Desire2Learn** (ad esempio: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
    2.  Fare clic su **Avanti**

4.  Nella pagina **Configura il single sign-on in Brightspace da Desire2Learn** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare i metadati nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurare Single Sign-On")

5.  Inviare il file di metadati scaricati il Brightspace dal team di supporto Desire2Learn.

    >[AZURE.NOTE] Il Brightspace dal team di supporto Desire2Learn deve eseguire la configurazione di Single Sign-on effettiva.
Si riceve una notifica quando Single Sign-on è stata attivata per l'abbonamento.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Brightspace da Desire2Learn, è necessario effettuato il provisioning in Brightspace da Desire2Learn.  
Nel caso Brightspace da Desire2Learn, è necessario essere create le Brightspace dal team di supporto Desire2Learn gli account utente.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Brightspace strumenti di creazione di account utente Desire2Learn o API fornite dal Brightspace da Desire2Learn effettuare il provisioning di Azure Active Directory gli account utente.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Per assegnare gli utenti a Brightspace da Desire2Learn, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Brightspace da Desire2Learn **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
