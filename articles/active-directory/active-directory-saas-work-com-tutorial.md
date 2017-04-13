<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Work.com | Microsoft Azure" 
    description="Informazioni su come utilizzare Work.com con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-workcom"></a>Esercitazione: Integrazione di Azure Active Directory con Work.com
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Work.com.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Work.com single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti AAD a cui dispone di assegnare Work.com accesso sarà grado di single sign nell'applicazione del sito di società di Work.com (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Work.com
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario:")

##<a name="enabling-the-application-integration-for-workcom"></a>Attivare l'integrazione delle applicazioni per Work.com
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Work.com.

###<a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Work.com, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-work-com-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-work-com-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-work-com-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Work.com**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-work-com-tutorial/IC794106.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Work.com**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Work.com] (./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Work.com con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato Work.com.com.

>[AZURE.NOTE] Per configurare il single sign-on, è necessario configurare un nome di dominio personalizzato Work.com ancora. È necessario definire almeno un nome di dominio, verificare il nome di dominio e distribuire in tutta l'organizzazione.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Accedere al tenant di Work.com come amministratore.

2.  Passare alla **configurazione**.

    ![Programma di installazione] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programma di installazione")

3.  Nel riquadro di spostamento sinistra, nella sezione **amministrazione** fare clic su **Gestione dei domini** per espandere la sezione correlata e quindi fare clic su un **Dominio personale** per aprire la pagina **My Domain** . 

    ![Il dominio personalizzato] (./media/active-directory-saas-work-com-tutorial/IC767825.png "Il dominio personalizzato")

4.  Per verificare che il dominio è stata impostata correttamente, verificare che sia in "**Passaggio 4 distribuito agli utenti**" e rivedere le "**Impostazioni personali dominio**".

    ![Distribuzione di più domini a utente] (./media/active-directory-saas-work-com-tutorial/IC784377.png "Distribuzione di più domini a utente")

5.  In una finestra del browser web diverso, accedere al portale classico Azure.

6.  Nella pagina integrazione applicazione **Work.com **, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurare Single Sign-On")

7.  Nella pagina **specificare come gli utenti di accedere al Work.com** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurare Single Sign-On")

8.  Nella pagina **Configura App URL** nella casella di testo **Work.com su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Work.com (ad esempio: " *http://company.my.salesforce.com*"), quindi fare clic su **Avanti**: 

    ![Configurare URL di App] (./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurare URL di App")

9.  Nella pagina **Configura il single sign-on in Work.com** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurare Single Sign-On")

10. Accedere al tenant di Work.com.

11. Passare alla **configurazione**.

    ![Programma di installazione] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programma di installazione")

12. Espandere il menu **Dei controlli di protezione** e quindi fare clic su **Impostazioni servizio Single Sign-On**.

    ![Impostazioni servizio Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794113.png "Impostazioni servizio Single Sign-On")

13. Nella pagina di finestra di dialogo **Impostazioni servizio Single Sign-On** , procedere come segue:

    ![SAML abilitata] (./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML abilitata")

    1.  Selezionare **SAML abilitato**.
    2.  Fare clic su **Nuovo**.

14. Nella sezione **Impostazioni SAML Single Sign-On** , procedere come segue:

    ![Impostazione SAML Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794114.png "Impostazione SAML Single Sign-On")

    1.  Nella casella di testo **nome** digitare un nome per la configurazione.  

        >[AZURE.NOTE] Fornire un valore per **nome** popolare automaticamente il **Nome dell'API** di testo.

    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Work.com** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente** .
    3.  Per caricare il certificato scaricato, fare clic su **Sfoglia**.
    4.  Nella casella di testo **Id entità** digitare **https://salesforce-work.com**.
    5.  Come **Tipo di identità SAML**, selezionare **asserzione contiene l'ID di federazione dall'oggetto utente**.
    6.  Come **Percorso di identità SAML**, selezionare **identità si trova nell'elemento NameIdentfier dell'istruzione oggetto**.
    7.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Work.com** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso del Provider di identità** .
    8.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Work.com** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione del Provider di identità** .
    9.  Come **Servizio Provider avviata richiedere l'associazione**, selezionare **HTTP Post**.
    10. Fare clic su **Salva**.

15. Nel portale classico Work.com, nel riquadro di spostamento sinistra, fare clic su **Gestione dei domini** per espandere la sezione correlata e quindi fare clic su un **Dominio personale** per aprire la pagina **My Domain** . 

    ![Il dominio personalizzato] (./media/active-directory-saas-work-com-tutorial/IC794115.png "Il dominio personalizzato")

16. Nella pagina **Dominio personale** , nella sezione **Accesso pagina Personalizzazione** fare clic su **Modifica**.

    ![Pagina di accesso personalizzazione] (./media/active-directory-saas-work-com-tutorial/IC767826.png "Pagina di accesso personalizzazione")

17. Nella pagina **Personalizzazione pagina di accesso** , nella sezione **Servizio di autenticazione** , viene visualizzato il nome delle **Impostazioni di Single Sign-on SAML** . Selezionarla e quindi fare clic su **Salva**.

    ![Pagina di accesso personalizzazione] (./media/active-directory-saas-work-com-tutorial/IC784366.png "Pagina di accesso personalizzazione")

18. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per gli utenti di Azure Active Directory per poter accedere, è necessario essere effettuato il provisioning di Work.com.  
In caso di Work.com, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale Work.com come amministratore.

2.  Passare alla **configurazione**.

    ![Programma di installazione] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programma di installazione")

3.  Passare a **gestire gli utenti \> utenti**.

    ![Gestire gli utenti] (./media/active-directory-saas-work-com-tutorial/IC784369.png "Gestire gli utenti")

4.  Fare clic su **nuovo utente**.

    ![Tutti gli utenti] (./media/active-directory-saas-work-com-tutorial/IC794117.png "Tutti gli utenti")

5.  Nella sezione Modifica utente, procedere come segue:

    ![L'utente modificare] (./media/active-directory-saas-work-com-tutorial/IC794118.png "L'utente modificare")

    1.  Digitare **Il cognome**, **Alias**, **posta elettronica**, **nome utente** e gli attributi di **nome alternativo** di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Selezionare **ruolo**, **licenza utente** e il **profilo**.
    3.  Fare clic su **Salva**.  

        >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica incluso un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Work.com utente account strumenti di creazione o API fornite dal Work.com agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Per assegnare gli utenti a Work.com, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione Work.com, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-work-com-tutorial/IC794119.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-work-com-tutorial/IC767830.png "Sì")
  
È ora necessario attendere 10 minuti e verificare che l'account è stata sincronizzata con Work.com.com.
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).