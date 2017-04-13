<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con sicurezza Stagnola | Microsoft Azure"
    description="Informazioni su come utilizzare la protezione Stagnola con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro!." 
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

#<a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Esercitazione: Integrazione di Azure Active Directory con sicurezza Stagnola
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Stagnola sicurezza.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Stagnola sicurezza single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a sicurezza Stagnola sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di sicurezza Stagnola (segno di provider avviato identità in) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazione per la protezione Stagnola
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Configurare Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configurare Single Sign-On")

##<a name="enabling-the-application-integration-for-tinfoil-security"></a>Attivare l'integrazione di applicazione per la protezione Stagnola
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazione per la protezione Stagnola.

###<a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>Per attivare l'integrazione di applicazione per la protezione Stagnola, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Stagnola sicurezza**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Stagnola protezione**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Stagnola sicurezza] (./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Stagnola sicurezza")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione Stagnola sicurezza con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per Stagnola sicurezza è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, la pagina **Stagnola sicurezza** applicazione integrazione, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Stagnola sicurezza** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Stagnola sicurezza risposta URL** digitare l'URL Stagnola sicurezza asserzione Consumer Service (ACS) (ad esempio: "*https://www.tinfoilsecurity.com/saml/consume*" e quindi fare clic su **Avanti**.

    >[AZURE.NOTE] Dovrebbe essere possibile ottenere l'URL ACS da Stagnola sicurezza metadati (https://www.tinfoilsecurity.com/saml/metadata).

    ![Configurare URL di App] (./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on di sicurezza Stagnola** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\Security.cer Stagnola**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito Stagnola sicurezza della società.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **Account personale**.

    ![Dashboard] (./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7.  Fare clic su **sicurezza**.

    ![Sicurezza] (./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Sicurezza")

8.  Nella pagina configurazione **Single Sign-On** procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")

    1.  Selezionare **Abilita SAML**.
    2.  Fare clic su **configurazione manuale**.
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on di sicurezza Stagnola** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **URL del Post SAML** .
    4.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale certificato SAML** .  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    5.  Copiare **l'ID Account**.
    6.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configurare Single Sign-On")

10. Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributi")

11. Per aggiungere i mapping attributo obbligatorio, procedere come segue:

    ![Attributi] (./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributi")

    1.  Fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** digitare **accountid**.
    3.  Nella casella di testo **Valore dell'attributo** , incollare il valore di ID account copiato nella sezione precedente.
    4.  Fare clic su **completa**.

12. Fare clic su **Applica modifiche**.

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Stagnola sicurezza, è necessario essere effettuato il provisioning in sicurezza Stagnola.  
In caso di sicurezza Stagnola, il provisioning è un'attività manuale.

###<a name="to-get-a-user-provisioned-perform-the-following-steps"></a>Per ottenere un utente viene completato il provisioning, procedere come segue:

1.  Se l'utente fa parte di un account dell'organizzazione, è necessario contattare il team di supporto di sicurezza Stagnola per ottenere l'account utente creato.

2.  Se l'utente è un utente Stagnola sicurezza SaaS normale, l'utente può aggiungere un collaboratore a uno dei siti degli utenti. In questo modo viene attivata un processo per inviare un invito per posta elettronica specificato per creare un nuovo account utente Stagnola sicurezza.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Stagnola sicurezza utente account creazione strumento o API forniti da Stagnola agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Per assegnare gli utenti a sicurezza Stagnola, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Protezione Stagnola **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).