<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con PolicyStat | Microsoft Azure" 
    description="Ecco come utilizzare PolicyStat con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>Esercitazione: Integrazione di Azure Active Directory con PolicyStat
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e PolicyStat.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant PolicyStat
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a PolicyStat sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di PolicyStat (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per PolicyStat
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-policystat-tutorial/IC808662.png "Scenario:")
##<a name="enabling-the-application-integration-for-policystat"></a>Attivare l'integrazione delle applicazioni per PolicyStat
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per PolicyStat.

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per PolicyStat, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-policystat-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-policystat-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-policystat-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **PolicyStat**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-policystat-tutorial/IC808627.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **PolicyStat**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![PolicyStat] (./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a PolicyStat con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
L'applicazione PolicyStat prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** .  
Schermata seguente è illustrato un esempio per l'oggetto.

![Attributi] (./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributi")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **PolicyStat** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al PolicyStat** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurare Single Sign-On")

3.  Nella pagina **Configura impostazioni di App** nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione PolicyStat URL (ad esempio: *"https://demo-azure.policystat.com"*), quindi scegliere **Avanti**.

    ![Configurare le impostazioni dell'App] (./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurare le impostazioni dell'App")

4.  Nella pagina **Configura il single sign-on in PolicyStat** fare clic su **Scarica metadati**e quindi salvare il file di metadati nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società PolicyStat come amministratore.

6.  Fare clic sulla scheda **amministratore** e quindi fare clic su **Configurazione Single Sign-On** nel riquadro di spostamento a sinistra.

    ![Menu amministratore] (./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu amministratore")

7.  Nella sezione **configurazione** selezionare **integrazione abilitare Single Sign-on**.

    ![Configurazione di Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808634.png "Configurazione di Single Sign-On")

8.  Fare clic su **Configura attributi**e quindi, nella sezione **Configura attributi** , eseguire le operazioni seguenti:

    ![Configurazione di Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configurazione di Single Sign-On")

    1.  Nella casella di testo **Attributo Username** digitare **uid**.
    2.  Nella casella di testo **Nome attributo** , digitare **nome**.
    3.  Nella casella di testo **Ultimo nome attributo** digitare **Cognome**.
    4.  Nella casella di testo **Degli attributi di posta elettronica** , digitare **emailaddress**.
    5.  Fare clic su **Salva modifiche**.

9.  Fare clic su **Metadati IDP Your**e quindi, nella sezione **Your IDP metadati** , eseguire le operazioni seguenti:

    ![Configurazione di Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configurazione di Single Sign-On")

    1.  Aprire un file di metadati scaricati, copiare il contenuto e quindi incollarla nella casella di testo **Di metadati del Provider di identità**
    2.  Fare clic su **Salva modifiche**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurare Single Sign-On")

11. 12. Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributi")

13. Per aggiungere i mapping attributo obbligatorio, procedere come segue:

    ![Attributi] (./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributi")

    1.  Fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** digitare **uid**.
    3.  Nella casella di testo **Valore dell'attributo** , selezionare **ExtractMailPrefix()**.
    4.  Nell'elenco di **Stampa** , selezionare **User.mail**.
    5.  Fare clic su **completa**.
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a PolicyStat, è necessario essere effettuato il provisioning in PolicyStat.  
PolicyStat supporta solo il provisioning di tempo dell'utente. In questo modo, non è necessaria aggiungere manualmente gli utenti a PolicyStat.  
Gli utenti verranno venga aggiunta automaticamente nel loro primo accesso tramite single sign in.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro PolicyStat utente account strumenti di creazione o API fornite dal PolicyStat agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>Per assegnare gli utenti a PolicyStat, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **PolicyStat **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-policystat-tutorial/IC808636.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-policystat-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).