<properties 
    pageTitle="Esercitazione: Azure Active Directory integrazione integrazione SugarCRM | Microsoft Azure" 
    description="Ecco come utilizzare SugarCRM con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>Esercitazione: Azure Active Directory integrazione integrazione SugarCRM
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e zucchero CRM.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un CRM zucchero single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a CRM zucchero sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di zucchero CRM (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni per zucchero CRM
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scenario:")

##<a name="enabling-the-application-integration-for-sugar-crm"></a>Attivare l'integrazione di applicazioni per zucchero CRM
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni per zucchero CRM.

###<a name="to-enable-the-application-integration-for-sugar-crm-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni per zucchero CRM, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Zucchero CRM**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Zucchero CRM**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Zucchero CRM] (./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Zucchero CRM")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a zucchero CRM con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato codificato base 64 al tenant di zucchero CRM.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Zucchero CRM** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come agli utenti di accedere a CRM zucchero** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Zucchero CRM accesso su URL** digitare l'URL utilizzato da agli utenti di accesso all'applicazione CRM zucchero (ad esempio: "*http://company.sugarondemand.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in CRM zucchero** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito aziendale zucchero CRM come amministratore.

6.  Passare a **amministratore**.

    ![Amministratore] (./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Amministratore")

7.  Nella sezione **amministrazione** fare clic su **Gestione delle Password**.

    ![Amministrazione] (./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Amministrazione")

8.  Selezionare **Abilita autenticazione SAML**.

    ![Amministrazione] (./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Amministrazione")

9.  Nella sezione **Autenticazione SAML** , procedere come segue:

    ![Autenticazione SAML] (./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "Autenticazione SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in CRM zucchero** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in CRM zucchero** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL OSU** .
    3.  Creare un file **con codifica Base 64** del certificato scaricata.

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollare l'intero certificato **X. 509** casella di testo.
    5.  Fare clic su **Salva**.

10. Nel portale di classica Azure, della pagina **Configura il single sign-on in CRM zucchero** finestra di dialogo, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a CRM zucchero, è necessario essere effettuato il provisioning a zucchero CRM.  
In caso di zucchero CRM, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Zucchero CRM** come amministratore.

2.  Passare a **amministratore**.

    ![Amministratore] (./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Amministratore")

3.  Nella sezione **amministrazione** fare clic su **Gestione degli utenti**.

    ![Amministrazione] (./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Amministrazione")

4.  Passare a **utenti \> creare nuovo utente**.

    ![Creare i nuovi utenti] (./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Creare i nuovi utenti")

5.  Nella scheda **Profilo utente** , procedere come segue:

    ![I nuovi utenti] (./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "I nuovi utenti")

    1.  Digitare il nome utente, l'ultimo nome e indirizzo e-mail di un utente di Azure Active Directory valido nelle caselle di testo correlati.

6.  Come **stato**, selezionare **Attiva**.

7.  Nella scheda Password, procedere come segue:

    ![I nuovi utenti] (./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "I nuovi utenti")

    1.  Digitare la password nella casella di testo correlato.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro zucchero CRM utente Creazione strumenti account o API fornite da zucchero CRM agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-sugar-crm-perform-the-following-steps"></a>Per assegnare gli utenti a zucchero CRM, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Zucchero CRM** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).