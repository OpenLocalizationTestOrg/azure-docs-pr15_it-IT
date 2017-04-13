<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con BlueJeans | Microsoft Azure" 
    description="Ecco come utilizzare BlueJeans con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>Esercitazione: Azure AD integrazione con BlueJeans

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e BlueJeans.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un BlueJeans single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a BlueJeans sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di BlueJeans (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per BlueJeans
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario:")
##<a name="enabling-the-application-integration-for-bluejeans"></a>Attivare l'integrazione delle applicazioni per BlueJeans

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per BlueJeans.

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per BlueJeans, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **BlueJeans**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **BlueJeans**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![BlueJeans] (./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a BlueJeans con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **BlueJeans** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al BlueJeans** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **BlueJeans su URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://company.BlueJeans.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in BlueJeans** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società **BlueJeans** come amministratore.

6.  Passare a **amministratore \> le impostazioni dei gruppi \> protezione**.

    ![Amministratore] (./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Amministratore")

7.  Nella sezione **sicurezza** , procedere come segue:

    ![SAML Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign-On")

    1.  Selezionare **SAML Single Sign-On**.
    2.  Selezionare **Abilita il provisioning automatico**.

8.  Passare alla procedura seguente:

    ![Percorso di certificato] (./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Percorso di certificato")

    1.  Fare clic su **Selezionare il File**e quindi caricare il certificato scaricato.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in BlueJeans** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in BlueJeans** copiare il valore **Cambia Password URL** e quindi incollarla nella casella di testo **URL Cambia Password** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in BlueJeans** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .

9.  Passare alla procedura seguente:

    ![Salvare le modifiche] (./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Salvare le modifiche")

    1.  Nella casella di testo **id utente** , digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  Nella casella di testo **messaggio di posta elettronica** , digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Fare clic su **Salva modifiche**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a BlueJeans, è necessario essere effettuato il provisioning in BlueJeans.  
In caso di BlueJeans, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **BlueJeans** come amministratore.

2.  Passare a **amministratore \> gestire gli utenti \> aggiungere utente**.

    ![Amministratore] (./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Amministratore")

    >[AZURE.IMPORTANT] La scheda **Aggiungi utente** è disponibile solo se nella **scheda Protezione**, **abilitare il provisioning automatico** sia selezionata.

3.  Nella sezione **Aggiungi utente** , procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Aggiunta utente")

    1.  Digitare un **Nome utente BlueJeans**, un **indirizzo di posta elettronica**, un **ID riunione BlueJeans**, un **Moderatore Passcode**, un **Nome e cognome**, la **società** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Aggiungi utente**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro BlueJeans utente account strumenti di creazione o API fornite dal BlueJeans agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Per assegnare gli utenti a BlueJeans, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **BlueJeans **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
