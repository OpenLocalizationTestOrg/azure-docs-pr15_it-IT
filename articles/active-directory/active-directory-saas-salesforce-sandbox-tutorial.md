<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox | Microsoft Azure"
    description="Informazioni su come utilizzare Salesforce Sandbox con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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
    ms.date="10/28/2016" 
    ms.author="jeedes" />


#<a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Esercitazione: Integrazione di Azure Active Directory con Salesforce Sandbox
>[AZURE.TIP]Per i commenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=521878).
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Salesforce Sandbox.  
Sandbox agli utenti di creare più copie dell'organizzazione in ambienti separati per diversi scopi, ad esempio sviluppo, test e risorse di formazione, senza compromettere i dati e le applicazioni nell'organizzazione di produzione Salesforce.  
Per ulteriori informazioni, vedere [Panoramica sulla modalità Sandbox](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Una sandbox Salesforce.com
  
Se non si dispone di una sandbox valida in Salesforce.com ancora, è necessario contattare Salesforce.
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni per Salesforce Sandbox
2.  Configurare il single sign-on
3.  Attivazione del dominio
4.  Configurare il provisioning dell'utente
5.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenario:")
##<a name="enabling-the-application-integration-for-salesforce-sandbox"></a>Attivare l'integrazione di applicazioni per Salesforce Sandbox
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni per Salesforce sandbox.

###<a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni per sandbox Salesforce, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazione**, fare clic su **Aggiungi un'App**e quindi fare clic su **Aggiungi un'applicazione per la propria organizzazione da utilizzare**.

    ![Operazioni da eseguire?] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Operazioni da eseguire?")

5.  Nella **casella Cerca**digitare **Salesforce Sandbox**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Raccolta dell'applicazione")

6.  Nel riquadro dei risultati selezionare **Salesforce Sandbox**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Salesforce Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Salesforce con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Salesforce Sandbox** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Salesforce Sandbox** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Salesforce Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  Nella pagina **Configura App URL** nella casella di testo **Su URL di accesso** , digitare l'URL utilizzando il modello seguente `http://company.my.salesforce.com`e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurare URL di App")

4. Se è già stato configurato single sign-on per un'altra istanza di Salesforce Sandbox nella directory, è necessario configurare anche l' **identificatore** per lo stesso valore di **accesso dall'URL**. Il campo **dell'identificatore** sono disponibili selezionando la casella di controllo **Mostra impostazioni avanzate** della pagina **Configura App URL** della finestra di dialogo.

4.  Nella pagina **Configura il single sign-on in modalità Salesforce Sandbox** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere la modalità sandbox Salesforce come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Imposta**.

    ![Programma di installazione] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Programma di installazione")

7.  Nel riquadro di spostamento a sinistra, fare clic su **Controlli di protezione**e quindi fare clic su **Impostazioni servizio Single Sign-On**.

    ![Impostazioni servizio Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Impostazioni servizio Single Sign-On")

8.  Nella sezione Impostazioni servizio Single Sign-On, procedere come segue:

    ![Impostazioni servizio Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Impostazioni servizio Single Sign-On")

    un.  Selezionare **SAML abilitato**.
    
    b.  Fare clic su **Nuovo**.

9.  Nella sezione Impostazioni SAML Single Sign-On, procedere come segue:

    ![Impostazioni SAML Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Impostazioni SAML Single Sign-On")

    un.  Nella casella di testo Nome digitare il nome della configurazione (ad esempio: *SPSSOWAAD\_Test*).
    
    b.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in modalità Salesforce Sandbox** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente** .
    
    c.  Nella casella di testo **Id entità** digitare **https://test.salesforce.com** se questa è la prima istanza di Salesforce Sandbox che si sta aggiungendo alla directory. Se è già stato aggiunto un'istanza di Salesforce Sandbox, quindi per il tipo di **ID entità** **Accesso su URL**, che deve essere in questo formato:`http://company.my.salesforce.com`
    
    d.  Fare clic su **Sfoglia** per caricare il certificato scaricato.
    
    e.  Come **Tipo di identità SAML**, selezionare **asserzione contiene l'ID di federazione dall'oggetto utente**.
    
    f.  Come **Percorso di identità SAML**, selezionare **identità si trova nell'elemento NameIdentifier dell'istruzione oggetto**.
    
    g.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in modalità Salesforce Sandbox** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso del Provider di identità** .
    
    h.  SFDC non supporta la disconnessione SAML.  In alternativa, incollare 'https://login.windows.net/common/wsfederation?wa=wsignout1.0' esso nella casella di testo **URL disconnessione del Provider di identità** .
    
    si.  Come **Servizio Provider avviata richiedere l'associazione**, selezionare **HTTP POST**.
    
    j. Fare clic su **Salva**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurare Single Sign-On")

##<a name="enabling-your-domain"></a>Attivazione del dominio
  
In questa sezione presuppone che sia già stato creato un dominio.  
Per ulteriori informazioni, vedere [Definizione del nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###<a name="to-enable-your-domain-perform-the-following-steps"></a>Per abilitare il dominio, procedere come segue:

1.  Nel riquadro di spostamento sinistra, fare clic su **Gestione dei domini**e quindi fare clic su **dominio personale.**

    ![Il dominio personalizzato] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Il dominio personalizzato")

    >[AZURE.NOTE]Assicurarsi che il dominio è stato configurato correttamente.

2.  Nella sezione **Impostazioni di pagina di accesso** , fare clic su **Modifica**, quindi, a seconda **Del servizio di autenticazione**, selezionare il nome del SAML Single Sign-On impostazione di dalla sezione precedente e infine fare clic su **Salva**.

    ![Il dominio personalizzato] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Il dominio personalizzato")
  
Non appena si dispone di un dominio configurato, gli utenti devono utilizzare l'URL di dominio per l'accesso a sandbox Salesforce.  
Per ottenere il valore dell'URL, fare clic sul profilo di Single Sign-on creata nella sezione precedente.
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory per Salesforce Sandbox dell'utente.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Nel portale di Salesforce, nella barra di spostamento superiore, selezionare il proprio nome per espandere il menu utente:

    ![Impostazioni personali] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Impostazioni personali")

2.  Dal menu di utente, selezionare **Impostazioni personali** per aprire la pagina **Impostazioni personali** .

3.  Nel riquadro sinistro fare clic su **personale** per espandere la sezione personale e quindi fare clic su **Reimposta Token sicurezza personale**:

    ![Impostazioni personali] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Impostazioni personali")

4.  Nella pagina **Reimposta Token sicurezza personale** , fare clic su **Reimposta Token di sicurezza** per richiedere a un messaggio di posta elettronica che contiene il token di sicurezza Salesforce.com.

    ![Nuovo Token] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nuovo Token")

5.  Selezionare la cartella Posta in arrivo per un messaggio di posta elettronica da Salesforce.com con "**conferma di sicurezza Salesforce.com**" come oggetto.

6.  Esaminare il messaggio di posta elettronica e copiare il valore di token di sicurezza.

7.  Nel portale di classica Azure, nella pagina integrazione applicazione **salesforce Sandbox** , fare clic su **Configura il provisioning dell'utente** per aprire la finestra di dialogo **Configura il Provisioning dell'utente** .

    ![Configura il provisioning dell'utente] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configura il provisioning dell'utente")

8.  Nella pagina **Immettere le credenziali di Salesforce Sandbox per abilitare il provisioning automatico degli utenti** , specificare le impostazioni di configurazione seguenti:

    ![Salesforce Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    un.  Nella casella di testo **Nome utente dell'amministratore Sandbox Salesforce** , digitare un nome di account sandbox Salesforce con il profilo di **Amministratore di sistema** in Salesforce.com assegnate.

    b.  Nella casella di testo **Password dell'amministratore Sandbox Salesforce** , digitare la password per l'account.

    c.  Nella casella di testo **Token di sicurezza utente** incollare il valore di token di sicurezza.

    d.  Fare clic su **convalida** per verificare la configurazione.

    e.  Fare clic sul pulsante **Avanti** per aprire la pagina di **Conferma** .

9.  Nella pagina di **Conferma** , fare clic su **completa** per salvare la configurazione.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>Per assegnare gli utenti a Salesforce Sandbox, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Salesforce Sandbox **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sì")
  
È ora necessario attendere 10 minuti e verificare che l'account è stato sincronizzato in modalità Salesforce Sandbox.
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586).
