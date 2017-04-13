<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Learningpool | Microsoft Azure" 
    description="Ecco come utilizzare Learningpool con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Esercitazione: Integrazione di Azure Active Directory con Learningpool
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Learningpool.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Learningpool single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Learningpool sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Learningpool (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Learningpool
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario:")
##<a name="enabling-the-application-integration-for-learningpool"></a>Attivare l'integrazione delle applicazioni per Learningpool
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Learningpool.

###<a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Learningpool, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-learningpool-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-learningpool-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Learningpool**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-learningpool-tutorial/IC795073.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Learningpool**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Learningpool] (./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Learningpool con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.
  
L'applicazione Learningpool prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** .  
Schermata seguente è illustrato un esempio per l'oggetto.

![Attributi Token SAML] (./media/active-directory-saas-learningpool-tutorial/IC795074.png "Attributi Token SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Learningpool** , nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributi")

2.  Per aggiungere i mapping attributo obbligatorio, procedere come segue:

    ###  

  	|Nome attributo                |Valore dell'attributo            |
  	|------------------------------|---------------------------|

     urn: oid:1.2.840.113556.1.4.221 | User.userPrincipalName
  	|-------------------------------|--------------------------|  
     urn: oid:2.5.4.42|User.givenName   
  	|urn: oid:0.9.2342.19200300.100.1.3|User.Mail
  	|urn: oid:2.5.4.4|User.surname

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.
    3.  Nell'elenco **Valore dell'attributo** , selezionare il valore dell'attributo visualizzato per tale riga.
    4.  Fare clic su **completa**.

3.  Fare clic su **Applica modifiche**.

4.  Nel browser, fare clic su **Indietro** per aprire nuovamente la finestra di dialogo **Avvio rapido** .

5.  Fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Singel Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurare Singel Sign-On")

6.  Nella pagina **specificare come gli utenti di accedere al Learningpool** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurare Single Sign-On")

7.  Nella pagina **Configura App URL** nella casella di testo **Learningpool su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Learningpool (ad esempio: https://parliament.preview.learningpool.com/auth/shibboleth/index.php), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurare URL di App")

8.  Nella pagina **Configura il single sign-on in Learningpool** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurare Single Sign-On")

9.  Inoltrare del file di metadati al team di supporto Learningpool.

    >[AZURE.NOTE]Il Single sign-on deve essere abilitato per il team di supporto Learningpool.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Learningpool, è necessario essere effettuato il provisioning in Learningpool.
  
Non esiste alcun elemento azione per la configurazione di provisioning a Learningpool dell'utente.  
Gli utenti devono essere creati dal team di supporto Learningpool.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro Learningpool utente account strumenti di creazione o API fornite dal Learningpool agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Per assegnare gli utenti a Learningpool, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Learningpool **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-learningpool-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).