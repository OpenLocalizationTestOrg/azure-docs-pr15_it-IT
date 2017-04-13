<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Azure" 
    description="Ecco come utilizzare Citrix ShareFile con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Citrix ShareFile.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Citrix ShareFile

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Citrix ShareFile sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Citrix ShareFile (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Citrix ShareFile
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario:")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>Attivare l'integrazione delle applicazioni per Citrix ShareFile

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Citrix ShareFile.

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Citrix ShareFile, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Citrix ShareFile**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Citrix ShareFile**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Citrix ShareFile] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Citrix ShareFile con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Citrix ShareFile** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Attivare single sign-on] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Attivare single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Citrix ShareFile** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** nella casella di testo **Citrix ShareFile accesso su URL** digitare l'URL utilizzando il seguente criterio `https://<tenant-name>.shareFile.com`e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Citrix ShareFile** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![ConfigureSingle Sign-On] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società **Citrix ShareFile** come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **amministratore**.

7.  Nel riquadro di spostamento sinistra, selezionare **Configura Single Sign-On**.

    ![Amministrazione dell'account] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Amministrazione dell'account")

8.  Nel **Single Sign-On / SAML 2.0 configurazione** finestra di dialogo in **Impostazioni di base**, eseguire le operazioni seguenti:

    ![Servizio Single sign-on] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "Servizio Single sign-on")

    1.  Fare clic su **Attiva SAML**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Citrix ShareFile** copiare il valore di **ID entità** e quindi incollarlo nel **Your IDP emittente / ID entità** casella di testo.
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Citrix ShareFile** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    4.  Nel portale di classica Azure, nella scheda **la configura il single sign-on in Citrix ShareFile** , copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .
    5.  Fare clic su **Cambia** accanto al campo **X. 509** e quindi caricare il certificato che è stato scaricato dal portale classico di Azure Active Directory.
        ![Impostazioni di base] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Impostazioni di base")

9.  Fare clic su **Salva** nel portale di gestione di Citrix ShareFile.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Citrix ShareFile, è necessario essere effettuato il provisioning in Citrix ShareFile.  
In caso di Citrix ShareFile, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Citrix ShareFile** .

2.  Fare clic su **gestire gli utenti \> Gestione utenti Home \> + creare dipendente**.

    ![Creare dipendente] (./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Creare dipendente")

3.  Immettere il **messaggio di posta elettronica**, **nome** e **Cognome** di un valido Azure AD account si desidera eseguire il provisioning.

    ![Informazioni di base] (./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Informazioni di base")

4.  Fare clic su **Aggiungi utente**.

    >[AZURE.NOTE] Il titolare di account AAD riceverà un messaggio di posta elettronica e visitare un collegamento per confermare il proprio account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Citrix ShareFile utente account strumenti di creazione o API fornite dal Citrix ShareFile agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Per assegnare gli utenti a Citrix ShareFile, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Citrix ShareFile **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
