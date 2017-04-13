<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con RightAnswers | Microsoft Azure" 
    description="Ecco come utilizzare RightAnswers con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>Esercitazione: Integrazione di Azure Active Directory con RightAnswers
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e RightAnswers. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un RightAnswers single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a RightAnswers sarà possibile accesso singolo nell'applicazione tramite l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per RightAnswers
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario:")
##<a name="enabling-the-application-integration-for-rightanswers"></a>Attivare l'integrazione delle applicazioni per RightAnswers
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per RightAnswers.

###<a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per RightAnswers, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **RightAnswers**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **RightAnswers**e quindi fare clic su **completa** per aggiungere l'applicazione.
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a RightAnswers con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **RightAnswers** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al RightAnswers** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configurare Single Sign-On")

3.  Nella pagina **Configura impostazioni di App** nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione RightAnswers (ad esempio: *https://fortify.rightanswers.com/portal/ss/*), quindi scegliere **Avanti**.

    ![Configurare le impostazioni dell'App] (./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configurare le impostazioni dell'App")

4.  Nella pagina **Configura il single sign-on in RightAnswers** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configurare Single Sign-On")

5.  Inviare il file di metadati scaricati al team di supporto RightAnswers.

    >[AZURE.NOTE] Il team di supporto RightAnswers deve eseguire la configurazione di Single Sign-on effettiva.
Si riceve una notifica quando Single Sign-on è stata attivata per l'abbonamento.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a RightAnswers, è necessario essere effettuato il provisioning in RightAnswers.  
In caso di RightAnswers, il provisioning è un'attività automatica.  
Non esiste alcun elemento azione dell'utente.
  
Gli utenti vengono creati automaticamente se necessario, durante il primo single sign-on tentativo.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro RightAnswers utente account strumenti di creazione o API fornite dal RightAnswers agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>Per assegnare gli utenti a RightAnswers, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **RightAnswers **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).