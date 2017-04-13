<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Overdrive libri | Microsoft Azure" 
    description="Informazioni su come utilizzare il materiale Overdrive con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro!" 
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

#<a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Esercitazione: Integrazione di Azure Active Directory con Overdrive libri
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e OverDrive.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un OverDrive single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a OverDrive sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di OverDrive (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per OverDrive
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenario:")
##<a name="enabling-the-application-integration-for-overdrive"></a>Attivare l'integrazione delle applicazioni per OverDrive
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per OverDrive.

###<a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per OverDrive, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **OverDrive**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **OverDrive**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![OverDrive] (./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a OverDrive con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **OverDrive** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Attivare single sign-on] (./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Attivare single sign-on")

2.  Nella pagina **da come si desidera agli utenti di accedere al OverDrive** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **OverDrive URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://mslibrarytest.libraryreserve.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurare URL di App")

4.  Della pagina **Configura il single sign-on in OverDrive** , scaricare il file di metadati e inviarlo al team di supporto OverDrive.

    ![Configurare il single sign-on] (./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configurare il single sign-on")

    >[AZURE.NOTE]Il team di supporto OverDrive Configura single sign-on per si e invia una notifica quando è stata completata la configurazione.

5.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione per OverDrive il provisioning dell'utente.  
Quando un utente assegnato tenta di accedere a OverDrive, viene creato un account OverDrive automaticamente se necessario.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro OverDrive utente account strumenti di creazione o API fornite dall'OverDrive agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>Per assegnare gli utenti a OverDrive, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **OverDrive **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).