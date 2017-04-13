<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Boomi | Microsoft Azure" 
    description="Ecco come utilizzare Boomi con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-boomi"></a>Esercitazione: Integrazione di Azure Active Directory con Boomi

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Boomi.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Boomi single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Boomi sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Boomi (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Boomi
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario:")
##<a name="enabling-the-application-integration-for-boomi"></a>Attivare l'integrazione delle applicazioni per Boomi

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Boomi.

###<a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Boomi, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-boomi-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-boomi-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-boomi-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Boomi**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-boomi-tutorial/IC790822.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Boomi**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Boomi] (./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Boomi con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Boomi** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Boomi** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Boomi risposta URL** digitare l' **URL di accesso AtomSphere Boomi** (ad esempio: "*https://platform.boomi.com/sso/AccountName/saml*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Boomi** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Boomi come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic sul nome della società e **il programma di installazione**.

    ![Programma di installazione] (./media/active-directory-saas-boomi-tutorial/IC790828.png "Programma di installazione")

7.  Fare clic su **Opzioni di Single Sign-on**.

    ![Opzioni di Single Sign-on] (./media/active-directory-saas-boomi-tutorial/IC790829.png "Opzioni di Single Sign-on")

8.  Nella sezione **Opzioni di Single Sign-On** , procedere come segue:

    ![Opzioni di Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790830.png "Opzioni di Single Sign-On")

    1.  Selezionare **Abilita SAML Single Sign-On**.
    2.  Fare clic su **Importa**per caricare il certificato scaricato.
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Boomi** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso del Provider di identità** .
    4.  Come **Federazione Id percorso**, selezionare **Id federazione nell'elemento NameID dell'argomento**.
    5.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Boomi, è necessario essere effettuato il provisioning in Boomi.  
In caso di Boomi, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Boomi** come amministratore.

2.  Passare a **Gestione utenti \> utenti**.

    ![Utenti] (./media/active-directory-saas-boomi-tutorial/IC790831.png "Utenti")

3.  Fare clic su **Aggiungi utente**.

    ![Aggiunta utente] (./media/active-directory-saas-boomi-tutorial/IC790832.png "Aggiunta utente")

4.  Nella pagina di finestra di dialogo **Aggiungi ruoli utente** , procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-boomi-tutorial/IC790833.png "Aggiunta utente")

    1.  Digitare il **nome**, **Cognome** e **posta elettronica** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su OK.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Boomi utente account strumenti di creazione o API fornite dal Boomi agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Per assegnare gli utenti a Boomi, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Boomi **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-boomi-tutorial/IC790834.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-boomi-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
