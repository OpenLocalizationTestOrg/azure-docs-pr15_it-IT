<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Benefitsolver | Microsoft Azure"
    description="Ecco come utilizzare Benefitsolver con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Esercitazione: Integrazione di Azure Active Directory con Benefitsolver

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Benefitsolver.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Benefitsolver single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Benefitsolver sarà possibile accesso singolo nell'applicazione tramite l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Benefitsolver
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scenario:")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>Attivare l'integrazione delle applicazioni per Benefitsolver

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Benefitsolver.

###<a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Benefitsolver, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Benefitsolver**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Benefitsolver**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Benefitssolver] (./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Benefitsolver con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
L'applicazione Benefitsolver prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** .  
Schermata seguente è illustrato un esempio per l'oggetto.

![Attributi] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributi")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Benefitsolver** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Benefitsolver** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurare Single Sign-On")

3.  Nella pagina **Configura impostazioni di App** , procedere come segue:

    ![Configurare le impostazioni dell'App] (./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configurare le impostazioni dell'App")

    1.  Nella casella di testo **Su URL di accesso** , digitare **http://azure.benefitsolver.com**.
    2.  Nella casella di testo **URL di risposta** , digitare **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  


    3.  Fare clic su **Avanti**.

4.  Nella pagina **Configura il single sign-on in Benefitsolver** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurare Single Sign-On")

5.  Inviare il file di metadati scaricati al team di supporto Benefitsolver.

    >[AZURE.NOTE] Il team di supporto Benefitsolver deve eseguire la configurazione di Single Sign-on effettiva.
Si riceve una notifica quando Single Sign-on è stata attivata per l'abbonamento.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurare Single Sign-On")

7.  Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributi")

8.  Per aggiungere i mapping attributo obbligatorio, procedere come segue:

    ![Attributi] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributi")

  	|Nome attributo|Valore dell'attributo|
  	|---|---|
  	|ClientID|È necessario ottenere questo valore dal team di supporto Benefitsolver.|
  	|ClientKey|È necessario ottenere questo valore dal team di supporto Benefitsolver.|
  	|LogoutURL|È necessario ottenere questo valore dal team di supporto Benefitsolver.|
  	|Campo ID dipendente|È necessario ottenere questo valore dal team di supporto Benefitsolver.|

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.
    3.  Nella casella di testo **Valore dell'attributo** , selezionare il valore dell'attributo visualizzato per tale riga.
    4.  Fare clic su **completa**.

9.  Fare clic su **Applica modifiche**.

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Benefitsolver, è necessario essere effettuato il provisioning in Benefitsolver.  
In caso di Benefitsolver, i dati sui dipendenti sono nell'applicazione compilata tramite un file di censimento dal sistema HRIS (in genere notte).  

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Benefitsolver utente account strumenti di creazione o API fornite dal Benefitsolver agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Per assegnare gli utenti a Benefitsolver, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Benefitsolver **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
