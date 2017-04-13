<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Clarizen | Microsoft Azure" 
    description="Ecco come utilizzare Clarizen con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Esercitazione: Integrazione di Azure Active Directory con Clarizen

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Clarizen.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Clarizen single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Clarizen sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Clarizen (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Clarizen
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario:")
##<a name="enabling-the-application-integration-for-clarizen"></a>Attivare l'integrazione delle applicazioni per Clarizen

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Clarizen.

###<a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Clarizen, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-clarizen-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-clarizen-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Clarizen**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-clarizen-tutorial/IC784680.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Clarizen**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Clarizen] (./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Clarizen con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Clarizen** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Clarizen** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurare Single Sign-On")

3.  Nella pagina **Configura il single sign-on in Clarizen** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurare Single Sign-On")

4.  In una finestra del browser web diverso, accedere al sito della società **Clarizen** come amministratore (ad esempio: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Fare clic su nome utente e quindi fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-clarizen-tutorial/IC784685.png "Impostazioni")

6.  Fare clic sulla scheda **Impostazioni globali** e quindi, accanto a **Autenticazione federato**, fare clic su **Modifica**.

    ![Impostazioni globali] (./media/active-directory-saas-clarizen-tutorial/IC786906.png "Impostazioni globali")

7.  Nella finestra di dialogo **Autenticazione federato** , procedere come segue:

    ![Autenticazione federato] (./media/active-directory-saas-clarizen-tutorial/IC785892.png "Autenticazione federato")

    1.  Fare clic su **Carica** per caricare il certificato scaricato.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Clarizen** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL di accesso** .
    3.  Nel portale di classica Azure, nella pagina di finestra di dialogo **Configura single disconnessione in Clarizen** , copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **URL Sign-out** .
    4.  Selezionare **Usa POST**.
    5.  Fare clic su **Salva**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Clarizen, è necessario essere effettuato il provisioning in Clarizen.  
In caso di Clarizen, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Clarizen** come amministratore.

2.  Fare clic su **persone**.

    ![Persone] (./media/active-directory-saas-clarizen-tutorial/IC784689.png "Persone")

3.  Fare clic su **Invita utenti**.

    ![Invitare utenti] (./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invitare utenti")

4.  Nella pagina di finestra di dialogo Invita altri utenti, procedere come segue:

    ![Invitare altre persone] (./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invitare altre persone")

    1.  Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica di un account Azure Active Directory valido che si desidera eseguire il provisioning.
    2.  Fare clic su **Invita**.

    >[AZURE.NOTE] Il titolare di account Azure Active Directory riceverà un messaggio di posta elettronica e visitare un collegamento per confermare il proprio account diventa attiva.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Per assegnare gli utenti a Clarizen, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Clarizen **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-clarizen-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
