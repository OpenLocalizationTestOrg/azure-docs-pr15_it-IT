<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con 15Five | Microsoft Azure" 
    description="Ecco come utilizzare 15Five con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-15five"></a>Esercitazione: Integrazione di Azure Active Directory con 15Five

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e 15Five. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un 15Five single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a 15Five sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di 15Five (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per 15Five
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-15five-tutorial/IC784667.png "Scenario:")
##<a name="enabling-the-application-integration-for-15five"></a>Attivare l'integrazione delle applicazioni per 15Five

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per 15Five.

###<a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per 15Five, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-15five-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-15five-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-15five-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **15Five**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-15five-tutorial/IC784668.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **15Five**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![15Five] (./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti autenticati a 15Five con il proprio account Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **15Five** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-15five-tutorial/IC784670.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al 15Five** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-15five-tutorial/IC784671.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **15Five URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://company.15Five.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-15five-tutorial/IC784672.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in 15Five** fare clic su **Scarica metadati**e quindi inoltrare il file di metadati al team di supporto 15Five.

    ![Configurare il single sign-on] (./media/active-directory-saas-15five-tutorial/IC784673.png "Configurare il single sign-on")

    >[AZURE.NOTE] Il Single sign-on deve essere abilitato per il team di supporto 15Five.

5.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-15five-tutorial/IC784674.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a 15Five, è necessario essere effettuato il provisioning in 15Five.  
In caso di 15Five, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **15Five** come amministratore.

2.  Passare alla **gestione della società**.

    ![Gestione della società] (./media/active-directory-saas-15five-tutorial/IC784675.png "Gestione della società")

3.  Passare a **persone \> aggiungere persone**.

    ![Persone] (./media/active-directory-saas-15five-tutorial/IC784676.png "Persone")

4.  Nella sezione Aggiungi nuovo utente, procedere come segue:

    ![Aggiungi nuova persona] (./media/active-directory-saas-15five-tutorial/IC784677.png "Aggiungi nuova persona")

    1.  Digitare il **nome**, **Cognome**, **titolo**, **indirizzo di posta elettronica** di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Chiudi**.

    >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica incluso un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro 15Five utente account strumenti di creazione o API fornite dall'15Five agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-15five-perform-the-following-steps"></a>Per assegnare gli utenti a 15Five, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **15Five **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-15five-tutorial/IC784678.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-15five-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
