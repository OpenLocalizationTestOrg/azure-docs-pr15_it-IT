<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Zendesk | Microsoft Azure" 
    description="Informazioni su come utilizzare Zendesk con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Esercitazione: Integrazione di Azure Active Directory con Zendesk
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Zendesk.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Zendesk
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Zendesk sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Zendesk (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Zendesk
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario:")

##<a name="enabling-the-application-integration-for-zendesk"></a>Attivare l'integrazione delle applicazioni per Zendesk
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Zendesk.

###<a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Zendesk, procedere come segue:

1.  Nel portale di gestione di Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-zendesk-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-zendesk-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Zendesk**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-zendesk-tutorial/IC773084.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Zendesk**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Zendesk] (./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Zendesk con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Zendesk è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di Azure Active Directory, nella pagina integrazione applicazione **Zendesk** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Servizio Single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773086.png "Servizio Single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Zendesk** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di app] (./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurare URL di app")
  
    un. Nella casella di testo **Zendesk URL di accesso** , digitare l'URL utilizzando il modello seguente:`https://<tenant-name>.zendesk.com`

    b. Fare clic su **Avanti**.



4.  Nella pagina **Configura il single sign-on in Zendesk** fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente il compiter.

    ![Configurare il single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società Zendesk come amministratore.

6.  Fare clic su **amministratore**.

7.  Nel riquadro di spostamento sinistra, fare clic su **Impostazioni**e quindi fare clic su **sicurezza**.

    ![Sicurezza] (./media/active-directory-saas-zendesk-tutorial/IC773089.png "Sicurezza")

8.  Nella pagina **sicurezza** fare clic sulla scheda **amministratore e agenti** .

9.  Selezionare **Single sign-on (SSO) e SAML**e quindi selezionare **SAML**.

10. Nel portale di Azure Active Directory, nella pagina **Configura il single sign-on in Zendesk** , copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **URL Single Sign-on SAML** .

11. Nel portale di Azure Active Directory, nella pagina **Configura il single sign-on in Zendesk** , copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione remoto** .

    ![Servizio Single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773090.png "Servizio Single sign-on")

12. Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale di certificato** .

    >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

13. Fare clic su **Salva**.

14. Nel portale di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a **Zendesk**, è necessario essere effettuato il provisioning in **Zendesk**.  
In caso di **Zendesk**, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente per Zendesk, procedere come segue:

1.  Accedere al tenant di **Zendesk** .

2.  Selezionare la scheda **Elenco clienti** .

3.  Selezionare la scheda **utente** e fare clic su **Aggiungi**.

    ![Aggiungi utente.] (./media/active-directory-saas-zendesk-tutorial/IC773632.png "Aggiungi utente.")

4.  Digitare l'indirizzo di posta elettronica di un account Azure Active Directory esistente si desidera eseguire il provisioning e quindi fare clic su **Salva**.

    ![I nuovi utenti] (./media/active-directory-saas-zendesk-tutorial/IC773633.png "I nuovi utenti")

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Zendesk utente account strumenti di creazione o API fornite dal Zendesk agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Per assegnare gli utenti a Zendesk, procedere come segue:

1.  Nel portale di Azure Active Directory, creare un account di prova.

2.  Nella pagina integrazione applicazione **Zendesk **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-zendesk-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
