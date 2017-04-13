<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con il margine di flessibilità | Microsoft Azure" 
    description="Informazioni su come utilizzare il margine di flessibilità con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>Esercitazione: Integrazione di Azure Active Directory con il margine di flessibilità
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e margine di flessibilità.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un margine di flessibilità single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a margine di flessibilità sarà possibile eseguire l'accesso singolo all'applicazione del sito di margine di flessibilità aziendale (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per il margine di flessibilità
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-slack-tutorial/IC794980.png "Scenario:")

##<a name="enabling-the-application-integration-for-slack"></a>Attivare l'integrazione delle applicazioni per il margine di flessibilità
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per il margine di flessibilità.

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per il margine di flessibilità, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-slack-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-slack-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-slack-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella di ricerca**digitare **il margine di flessibilità**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-slack-tutorial/IC794981.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **il margine di flessibilità**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Scenario:] (./media/active-directory-saas-slack-tutorial/IC796925.png "Scenario:")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione per il margine di flessibilità con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **margine di flessibilità** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794982.png "Configurare Single Sign-On")

2.  Nella pagina **da come si desidera agli utenti di accedere al margine di flessibilità** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794983.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** digitare l'URL del tenant di margine di flessibilità nella casella di testo **Margine di flessibilità URL di accesso** (ad esempio: "*https://azuread.slack.com*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-slack-tutorial/IC794984.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on al margine di flessibilità** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794985.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito del margine di flessibilità della società.

6.  Passare a **a Microsoft Azure Active Directory \> impostazioni Team**.

    ![Impostazioni del team] (./media/active-directory-saas-slack-tutorial/IC794986.png "Impostazioni del team")

7.  Nella sezione **Impostazioni del Team** , fare clic sulla scheda **autenticazione** e quindi fare clic su **Cambia impostazioni**.

    ![Impostazioni del team] (./media/active-directory-saas-slack-tutorial/IC794987.png "Impostazioni del team")

8.  Nella finestra di dialogo **Impostazioni di autenticazione SAML** , procedere come segue:

    ![Impostazioni di SAML] (./media/active-directory-saas-slack-tutorial/IC794988.png "Impostazioni di SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on al margine di flessibilità** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **SAML 2.0 Endpoint HTTP ()** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on al margine di flessibilità** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **Emittente Provider di identità** .
    3.  Creare un file **con codifica base 64** del certificato scaricata.
    
        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato pubblico** .
    5.  Deselezionare **Consenti agli utenti di modificare l'indirizzo di posta elettronica**.
    6.  Selezionare **Consenti agli utenti di scegliere il proprio nome utente**.
    7.  Come **l'autenticazione per il team deve essere utilizzata da**, selezionare **sia obbligatorio**.
    8.  Fare clic su **Salva configurazione**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794989.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory accedere al margine di flessibilità, è necessario essere effettuato il provisioning in margine di flessibilità.
  
Non esiste alcun elemento azione per la configurazione di provisioning dell'utente per il margine di flessibilità.  
Quando un utente assegnato tenta di accedere a un margine di flessibilità, se necessario viene automaticamente creato un account margine di flessibilità.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>Per assegnare gli utenti al margine di flessibilità, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **margine di flessibilità **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-slack-tutorial/IC794990.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-slack-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).