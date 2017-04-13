<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con e Generatore | Microsoft Azure" 
    description="Informazioni su come utilizzare Generatore e con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Esercitazione: Integrazione di Azure Active Directory con Generatore di e
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure ed e generatore.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant e Generatore
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a generatore e sarà possibile eseguire l'accesso singolo all'applicazione del sito di società e Generatore (servizio provider avviato accesso), o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni e Generatore
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-e-builder-tutorial/IC777378.png "Scenario:")
##<a name="enabling-the-application-integration-for-e-builder"></a>Attivare l'integrazione di applicazioni e Generatore
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni e generatore.

###<a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni e generatore, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-e-builder-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-e-builder-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-e-builder-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **E Generatore**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-e-builder-tutorial/IC777379.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Generatore di e**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Generatore di e] (./media/active-directory-saas-e-builder-tutorial/IC777380.png "Generatore di e")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti autenticati al generatore e con il proprio account Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina di integrazione **E Generatore** applicazione, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al generatore e** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **e Generatore effettuarlo URL** digitare l'URL utilizzando il modello seguente "*https://\<tenant nome\>.e Builder.com*", quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configurare URL di app")

4.  Scegliere **Configura il single sign-on in Generatore di e** pagina per scaricare i metadati **Download dei metadati**e quindi il file di dati in locale come **c:\\BuilderMetaData.xml e**.

    ![Configurare il single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configurare il single sign-on")

5.  Inoltra del file di metadati al generatore e il team di supporto. Le esigenze del team di supporto Configura single sign-on per è.

6.  Selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning al generatore e dell'utente.  
Quando un utente assegnato tenta di accedere a mediante il pannello di accesso e del generatore, generatore e verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente dal generatore e.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Per assegnare gli utenti e generatore, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina di integrazione **E Generatore **applicazione, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-e-builder-tutorial/IC777386.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-e-builder-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
