<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Cisco Webex | Microsoft Azure" 
    description="Informazioni su come utilizzare Cisco Webex con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Esercitazione: Integrazione di Azure Active Directory con Cisco Webex

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Cisco Webex.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Cisco Webex

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Cisco Webex sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Cisco Webex (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Cisco Webex
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scenario:")
##<a name="enabling-the-application-integration-for-cisco-webex"></a>Attivare l'integrazione delle applicazioni per Cisco Webex

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Cisco Webex.

###<a name="to-enable-the-application-integration-for-cisco-webex-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Cisco Webex, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Cisco Webex**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Cisco Webex**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Cisco Webex] (./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Cisco Webex con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Cisco Webex** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Cisco Webex** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** , eseguire le operazioni seguenti e quindi fare clic su **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurare URL di app")

    1.  Nella casella di testo **Meglio su URL** digitare l'URL di tenant Cisco Webex (ad esempio: *http://contoso.webex.com*).
    2.  Nella casella di testo **Cisco Webex risposta URL** digitare l' **URL di Cisco Webex AssertionConsumerService** (ad esempio: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  Nella pagina **Configura il single sign-on in Cisco Webex** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito aziendale Cisco Webex come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Amministrazione del sito**.

    ![Amministrazione sito] (./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Amministrazione sito")

7.  Nella sezione **Gestisci sito** fare clic su **Configurazione Single Sign-on**.

    ![Configurazione di Single Sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configurazione di Single Sign-on")

8.  Nella sezione Configurazione di Single Sign-on Web federato, procedere come segue:

    ![Federato configurazione Single Sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federato configurazione Single Sign-on")

    1.  Dall'elenco **Protocollo federazione** selezionare **SAML 2.0**.
    2.  Creare un file **con codifica Base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    3.  Aprire il certificato codificato base 64 nel blocco note e quindi copiare il contenuto di esso.
    4.  Fare clic su **Importa SAML metadati**e quindi incollare il certificato codificato base 64.
    5.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Cisco Webex** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente per SAML (IdP ID)** .
    6.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Cisco Webex** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso del servizio Single Sign-on cliente** .
    7.  Nell'elenco **Formato NameID** selezionare **indirizzo di posta elettronica**.
    8.  Nella casella di testo **AuthnContextClassRef** digitare **Urn: oasis: nomi: tc: SAML:2.0:ac:classes:Password**.
    9.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Cisco Webex** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione del servizio di assistenza clienti Single Sign-on** .
    10. Fare clic su **Aggiorna**.

9.  Nel portale di classica Azure, della pagina **Configura il single sign-on in Cisco Webex** finestra di dialogo, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa**.

    ![Configurare il single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Cisco Webex, è necessario essere effettuato il provisioning in Cisco Webex.  
In caso di Cisco Webex il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Cisco Webex** .

2.  Passare a **gestire gli utenti \> aggiungere utente**.

    ![Aggiungere utenti] (./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Aggiungere utenti")

3.  Nella sezione Aggiungi utente, eseguire la procedura seguente:

    ![Aggiungi utente.] (./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Aggiungi utente.")

    1.  Come **Tipo di Account**, selezionare **Host**.
    2.  Digitare le informazioni di un utente di Azure Active Directory esistente nelle caselle di testo seguenti: **nome, cognome**, **nome utente**, **messaggio di posta elettronica**, **la Password**, **Conferma Password**.
    3.  Fare clic su **Aggiungi**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Cisco Webex utente account strumenti di creazione o API fornite dal Cisco Webex agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-cisco-webex-perform-the-following-steps"></a>Per assegnare gli utenti a Cisco Webex, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Cisco Webex **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
