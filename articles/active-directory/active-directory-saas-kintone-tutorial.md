<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Kintone | Microsoft Azure" 
    description="Ecco come utilizzare Kintone con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-kintone"></a>Esercitazione: Integrazione di Azure Active Directory con Kintone
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Kintone.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Kintone single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Kintone sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Kintone (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Kintone
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario:")
##<a name="enabling-the-application-integration-for-kintone"></a>Attivare l'integrazione delle applicazioni per Kintone
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Kintone.

###<a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Kintone, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-kintone-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-kintone-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-kintone-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Kintone**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-kintone-tutorial/IC785867.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Kintone**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Kintone] (./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Kintone con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Kintone** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Kintone** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Kintone su URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://company.kintone.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Kintone** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società **Kintone** come amministratore.

6.  Fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Impostazioni")

7.  Fare clic su **utenti e amministrazione del sistema**.

    ![Utenti e amministrazione del sistema] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Utenti e amministrazione del sistema")

8.  In **amministrazione del sistema \> protezione** fare clic su **Login**.

    ![Account di accesso] (./media/active-directory-saas-kintone-tutorial/IC785881.png "Account di accesso")

9.  Fare clic su **Abilita SAML autenticazione**.

    ![Autenticazione SAML] (./media/active-directory-saas-kintone-tutorial/IC785882.png "Autenticazione SAML")

10. Nella sezione autenticazione SAML, procedere come segue:

    ![Autenticazione SAML] (./media/active-directory-saas-kintone-tutorial/IC785883.png "Autenticazione SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Kintone** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Kintone** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .
    3.  Fare clic su **Sfoglia** per caricare il certificato scaricato.
    4.  Fare clic su **Salva**.

11. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Kintone, è necessario essere effettuato il provisioning in Kintone.  
In caso di Kintone, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Kintone** come amministratore.

2.  Fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Impostazioni")

3.  Fare clic su **utenti e amministrazione del sistema**.

    ![Utente e amministrazione del sistema] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Utente e amministrazione del sistema")

4.  **Amministrazione degli utenti**fare clic su **utenti e reparti**.

    ![Reparto e gli utenti] (./media/active-directory-saas-kintone-tutorial/IC785888.png "Reparto e gli utenti")

5.  Fare clic su **nuovo utente**.

    ![Nuovi utenti] (./media/active-directory-saas-kintone-tutorial/IC785889.png "Nuovi utenti")

6.  Nella sezione **Nuovo utente** , procedere come segue:

    ![Nuovi utenti] (./media/active-directory-saas-kintone-tutorial/IC785890.png "Nuovi utenti")

    1.  Digitare un **Nome visualizzato**, **Nome di accesso**, **Nuova Password**, **Confermare la Password**, **Indirizzo di posta elettronica** e altri dettagli di un account valido AAD che si desidera eseguire il provisioning in texboxes correlati.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Kintone utente account strumenti di creazione o API fornite dal Kintone agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Per assegnare gli utenti a Kintone, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Kintone **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-kintone-tutorial/IC785891.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-kintone-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).