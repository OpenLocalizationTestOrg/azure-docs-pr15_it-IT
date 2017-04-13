<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Thirdlight | Microsoft Azure" 
    description="Ecco come utilizzare Thirdlight con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Esercitazione: Integrazione di Azure Active Directory con Thirdlight
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Thirdlight.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Thirdlight single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Thirdlight sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Thirdlight (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Thirdlight
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scenario:")

##<a name="enabling-the-application-integration-for-thirdlight"></a>Attivare l'integrazione delle applicazioni per Thirdlight
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Thirdlight.

###<a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Thirdlight, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Thirdlight**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Thirdlight**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![ThirdLight] (./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Thirdlight con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Thirdlight è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Thirdlight** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Thirdlight** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Thirdlight URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Thirdlight (ad esempio: "*http://azuresso2.thirdlight.com/*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Thirdlight** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Thirdlight come amministratore.

6.  Passare a **configurazione \> amministrazione del sistema**e quindi fare clic su **SAML2**.

    ![Amministrazione del sistema] (./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Amministrazione del sistema")

7.  Nella sezione Configurazione SAML2, procedere come segue:

    ![SAML Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")

    1.  Selezionare **Abilita SAML2 Single Sign-On**.
    2.  Come **origine per i metadati IdP**, selezionare **Carica IdP metadati da XML**.
    3.  Aprire il file di metadati scaricati, copiare il contenuto e quindi incollarla nella casella di testo **IdP metadati XML** .
    4.  Fare clic su **Salva SAML2 impostazioni**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Thirdlight, è necessario essere effettuato il provisioning in Thirdlight.  
In caso di Thirdlight, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Thirdlight** come amministratore.

2.  Passare alla scheda **utenti** .

3.  Selezionare **utenti e gruppi**.

4.  Fare clic su **Aggiungi nuovo utente** .

5.  Immettere il **nome utente, nome o descrizione, messaggio di posta elettronica, scegliere un'impostazione predefinita o di nuovi membri del gruppo** di un account valido AAD che si desidera effettuare il provisioning.

6.  Fare clic su **Crea**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Thirdlight utente account strumenti di creazione o API fornite dal Thirdlight agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Per assegnare gli utenti a Thirdlight, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Thirdlight **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).