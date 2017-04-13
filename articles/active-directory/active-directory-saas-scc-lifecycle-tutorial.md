<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory ciclo di vita del controllo del codice sorgente | Microsoft Azure" 
    description="Informazioni su come utilizzare ciclo di vita del controllo del codice sorgente con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Esercitazione: Integrazione di Azure Active Directory ciclo di vita del controllo del codice sorgente
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e ciclo di vita del controllo del codice sorgente.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un ciclo di vita di controllo del codice sorgente single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a ciclo di vita del controllo del codice sorgente sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di ciclo di vita di controllo del codice sorgente (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per ciclo di vita del controllo del codice sorgente
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenario:")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>Attivare l'integrazione delle applicazioni per ciclo di vita del controllo del codice sorgente
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per ciclo di vita del controllo del codice sorgente.

###<a name="to-enable-the-application-integration-for-scc-lifecycle-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per ciclo di vita del controllo del codice sorgente, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Ciclo di vita del controllo del codice sorgente**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Ciclo di vita del controllo del codice sorgente**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Ciclo di vita del controllo del codice sorgente] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "Ciclo di vita del controllo del codice sorgente")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a ciclo di vita del controllo del codice sorgente con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Ciclo di vita del controllo del codice sorgente** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al ciclo di vita del controllo del codice sorgente** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione del ciclo di vita di controllo del codice sorgente utilizzando il modello seguente "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on al ciclo di vita del controllo del codice sorgente** fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurare Single Sign-On")

5.  Inoltrare del file di metadati al team di supporto di ciclo di vita di controllo del codice sorgente.

    >[AZURE.NOTE]Il Single sign-on deve essere abilitato per il team di supporto del ciclo di vita di controllo del codice sorgente.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory accedere al ciclo di vita del controllo del codice sorgente, è necessario essere effettuato il provisioning nel ciclo di vita di controllo del codice sorgente.
  
Non esiste alcun elemento azione per la configurazione di provisioning dell'utente al ciclo di vita del controllo del codice sorgente.  
Quando un utente assegnato tenta di accedere a ciclo di vita del controllo del codice sorgente, se necessario viene automaticamente creato un account di ciclo di vita di controllo del codice sorgente.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro controllo del codice sorgente del ciclo di vita utente account strumenti di creazione o API fornite dal ciclo di vita del controllo del codice sorgente agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-scc-lifecycle-perform-the-following-steps"></a>Per assegnare gli utenti al ciclo di vita del controllo del codice sorgente, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Ciclo di vita del controllo del codice sorgente **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).