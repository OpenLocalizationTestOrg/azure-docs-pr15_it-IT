<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Panorama9 | Microsoft Azure" 
    description="Ecco come utilizzare Panorama9 con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Esercitazione: Integrazione di Azure Active Directory con Panorama9
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Panorama9.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Panorama9 single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Panorama9 sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Panorama9 (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Panorama9
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scenario:")
##<a name="enabling-the-application-integration-for-panorama9"></a>Attivare l'integrazione delle applicazioni per Panorama9
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Panorama9.

###<a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Panorama9, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-panorama9-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-panorama9-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Panorama9**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-panorama9-tutorial/IC790017.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Panorama9**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Panorama9] (./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Panorama9 con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Panorama9 è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Panorama9** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Panorama9** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** nella casella di testo **Panorama9 su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere a Panorama9 (ad esempio: "*https://dashboard.panorama9.com/saml/access/3262*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Panorama9** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvarla in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Panorama9 come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Gestisci**e quindi fare clic su **estensioni**.

    ![Estensioni] (./media/active-directory-saas-panorama9-tutorial/IC790023.png "Estensioni")

7.  Nella finestra di dialogo **estensioni** , fare clic su **Single Sign-On**.

    ![Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790024.png "Single Sign-On")

8.  Nella sezione **Impostazioni** , procedere come segue:

    ![Impostazioni] (./media/active-directory-saas-panorama9-tutorial/IC790025.png "Impostazioni")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Panorama9** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL del provider di identità** .
    2.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale di certificato** .  

        >[AZURE.TIP]Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    3.  Fare clic su **Salva**.

9.  Nel portale classico di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Panorama9, è necessario essere effettuato il provisioning in Panorama9.  
In caso di Panorama9, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Panorama9** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **Gestisci**e quindi fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-panorama9-tutorial/IC790027.png "Utenti")

3.  Fare clic su **+**.

4.  Nella sezione dati utente, procedere come segue:

    ![Utenti] (./media/active-directory-saas-panorama9-tutorial/IC790028.png "Utenti")

    1.  Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica di un utente di Azure Active Directory valido che si desidera eseguire il provisioning.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro Panorama9 utente account strumenti di creazione o API fornite dal Panorama9 agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Per assegnare gli utenti a Panorama9, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Panorama9** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-panorama9-tutorial/IC790029.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-panorama9-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).