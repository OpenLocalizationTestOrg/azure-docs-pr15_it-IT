<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con il Software Igloo | Microsoft Azure" 
    description="Informazioni su come usare Igloo Software con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Esercitazione: Integrazione di Azure Active Directory con il Software Igloo
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Igloo Software.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un [Software di Igloo](http://www.igloosoftware.com/) Single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Software Igloo sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Software Igloo (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni Software Igloo
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scenario:")
##<a name="enabling-the-application-integration-for-igloo-software"></a>Attivare l'integrazione di applicazioni Software Igloo
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni Software Igloo.

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni Software Igloo, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Igloo Software**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Igloo Software**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Igloo] (./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Software Igloo con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato codificato base 64 al tenant di Desktop centrale.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Igloo Software** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Software Igloo** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Microsoft Azure Active Directory Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure Active Directory Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Igloo Software URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://company.igloocommunities.com/?signin*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on Igloo software** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito della società Igloo Software.

6.  Passare al **Pannello di controllo**.

    ![Pannello di controllo] (./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Pannello di controllo")

7.  Nella scheda **appartenenza** , fare clic su **Impostazioni di accesso**.

    ![Accedere a impostazioni] (./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Accedere a impostazioni")

8.  Nella sezione Configurazione SAML fare clic su **Configurare l'autenticazione SAML**.

    ![Configurazione di SAML] (./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configurazione di SAML")

9.  Nella sezione **Configurazione generale** , procedere come segue:

    ![Configurazione generale] (./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configurazione generale")

    1.  Nella casella di testo **Nome della connessione** , digitare un nome personalizzato per la configurazione.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Igloo software** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso IdP** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Igloo software** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione IdP** .
    4.  Come **risposta disconnessione e tipo di richiesta HTTP**, selezionare **POST**.
    5.  Creare un file di testo del certificato scaricata.
        
        >[AZURE.TIP]Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    6.  Rimuovere la prima riga e l'ultima riga rispetto alla versione di file di testo del certificato, copiare il testo rimanente certificato e quindi incollarla nella casella di testo **Certificato pubblico** .

10. In **risposta e la configurazione di autenticazione**, procedere come segue:

    ![Configurazione di autenticazione e risposta] (./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configurazione di autenticazione e risposta")

    1.  Selezionare **ADFS Microsoft**come **Provider di identità**.
    2.  Come **Tipo di identificatore**, selezionare **l'Indirizzo di posta elettronica**.
    3.  Nella casella di testo **Degli attributi di posta elettronica** , digitare **emailaddress**.
    4.  Nella casella di testo **Nome attributo** digitare **givenname**.
    5.  Nella casella di testo **Ultimo nome attributo** digitare **Cognome**.

11. Eseguire la procedura seguente per completare la configurazione:

    ![Creazione dell'utente nella accesso] (./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Creazione dell'utente nella accesso")

    1.  Come **la creazione dell'utente nella accesso**, selezionare **Crea un nuovo utente del sito quando eseguire l'accesso**.
    2.  Come **accedere a impostazioni**, selezionare **il pulsante SAML usare nella schermata "Accesso"**.
    3.  Fare clic su **Salva**.

12. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione a Software Igloo il provisioning dell'utente.  
Quando un utente assegnato tenta di accedere a Software Igloo mediante il pannello di accesso, Software Igloo verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da parte di Software Igloo.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Per assegnare gli utenti a Igloo Software, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Igloo Software **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).