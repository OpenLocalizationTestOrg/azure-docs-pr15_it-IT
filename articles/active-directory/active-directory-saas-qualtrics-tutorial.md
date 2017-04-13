<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Qualtrics | Microsoft Azure" 
    description="Ecco come utilizzare Qualtrics con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Esercitazione: Integrazione di Azure Active Directory con Qualtrics
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Qualtrics.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Qualtrics single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Qualtrics sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Qualtrics (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Qualtrics
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scenario:")
##<a name="enabling-the-application-integration-for-qualtrics"></a>Attivare l'integrazione delle applicazioni per Qualtrics
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Qualtrics.

###<a name="to-enable-the-application-integration-for-qualtrics-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Qualtrics, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Qualtrics**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Qualtrics**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Qualtrics] (./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Qualtrics con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Qualtrics** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Qualtrics** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** digitare l'URL nella casella di testo **Qualtrics su URL di accesso** (ad esempio: "*https://ssotest2ut1.qualtrics.com*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Qualtrics** fare clic su **Scarica metadati**e quindi salvare il file di metadati nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configurare Single Sign-On")

5.  Inviare la metadatafile al team di supporto Qualtrics.

    >[AZURE.NOTE]La configurazione single sign-on deve essere eseguita dal team di supporto Qualtrics. Si riceve una notifica non appena la configurazione è stata completata.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning a Qualtrics dell'utente.  
Quando un utente assegnato tenta di accedere a Qualtrics mediante il pannello di accesso, Qualtrics verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da Qualtrics.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-qualtrics-perform-the-following-steps"></a>Per assegnare gli utenti a Qualtrics, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Qualtrics **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).