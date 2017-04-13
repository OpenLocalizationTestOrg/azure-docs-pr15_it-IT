<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory invio | Microsoft Azure" 
    description="Informazioni su come usare invio con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-envoy"></a>Esercitazione: Integrazione di Azure Active Directory invio
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e invio.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di invio
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a invio sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di invio (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per invio
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario:")
##<a name="enabling-the-application-integration-for-envoy"></a>Attivare l'integrazione delle applicazioni per invio
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per invio.

###<a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per invio, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-envoy-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-envoy-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-envoy-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **INVIO**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-envoy-tutorial/IC776760.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **INVIO**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![INVIO] (./media/active-directory-saas-envoy-tutorial/IC776777.png "INVIO")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione per invio con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per invio è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, informazioni [su come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **INVIO** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Attivare single sign-on] (./media/active-directory-saas-envoy-tutorial/IC776778.png "Attivare single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al invio** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-envoy-tutorial/IC776779.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **INVIO URL di accesso** , digitare l'URL utilizzando il modello seguente "https://*\<nome tenant\>. Envoy.com*", quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in invio** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\Envoy.cer**.

    ![Configurare il single sign-on] (./media/active-directory-saas-envoy-tutorial/IC776781.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito della società invio.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Impostazioni**.

    ![INVIO] (./media/active-directory-saas-envoy-tutorial/IC776782.png "INVIO")

7.  Fare clic su **società**.

    ![Società] (./media/active-directory-saas-envoy-tutorial/IC776783.png "Società")

8.  Fare clic su **SAML**.

    ![SAML] (./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  Nella sezione configurazione **Autenticazione SAML** , procedere come segue:

    ![Autenticazione SAML] (./media/active-directory-saas-envoy-tutorial/IC776785.png "Autenticazione SAML")

    >[AZURE.NOTE] Il valore per l'ID di posizione sede centrale viene generato dall'applicazione automaticamente.

    1.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale** .  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in invio** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **URL SAML HTTP del Provider di identità** .
    3.  Fare clic su **Salva modifiche**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-envoy-tutorial/IC776786.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning per l'invio dell'utente.  
Quando un utente assegnato tenta di accedere all'invio mediante il pannello di accesso, invio verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da INVIO.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Per assegnare gli utenti a invio, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **INVIO **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-envoy-tutorial/IC776787.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-envoy-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).