<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con TeamSeer | Microsoft Azure" 
    description="Ecco come utilizzare TeamSeer con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Esercitazione: Integrazione di Azure Active Directory con TeamSeer
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e TeamSeer.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant TeamSeer
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a TeamSeer sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di TeamSeer (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per TeamSeer
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenario:")

##<a name="enabling-the-application-integration-for-teamseer"></a>Attivare l'integrazione delle applicazioni per TeamSeer
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per TeamSeer.

###<a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per TeamSeer, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-teamseer-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-teamseer-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **TeamSeer**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-teamseer-tutorial/IC789619.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **TeamSeer**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![TeamSeer] (./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a TeamSeer con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **TeamSeer** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al TeamSeer** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **TeamSeer URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://www.teamseer.com/companyid*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in TeamSeer** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società TeamSeer come amministratore.

6.  Passare a **amministratore HR**.

    ![Amministratore di risorse Umane] (./media/active-directory-saas-teamseer-tutorial/IC789634.png "Amministratore di risorse Umane")

7.  Fare clic su **Imposta**.

    ![Programma di installazione] (./media/active-directory-saas-teamseer-tutorial/IC789635.png "Programma di installazione")

8.  Fare clic su **Configura dettagli provider SAML**.

    ![Impostazioni di SAML] (./media/active-directory-saas-teamseer-tutorial/IC789636.png "Impostazioni di SAML")

9.  Nella sezione Dettagli provider SAML, procedere come segue:

    ![Impostazioni di SAML] (./media/active-directory-saas-teamseer-tutorial/IC789637.png "Impostazioni di SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in TeamSeer** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL** .
    2.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    3.  Aprire il certificato codificato base 64 nel blocco note, copiare il contenuto che ne negli Appunti e lo si incolla alla casella di testo **IdP certificato pubblico** .

10. Per completare la configurazione del provider SAML, procedere come segue:

    ![Impostazioni di SAML] (./media/active-directory-saas-teamseer-tutorial/IC789638.png "Impostazioni di SAML")

    1.  **Verificare gli indirizzi di posta elettronica**, digitare indirizzo di posta elettronica dell'utente test.
    2.  Nella casella di testo **emittente** , digitare l'URL emittente del provider di servizi.
    3.  Fare clic su **Salva**.

11. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a TeamSeer, è necessario essere effettuato il provisioning in ShiftPlanning.  
In caso di TeamSeer, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **TeamSeer** come amministratore.

2.  Eseguire i passaggi seguenti:

    ![Amministratore di risorse Umane] (./media/active-directory-saas-teamseer-tutorial/IC789640.png "Amministratore di risorse Umane")

    1.  Passare a **amministratore HR \> utenti**.
    2.  Fare clic su **eseguire la procedura guidata nuovo utente**.

3.  Nella sezione **Dettagli utente** , procedere come segue:

    ![Dettagli utente] (./media/active-directory-saas-teamseer-tutorial/IC789641.png "Dettagli utente")

    1.  Digitare il **nome**, **Cognome**, **nome utente (indirizzo di posta elettronica)** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Avanti**.

4.  Seguire le istruzioni visualizzate per l'aggiunta di un nuovo utente e fare clic su **Fine**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro TeamSeer utente account strumenti di creazione o API fornite dal TeamSeer provisioning degli account utente di Azure Active Directory.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Per assegnare gli utenti a TeamSeer, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **TeamSeer **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-teamseer-tutorial/IC789642.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-teamseer-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).