<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con TOPdesk - pubblico | Microsoft Azure" 
    description="Informazioni su come utilizzare TOPdesk - pubblico con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Esercitazione: Integrazione della Directory Azure con TOPdesk - pubblico

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e TOPdesk - pubblico.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un TOPdesk - single sign-on pubblico abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory è stato assegnato a TOPdesk - pubblico sarà in grado di eseguire l'accesso singolo all'applicazione il TOPdesk - sito pubblico società (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per TOPdesk - pubblico
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario:")

##<a name="enabling-the-application-integration-for-topdesk---public"></a>Attivare l'integrazione delle applicazioni per TOPdesk - pubblico
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per TOPdesk - pubblico.

###<a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per TOPdesk - pubblico, eseguire le operazioni seguenti:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **TOPdesk - pubblico**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **TOPdesk - pubblico**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![TOPdesk pubblico] (./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk pubblico")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a TOPdesk - pubblico con il proprio account Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Configurazione single sign-on per TOPdesk - pubblico è necessario caricare un file di icona logo. Per ottenere il file icona, contattare il team di supporto TOPdesk.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Accedere al sito aziendale **TOPdesk - pubblico** come amministratore.

2.  Nel menu **TOPdesk** fare clic su **Impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Impostazioni")

3.  Fare clic su **impostazioni di accesso**.

    ![Impostazioni di accesso] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Impostazioni di accesso")

4.  Espandere il menu **Impostazioni di accesso** e quindi fare clic su **Generale**.

    ![Generale] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Generale")

5.  Nella sezione **pubblica** della sezione di configurazione **accesso SAML** , procedere come segue:

    ![Technical Settings] (./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")

    1.  Fare clic su **Download** per scaricare il file di metadati pubblico e quindi salvarla in locale nel computer in uso.
    2.  Aprire il file di metadati e quindi individuare il nodo **AssertionConsumerService** .
        ![AssertionConsumerService] (./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  Copiare il valore di **AssertionConsumerService** .  

        >[AZURE.NOTE] Il valore nella sezione **Configura URL App** sarà necessario più avanti in questa esercitazione.

6.  In una finestra del browser web diverso, accedere al **portale classica Azure** come amministratore.

7.  Nella pagina integrazione applicazione **TOPdesk - pubblico** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configurare Single Sign-On")

8.  Nella pagina **specificare come gli utenti di accedere al TOPdesk - pubblico** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configurare Single Sign-On")

9.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configurare URL di App")

    1.  Nella casella di testo **TOPdesk - accesso su URL pubblico** , digitare l'URL utilizzato dagli utenti per accedere al TOPdesk - pubblico applicazione (ad esempio: "*https://qssolutions.topdesk.net*").
    2.  Nella casella di testo **TOPdesk-URL pubblico risposta** incollare **TOPdesk - URL pubblico AssertionConsumerService** (ad esempio: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Fare clic su **Avanti**.

10. Nella pagina **Configura il single sign-on in TOPdesk - pubblico** per scaricare il file di metadati, fare clic su **Scarica metadati**e quindi salvare il file in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configurare Single Sign-On")

11. Per creare un file di certificato, procedere come segue:

    ![Certificato] (./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificato")

    1.  Aprire il file di metadati scaricati.
    2.  Espandere il nodo **RoleDescriptor** contenente un **xsi: Type** di **notte: ApplicationServiceType**.
    3.  Copiare il valore del nodo **X509Certificate** .
    4.  Salvare il valore di **X509Certificate** copiato in locale nel computer in uso in un file.

12. Scegliere **Impostazioni**il TOPdesk - sito pubblico società, nel menu **TOPdesk** .

    ![Impostazioni] (./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Impostazioni")

13. Fare clic su **impostazioni di accesso**.

    ![Impostazioni di accesso] (./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Impostazioni di accesso")

14. Espandere il menu **Impostazioni di accesso** e quindi fare clic su **Generale**.

    ![Generale] (./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "Generale")

15. Nella sezione **pubblica** , fare clic su **Aggiungi**.

    ![Accesso SAML] (./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "Accesso SAML")

16. Nella scheda **Assistente configurazione SAML** , procedere come segue:

    ![Assistente configurazione SAML] (./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "Assistente configurazione SAML")

    1.  Per caricare un file di metadati scaricati, in **Metadati federazione**, fare clic su **Sfoglia**.
    2.  Per caricare un file di certificato, in **Certificato RSA ()**, fare clic su **Sfoglia**.
    3.  Per caricare il file del logo ottenuto dal team di supporto di TOPdesk, sotto l' **icona Logo**, fare clic su **Sfoglia**.
    4.  Nella casella di testo **attributo nome utente** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  Nella casella di testo **nome visualizzato** digitare un nome per la configurazione.
    6.  Fare clic su **Salva**.

17. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a TOPdesk - pubblico, è necessario eseguire il provisioning in TOPdesk - pubblico.  
In caso di TOPdesk - pubblico, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **TOPdesk - pubblico** come amministratore.

2.  Nel menu nella parte superiore, fare clic su **TOPdesk \> nuovo \> file di supporto \> persona**.

    ![Persona] (./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Persona")

3.  Nella finestra di dialogo nuova persona, procedere come segue:

    ![Nuova persona] (./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "Nuova persona")

    1.  Fare clic sulla scheda Generale.
    2.  Nella casella di testo Cognome, digitare il cognome di un account Azure Active Directory valido che si desidera eseguire il provisioning.
    3.  Selezionare un **sito** per l'account.
    4.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro TOPdesk - strumenti di creazione di account utente pubblici o API fornite dal TOPdesk - pubblico per gli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Per assegnare gli utenti a TOPdesk - pubblico, eseguire le operazioni seguenti:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **TOPdesk - pubblico **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).