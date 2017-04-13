<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Uservoicehttp | Microsoft Azure" 
    description="Informazioni su come utilizzare Uservoicehttp con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Esercitazione: Integrazione di Azure Active Directory con Uservoicehttp
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Uservoicehttp.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Uservoicehttp
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Uservoicehttp sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Uservoicehttp (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Uservoicehttp
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario:")

##<a name="enabling-the-application-integration-for-uservoice"></a>Attivare l'integrazione delle applicazioni per Uservoicehttp
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Uservoicehttp.

###<a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Uservoicehttp, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-uservoice-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-uservoice-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Uservoicehttp**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-uservoice-tutorial/IC777513.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Uservoicehttp**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Uservoicehttp] (./media/active-directory-saas-uservoice-tutorial/IC777810.png "Uservoicehttp")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Uservoicehttp con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Uservoicehttp è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Uservoicehttp** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Uservoicehttp** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Uservoicehttp URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. UserVoice.com*", quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Uservoicehttp** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\UserVoice.cer**.

    ![Configurare il single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società Uservoicehttp come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su impostazioni e quindi selezionare portale Web dal menu di scelta.

    ![Impostazioni] (./media/active-directory-saas-uservoice-tutorial/IC777519.png "Impostazioni")

7.  Nella scheda **portale Web** , nella sezione **autenticazione utente** , fare clic su **Modifica** per aprire la pagina della finestra di dialogo **Modifica autenticazione dell'utente**

    ![Portale Web] (./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portale Web")

8.  Nella scheda **Modifica autenticazione dell'utente** , procedere come segue:

    ![Modificare l'autenticazione utente] (./media/active-directory-saas-uservoice-tutorial/IC777521.png "Modificare l'autenticazione utente")

    1.  Fare clic su **Single Sign-On (SSO)**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Uservoicehttp** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **Single Sign-on remoto accesso aggiuntivo** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Uservoicehttp** copiare il valore di **URL disconnessione remoto** e quindi incollarlo in **casella di testo Sign-Out remoto Single Sign-on**.
    4.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **corrente ID digitale SHA1 certificato** .  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    5.  Fare clic su **Salva impostazioni di autenticazione**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Uservoicehttp, è necessario essere effettuato il provisioning in Uservoicehttp.  
In caso di Uservoicehttp, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Uservoicehttp** .

2.  Passare a **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-uservoice-tutorial/IC777811.png "Impostazioni")

3.  Fare clic su **Generale**.

4.  Fare clic su **agenti e autorizzazioni**.

    ![Gli agenti e autorizzazioni] (./media/active-directory-saas-uservoice-tutorial/IC777812.png "Gli agenti e autorizzazioni")

5.  Fare clic su **Aggiungi amministratori**.

    ![Aggiungere amministratori] (./media/active-directory-saas-uservoice-tutorial/IC777813.png "Aggiungere amministratori")

6.  Nella finestra di dialogo **invita gli amministratori** , procedere come segue:

    ![Invitare gli amministratori] (./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invitare gli amministratori")

    1.  In TextBox messaggi di posta elettronica digitare l'indirizzo di posta elettronica dell'account di cui eseguire il provisioning e quindi fare clic su **Aggiungi**.
    2.  Fare clic su **Invita**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Uservoicehttp utente account strumenti di creazione o API fornite dall'Uservoicehttp agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Per assegnare gli utenti a Uservoicehttp, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Uservoicehttp **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-uservoice-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).