<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Jobscience | Microsoft Azure" 
    description="Ecco come utilizzare Jobscience con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Esercitazione: Integrazione di Azure Active Directory con Jobscience
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Jobscience.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Una sottoscrizione Jobscience Single Sign-On abilitato
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Jobscience sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Jobscience (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Jobscience
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario:")
##<a name="enabling-the-application-integration-for-jobscience"></a>Attivare l'integrazione delle applicazioni per Jobscience
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Jobscience.

###<a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Jobscience, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-jobscience-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-jobscience-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **jobscience**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-jobscience-tutorial/IC784342.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Jobscience**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Jobscience] (./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Jobscience con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Jobscience è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Accedere al sito aziendale Jobscience come amministratore.

2.  Passare alla **configurazione**.

    ![Programma di installazione] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programma di installazione")

3.  Nel riquadro di spostamento sinistra, nella sezione **amministrazione** fare clic su **Gestione dei domini** per espandere la sezione correlata e quindi fare clic su un **Dominio personale** per aprire la pagina **My Domain** . 

    ![Il dominio personalizzato] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Il dominio personalizzato")

4.  Per verificare che il dominio è stata impostata correttamente, verificare che sia in "**Passaggio 4 distribuito agli utenti**" e rivedere le "**Impostazioni personali dominio**".

    ![Distribuzione di più domini a utente] (./media/active-directory-saas-jobscience-tutorial/IC784377.png "Distribuzione di più domini a utente")

5.  In una finestra del browser web diverso, accedere al portale classico Azure.

6.  Nella pagina integrazione applicazione **Jobscience** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurare Single Sign-On")

7.  Nella pagina **specificare come gli utenti di accedere al Jobscience** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurare Single Sign-On")

8.  Nella pagina **Configura App URL** nella casella di testo **Jobscience URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://company.my.salesforce.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurare URL di App")

9.  Nella pagina **Configura il single sign-on in Jobscience** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurare Single Sign-On")

10. Nel sito della società Jobscience fare clic su **Controlli di protezione**e quindi fare clic su **Impostazioni servizio Single Sign-On**.

    ![Controlli di protezione] (./media/active-directory-saas-jobscience-tutorial/IC784364.png "Controlli di protezione")

11. Nella sezione **Impostazioni servizio Single Sign-On** , procedere come segue:

    ![Impostazioni servizio Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC781026.png "Impostazioni servizio Single Sign-On")

    1.  Selezionare **SAML abilitato**.
    2.  Fare clic su **Nuovo**.

12. Nella finestra di dialogo **SAML Single Sign-On impostazione modificare** , procedere come segue:

    ![Impostazione SAML Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784365.png "Impostazione SAML Single Sign-On")

    1.  Nella casella di testo **nome** digitare un nome per la configurazione.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Jobscience** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente**
    3.  Nella casella di testo **Id entità** digitare **https://salesforce-jobscience.com**
    4.  Fare clic su **Sfoglia** per caricare il certificato di Azure Active Directory.
    5.  Come **Tipo di identità SAML**, selezionare **asserzione contiene l'ID di federazione dall'oggetto utente**.
    6.  Come **Percorso di identità SAML**, selezionare **identità si trova nell'elemento NameIdentfier dell'istruzione oggetto**.
    7.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Jobscience** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso del Provider di identità**
    8.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Jobscience** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione del Provider di identità**
    9.  Fare clic su **Salva**.

13. Nel riquadro di spostamento sinistra, nella sezione **amministrazione** fare clic su **Gestione dei domini** per espandere la sezione correlata e quindi fare clic su un **Dominio personale** per aprire la pagina **My Domain** . 

    ![Il dominio personalizzato] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Il dominio personalizzato")

14. Nella pagina **Dominio personale** , nella sezione **Accesso pagina Personalizzazione** fare clic su **Modifica**.

    ![Pagina di accesso personalizzazione] (./media/active-directory-saas-jobscience-tutorial/IC767826.png "Pagina di accesso personalizzazione")

15. Nella pagina **Personalizzazione pagina di accesso** , nella sezione **Servizio di autenticazione** , viene visualizzato il nome delle **Impostazioni di Single Sign-on SAML** . Selezionarla e quindi fare clic su **Salva**.

    ![Pagina di accesso personalizzazione] (./media/active-directory-saas-jobscience-tutorial/IC784366.png "Pagina di accesso personalizzazione")

16. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurare Single Sign-On")
  
Per ottenere il SP avviati Single Sign-on URL di accesso fare clic su **Impostazioni servizio Single Sign-On** nella sezione menu **Dei controlli di protezione** .

![Controlli di protezione] (./media/active-directory-saas-jobscience-tutorial/IC784368.png "Controlli di protezione")
  
Fare clic sul profilo di Single Sign-on creata nel passaggio precedente.  
In questa pagina vengono Single Sign sull'URL per la propria azienda (ad esempio *https://companyname.my.salesforce.com?so=companyid*).
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Jobscience, è necessario essere effettuato il provisioning in Jobscience.  
In caso di Jobscience, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Jobscience** come amministratore.

2.  Passare alla configurazione

    ![Programma di installazione] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programma di installazione")

3.  Passare a **gestire gli utenti \> utenti**.

    ![Utenti] (./media/active-directory-saas-jobscience-tutorial/IC784369.png "Utenti")

4.  Fare clic su **nuovo utente**.

    ![Tutti gli utenti] (./media/active-directory-saas-jobscience-tutorial/IC784370.png "Tutti gli utenti")

5.  Nella finestra di dialogo **Modifica utente** , procedere come segue:

    ![L'utente modificare] (./media/active-directory-saas-jobscience-tutorial/IC784371.png "L'utente modificare")

    1.  Digitare un nome, cognome, alias, messaggio di posta elettronica, le proprietà nome e il nome alternativo utente dell'utente Azure AD effettuare il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Salva**.

    >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica contenente un collegamento per confermare l'account in cui viene attivato.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Jobscience utente account strumenti di creazione o API fornite dal Jobscience agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Per assegnare gli utenti a Jobscience, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Jobscience **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-jobscience-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).