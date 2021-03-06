<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Abintegro | Microsoft Azure" 
    description="Ecco come utilizzare Abintegro con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Esercitazione: Integrazione di Azure Active Directory con Abintegro

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Abintegro.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Abintegro single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Abintegro sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Abintegro (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Abintegro
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scenario:")
##<a name="enabling-the-application-integration-for-abintegro"></a>Attivare l'integrazione delle applicazioni per Abintegro

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Abintegro.

###<a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Abintegro, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-abintegro-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-abintegro-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **abintegro**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-abintegro-tutorial/IC790077.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Abintegro**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Abintegro] (./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Abintegro con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Abintegro** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare l'accesso singolo] (./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurare l'accesso singolo")

2.  Nella pagina **specificare come gli utenti di accedere al Abintegro** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare l'accesso singolo] (./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurare l'accesso singolo")

3.  Nella pagina **Configura App URL** nella casella di testo **Abintegro su URL di accesso** , digitare l'URL utilizzato dagli utenti per effettuare l'accesso a Abintegro (ad esempio: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Abintegro** fare clic su **Scarica metadati**e quindi salvare il file di metadati nel computer in uso.

    ![Configurare l'accesso singolo] (./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurare l'accesso singolo")

5.  Inviare la metadatafile al team di supporto Abintegro.

    >[AZURE.NOTE] La configurazione single sign-on deve essere eseguita dal team di supporto Abintegro. Si riceve una notifica non appena la configurazione è stata completata.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare l'accesso singolo] (./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurare l'accesso singolo")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Non esiste alcun elemento azione per la configurazione di provisioning a Abintegro dell'utente.  
Quando un utente assegnato tenta di accedere a Abintegro mediante il pannello di accesso, Abintegro verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da Abintegro.
##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Per assegnare gli utenti a Abintegro, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Abintegro **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-abintegro-tutorial/IC790084.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-abintegro-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
