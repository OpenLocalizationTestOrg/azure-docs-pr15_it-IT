<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Onit | Microsoft Azure" 
    description="Ecco come utilizzare Onit con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-onit"></a>Esercitazione: Integrazione di Azure Active Directory con Onit
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Onit.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Onit single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Onit sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Onit (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Onit
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-onit-tutorial/IC791166.png "Scenario:")
##<a name="enabling-the-application-integration-for-onit"></a>Attivare l'integrazione delle applicazioni per Onit
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Onit.

###<a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Onit, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-onit-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-onit-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-onit-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Onit**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-onit-tutorial/IC791167.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Onit**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Onit] (./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Onit con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Onit è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).
  
L'applicazione Onit prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** .  
Schermata seguente è illustrato un esempio per l'oggetto.

![Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Onit** , nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-onit-tutorial/IC791169.png "Attributi")

2.  Per aggiungere i mapping attributo obbligatorio, procedere come segue:

    
  	|Nome attributo|Valore dell'attributo|
  	|---|---|
  	|nome|User.userPrincipalName|
  	|Posta elettronica|User.Mail|

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.
    3.  Nell'elenco **Valore dell'attributo** , selezionare il valore dell'attributo visualizzato per tale riga.
    4.  Fare clic su **completa**.

3.  Fare clic su **Applica modifiche**.

4.  Nel browser, fare clic su **Indietro** per aprire nuovamente la finestra di dialogo **Avvio rapido** .

5.  Fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791170.png "Configurare Single Sign-On")

6.  Nella pagina **specificare come gli utenti di accedere al Onit** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791171.png "Configurare Single Sign-On")

7.  Nella pagina **Configura App URL** nella casella di testo **Onit su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Onit (ad esempio: "*https://ms-sso-test.onit.com*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-onit-tutorial/IC791172.png "Configurare URL di App")

8.  Nella pagina **Configura il single sign-on in Onit** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791173.png "Configurare Single Sign-On")

9.  In una finestra del browser web diverso, accedere a sito della società Onit come amministratore.

10. Nel menu nella parte superiore, fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-onit-tutorial/IC791174.png "Amministrazione")

11. Fare clic su **Modifica Corporation**.

    ![Modifica Corporation] (./media/active-directory-saas-onit-tutorial/IC791175.png "Modifica Corporation")

12. Fare clic sulla scheda **sicurezza** .

    ![Informazioni sulla società di modifica] (./media/active-directory-saas-onit-tutorial/IC791176.png "Informazioni sulla società di modifica")

13. Nella scheda **protezione** , procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")

    1.  Come **Strategia di autenticazione**, selezionare **Single Sign-On e la Password**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Onit** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di destinazione Idp** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Onit** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione Idp** .
    4.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale del certificato Idp (SHA1)** .  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    5.  Come **Tipo di Single Sign-on**, selezionare **SAML**.
    6.  Nella casella di testo **il testo del pulsante di accesso Single Sign-on** , digitare il testo di un pulsante desiderato.
    7.  Selezionare **Login con Single Sign-on: necessario per i seguenti domini/utenti**, digitare l'indirizzo di posta elettronica di un utente di prova nella casella di testo correlato e quindi fare clic su **Aggiorna**.
        ![Modifica Corporation] (./media/active-directory-saas-onit-tutorial/IC791178.png "Modifica Corporation")

14. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791179.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Onit, è necessario essere effettuato il provisioning in Onit.  
In caso di Onit, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Onit** come amministratore.

2.  Fare clic su **Aggiungi utente**.

    ![Amministrazione] (./media/active-directory-saas-onit-tutorial/IC791180.png "Amministrazione")

3.  Nella pagina di finestra di dialogo **Aggiungi utente** , procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-onit-tutorial/IC791181.png "Aggiunta utente")

    1.  Digitare il **nome** e l' **Indirizzo di posta elettronica** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Crea**.  

        >[AZURE.NOTE] Il proprietario dell'account verrà visualizzato un messaggio di posta elettronica incluso un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Onit utente account strumenti di creazione o API fornite dall'Onit agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-onit-perform-the-following-steps"></a>Per assegnare gli utenti a Onit, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Onit **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-onit-tutorial/IC791182.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-onit-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).