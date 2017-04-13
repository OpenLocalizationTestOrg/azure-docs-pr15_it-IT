<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Zscaler | Microsoft Azure" 
    description="Informazioni su come utilizzare Zscaler con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Zscaler. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Zscaler
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Zscaler
2.  Configurare il single sign-on
3.  Configurazione delle impostazioni del proxy
4.  Configurare il provisioning dell'utente
5.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenario:")

##<a name="enabling-the-application-integration-for-zscaler"></a>Attivare l'integrazione delle applicazioni per Zscaler
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Zscaler.

###<a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Zscaler, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-zscaler-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-zscaler-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Zscaler**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-zscaler-tutorial/IC769227.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Zscaler**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Zscaler] (./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Zscaler con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato Zscaler.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Zscaler** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Attivare single sign-on] (./media/active-directory-saas-zscaler-tutorial/IC769229.png "Attivare single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Zscaler** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configura single accesso dall'] (./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configura single accesso dall'")

3.  Nella pagina **Configura App URL** nella casella di testo **Zscaler URL di accesso** , digitare il segno di URL ottenuto dal Zscaler e quindi fare clic su **Avanti**: 

    >[AZURE.NOTE] Se si conosce l'URL di accesso, contattare il team di supporto Zscaler.

    ![Configurare URL di app] (./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in Zscaler** , procedere come segue:

    ![Configurare il single sign-on] (./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configurare il single sign-on")

    1.  Fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\Zscaler.cer**.
    2.  Copiare l' **URL di richiesta di autenticazione** negli Appunti.

5.  Accedere al tenant di Zscaler.

6.  Nel menu nella parte superiore, fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-zscaler-tutorial/IC769486.png "Amministrazione")

7.  In **Gestisci amministratori e i ruoli**, fare clic su **Gestisci utenti e l'autenticazione**.

    ![Gestire ruoli e gli amministratori] (./media/active-directory-saas-zscaler-tutorial/IC769487.png "Gestire ruoli e gli amministratori")

8.  Nella sezione **Scegliere opzione autenticazione per l'organizzazione** , procedere come segue:

    ![Scegliere le opzioni di autenticazione] (./media/active-directory-saas-zscaler-tutorial/IC769488.png "Scegliere le opzioni di autenticazione")

    1.  Selezionare **autentica utilizzando SAML Single Sign-on**.
    2.  Fare clic su **Configura parametri SAML Single Sign-On**.

9.  Nella pagina di finestra di dialogo **Configurazione SAML Single Sign-On parametri** , eseguire le operazioni seguenti e quindi fare clic su **Chiudi**:

    ![Carica certificato] (./media/active-directory-saas-zscaler-tutorial/IC769489.png "Carica certificato")

    1.  Nella casella di testo **URL del portale di SAML al quale gli utenti vengono inviati per l'autenticazione** , incollare il valore del campo **URL di richiesta di autenticazione** dal portale di classica Azure.
    2.  Nella casella di testo **attributo che contiene il nome di accesso** digitare **NameID**.
    3.  Nella casella **Caricare il certificato SSL pubblico** , caricare il certificato scaricato dal portale di classica Azure.
    4.  Selezionare **Abilita il Provisioning automatico SAML**.

10. Nella scheda **Configurare l'autenticazione utente** , procedere come segue:

    ![Configurare l'autenticazione utente] (./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configurare l'autenticazione utente")

    1.  Fare clic su **Salva**.
    2.  Fare clic su **Attiva ora**.

11. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configurare il single sign-on")

##<a name="configuring-proxy-settings"></a>Configurazione delle impostazioni del proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni del proxy in Internet Explorer

1.  Avviare **Internet Explorer**.

2.  Scegliere **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet** .

    ![Opzioni Internet] (./media/active-directory-saas-zscaler-tutorial/IC769492.png "Opzioni Internet")

3.  Fare clic sulla scheda **connessioni** .

    ![Connessioni] (./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connessioni")

4.  Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN** .

5.  Nella sezione server Proxy, procedere come segue:

    ![Server proxy] (./media/active-directory-saas-zscaler-tutorial/IC769494.png "Server proxy")

    1.  Selezionare Usa un server proxy per la rete LAN.
    2.  Nella casella di testo indirizzo, digitare **gateway.zscalertwo.net**.
    3.  Nella casella di testo porta digitare **80**.
    4.  Selezionare **Ignora server proxy per indirizzi locali**.
    5.  Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)** .

6.  Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet** .

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Zscaler, è necessario essere effettuato il provisioning in Zscaler.  
In caso di Zscaler, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al tenant di **Zscaler** .

2.  Fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-zscaler-tutorial/IC781035.png "Amministrazione")

3.  Fare clic su **gestione degli utenti**.

    ![Gestione degli utenti] (./media/active-directory-saas-zscaler-tutorial/IC781036.png "Gestione degli utenti")

4.  Nella scheda **utenti** , fare clic su **Aggiungi**.

    ![Aggiungere] (./media/active-directory-saas-zscaler-tutorial/IC781037.png "Aggiungere")

5.  Nella sezione Aggiungi utente, procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-zscaler-tutorial/IC781038.png "Aggiunta utente")

    1.  Digitare **l'ID utente**, **Nome utente**, **Password**, la **Conferma Password**e quindi selezionare il **reparto** di un account valido AAD che si desidera eseguire il provisioning e **gruppi** .
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Zscaler utente account strumento per la creazione o API fornite dal Zscaler agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Per assegnare gli utenti a Zscaler, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Zscaler** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-zscaler-tutorial/IC769495.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-zscaler-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
