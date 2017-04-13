<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con indicazioni su Microsoft | Microsoft Azure" 
    description="Informazioni su come utilizzare le indicazioni stradali su Microsoft con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Esercitazione: Integrazione di Azure Active Directory con indicazioni su Microsoft

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure Active Directory e indicazioni su Microsoft.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Informazioni su come sottoscrizione a Microsoft

Se non si dispone di un direzioni federati nell'abbonamento a Microsoft ancora, inviare tramite posta elettronica una richiesta di "*service@DirectionsOnMicrosoft.com*".

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a informazioni su Microsoft sarà possibile accedere solo nell'applicazione tramite il single sign-on.

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni per ottenere informazioni su Microsoft
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scenario:")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>Attivare l'integrazione di applicazioni per ottenere informazioni su Microsoft

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni per ottenere informazioni su Microsoft.

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni per ottenere informazioni su Microsoft, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **le indicazioni stradali su Microsoft**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **informazioni su Microsoft**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Scenario:] (./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scenario:")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione per ottenere informazioni su come Microsoft con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina **informazioni su Microsoft** applicazione integrazione fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Attivare Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Attivare Single Sign-On")

2.  Nella pagina **specificare come agli utenti di effettuare l'accesso a informazioni su Microsoft** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Microsoft Azure Active Directory Singel Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure Active Directory Singel Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo su URL di accesso, digitare **https://www.directionsonmicrosoft.com/user/login**e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in indicazioni su Microsoft** , fare clic su **Scarica i metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurare Single Sign-On")

5.  Inviare il file di metadati per le indicazioni del team di supporto Microsoft (*service@DirectionsOnMicrosoft.com*). Per abilitare le indicazioni del team di supporto Microsoft individuare l'appartenenza sito federato, includere le informazioni sulla società nella posta elettronica.

    >[AZURE.NOTE] Single sign-on per ottenere informazioni su come Microsoft deve essere abilitato per le indicazioni del team di supporto tecnico Microsoft.
Si riceverà una notifica quando il single sign-on è stato attivato.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Non esiste alcun elemento azione per la configurazione di provisioning per ottenere informazioni su come Microsoft dell'utente.  
Quando un utente assegnato tenta di accedere a informazioni su Microsoft mediante il pannello di accesso, informazioni su Microsoft verifica l'esistenza dell'utente. Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente per ottenere informazioni su come Microsoft.
##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>Per assegnare gli utenti per ottenere informazioni su come Microsoft, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina **informazioni su Microsoft **applicazione integrazione fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sì")
