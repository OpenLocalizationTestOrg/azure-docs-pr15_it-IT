<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con SimpleNexus | Microsoft Azure" 
    description="Ecco come utilizzare SimpleNexus con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Esercitazione: Integrazione di Azure Active Directory con SimpleNexus
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e SimpleNexus.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un SimpleNexus single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a SimpleNexus sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di SimpleNexus (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per SimpleNexus
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scenario:")
##<a name="enabling-the-application-integration-for-simplenexus"></a>Attivare l'integrazione delle applicazioni per SimpleNexus
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per SimpleNexus.

###<a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per SimpleNexus, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **nexus semplice**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **SimpleNexus**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Nexus semplice] (./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Nexus semplice")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a SimpleNexus con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **SimpleNexus** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al SimpleNexus** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** nella casella di testo **SimpleNexus URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://simplenexus.com/CompanyName\_accesso*", quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in SimpleNexus** fare clic su **Scarica metadati**e quindi inoltrare il file di metadati al team di supporto SimpleNexus.

    ![Configurare Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurare Single Sign-On")

    >[AZURE.NOTE] Il Single sign-on deve essere abilitato per il team di supporto SimpleNexus.

5.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a SimpleNexus, è necessario essere effettuato il provisioning in SimpleNexus.  
In caso di SimpleNexus, il provisioning è un'attività manuale eseguita dall'amministratore del tenant.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro SimpleNexus utente account strumenti di creazione o API fornite dal SimpleNexus agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>Per assegnare gli utenti a SimpleNexus, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **SimpleNexus **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).