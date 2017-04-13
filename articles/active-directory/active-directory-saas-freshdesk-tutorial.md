<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Freshdesk | Microsoft Azure" 
    description="Ecco come utilizzare Freshdesk con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Esercitazione: Integrazione di Azure Active Directory con Freshdesk
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Freshdesk.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Freshdesk
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Freshdesk sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Freshdesk (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Freshdesk
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenario:")
##<a name="enabling-the-application-integration-for-freshdesk"></a>Attivare l'integrazione delle applicazioni per Freshdesk
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Freshdesk.

###<a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Freshdesk, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Freshdesk**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Freshdesk**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Freshdesk] (./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Freshdesk con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Freshdesk è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Freshdesk** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Freshdesk** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Freshdesk URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. Freshdesk.com*", quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Freshdesk** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\Freshdesk.cer**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Freshdesk come amministratore.

6.  Nel menu nella parte superiore, fare clic su **amministratore**.

    ![Amministratore] (./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Amministratore")

7.  Nella scheda **Impostazioni generali** fare clic su **sicurezza**.

    ![Sicurezza] (./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sicurezza")

8.  Nella sezione **sicurezza** , procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign-On")

    1.  **Per **Single Sign On (SSO)**, selezionare.**
    2.  Selezionare **SAML Single Sign-on**.
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Freshdesk** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso SAML** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Freshdesk** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .
    5.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale certificato di sicurezza** .  

        >[AZURE.TIP]Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    6.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Freshdesk, è necessario essere effettuato il provisioning in Freshdesk.  
In caso di Freshdesk, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Freshdesk** .

2.  Nel menu nella parte superiore, fare clic su **amministratore**.

    ![Amministratore] (./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Amministratore")

3.  Nella scheda **Impostazioni generali** fare clic su **agenti**.

    ![Agenti] (./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agenti")

4.  Fare clic su **nuovo agente**.

    ![Nuovo agente] (./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Nuovo agente")

5.  Nella finestra di dialogo informazioni agente, procedere come segue:

    ![Informazioni agente] (./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Informazioni agente")

    1.  Nella casella di testo **Nome e cognome** , digitare il nome dell'account Azure Active Directory che si desidera eseguire il provisioning.
    2.  Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica di Azure Active Directory dell'account Azure Active Directory che si desidera eseguire il provisioning.
    3.  Nella casella di testo **titolo** digitare il titolo dell'account Azure Active Directory che si desidera eseguire il provisioning.
    4.  Selezionare **il ruolo di agenti**e quindi fare clic su **Assegna**.
    5.  Fare clic su **Salva**.
    
        >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica contenente un collegamento per confermare l'account in cui viene attivato.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Freshdesk utente account strumenti di creazione o API fornite dal Freshdesk agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Per assegnare gli utenti a Freshdesk, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Freshdesk **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).