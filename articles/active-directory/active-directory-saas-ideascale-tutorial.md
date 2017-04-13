<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con IdeaScale | Microsoft Azure" 
    description="Ecco come utilizzare IdeaScale con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Esercitazione: Integrazione di Azure Active Directory con IdeaScale
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e IdeaScale.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un IdeaScale single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a IdeaScale sarà possibile accesso singolo nell'applicazione tramite l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per IdeaScale
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario:")
##<a name="enabling-the-application-integration-for-ideascale"></a>Attivare l'integrazione delle applicazioni per IdeaScale
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per IdeaScale.

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per IdeaScale, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-ideascale-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-ideascale-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **IdeaScale**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-ideascale-tutorial/IC790841.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **IdeaScale**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![IdeaScale] (./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a IdeaScale con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per IdeaScale è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **IdeaScale** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al IdeaScale** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **IdeaScale su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione IdeaScale (ad esempio: "*https://company.IdeaScale.com*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in IdeaScale** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società IdeaScale come amministratore.

6.  Passare a **Impostazioni Community**.

    ![Impostazioni della community] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Impostazioni della community")

7.  Passare a **protezione \> singolo impostazioni iscrizione**.

    ![Impostazioni di accesso singolo] (./media/active-directory-saas-ideascale-tutorial/IC790848.png "Impostazioni di accesso singolo")

8.  Come **Tipo di accesso singolo**, selezionare **SAML 2.0**.

    ![Unico tipo di accesso] (./media/active-directory-saas-ideascale-tutorial/IC790849.png "Unico tipo di accesso")

9.  Nella finestra di dialogo **Impostazioni di accesso singolo** , procedere come segue:

    ![Impostazioni di accesso singolo] (./media/active-directory-saas-ideascale-tutorial/IC790850.png "Impostazioni di accesso singolo")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in IdeaScale** copiare il valore di **ID entità** e quindi incollarla nella casella di testo **ID entità IdP SAML** .
    2.  Copiare il contenuto del file di metadati scaricati e quindi incollarla nella casella di testo **SAML IdP metadati** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in IdeaScale** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL di successo di disconnessione** .
    4.  Fare clic su **Salva modifiche**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a IdeaScale, è necessario essere effettuato il provisioning in IdeaScale.  
In caso di IdeaScale, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **IdeaScale** come amministratore.

2.  Passare a **Impostazioni Community**.

    ![Impostazioni della community] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Impostazioni della community")

3.  Passare a **impostazioni di base \> Gestione membri**.

4.  Fare clic su **Aggiungi membro**.

    ![Gestione membri] (./media/active-directory-saas-ideascale-tutorial/IC790852.png "Gestione membri")

5.  Nella sezione Aggiungi nuovo membro, procedere come segue:

    ![Aggiungere nuovi membri] (./media/active-directory-saas-ideascale-tutorial/IC790853.png "Aggiungere nuovi membri")

    1.  Nella casella di testo a **Indirizzi di posta elettronica** digitare l'indirizzo di posta elettronica di un account valido AAD che si desidera eseguire il provisioning.
    2.  Fare clic su **Salva modifiche**.

    >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica con un collegamento di confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro IdeaScale utente account strumenti di creazione o API fornite dall'IdeaScale agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Per assegnare gli utenti a IdeaScale, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **IdeaScale **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).