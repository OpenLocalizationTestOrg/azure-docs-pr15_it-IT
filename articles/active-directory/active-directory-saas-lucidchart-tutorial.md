<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Lucidchart | Microsoft Azure" 
    description="Ecco come utilizzare Lucidchart con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Esercitazione: Integrazione di Azure Active Directory con Lucidchart
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Lucidchart.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Lucidchart single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Lucidchart sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Lucidchart (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Lucidchart
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario:")
##<a name="enabling-the-application-integration-for-lucidchart"></a>Attivare l'integrazione delle applicazioni per Lucidchart
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Lucidchart.

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Lucidchart, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Lucidchart**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Lucidchart**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Lucidchart] (./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Lucidchart con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Lucidchart** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Lucidchart** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Lucidchart su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Lucidchart (ad esempio: "*https://chart2.office.lucidchart.com/saml/sso/azure*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Lucidchart** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di dati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Lucidchart come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Team**.

    ![Team] (./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")

7.  Fare clic su **applicazione \> gestire SAML**.

    ![Gestire SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gestire SAML")

8.  Nella pagina di finestra di dialogo **Impostazioni di autenticazione SAML** , procedere come segue:

    1.  Selezionare **Abilita autenticazione SAML**e quindi fare clic su **facoltativo**.
        ![Impostazioni di autenticazione SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Impostazioni di autenticazione SAML")
    2.  Nella casella di testo **dominio** digitare il nome del dominio e quindi fare clic su **Cambia certificato**.
        ![Cambia certificato] (./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Cambia certificato")
    3.  Aprire un file di metadati scaricati, copiare il contenuto e quindi incollarla nella casella di testo **Caricare metadati** .
        ![Caricare i metadati] (./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Caricare i metadati")
    4.  Selezionare **Aggiungi automaticamente i nuovi utenti al team di**e quindi fare clic su **Salva modifiche**.
        ![Salvare le modifiche] (./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Salvare le modifiche")

9.  Selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning a Lucidchart dell'utente.  
Quando un utente assegnato tenta di accedere a Lucidchart mediante il pannello di accesso, Lucidchart verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da Lucidchart.
##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Per assegnare gli utenti a Lucidchart, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Lucidchart **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).