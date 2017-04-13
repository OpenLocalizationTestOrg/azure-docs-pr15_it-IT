<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ITRP | Microsoft Azure" 
    description="Ecco come utilizzare ITRP con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-itrp"></a>Esercitazione: Integrazione di Azure Active Directory con ITRP
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e ITRP.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant ITRP
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a ITRP sarà possibile eseguire l'accesso singolo all'applicazione del sito di società ITRP (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per ITRP
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-itrp-tutorial/IC775551.png "Scenario:")
##<a name="enabling-the-application-integration-for-itrp"></a>Attivare l'integrazione delle applicazioni per ITRP
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per ITRP.

###<a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per ITRP, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-itrp-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-itrp-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-itrp-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **ITRP**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-itrp-tutorial/IC775565.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **ITRP**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a ITRP con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per ITRP è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **ITRP** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-itrp-tutorial/IC771709.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al ITRP** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **ITRP URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. ITRP.com*", quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in ITRP** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\ITRP.cer**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società ITRP come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Impostazioni**.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  Nel riquadro di spostamento sinistra selezionare **Single Sign-On**.

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8.  Nella sezione di configurazione Single Sign-On, procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")

    1.  Fare clic su **Attiva**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in ITRP** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione remoto** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in ITRP** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **URL Single Sign-on SAML** .
    4.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale di certificato** .
        
        >[AZURE.TIP]Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    5.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a ITRP, è necessario essere effettuato il provisioning in ITRP.  
In caso di ITRP, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **ITRP** .

2.  Nella barra degli strumenti nella parte superiore, fare clic su **record**.

    ![Amministratore] (./media/active-directory-saas-itrp-tutorial/IC775575.png "Amministratore")

3.  Dal menu a comparsa, selezionare **persone**.

    ![Persone] (./media/active-directory-saas-itrp-tutorial/IC775587.png "Persone")

4.  Fare clic su **Aggiungi nuova persona** ("+").

    ![Amministratore] (./media/active-directory-saas-itrp-tutorial/IC775576.png "Amministratore")

5.  Nella finestra di dialogo Aggiungi nuovo utente, procedere come segue:

    ![Utente] (./media/active-directory-saas-itrp-tutorial/IC775577.png "Utente")

    1.  Digitare il **nome**, **messaggio di posta elettronica** di un account valido AAD che si desidera eseguire il provisioning.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro ITRP utente account strumenti di creazione o API fornite dall'ITRP agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Per assegnare gli utenti a ITRP, procedere come segue:

1.  Nel portale di Azure Active Directory, creare un account di prova.

2.  Nella pagina integrazione applicazione **ITRP **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-itrp-tutorial/IC775588.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-itrp-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).