<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Picturepark | Microsoft Azure" 
    description="Ecco come utilizzare Picturepark con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Esercitazione: Integrazione di Azure Active Directory con Picturepark
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Picturepark.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Picturepark
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Picturepark sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Picturepark (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Picturepark
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario:")

##<a name="enabling-the-application-integration-for-picturepark"></a>Attivare l'integrazione delle applicazioni per Picturepark
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Picturepark.

###<a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Picturepark, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-picturepark-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-picturepark-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Picturepark**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-picturepark-tutorial/IC795056.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Picturepark**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Picturepark] (./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Picturepark con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Picturepark è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, informazioni [su come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)..

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Picturepark** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Picturepark** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Picturepark su URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://company.picturepark.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Picturepark** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Picturepark come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Strumenti di amministrazione**e quindi fare clic su **Management Console**.

    ![Console di gestione] (./media/active-directory-saas-picturepark-tutorial/IC795062.png "Console di gestione")

7.  Fare clic su **autenticazione**e quindi fare clic su **provider di identità**.

    ![Autenticazione] (./media/active-directory-saas-picturepark-tutorial/IC795063.png "Autenticazione")

8.  Nella sezione **configurazione del provider di identità** , procedere come segue:

    ![Configurazione del provider di identità] (./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configurazione del provider di identità")

    1.  Fare clic su **Aggiungi**.
    2.  Digitare un nome per la configurazione.
    3.  Selezionare **Imposta come predefinito**.
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Picturepark** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **URI emittente** .
    5.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **Attendibili emittente cursore stampa** .  

        >[AZURE.TIP]Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    6.  Fare clic su **JoinDefaultUsersGroup**.
    7.  Per impostare l'attributo **Emailaddress** nella casella di testo **domanda** , digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
        ![Configurazione] (./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configurazione")
    8.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Picturepark, è necessario essere effettuato il provisioning in Picturepark.  
In caso di Picturepark, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Picturepark** .

2.  Nella barra degli strumenti nella parte superiore, fare clic su **Strumenti di amministrazione**e quindi fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-picturepark-tutorial/IC795067.png "Utenti")

3.  Nella scheda **Panoramica di utenti** , fare clic su **Nuovo**.

    ![Gestione degli utenti] (./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gestione degli utenti")

4.  Nella finestra di dialogo **Crea utente** , procedere come segue:

    ![Creare utente] (./media/active-directory-saas-picturepark-tutorial/IC795069.png "Creare utente")

    1.  Tipo i: **indirizzo di posta elettronica**, **la Password**, **confermare la Password**, **nome**, **Cognome**, **società**, **paese**, **ZIP**, **Città** di Azure Active Directory utente valido desiderata disposizione on nelle caselle di testo correlati.
    2.  Selezionare una **lingua**.
    3.  Fare clic su **Crea**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro Picturepark utente account strumenti di creazione o API fornite dal Picturepark agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Per assegnare gli utenti a Picturepark, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Picturepark **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-picturepark-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).