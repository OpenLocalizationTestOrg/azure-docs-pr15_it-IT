<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con nuovo Relic | Microsoft Azure" 
    description="Informazioni su come usare Relic nuovo con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Esercitazione: Integrazione di Azure Active Directory con Relic nuovo
  
L'obiettivo di questa esercitazione è sufficiente visualizzare come configurare il single sign-on tra Azure Active Directory e Relic nuovo.
  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un nuovo Relic single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Relic nuovo sarà possibile il single sign-on mediante il pannello di accesso AAD.

1.  Attivare l'integrazione delle applicazioni per Relic nuovo
2.  Configurazione di Single Sign-On
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario:")
##<a name="enabling-the-application-integration-for-new-relic"></a>Attivare l'integrazione delle applicazioni per Relic nuovo
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Relic nuovo.

###<a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Relic nuovo, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-new-relic-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-new-relic-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Relic nuovo**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-new-relic-tutorial/IC797031.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Nuovo Relic**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Nuovo Relic] (./media/active-directory-saas-new-relic-tutorial/IC797032.png "Nuovo Relic")
##<a name="configuring-single-sign-on"></a>Configurazione di Single Sign-On
  
In questa sezione viene descritto come consentire agli utenti di eseguire l'autenticazione a Relic nuovo con il proprio account Azure Active Directory mediante la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Relic nuovo** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al nuovo Relic** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Nuovo Relic su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Relic nuovo e quindi fare clic su **Avanti**. 

    L'app URL è il nuovo Relic tenant (ad esempio: *https://rpm.newrelic.com*):

    ![Configurare URL di App] (./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in nuovo Relic** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere al sito aziendale **Relic nuovo** come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Impostazioni Account**.

    ![Impostazioni account] (./media/active-directory-saas-new-relic-tutorial/IC797036.png "Impostazioni account")

7.  Fare clic sulla scheda **protezione e l'autenticazione** e quindi fare clic sulla scheda **Single sign-on** .

    ![Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")

8.  Nella scheda SAML, procedere come segue:

    ![SAML] (./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Fare clic su **Scegli File** per caricare il certificato di Azure Active Directory scaricato.
    2.  Nel portale di classica Azure, nella pagina **Configura il single sign-on in nuovo Relic** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso remoto** .
    3.  Nel portale di classica Azure, nella pagina **Configura il single sign-on in nuovo Relic** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **disconnessione URL di destinazione** .
    4.  Fare clic su **Salva modifiche personali**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Relic nuovo, è necessario essere effettuato il provisioning in Relic nuovo.  
In caso di nuovo Relic, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente alla nuova Relic, procedere come segue:

1.  Accedere al sito aziendale **Relic nuovo** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **Impostazioni Account**.

    ![Impostazioni account] (./media/active-directory-saas-new-relic-tutorial/IC797040.png "Impostazioni account")

3.  Nel riquadro **Account** sul lato sinistro, fare clic su **Riepilogo**e quindi fare clic su **Aggiungi utente**.

    ![Impostazioni account] (./media/active-directory-saas-new-relic-tutorial/IC797041.png "Impostazioni account")

4.  Nella finestra di dialogo **utenti attivi** , procedere come segue:

    ![Utenti attivi] (./media/active-directory-saas-new-relic-tutorial/IC797042.png "Utenti attivi")

    1.  Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica di un utente di Azure Active Directory valido che si desidera eseguire il provisioning.
    2.  Come selezionare **ruolo** **utente**.
    3.  Fare clic su **Aggiungi utente**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro Relic nuovo utente account strumenti di creazione o API fornite dal Relic nuovo agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Per assegnare gli utenti al nuovo Relic, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Relic nuovo** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-new-relic-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).




