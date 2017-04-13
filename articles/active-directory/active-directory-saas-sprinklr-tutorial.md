<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Sprinklr | Microsoft Azure" 
    description="Ecco come utilizzare Sprinklr con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Esercitazione: Integrazione di Azure Active Directory con Sprinklr
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Sprinklr.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Sprinklr
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Sprinklr sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Sprinklr (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Sprinklr
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scenario:")

##<a name="enabling-the-application-integration-for-sprinklr"></a>Attivare l'integrazione delle applicazioni per Sprinklr
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Sprinklr.

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Sprinklr, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Sprinklr**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Sprinklr**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Sprinklr] (./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Sprinklr con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Sprinklr** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Sprinklr** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** nella casella di testo **Sprinklr URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://\<nome tenant\>. sprinklr.com*", quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Sprinklr** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società Sprinklr come amministratore.

6.  Passare a **amministrazione \> impostazioni**.

    ![Amministrazione] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Amministrazione")

7.  Passare a **gestire Partner \> Single Sign** dal riquadro di sinistra.

    ![Gestire i Partner] (./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gestire i Partner")

8.  Fare clic su **+ Add Single Sign on**.

    ![Punti di accesso singolo] (./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Punti di accesso singolo")

9.  Nella pagina **Single Sign-on** , procedere come segue:

    ![Punti di accesso singolo] (./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Punti di accesso singolo")

    1.  Nella casella di testo **nome** digitare un nome per la configurazione (ad esempio: *WAADSSOTest*).
    2.  Selezionare **attivato**.
    3.  Selezionare **Nuovo certificato Single Sign-on**.
    4.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    5.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato di Provider di identità**
    6.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Sprinklr** copiare il valore di **ID Provider di identità** e quindi incollarla nella casella di testo **Id entità** .
    7.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Sprinklr** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso del Provider di identità** .
    8.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Sprinklr** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione del Provider di identità** .
    9.  Come **Tipo di ID utente SAML**, selezionare **asserzione contiene utente "s sprinklr.com username**.
    10. Come **Percorso di ID utente di SAML**, selezionare **ID utente si trova nell'elemento identificatore del nome dell'istruzione oggetto**.
    11. Chiudere **salvare**.

        ![SAML] (./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per gli utenti AAD in grado di accedere, è necessario essere effettuato il provisioning per l'accesso all'interno dell'applicazione Sprinklr.  
In questa sezione viene descritto come creare gli account utente AAD all'interno di Sprinklr.

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente in Sprinklr, procedere come segue:

1.  Accedere come amministratore sito Sprinklr della società.

2.  Passare a **amministrazione \> impostazioni**.

    ![Amministrazione] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Amministrazione")

3.  Passare a **Gestione Client \> utenti** dal riquadro di sinistra.

    ![Impostazioni] (./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Impostazioni")

4.  Fare clic su **Aggiungi utente**.

    ![Impostazioni] (./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Impostazioni")

5.  Nella finestra di dialogo **Modifica utente** , procedere come segue:

    ![Modifica utente] (./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Modifica utente")

    1.  Nel **messaggio di posta elettronica**, caselle di testo **nome** e **Cognome** , digitare le informazioni di un account utente di Azure Active Directory si desidera effettuare il provisioning.
    2.  Selezionare **Password disabilitato**.
    3.  Selezionare una **lingua**.
    4.  Selezionare un **tipo di utente**.
    5.  Fare clic su **Aggiorna**.

    >[AZURE.IMPORTANT] **Password disabilitato** deve essere selezionata per consentire agli utenti di accedere tramite un provider di identità.

6.  Passare al **ruolo ricoperto dai partecipanti**e quindi eseguire le operazioni seguenti:

    ![Ruoli partner] (./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Ruoli partner")

    1.  Selezionare dall'elenco **globale** **tutti\_autorizzazioni**.
    2.  Fare clic su **Aggiorna**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Sprinklr utente account strumenti di creazione o API fornite dal Sprinklr provisioning degli account utente di Azure Active Directory.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Per assegnare gli utenti a Sprinklr, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Sprinklr **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).