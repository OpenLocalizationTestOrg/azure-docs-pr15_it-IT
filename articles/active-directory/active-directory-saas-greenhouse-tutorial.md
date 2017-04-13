<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con serra | Microsoft Azure" 
    description="Ecco come utilizzare serra con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Esercitazione: Integrazione di Azure Active Directory con serra
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e serra.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un effetto serra single sign-on abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a serra sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di serra (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per serra
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario:")
##<a name="enabling-the-application-integration-for-greenhouse"></a>Attivare l'integrazione delle applicazioni per serra
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per serra.

###<a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per serra, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **serra**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **serra**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Serra] (./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Serra")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a serra con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **serra** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al serra** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Su URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://company.greenhouse.io*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in serra** fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configurare il single sign-on")

5.  Inoltrare del file di metadati al team di supporto serra.

    >[AZURE.NOTE] Il Single sign-on deve essere abilitato per il team di supporto serra.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a serra, è necessario essere effettuato il provisioning in serra.  
Nel caso dei serra, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **serra** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **Configura**e quindi fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Utenti")

3.  Fare clic su **nuovi utenti**.

    ![I nuovi utenti] (./media/active-directory-saas-greenhouse-tutorial/IC790792.png "I nuovi utenti")

4.  Nella sezione **Aggiungi nuovo utente** , procedere come segue:

    ![Aggiungere i nuovi utenti] (./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Aggiungere i nuovi utenti")

    1.  Nella casella di testo **invio messaggi di posta elettronica utente** digitare l'indirizzo di posta elettronica di un account Azure Active Directory valido che si desidera eseguire il provisioning.
    2.  Fare clic su **Salva**.
        
        >[AZURE.NOTE] Gli utenti di Azure Active Directory verranno visualizzato un messaggio di posta elettronica incluso un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro effetto serra utente account strumenti di creazione o API fornite dal serra agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Per assegnare gli utenti a serra, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **serra **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).