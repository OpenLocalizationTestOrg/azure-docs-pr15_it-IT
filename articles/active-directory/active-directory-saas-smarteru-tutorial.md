<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con SmarterU | Microsoft Azure" 
    description="Ecco come utilizzare SmarterU con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Esercitazione: Integrazione di Azure Active Directory con SmarterU
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e SmarterU.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant SmarterU
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a SmarterU sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di SmarterU (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per SmarterU
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scenario:")

##<a name="enabling-the-application-integration-for-smarteru"></a>Attivare l'integrazione delle applicazioni per SmarterU
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per SmarterU.

###<a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per SmarterU, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-smarteru-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-smarteru-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **SmarterU**.

    ![Fallery applicazione] (./media/active-directory-saas-smarteru-tutorial/IC777321.png "Fallery applicazione")

7.  Nel riquadro dei risultati, selezionare **SmarterU**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![SmarterU] (./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a SmarterU con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **SmarterU** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al SmarterU** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurare Single Sign-On")

3.  Scegliere **Configura il single sign-on in SmarterU** pagina per scaricare i metadati **Download dei metadati**e quindi il file di dati in locale come **c:\\SmarterUMetaData.cer**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurare Single Sign-On")

4.  In una finestra del browser web diverso, accedere a sito della società SmarterU come amministratore.

5.  Nella barra degli strumenti nella parte superiore, fare clic su **Impostazioni Account**.

    ![Impostazioni account] (./media/active-directory-saas-smarteru-tutorial/IC777326.png "Impostazioni account")

6.  Nella pagina di configurazione dell'account, eseguire la procedura seguente:

    ![Autorizzazione esterni] (./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorizzazione esterni")

    1.  Selezionare **Abilita autorizzazione esterni**.
    2.  Nella sezione **Controllo Login Master** fare clic sulla scheda **SmarterU** .
    3.  Nella sezione **Accesso utente predefinito** , selezionare la scheda **SmarterU** .
    4.  Selezionare **Abilita Okta**.
    5.  Copiare il contenuto del file di metadati scaricato e quindi incollarla nella casella di testo **Okta metadati** .
    6.  Fare clic su **Salva**.

7.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a SmarterU, è necessario essere effettuato il provisioning in SmarterU.  
In caso di SmarterU, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **SmarterU** .

2.  Accedi a **utenti**.

3.  Nella sezione utenti, procedere come segue:

    ![I nuovi utenti] (./media/active-directory-saas-smarteru-tutorial/IC777329.png "I nuovi utenti")

    1.  Fare clic su **+ utente**.
    2.  Digitare i valori correlati attributo dell'account utente di Azure Active Directory nelle caselle di testo seguenti: **posta elettronica principale**, **ID dipendente**, **Password**, **verificare la Password**, **assegnato nome**, **Cognome**.
    3.  Fare clic su **Attiva**.
    4.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro SmarterU utente account strumenti di creazione o API fornite dal SmarterU agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Per assegnare gli utenti a SmarterU, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **SmarterU **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-smarteru-tutorial/IC777330.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).