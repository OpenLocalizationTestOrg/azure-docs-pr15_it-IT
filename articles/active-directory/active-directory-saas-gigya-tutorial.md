<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Gigya | Microsoft Azure" 
    description="Ecco come utilizzare Gigya con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>Esercitazione: Integrazione di Azure Active Directory con Gigya
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Gigya.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Gigya single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Gigya sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Gigya (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Gigya
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Configurare Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurare Single Sign-On")
##<a name="enabling-the-application-integration-for-gigya"></a>Attivare l'integrazione delle applicazioni per Gigya
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Gigya.

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Gigya, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-gigya-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-gigya-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-gigya-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Gigya**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-gigya-tutorial/IC789513.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Gigya**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Gigya] (./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Gigya con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Gigya** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Gigya** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Gigya su URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://company.gigya.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Gigya** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Gigya come amministratore.

6.  Passare a **impostazioni \> accesso SAML**, quindi fare clic sul pulsante **Aggiungi** .

    ![Accesso SAML] (./media/active-directory-saas-gigya-tutorial/IC789532.png "Accesso SAML")

7.  Nella sezione **Accesso SAML** , procedere come segue:

    ![Configurazione di SAML] (./media/active-directory-saas-gigya-tutorial/IC789533.png "Configurazione di SAML")

    1.  Nella casella di testo **nome** digitare un nome per la configurazione.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Gigya** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Gigya** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL del servizio Single Sign-On** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Gigya** copiare il valore di **Nome identificatore di formato** e quindi incollarlo nella casella di testo **Nome ID formato** .
    5.  Creare un file **con codifica base 64** del certificato scaricata.
        
        >[AZURE.TIP]Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    6.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato x. 509** .
    7.  Fare clic su **Salva impostazioni**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Gigya, è necessario essere effettuato il provisioning in Gigya.  
In caso di Gigya, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Gigya** come amministratore.

2.  Passare a **amministratore \> Gestione utenti**e quindi fare clic su **Invita utenti**.

    ![Gestire gli utenti] (./media/active-directory-saas-gigya-tutorial/IC789535.png "Gestire gli utenti")

3.  Nella finestra di dialogo Invita utenti, procedere come segue:

    ![Invitare utenti] (./media/active-directory-saas-gigya-tutorial/IC789536.png "Invitare utenti")

    1.  Nella casella di testo **messaggio di posta elettronica** digitare l'alias di posta elettronica di un account Azure Active Directory valido che si desidera eseguire il provisioning.
    2.  Fare clic su **Invita utenti**.
    
        >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica contenente un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Gigya utente account strumenti di creazione o API fornite dal Gigya agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Per assegnare gli utenti a Gigya, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Gigya **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-gigya-tutorial/IC789537.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-gigya-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).