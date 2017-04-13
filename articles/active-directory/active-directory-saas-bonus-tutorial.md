<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Bonus.ly | Microsoft Azure" 
    description="Ecco come utilizzare Bonus.ly con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Esercitazione: Integrazione di Azure Active Directory con Bonus.ly

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Bonus.ly. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di test in Bonus.ly

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Bonus.ly
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenario:")
##<a name="enabling-the-application-integration-for-bonusly"></a>Attivare l'integrazione delle applicazioni per Bonus.ly

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Bonus.ly.

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Bonus.ly, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Attivare single sign-on] (./media/active-directory-saas-bonus-tutorial/IC773680.png "Attivare single sign-on")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-bonus-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-bonus-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-bonus-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Bonus.ly**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-bonus-tutorial/IC773681.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Bonus.ly**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Bonus.ly con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Bonus.ly è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Bonus.ly** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Bonus.ly** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Bonus.ly Tenant URL** digitare l'URL utilizzando il modello seguente "https://*\<nome tenant\>. Bonus.ly*"e quindi fare clic su **Avanti**: 

    ![Configurare URL di app] (./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in Bonus.ly** fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\Bonusly.cer**.

    ![Configurare il single sign-on] (./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurare il single sign-on")

5.  In una nuova finestra del browser, accedere al tenant di **Bonus.ly** .

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Impostazioni**e quindi selezionare **le integrazioni e App**.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  **Single Sign-On**, selezionare **SAML**.

8.  Nella scheda **SAML** , procedere come segue:

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Bonus.ly** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di destinazione IdP Single Sign-on** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Bonus.ly** copiare il valore di **ID emittente** e quindi incollarla nella casella di testo **IdP emittente** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Bonus.ly** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso IdP** .
    4.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale del certificato** .

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

9.  Fare clic su **Salva**.

10. Nel portale classico di Microsoft Azure, selezionare la conferma di configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Bonus.ly, è necessario essere effettuato il provisioning in Bonus.ly.  
In caso di Bonus.ly, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  In una finestra del browser web, accedere al tenant Bonus.ly.

2.  Fare clic su **Impostazioni**

    ![Impostazioni] (./media/active-directory-saas-bonus-tutorial/IC781041.png "Impostazioni")

3.  Fare clic sulla scheda **utenti e premi** .

    ![Utenti e premi] (./media/active-directory-saas-bonus-tutorial/IC781042.png "Utenti e premi")

4.  Fare clic su **Gestisci utenti**.

    ![Gestire gli utenti] (./media/active-directory-saas-bonus-tutorial/IC781043.png "Gestire gli utenti")

5.  Fare clic su **Aggiungi utente**.

    ![Aggiunta utente] (./media/active-directory-saas-bonus-tutorial/IC781044.png "Aggiunta utente")

6.  Nella finestra di dialogo **Aggiungi utente** , procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-bonus-tutorial/IC781045.png "Aggiunta utente")

    1.  Digitare il "**posta elettronica**, **nome**, **Cognome**" di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Salva**.

    >[AZURE.NOTE] Il titolare di account AAD verrà visualizzato un messaggio di posta elettronica contenente un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Bonus.ly utente account strumenti di creazione o API fornite dal Bonus.ly agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Per assegnare gli utenti a Bonus.ly, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione Bonus.ly, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-bonus-tutorial/IC773690.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-bonus-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
