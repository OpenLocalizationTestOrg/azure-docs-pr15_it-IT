<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Coupa | Microsoft Azure" 
    description="Ecco come utilizzare Coupa con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-coupa"></a>Esercitazione: Integrazione di Azure Active Directory con Coupa

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Coupa.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Coupa single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Coupa sarà possibile accesso singolo nell'applicazione tramite l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Coupa
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario:")
##<a name="enabling-the-application-integration-for-coupa"></a>Attivare l'integrazione delle applicazioni per Coupa

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Coupa.

###<a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Coupa, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-coupa-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-coupa-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-coupa-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Coupa**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-coupa-tutorial/IC791898.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Coupa**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Coupa] (./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Coupa con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Coupa è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Accedere al sito aziendale Coupa come amministratore.

2.  Passare a **configurazione \> controllo sicurezza**.

    ![Controlli di protezione] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Controlli di protezione")

3.  Per scaricare il file di metadati Coupa nel computer in uso, fare clic su **Download e l'importazione di metadati SP**.

    ![Metadati Coupa SP] (./media/active-directory-saas-coupa-tutorial/IC791901.png "Metadati Coupa SP")

4.  In una nuova finestra del browser, accedere al portale di classica Azure.

5.  Nella pagina integrazione applicazione **Coupa** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurare Single Sign-On")

6.  Nella pagina **specificare come gli utenti di accedere al Coupa** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurare Single Sign-On")

7.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurare URL di App")

    1.  Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Coupa (ad esempio: "*http://company.Coupa.com*").
    2.  Aprire il file di metadati Coupa scaricato e quindi copiare l' **Indice AssertionConsumerService/URL**.
    3.  Nella casella di testo **URL di risposta Coupa** , incollare il valore di **Indice AssertionConsumerService/URL** .
    4.  Fare clic su **Avanti**.

8.  Nella pagina **Configura il single sign-on in Coupa** per scaricare il file di metadati, fare clic su **Scarica metadati**e quindi salvare il file in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurare Single Sign-On")

9.  Nel sito della società Coupa, passare a **configurazione \> controllo sicurezza**.

    ![Controlli di protezione] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Controlli di protezione")

10. Nella sezione **accesso con le credenziali Coupa** , procedere come segue:

    ![Accedere usando le credenziali Coupa] (./media/active-directory-saas-coupa-tutorial/IC791906.png "Accedere usando le credenziali Coupa")

    1.  Selezionare **l'accesso utilizzando SAML**.
    2.  Fare clic su **Sfoglia** per caricare un file di metadati di Azure Active scaricato.
    3.  Fare clic su **Salva**.

11. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Coupa, è necessario essere effettuato il provisioning in Coupa.  
In caso di Coupa, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Coupa** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **Impostazioni**e quindi fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-coupa-tutorial/IC791908.png "Utenti")

3.  Fare clic su **Crea**.

    ![Creare gli utenti] (./media/active-directory-saas-coupa-tutorial/IC791909.png "Creare gli utenti")

4.  Nella sezione **Utente creare** , eseguire i passaggi seguenti:

    ![Dettagli utente] (./media/active-directory-saas-coupa-tutorial/IC791910.png "Dettagli utente")

    1.  Digitare **l'accesso**, **nome**, **Cognome**, **ID Single Sign-On**, gli attributi di **posta elettronica** di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Crea**.

    >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica con un collegamento di confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Coupa utente account strumenti di creazione o API fornite dal Coupa agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-coupa-perform-the-following-steps"></a>Per assegnare gli utenti a Coupa, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Coupa **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-coupa-tutorial/IC791911.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-coupa-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
