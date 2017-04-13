<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Zoom | Microsoft Azure" 
    description="Informazioni su come usare lo Zoom con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-zoom"></a>Esercitazione: Integrazione di Azure Active Directory con Zoom
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Zoom e dettaglio Azure.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di Zoom
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Zoom sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Zoom (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md)
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per lo Zoom
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-zoom-tutorial/IC784693.png "Scenario:")

##<a name="enabling-the-application-integration-for-zoom"></a>Attivare l'integrazione delle applicazioni per lo Zoom
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per lo Zoom.

###<a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per lo Zoom, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-zoom-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-zoom-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-zoom-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Zoom**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-zoom-tutorial/IC784694.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Zoom**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Eseguire lo zoom] (./media/active-directory-saas-zoom-tutorial/IC784695.png "Eseguire lo zoom")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione per eseguire lo Zoom con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **eseguire lo Zoom** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784696.png "Configurare il single sign-on")

2.  Nella pagina **da come si desidera agli utenti di accedere al Zoom** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784697.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Zoom URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://company.zoom.us*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-zoom-tutorial/IC784698.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Zoom** , fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784699.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito della società Zoom.

6.  Fare clic sulla scheda **Single Sign-On** .

    ![Servizio Single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784700.png "Servizio Single sign-on")

7.  Fare clic sulla scheda **Controllo di sicurezza** e quindi passare a impostazioni **Single Sign-On** .

8.  Nella sezione Single Sign-On, procedere come segue:

    ![Servizio Single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784701.png "Servizio Single sign-on")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Zoom** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL della pagina di accesso** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Zoom** copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **URL della pagina disconnessione** .
    3.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **certificato di provider di identità**
    5.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Zoom** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente** .
    6.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784702.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Zoom, è necessario essere effettuato il provisioning in Zoom.  
In caso di Zoom, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Zoom** come amministratore.

2.  Fare clic sulla scheda **Gestione degli Account** e quindi fare clic su **Gestione degli utenti**.

3.  Nella sezione gestione degli utenti, fare clic su **aggiungere utenti**.

    ![Gestione degli utenti] (./media/active-directory-saas-zoom-tutorial/IC784703.png "Gestione degli utenti")

4.  Nella pagina **Aggiungi utenti** , procedere come segue:

    ![Aggiungere utenti] (./media/active-directory-saas-zoom-tutorial/IC784704.png "Aggiungere utenti")

    1.  Come **Tipo di utente**, selezionare **base**.
    2.  Nella casella di testo a **messaggi di posta elettronica** digitare l'indirizzo di posta elettronica di un account valido AAD che si desidera eseguire il provisioning.
    3.  Fare clic su **Aggiungi**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro utente account creazione strumenti di Zoom o API fornite dal Zoom effettuare il provisioning di AAD gli account utente.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Per assegnare gli utenti per eseguire lo Zoom, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **eseguire lo Zoom **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-zoom-tutorial/IC784705.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-zoom-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).