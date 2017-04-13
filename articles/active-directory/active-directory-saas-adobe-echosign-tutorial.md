<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Adobe EchoSign | Microsoft Azure" 
    description="Informazioni su come utilizzare Adobe EchoSign con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe EchoSign

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure ed EchoSign di Adobe.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un EchoSign Adobe singolo accesso dall'abbonamento abilitato

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Adobe EchoSign sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Adobe EchoSign (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Adobe EchoSign
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scenario:")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>Attivare l'integrazione delle applicazioni per Adobe EchoSign

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Adobe EchoSign.

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Adobe EchoSign, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Adobe EchoSign**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Adobe EchoSign**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Adobe EchoSign] (./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Adobe EchoSign con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Adobe EchoSign** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Adobe EchoSign** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Adobe EchoSign accesso su URL** digitare l'URL utilizzando il modello seguente "*https://company.echosign.com/*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Adobe EchoSign** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito Adobe EchoSign della società.

6.  Nel menu nella parte superiore, fare clic su **Account**e quindi, nel riquadro di spostamento nella matrice a sinistra, fare clic su **Impostazioni SAML** in **Impostazioni Account**.

    ![Account] (./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Account")

7.  Nella sezione Impostazioni SAML, procedere come segue:

    ![Impostazioni di SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Impostazioni di SAML")

    1.  Come **SAML modalità**, selezionare **SAML obbligatorio**.
    2.  Selezionare **Consenti agli amministratori di Account EchoSign di accedere usando le proprie credenziali di EchoSign**.
    3.  Come **La creazione dell'utente**, selezionare **Aggiungi automaticamente gli utenti autenticati tramite SAML**.

8.  Spostare, procedere come segue:

    ![Impostazioni di SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Impostazioni di SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Adobe EchoSign** copiare il valore di **ID entità** e quindi incollarla nella casella di testo **ID entità IdP** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Adobe EchoSign** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso IdP** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Adobe EchoSign** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione IdP** .
    4.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o) 
 5.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **IdP certificato** 6.  Fare clic su **Salva modifiche**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Adobe EchoSign, è necessario essere effettuato il provisioning in Adobe EchoSign.  
In caso di Adobe EchoSign, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Adobe EchoSign** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **Account**e quindi nel riquadro di spostamento nella matrice a sinistra fare clic su **utenti e gruppi**e quindi fare clic su **Crea un nuovo utente**.

    ![Account] (./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Account")

3.  Nella sezione **Crea nuovo utente** , procedere come segue:

    ![Creare utente] (./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Creare utente")

    1.  Digitare l' **Indirizzo di posta elettronica**, **nome** e **Cognome** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Crea utente**.

        >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica contenente un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Adobe EchoSign utente account strumenti di creazione o API forniti da Adobe EchoSign agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Per assegnare gli utenti a Adobe EchoSign, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Adobe EchoSign **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
