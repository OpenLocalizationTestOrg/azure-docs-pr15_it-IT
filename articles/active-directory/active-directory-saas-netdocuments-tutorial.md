<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con NetDocuments | Microsoft Azure" 
    description="Ecco come utilizzare NetDocuments con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Esercitazione: Integrazione di Azure Active Directory con NetDocuments
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e NetDocuments.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant NetDocuments
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a NetDocuments sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di NetDocuments (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per NetDocuments
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scenario:")
##<a name="enabling-the-application-integration-for-netdocuments"></a>Attivare l'integrazione delle applicazioni per NetDocuments
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per NetDocuments.

###<a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per NetDocuments, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **NetDocuments**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **NetDocuments**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![NetDocuments] (./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a NetDocuments con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per NetDocuments è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **NetDocuments** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al NetDocuments** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurare URL di App")

    1.  Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione NetDocuments (ad esempio: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  Nella casella di testo **URL di risposta NetDocuments** digitare lo stesso valore immesso nella casella di testo **Su URL di accesso** .  

        >[AZURE.NOTE]È possibile trovare il valore corretto alla fine della finestra di dialogo **Identità federato** (vedere la schermata per passaggio 9).

    3.  Fare clic su **Avanti**

4.  Nella pagina **Configura il single sign-on in NetDocuments** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società NetDocuments come amministratore.

6.  Passare a **amministratore**.

7.  Fare clic su **Aggiungi e Rimuovi utenti e gruppi**.

    ![Archivio] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Archivio")

8.  Fare clic su **Configura le opzioni di autenticazione avanzate**.

    ![Configurare l'autenticazione avanzata] (./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurare l'autenticazione avanzata")

9.  Nella finestra di dialogo **Identità federato** , procedere come segue:

    ![Federato Identitty] (./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Federato Identitty")

    1.  Come **tipo di server identità federato**, selezionare **Active Directory Federation Services**.
    2.  Fare clic su **Scegli file**per caricare il file di metadati scaricati.
    3.  Fare clic su **OK**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a NetDocuments, è necessario essere effettuato il provisioning in NetDocuments.  
In caso di NetDocuments, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Meglio al sito della società **NetDocuments** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **amministratore**.

    ![Amministratore] (./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Amministratore")

3.  Fare clic su **Aggiungi e Rimuovi utenti e gruppi**.

    ![Archivio] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Archivio")

4.  Nella casella di testo **Indirizzo di posta elettronica** digitare l'indirizzo di posta elettronica di un account Azure Active Directory valido si desidera eseguire il provisioning e quindi fare clic su **Aggiungi utente**.

    ![Indirizzo di posta elettronica] (./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Indirizzo di posta elettronica")

    >[AZURE.NOTE]Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica contenente un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro NetDocuments utente account strumenti di creazione o API fornite dal NetDocuments agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>Per assegnare gli utenti a NetDocuments, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **NetDocuments **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).