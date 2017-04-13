<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con AppDynamics | Microsoft Azure" 
    description="Ecco come utilizzare AppDynamics con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Esercitazione: Integrazione di Azure Active Directory con AppDynamics

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e AppDynamics. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un AppDynamics single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a AppDynamics sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di AppDynamics (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per AppDynamics
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario:")
##<a name="enabling-the-application-integration-for-appdynamics"></a>Attivare l'integrazione delle applicazioni per AppDynamics

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per AppDynamics.

###<a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per AppDynamics, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **AppDynamics**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **AppDynamics**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![AppDynamics] (./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a AppDynamics con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **AppDynamics** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare l'accesso singolo] (./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurare l'accesso singolo")

2.  Nella pagina **specificare come gli utenti di accedere al AppDynamics** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare l'accesso singolo] (./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurare l'accesso singolo")

3.  Nella pagina **Configura App URL** nella casella di testo **AppDynamics su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso a AppDynamics ("*https://companyname.saas.appdynamics.com*") e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in AppDynamics** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare l'accesso singolo] (./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurare l'accesso singolo")

5.  In una finestra del browser web diverso, accedere a sito della società AppDynamics come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Impostazioni**e quindi fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Amministrazione")

7.  Fare clic sulla scheda **Provider di autenticazione** .

    ![Provider di autenticazione] (./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Provider di autenticazione")

8.  Nella sezione **Provider di autenticazione** , procedere come segue:

    ![Configurazione di SAML] (./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configurazione di SAML")

    1.  Come **Provider di autenticazione**, selezionare **SAML**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in AppDynamics** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in AppDynamics** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .
    4.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    5.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **certificato**
    6.  Fare clic su **Salva**.
        ![Salva] (./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Salva")

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare l'accesso singolo] (./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurare l'accesso singolo")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a AppDynamics, è necessario essere effettuato il provisioning in AppDynamics.  
In caso di AppDynamics, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere come amministratore sito AppDynamics della società.

2.  Accedi a **utenti**e quindi fare clic su **+** per aprire la finestra di dialogo **Crea utente** .

    ![Utenti] (./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Utenti")

3.  Nella sezione **Crea utente** , procedere come segue:

    ![Creare utente] (./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Creare utente")

    1.  Digitare il **nome utente**, **nome** **posta elettronica**, **Nuova Password**, **Ripeti nuova Password** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro AppDynamics utente account strumenti di creazione o API fornite dall'AppDynamics provisioning degli account utente di Azure Active Directory.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Per assegnare gli utenti a AppDynamics, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **AppDynamics **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
