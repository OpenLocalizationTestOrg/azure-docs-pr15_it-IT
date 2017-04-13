<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con bambù HR | Microsoft Azure" 
    description="Informazioni su come utilizzare bambù HR con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Esercitazione: Integrazione di Azure Active Directory con bambù HR

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e BambooHR.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un BambooHR single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a BambooHR sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di BambooHR (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per BambooHR
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario:")
##<a name="enabling-the-application-integration-for-bamboohr"></a>Attivare l'integrazione delle applicazioni per BambooHR

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per BambooHR.

###<a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per BambooHR, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **BambooHR**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **BambooHR**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![BambooHR] (./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a BambooHR con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **BambooHR** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Scenario:] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scenario:")

2.  Nella pagina **specificare come gli utenti di accedere al BambooHR** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** nella casella di testo **BambooHR su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione BambooHR (ad esempio: https://company.bamboohr.com), quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in BambooHR** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società BambooHR come amministratore.

6.  Nella home page, procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Single Sign-On")

    1.  Fare clic su **App**.
    2.  Nel menu App sulla sinistra, fare clic su **Single Sign-On**.
    3.  Fare clic su **SAML Single Sign-On**.

7.  Nella sezione **SAML Single Sign-On** , procedere come segue:

    ![SAML Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in BambooHR** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL di accesso Single Sign-on** .
    2.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    3.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato x. 509**
    4.  Fare clic su **Salva**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a BambooHR, è necessario essere effettuato il provisioning in BambooHR.  
In caso di BambooHR, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito **BambooHR** come amministratore.

2.  Nella barra degli strumenti nella parte superiore, fare clic su **Impostazioni**.

    ![Impostazione] (./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Impostazione")

3.  Fare clic su **Panoramica**.

4.  Nel riquadro di spostamento sinistro, passare a **protezione \> utenti**.

5.  Digitare l'indirizzo utente nome, password e posta elettronica di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.

6.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro BambooHR utente account strumenti di creazione o API fornite dal BambooHR agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Per assegnare gli utenti a BambooHR, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **BambooHR **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
