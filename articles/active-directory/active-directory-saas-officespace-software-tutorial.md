<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con il OfficeSpace Software | Microsoft Azure" 
    description="Informazioni su come usare OfficeSpace Software con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Esercitazione: Integrazione di Azure Active Directory con il OfficeSpace Software
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e OfficeSpace Software.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un OfficeSpace Software single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a OfficeSpace Software sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di OfficeSpace Software (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni OfficeSpace Software
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scenario:")
##<a name="enabling-the-application-integration-for-officespace-software"></a>Attivare l'integrazione di applicazioni OfficeSpace Software
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni OfficeSpace Software.

###<a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni OfficeSpace Software, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **OfficeSpace Software**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **OfficeSpace Software**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Software OfficeSpace] (./media/active-directory-saas-officespace-software-tutorial/IC781007.png "Software OfficeSpace")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a OfficeSpace Software con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per OfficeSpace Software è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **OfficeSpace Software** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare accesso singolo = attivo] (./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurare accesso singolo = attivo")

2.  Nella pagina **specificare come gli utenti di accedere al OfficeSpace Software** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **OfficeSpace Software accesso su URL** digitare l'URL utilizzato dagli utenti per eseguire l'accesso a un'applicazione Software di OfficeSpace (ad esempio: "*https://company.officespacesoftware.com*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on OfficeSpace software** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito della società OfficeSpace Software.

6.  Passare a **amministratore \> connettori**.

    ![Amministratore] (./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Amministratore")

7.  Fare clic su **autorizzazione SAML**.

    ![Connettori] (./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connettori")

8.  Nella sezione **Autorizzazione SAML** , procedere come segue:

    ![Configurazione di SAML] (./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configurazione di SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on OfficeSpace software** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **url del provider di disconnessione** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on OfficeSpace software** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **url di destinazione idp Client** .
    3.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale del certificato Client idp** .  

        >[AZURE.TIP]
        Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    4.  Fare clic su **Salva impostazioni**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a OfficeSpace Software, è necessario essere effettuato il provisioning in OfficeSpace Software. In caso di OfficeSpace Software, il provisioning è un'attività automatica.  
Non esiste alcun elemento azione dell'utente.  
Gli utenti vengono creati automaticamente se necessario, durante il primo single sign-on tentativo.

>[AZURE.NOTE]È possibile utilizzare altri OfficeSpace Software utente account creazione strumenti o API fornite da parte di OfficeSpace Software agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>Per assegnare gli utenti a OfficeSpace Software, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **OfficeSpace Software **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).