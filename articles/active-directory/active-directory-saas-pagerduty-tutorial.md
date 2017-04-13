<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Pagerduty | Microsoft Azure" 
    description="Ecco come utilizzare Pagerduty con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Esercitazione: Integrazione di Azure Active Directory con Pagerduty
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Pagerduty.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Pagerduty
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Pagerduty sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Pagerduty (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Pagerduty
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scenario:")
##<a name="enabling-the-application-integration-for-pagerduty"></a>Attivare l'integrazione delle applicazioni per Pagerduty
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Pagerduty.

###<a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Pagerduty, procedere come segue:

1.  Nel portale di gestione di Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Pagerduty**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Pagerduty**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![PagerDuty] (./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Pagerduty con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Pagerduty** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Pagerduty** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Pagerduty URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. Pagerduty.com*", quindi scegliere **Avanti**.

    ![Configurare app url] (./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurare app url")

4.  Nella pagina **Configura il single sign-on in Pagerduty** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società Pagerduty come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Impostazioni Account**.

    ![Impostazioni account] (./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Impostazioni account")

7.  Fare clic su **servizio single sign-on**.

    ![Servizio Single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Servizio Single sign-on")

8.  Nella pagina abilitare Single Sign-on (SSO), procedere come segue:

    ![Attivare single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Attivare single sign-on")

    1.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    2.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato x. 509**
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Pagerduty** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Pagerduty** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .
    5.  Selezionare **Attiva Single Sign-on**.
    6.  Fare clic su **Salva modifiche**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Pagerduty, è necessario essere effettuato il provisioning in Pagerduty.  
In caso di Pagerduty, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Pagerduty** .

2.  Nel menu nella parte superiore, fare clic su **utenti**.

3.  Fare clic su **aggiungere utenti**.

    ![Aggiunta di utenti] (./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Aggiunta di utenti")

4.  Nella finestra di dialogo **invita il team** , digitare **nome nome e cognome** e indirizzo di **posta elettronica** dell'utente Azure Active Directory che si desidera eseguire il provisioning, fare clic su **Aggiungi**e quindi fare clic su **Invia invita**.

    ![Invitare il team] (./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Invitare il team")

    >[AZURE.NOTE] Tutti gli utenti aggiunti riceveranno un invito per creare un account PagerDuty.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Pagerduty utente account strumenti di creazione o API fornite dal Pagerduty agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Per assegnare gli utenti a Pagerduty, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Pagerduty **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).