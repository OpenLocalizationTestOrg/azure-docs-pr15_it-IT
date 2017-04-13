<properties 
    pageTitle="Esercitazione: Azure Active Directory integrazione Aha! | Microsoft Azure" 
    description="Informazioni su come utilizzare Aha! con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>Esercitazione: Azure Active Directory integrazione Aha!

L'obiettivo di questa esercitazione consiste nel visualizzare l'integrazione di Azure e Aha.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Aha! Single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory è stato assegnato a Aha! saranno in grado di eseguire l'accesso singolo all'applicazione il Aha! sito aziendale (segno di provider avviato servizio su) oppure usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Per consentire l'integrazione delle applicazioni per Aha.
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-aha-tutorial/IC798944.png "Scenario:")
##<a name="enabling-the-application-integration-for-aha"></a>Per consentire l'integrazione delle applicazioni per Aha.

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Aha!.

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Aha!, eseguire le operazioni seguenti:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-aha-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-aha-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-aha-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Aha!**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-aha-tutorial/IC798945.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Aha!**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Aha!] (./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Aha! con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di Azure classico sul **Aha!** integrazione di applicazioni della pagina, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798946.png "Configurare Single Sign-On")

2.  Nel **specificare come gli utenti di accedere al Aha!** pagina, selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798947.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** in **Aha! Accesso su URL** casella di testo, digitare l'URL utilizzato dagli utenti per sign-on per il Aha! Applicazione (ad esempio: "*https://company.aha.io/session/new*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-aha-tutorial/IC798948.png "Configurare URL di App")

4.  Nel **configurare il single sign-on Aha!** pagina per scaricare il file di metadati, fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798949.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere al Aha! sito della società come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-aha-tutorial/IC798950.png "Impostazioni")

7.  Fare clic su **Account**.

    ![Profilo] (./media/active-directory-saas-aha-tutorial/IC798951.png "Profilo")

8.  Fare clic su **sicurezza e single sign-on**.

    ![Sicurezza e single sign-on] (./media/active-directory-saas-aha-tutorial/IC798952.png "Sicurezza e single sign-on")

9.  Nella sezione **Single Sign-On** , come **Provider di identità**, selezionare **SAML2.0**.

    ![Sicurezza e single sign-on] (./media/active-directory-saas-aha-tutorial/IC798953.png "Sicurezza e single sign-on")

10. Nella pagina configurazione **Single Sign-On** procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")

    1.  Nella casella di testo **nome** digitare un nome per la configurazione.
    2.  Per **configurare con**, selezionare **File di metadati**.
    3.  Per caricare il file di metadati scaricato, fare clic su **Sfoglia**.
    4.  Fare clic su **Aggiorna**.

11. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798955.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Aha!, è necessario effettuato il provisioning in Aha!.  
Nel caso dei Aha!, il provisioning è un'attività automatica.  
Non esiste alcun elemento azione dell'utente.
  
Gli utenti vengono creati automaticamente se necessario, durante il primo single sign-on tentativo.

>[AZURE.NOTE] È possibile usare altri Aha! strumenti di creazione di account utente o API fornite dall'Aha! Per eseguire il provisioning degli account utente AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>Per assegnare gli utenti a Aha!, eseguire le operazioni seguenti:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nel **Aha!** integrazione di applicazioni pagina fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-aha-tutorial/IC798956.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-aha-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
