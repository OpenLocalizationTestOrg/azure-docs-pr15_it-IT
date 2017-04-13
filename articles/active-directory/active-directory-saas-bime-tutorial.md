<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Bime | Microsoft Azure" 
    description="Ecco come utilizzare Bime con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-bime"></a>Esercitazione: Integrazione di Azure Active Directory con Bime

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Bime.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Bime

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Bime sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Bime (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Bime
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-bime-tutorial/IC775552.png "Scenario:")
##<a name="enabling-the-application-integration-for-bime"></a>Attivare l'integrazione delle applicazioni per Bime

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Bime.

###<a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Bime, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-bime-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-bime-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-bime-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Bime**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-bime-tutorial/IC775553.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Bime**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Bime] (./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Bime con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Bime è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Bime** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-bime-tutorial/IC771709.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Bime** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775555.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Bime URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. Bimeapp.com*", quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-bime-tutorial/IC775556.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Bime** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\Bime.cer**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775557.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Bime come amministratore.

6.  Nella barra degli strumenti fare clic su **amministratore**e quindi su **Account**.

    ![Amministratore] (./media/active-directory-saas-bime-tutorial/IC775558.png "Amministratore")

7.  Nella pagina di configurazione dell'account, eseguire la procedura seguente:

    ![Configurare Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775559.png "Configurare Single Sign-On")

    1.  Selezionare **Abilita SAML autenticazione**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Bime** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso remoto** .
    3.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale di certificato** .  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    4.  Fare clic su **Salva**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775560.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Bime, è necessario essere effettuato il provisioning in Bime.  
In caso di Bime, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al tenant di **Bime** .

2.  Nella barra degli strumenti fare clic su **amministratori**e **utenti**.

    ![Amministratore] (./media/active-directory-saas-bime-tutorial/IC775561.png "Amministratore")

3.  Nell' **Elenco di utenti**, fare clic su **Aggiungi nuovo utente** ("+").

    ![Utenti] (./media/active-directory-saas-bime-tutorial/IC775562.png "Utenti")

4.  Nella pagina **Dettagli utente** finestra di dialogo, procedere come segue:

    ![Dettagli utente] (./media/active-directory-saas-bime-tutorial/IC775563.png "Dettagli utente")

    1.  Immettere il nome, cognome, Login, posta elettronica di un account valido AAD che si desidera eseguire il provisioning.
    2.  Fare clic su Salva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Bime utente account strumenti di creazione o API fornite dal Bime agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-bime-perform-the-following-steps"></a>Per assegnare gli utenti a Bime, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Bime **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-bime-tutorial/IC775564.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-bime-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
