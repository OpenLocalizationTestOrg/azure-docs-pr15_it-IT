<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con SumoLogic | Microsoft Azure" 
    description="Ecco come utilizzare SumoLogic con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Esercitazione: Integrazione di Azure Active Directory con SumoLogic
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e SumoLogic.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant SumoLogic
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory è stato assegnato a SumoLogicwill essere grado di single sign in applicazione nel SumoLogic società sito (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per SumoLogic
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scenario:")

##<a name="enabling-the-application-integration-for-sumologic"></a>Attivare l'integrazione delle applicazioni per SumoLogic
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per SumoLogic.

###<a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per SumoLogic, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-sumologic-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-sumologic-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **sumologic**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-sumologic-tutorial/IC778550.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **SumoLogic**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![SumoLogic] (./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a SumoLogic con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato codificato base 64 il SumoLogictenant.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **SumoLogic** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al SumoLogic** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **SumoLogic URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. SumoLogic.com*", quindi scegliere **Avanti**.

    ![Configura aoo URL] (./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configura aoo URL")

4.  Nella pagina **Configura il single sign-on in SumoLogic** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società SumoLogic come amministratore.

6.  Passare a **gestire \> protezione**.

    ![Gestire] (./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gestire")

7.  Fare clic su **SAML**.

    ![Impostazioni di sicurezza globale] (./media/active-directory-saas-sumologic-tutorial/IC778557.png "Impostazioni di sicurezza globale")

8.  Nell'elenco **Selezionare una configurazione o crearne uno nuovo** , selezionare **Azure Active Directory**e quindi fare clic su **Configura**.

    ![Configurare SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurare SAML 2.0")

9.  Nella finestra di dialogo **Configura SAML 2.0** , procedere come segue:

    ![Configurare SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurare SAML 2.0")

    1.  Nella casella di testo **Nome configurazione** digitare **Azure Active Directory**.
    2.  Selezionare **la modalità Debug**.
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in SumoLogic** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in SumoLogic** copiare il valore di **URL di richiesta di autenticazione** e quindi incollarla nella casella di testo **URL richiesta uso** .
    5.  Creare un file **con codifica Base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    6.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollare l'intero certificato **X. 509** casella di testo.
    7.  **Attributo di posta elettronica**, selezionare **Usa SAML oggetto**.
    8.  Selezionare **SP avviata Configurazione account di accesso**.
    9.  Nella casella di testo **Percorso di accesso** , digitare **Azure**.
    10. Fare clic su **Salva**.

10. Nel portale di classica Azure, della pagina **Configura il single sign-on in SumoLogic** finestra di dialogo, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa**.

    ![Configurare il single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a SumoLogic, è necessario essere effettuato il provisioning di SumoLogic.  
In caso di SumoLogic, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **SumoLogic** .

2.  Passare a **gestire \> utenti**.

    ![Utenti] (./media/active-directory-saas-sumologic-tutorial/IC778561.png "Utenti")

3.  Fare clic su **Aggiungi**.

    ![Utenti] (./media/active-directory-saas-sumologic-tutorial/IC778562.png "Utenti")

4.  Nella finestra di dialogo **Nuovo utente** , eseguire la procedura seguente:

    ![I nuovi utenti] (./media/active-directory-saas-sumologic-tutorial/IC778563.png "I nuovi utenti")

    1.  Digitare le informazioni correlate di account Azure Active Directory da effettuare il provisioning nelle caselle di testo **nome**, **Cognome** e **posta elettronica** .
    2.  Selezionare un ruolo.
    3.  Come **stato**, selezionare **Attiva**.
    4.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro SumoLogic utente account strumenti di creazione o API fornite dal SumoLogic agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Per assegnare gli utenti a SumoLogic, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **SumoLogic** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-sumologic-tutorial/IC778564.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-sumologic-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).