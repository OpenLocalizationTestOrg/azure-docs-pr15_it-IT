<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con kudo | Microsoft Azure" 
    description="Ecco come utilizzare kudo con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-kudos"></a>Esercitazione: Integrazione di Azure Active Directory con kudo
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e kudo.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant kudo
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a kudo sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di kudo (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per kudo
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-kudos-tutorial/IC787799.png "Scenario:")
##<a name="enabling-the-application-integration-for-kudos"></a>Attivare l'integrazione delle applicazioni per kudo
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per kudo.

###<a name="to-enable-the-application-integration-for-kudos-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per kudo, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-kudos-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-kudos-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-kudos-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **kudo**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-kudos-tutorial/IC787800.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **kudo**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Kudo] (./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudo")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a kudo con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **kudo** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al kudo** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Kudo su URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://company.kudosnow.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in kudo** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere come amministratore in sito kudo della società.

6.  Nel menu nella parte superiore, fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Impostazioni")

7.  Fare clic su **integrazioni \> Single Sign-on**.

8.  Nella sezione **Single Sign-on** , procedere come segue:

    ![Single Sign-on] (./media/active-directory-saas-kudos-tutorial/IC787807.png "Single Sign-on")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in kudo** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL di accesso** .
    2.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP]
        Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    3.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **certificato x. 509**
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in kudo** copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarlo nella casella di testo **Disconnessione all'URL** .
    5.  Nella casella di testo **Your kudo URL** digitare il nome della società.
    6.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a kudo, è necessario essere effettuato il provisioning in kudo.  
Nel caso dei kudo, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **kudo** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Impostazioni")

3.  Fare clic su **utente amministratore**.

4.  Fare clic sulla scheda **utenti** e quindi fare clic su **Aggiungi un utente**.

    ![Amministratore utenti] (./media/active-directory-saas-kudos-tutorial/IC787809.png "Amministratore utenti")

5.  Nella sezione **aggiungere un utente** , procedere come segue:

    ![Aggiungere un utente] (./media/active-directory-saas-kudos-tutorial/IC787810.png "Aggiungere un utente")

    1.  Digitare il **nome**, **Cognome**, **posta elettronica** e altri dettagli di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Crea utente**.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro kudo utente account strumenti di creazione o API fornite dal kudo agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-kudos-perform-the-following-steps"></a>Per assegnare gli utenti a kudo, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **kudo **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-kudos-tutorial/IC787811.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-kudos-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).