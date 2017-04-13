<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con su richiesta elemento essenziale | Microsoft Azure" 
    description="Informazioni su come utilizzare su richiesta elemento essenziale con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Esercitazione: Integrazione di Azure Active Directory con elemento essenziale su richiesta

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e su richiesta elemento essenziale.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant elemento essenziale su richiesta

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a su richiesta elemento essenziale sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di su richiesta elemento essenziale (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni per l'elemento essenziale su richiesta
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenario:")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Attivare l'integrazione di applicazioni per l'elemento essenziale su richiesta

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni per l'elemento essenziale su richiesta.

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni per l'elemento essenziale su richiesta, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella di ricerca**, digitare **l'elemento essenziale su richiesta**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **l'Elemento essenziale su richiesta**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Elemento essenziale su richiesta] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Elemento essenziale su richiesta")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a su richiesta elemento essenziale con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Su richiesta elemento essenziale** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Attivare Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Attivare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere su richiesta elemento essenziale** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Microsoft Azure Active Directory Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure Active Directory Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Elemento essenziale su richiesta URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://company.csod.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on all'elemento essenziale su richiesta** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurare Single Sign-On")

5.  Inviare che gli elementi seguenti per su richiesta l'elemento essenziale il team di supporto:

    1.  Il certificato scaricato
    2.  Il valore di **URL di accesso remoto**
    3.  Il valore di **URL disconnessione remoto** .

    >[AZURE.NOTE] Single Sign-On deve essere configurato per il team di supporto elemento essenziale su richiesta.
Si riceve una notifica dal team del supporto tecnico quando è stata completata la configurazione.

6.  Selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory accedere all'elemento essenziale su richiesta, è necessario essere effettuato il provisioning in elemento essenziale su richiesta.  
In caso di base su richiesta, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Inviare le informazioni (ad esempio: nome, Emial) relative all'utente di Azure Active Directory si desidera eseguire il provisioning per su richiesta l'elemento essenziale il team di supporto.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro elemento essenziale su richiesta utente account strumenti di creazione o API fornite dall'elemento essenziale su richiesta agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Per assegnare gli utenti su richiesta elemento essenziale, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Su richiesta elemento essenziale **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Assegnare agli utenti] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Assegnare agli utenti")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
