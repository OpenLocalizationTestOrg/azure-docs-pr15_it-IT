<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Beta Zscaler | Microsoft Azure" 
    description="Informazioni su come utilizzare Zscaler Beta con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Esercitazione: Integrazione di Azure Active Directory con Zscaler Beta
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e ZScaler Beta.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un ZScaler Beta single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a ZScaler Beta sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di ZScaler Beta (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazione per la versione ZScaler Beta
2.  Configurare il single sign-on
3.  Configurazione delle impostazioni del proxy
4.  Configurare il provisioning dell'utente
5.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-zscaler-beta-tutorial/IC800223.png "Scenario:")

##<a name="enabling-the-application-integration-for-zscaler-beta"></a>Attivare l'integrazione di applicazione per la versione ZScaler Beta
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazione per la versione ZScaler Beta.

###<a name="to-enable-the-application-integration-for-zscaler-beta-perform-the-following-steps"></a>Per attivare l'integrazione di applicazione per la versione ZScaler Beta, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zscaler-beta-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-zscaler-beta-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-zscaler-beta-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-zscaler-beta-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **ZScaler Beta**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-zscaler-beta-tutorial/IC800224.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **ZScaler Beta**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![ZScaler uno] (./media/active-directory-saas-zscaler-beta-tutorial/IC800216.png "ZScaler uno")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a ZScaler Beta con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato codificato base 64 al tenant di ZScaler Beta.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **ZScaler Beta** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-zscaler-beta-tutorial/IC800225.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al ZScaler Beta** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-zscaler-beta-tutorial/IC800226.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **ZScaler Beta accesso su URL** digitare l'URL utilizzato da agli utenti di accesso all'applicazione ZScaler Beta e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-zscaler-beta-tutorial/IC800227.png "Configurare URL di App")

    >[AZURE.NOTE] Se necessario, è possibile ottenere il valore effettivo per l'ambiente dal team di supporto ZScaler Beta.

4.  Nella pagina **Configura il single sign-on in ZScaler Beta** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-zscaler-beta-tutorial/IC800228.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito della società ZScaler Beta.

6.  Nel menu nella parte superiore, fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-zscaler-beta-tutorial/IC800206.png "Amministrazione")

7.  In **Gestisci amministratori e i ruoli**, fare clic su **Gestisci utenti e l'autenticazione**.

    ![Gestire utenti e l'autenticazione] (./media/active-directory-saas-zscaler-beta-tutorial/IC800207.png "Gestire utenti e l'autenticazione")

8.  Nella sezione **Scegliere le opzioni di autenticazione per l'organizzazione** , procedere come segue:

    ![Autenticazione] (./media/active-directory-saas-zscaler-beta-tutorial/IC800208.png "Autenticazione")

    1.  Selezionare **autentica utilizzando SAML Single Sign-On**.
    2.  Fare clic su **Configura parametri SAML Single Sign-On**.

9.  Nella pagina di finestra di dialogo **Configurazione SAML Single Sign-On parametri** , eseguire le operazioni seguenti e quindi fare clic su **Chiudi**:

    ![Single Sign-On] (./media/active-directory-saas-zscaler-beta-tutorial/IC800209.png "Single Sign-On")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in ZScaler Beta** copiare il valore di **URL di richiesta di autenticazione** e quindi incollarla nella casella di testo **URL del portale di SAML al quale gli utenti vengono inviati per l'autenticazione** .
    2.  Nella casella di testo **attributo che contiene il nome di accesso** digitare **NameID**.
    3.  Per caricare il certificato scaricato, fare clic su **Zscaler pem**.
    4.  Selezionare **Abilita il Provisioning automatico SAML**.

10. Nella scheda **Configurare l'autenticazione utente** , procedere come segue:

    ![Amministrazione] (./media/active-directory-saas-zscaler-beta-tutorial/IC800210.png "Amministrazione")

    1.  Fare clic su **Salva**.
    2.  Fare clic su **Attiva ora**.

11. Nel portale di classica Azure, della pagina **Configura il single sign-on in ZScaler Beta** finestra di dialogo, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-zscaler-beta-tutorial/IC800229.png "Configurare Single Sign-On")

##<a name="configuring-proxy-settings"></a>Configurazione delle impostazioni del proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Per configurare le impostazioni del proxy in Internet Explorer

1.  Avviare **Internet Explorer**.

2.  Scegliere **Opzioni Internet** dal menu **Strumenti** per aprire la finestra di dialogo **Opzioni Internet** .

    ![Opzioni Internet] (./media/active-directory-saas-zscaler-beta-tutorial/IC769492.png "Opzioni Internet")

3.  Fare clic sulla scheda **connessioni** .

    ![Connessioni] (./media/active-directory-saas-zscaler-beta-tutorial/IC769493.png "Connessioni")

4.  Fare clic su **Impostazioni LAN** per aprire la finestra di dialogo **Impostazioni LAN** .

5.  Nella sezione server Proxy, procedere come segue:

    ![Server proxy] (./media/active-directory-saas-zscaler-beta-tutorial/IC769494.png "Server proxy")

    1.  Selezionare Usa un server proxy per la rete LAN.
    2.  Nella casella di testo indirizzo, digitare **gateway.zscalerBeta.net**.
    3.  Nella casella di testo porta digitare **80**.
    4.  Selezionare **Ignora server proxy per indirizzi locali**.
    5.  Fare clic su **OK** per chiudere la finestra di dialogo **Impostazioni rete locale (LAN)** .

6.  Fare clic su **OK** per chiudere la finestra di dialogo **Opzioni Internet** .

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a ZScaler Beta, è necessario essere effettuato il provisioning di ZScaler Beta.  
Nel caso dei ZScaler Beta, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al tenant di **Zscaler** .

2.  Fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-zscaler-beta-tutorial/IC781035.png "Amministrazione")

3.  Fare clic su **gestione degli utenti**.

    ![Aggiungere] (./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Aggiungere")

4.  Nella scheda **utenti** , fare clic su **Aggiungi**.

    ![Aggiungere] (./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Aggiungere")

5.  Nella sezione Aggiungi utente, procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-zscaler-beta-tutorial/IC781038.png "Aggiunta utente")

    1.  Digitare **l'ID utente**, **Nome utente**, **Password**, la **Conferma Password**e quindi selezionare il **reparto** di un account valido AAD che si desidera eseguire il provisioning e **gruppi** .
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro ZScaler Beta utente account strumenti di creazione o API fornite dal ZScaler Beta agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-zscaler-beta-perform-the-following-steps"></a>Per assegnare gli utenti a ZScaler Beta, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **ZScaler Beta** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-zscaler-beta-tutorial/IC800230.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-zscaler-beta-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).