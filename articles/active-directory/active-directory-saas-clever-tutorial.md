<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Clever | Microsoft Azure" 
    description="Ecco come utilizzare Clever con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>Esercitazione: Integrazione di Azure Active Directory con Clever

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Clever. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant intelligente

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Clever sarà possibile eseguire l'accesso singolo all'applicazione del sito di società intelligente (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Clever
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario:")
##<a name="enabling-the-application-integration-for-clever"></a>Attivare l'integrazione delle applicazioni per Clever

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Clever.

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Clever, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-clever-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-clever-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-clever-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Clever**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-clever-tutorial/IC798978.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Clever**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Intelligente] (./media/active-directory-saas-clever-tutorial/IC798979.png "Intelligente")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Clever con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
L'applicazione intelligente prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** .  
Schermata seguente è illustrato un esempio per l'oggetto.

![Attributi] (./media/active-directory-saas-clever-tutorial/IC798980.png "Attributi")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Clever** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC784682.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Clever** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798981.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Intelligente accesso su URL** digitare l'URL utilizzato da agli utenti di accesso all'applicazione intelligente (ad esempio: *https://clever.com/in/azsandbox*), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-clever-tutorial/IC798982.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Clever** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798983.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito aziendale intelligente come amministratore.

6.  Nella barra degli strumenti fare clic su **Login immediata**.

    ![Accesso immediato] (./media/active-directory-saas-clever-tutorial/IC798984.png "Accesso immediato")

7.  Nella pagina di **Accesso istantaneo** , effettuare le operazioni seguenti:

    ![Accesso immediato] (./media/active-directory-saas-clever-tutorial/IC798985.png "Accesso immediato")

    1.  Digitare l' **URL di accesso**.  

        >[AZURE.NOTE] L' **URL di accesso** è un valore personalizzato.
È possibile ottenere il valore effettivo dal team di supporto intelligente.

    2.  Come **Sistema di identità**, selezionare **ADFS**.
    3.  Fare clic su **Salva**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798986.png "Configurare Single Sign-On")

9.  Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-clever-tutorial/IC795920.png "Attributi")

10. Per aggiungere i mapping attributo obbligatorio, procedere come segue:

    ![attributi token SAML] (./media/active-directory-saas-clever-tutorial/IC795921.png "attributi token SAML")

  	|Nome attributo|Valore dell'attributo|
  	|---|---|
  	|Clever.Student.Credentials.District\_nome utente|User.userPrincipalName|

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.
    3.  Nella casella di testo **Valore dell'attributo** , selezionare il valore dell'attributo visualizzato per tale riga.
    4.  Fare clic su **completa**.

11. Fare clic su **Applica modifiche**.

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Clever, è necessario essere effettuato il provisioning in Clever.  
In caso di Clever, il provisioning è un'attività manuale che deve essere eseguita dal team di supporto intelligente.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro strumento Creazione account utente intelligente o API fornite dal Clever agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>Per assegnare gli utenti a Clever, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Clever **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-clever-tutorial/IC798987.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-clever-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
