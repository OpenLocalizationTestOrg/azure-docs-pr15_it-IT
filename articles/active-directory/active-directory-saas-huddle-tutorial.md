<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Huddle | Microsoft Azure" 
    description="Ecco come utilizzare Huddle con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>Esercitazione: Integrazione di Azure Active Directory con Huddle
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Huddle.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Huddle single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Huddle sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Huddle (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Huddle
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Configurare Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurare Single Sign-On")
##<a name="enabling-the-application-integration-for-huddle"></a>Attivare l'integrazione delle applicazioni per Huddle
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Huddle.

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Huddle, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-huddle-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-huddle-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-huddle-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Huddle**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-huddle-tutorial/IC787831.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Huddle**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Huddle] (./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Huddle con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Huddle** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurare Single Sign-On")

2.  Nella pagina **da come si desidera agli utenti di accedere al Huddle** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Huddle su URL di accesso** , digitare l'URL del tenant di Huddle mediante il modello "*http://company.huddle.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Huddle** , procedere come segue:

    ![Configurare Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurare Single Sign-On")

    1.  Fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.
    2.  Copiare il valore **dell'URL emittente** , il valore di **SAML Single Sign-on URL** e il certificato scaricato e inviarlo al team di supporto Huddle.

    >[AZURE.NOTE] Il Single sign-on deve essere abilitato per il team di supporto Huddle.
Si riceve una notifica quando è stata completata la configurazione.

5.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Huddle, è necessario essere effettuato il provisioning in Huddle.  
In caso di Huddle, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Huddle** come amministratore.

2.  Fare clic su **area di lavoro**.

3.  Fare clic su **persone \> invitare altre persone**.

    ![Persone] (./media/active-directory-saas-huddle-tutorial/IC787838.png "Persone")

4.  Nella sezione **creare un nuovo invito** , procedere come segue:

    ![Nuovo invito] (./media/active-directory-saas-huddle-tutorial/IC787839.png "Nuovo invito")

    1.  Nell'elenco **Selezionare un team per invitare persone a partecipare** , selezionare **team**.
    2.  Digitare l' **Indirizzo di posta elettronica** di un account valido AAD che si desidera eseguire il provisioning nella casella di testo correlato.
    3.  Fare clic su **Invita**.

    >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica incluso un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Huddle utente account strumenti di creazione o API fornite dall'Huddle effettuare il provisioning di AAD gli account utente.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Per assegnare gli utenti a Huddle, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Huddle **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-huddle-tutorial/IC787840.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-huddle-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).