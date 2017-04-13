<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con FreshService | Microsoft Azure" 
    description="Ecco come utilizzare FreshService con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Esercitazione: Integrazione di Azure Active Directory con FreshService
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e FreshService.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un FreshService single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a FreshService sarà possibile accesso singolo nell'applicazione tramite l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per FreshService
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario:")
##<a name="enabling-the-application-integration-for-freshservice"></a>Attivare l'integrazione delle applicazioni per FreshService
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per FreshService.

###<a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per FreshService, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-freshservice-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-freshservice-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **FreshService**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-freshservice-tutorial/IC790808.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **FreshService**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Freshservice] (./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a FreshService con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per FreshService è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **FreshService** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al FreshService** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** nella casella di testo **FreshService su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Freshdesk (ad esempio: "*http://democompany.freshservice.com/*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in FreshService** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società FreshService come amministratore.

6.  Nel menu nella parte superiore, fare clic su **amministratore**.

    ![Amministratore] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Amministratore")

7.  Nel **Portale clienti**fare clic su **sicurezza**.

    ![Sicurezza] (./media/active-directory-saas-freshservice-tutorial/IC790815.png "Sicurezza")

8.  Nella sezione **sicurezza** , procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign-On")

    1.  Passare **all'accesso singolo OnON**.
    2.  Selezionare **SAML Single Sign-on**.
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in FreshService** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso SAML** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in FreshService** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .
    5.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale certificato di sicurezza** .
    
        >[AZURE.TIP]Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a FreshService, è necessario essere effettuato il provisioning in FreshService.  
In caso di FreshService, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **FreshService** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **amministratore**.

    ![Amministratore] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Amministratore")

3.  Nella sezione **Gestione degli utenti** , fare clic su **richiedente**.

    ![Richiedente] (./media/active-directory-saas-freshservice-tutorial/IC790818.png "Richiedente")

4.  Fare clic su **Nuovo richiedente**.

    ![Nuovi richiedente] (./media/active-directory-saas-freshservice-tutorial/IC790819.png "Nuovi richiedente")

5.  Nella sezione **Nuovo richiedente** , procedere come segue:

    ![Nuovo richiedente] (./media/active-directory-saas-freshservice-tutorial/IC790820.png "Nuovo richiedente")

    1.  Immettere gli attributi **nome** e la **posta elettronica** di un account Azure Active Directory valido che si desidera effettuare il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Salva**.

    >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica incluso un collegamento per confermare l'account diventa attiva

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro FreshService utente account strumenti di creazione o API fornite dal FreshService agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Per assegnare gli utenti a FreshService, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **FreshService **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).