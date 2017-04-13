<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Sciforma | Microsoft Azure" 
    description="Ecco come utilizzare Sciforma con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-ad-integration-with-sciforma"></a>Esercitazione: Azure e integrazione con Sciforma
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Sciforma.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Sciforma
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Sciforma sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Sciforma (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Sciforma
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario:")
##<a name="enabling-the-application-integration-for-sciforma"></a>Attivare l'integrazione delle applicazioni per Sciforma
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Sciforma.

###<a name="to-enable-the-application-integration-for-sciforma-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Sciforma, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-sciforma-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-sciforma-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Sciforma**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-sciforma-tutorial/IC777370.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Sciforma**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Sciforma] (./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Sciforma con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Sciforma** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Sciforma** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Sciforma URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. Sciforma.com*", quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configurare URL di app")

4.  Scegliere **Configura il single sign-on in Sciforma** pagina per scaricare i metadati **Download dei metadati**e quindi il file di dati in locale come **c:\\SciformaMetaData.xml**.

    ![Configurare il single sign-on] (./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configurare il single sign-on")

5.  Inoltra del file di metadati a Sciforma il team di supporto. Le esigenze del team di supporto Configura single sign-on per è.

6.  Selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning a Sciforma dell'utente.  
Quando un utente assegnato tenta di accedere a Sciforma mediante il pannello di accesso, Sciforma verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da Sciforma.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-sciforma-perform-the-following-steps"></a>Per assegnare gli utenti a Sciforma, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Sciforma **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-sciforma-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).