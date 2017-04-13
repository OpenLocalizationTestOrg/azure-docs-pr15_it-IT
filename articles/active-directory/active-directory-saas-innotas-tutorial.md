<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Innotas | Microsoft Azure"
    description="Ecco come utilizzare Innotas con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-innotas"></a>Esercitazione: Integrazione di Azure Active Directory con Innotas
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Innotas.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Innotas
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Innotas sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Innotas (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Innotas
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-innotas-tutorial/IC777331.png "Scenario:")
##<a name="enabling-the-application-integration-for-innotas"></a>Attivare l'integrazione delle applicazioni per Innotas
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Innotas.

###<a name="to-enable-the-application-integration-for-innotas-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Innotas, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-innotas-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-innotas-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-innotas-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Innotas**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-innotas-tutorial/IC777332.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Innotas**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Innotas] (./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Innotas con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Innotas** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-innotas-tutorial/IC777334.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Innotas** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-innotas-tutorial/IC777335.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Innotas URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. Innotas.com*", quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurare URL di app")

4.  Scegliere **Configura il single sign-on in Innotas** pagina per scaricare i metadati **Download dei metadati**e quindi il file di dati in locale come **c:\\InnotasMetaData.xml**.

    ![Configurare il single sign-on] (./media/active-directory-saas-innotas-tutorial/IC777337.png "Configurare il single sign-on")

5.  Inoltra del file di metadati a Innotas il team di supporto. Le esigenze del team di supporto Configura single sign-on per è.

6.  Selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-innotas-tutorial/IC777338.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning a Innotas dell'utente.  
Quando un utente assegnato tenta di accedere a Innotas mediante il pannello di accesso, Innotas verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da Innotas.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-innotas-perform-the-following-steps"></a>Per assegnare gli utenti a Innotas, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Innotas **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-innotas-tutorial/IC777339.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-innotas-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).