<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Veracode | Microsoft Azure" 
    description="Ecco come utilizzare Veracode con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-veracode"></a>Esercitazione: Integrazione di Azure Active Directory con Veracode
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Veracode. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Veracode single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Veracode sarà possibile accesso singolo nell'applicazione tramite l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Veracode
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-veracode-tutorial/IC802903.png "Scenario:")

##<a name="enabling-the-application-integration-for-veracode"></a>Attivare l'integrazione delle applicazioni per Veracode
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Veracode.

###<a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Veracode, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-veracode-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-veracode-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-veracode-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Veracode**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-veracode-tutorial/IC802904.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Veracode**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Veracode] (./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Veracode con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
L'applicazione Veracode prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** .  
Schermata seguente è illustrato un esempio per l'oggetto.

![Attributi] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributi")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Veracode** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Veracode** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurare Single Sign-On")

3.  Nella pagina **Configura impostazioni di App** , fare clic su **Avanti**.

    ![Configurare le impostazioni dell'App] (./media/active-directory-saas-veracode-tutorial/IC802909.png "Configurare le impostazioni dell'App")

4.  Nella pagina **Configura il single sign-on in Veracode** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Veracode come amministratore.

6.  Nel menu nella parte superiore, fare clic su **Impostazioni**e quindi fare clic su **amministratore**.

    ![Amministrazione] (./media/active-directory-saas-veracode-tutorial/IC802911.png "Amministrazione")

7.  Fare clic sulla scheda **SAML** .

8.  Nella sezione **Impostazioni SAML dell'organizzazione** , procedere come segue:

    ![Amministrazione] (./media/active-directory-saas-veracode-tutorial/IC802912.png "Amministrazione")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Veracode** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente**
    2.  Per caricare il certificato scaricato, fare clic su **Scegli File**.
    3.  Selezionare **Attiva registrazione automatica**.

9.  Nella sezione **Impostazioni di registrazione automatica** , eseguire le operazioni seguenti e quindi fare clic su **Salva**:

    ![Amministrazione] (./media/active-directory-saas-veracode-tutorial/IC802913.png "Amministrazione")

    1.  Come **L'attivazione di nuovo**, selezionare la casella di controllo **Non attivazione obbligatoria**.
    2.  Come **Gli aggiornamenti dei dati utente**, selezionare **Preferenze Veracode utente dati**.
    3.  Per **Informazioni dettagliate attributo SAML**, eseguire le operazioni seguenti:
        -   **Ruoli degli utenti**
        -   **Amministratore dei criteri**
        -   **Revisore**
        -   **Cliente potenziale di sicurezza**
        -   **Executive**
        -   **Autore**
        -   **Autore**
        -   **Tutti i tipi di analisi**
        -   **Appartenenze al team**
        -   **Team predefinito**

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurare Single Sign-On")

11. Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributi")

12. Per aggiungere i mapping attributo obbligatorio, procedere come segue:

    ![Attributi] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributi")

  	| Nome attributo | Valore dell'attributo |
  	|:---------------|:----------------|
  	| nome      | User.givenName  |
  	| Cognome       | User.surname    |
  	| Posta elettronica          | User.Mail       |

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **Aggiungi attributo utente**.
    
    2.  Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.

    3.  Nella casella di testo **Valore dell'attributo** , selezionare il valore dell'attributo visualizzato per tale riga.

    4.  Fare clic su **completa**.

13. Fare clic su **Applica modifiche**.

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Veracode, è necessario essere effettuato il provisioning in Veracode.  
In caso di Veracode, il provisioning è un'attività automatica.  
Non esiste alcun elemento azione dell'utente..
  
Gli utenti vengono creati automaticamente se necessario, durante il primo single sign-on tentativo.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Veracode utente account strumenti di creazione o API fornite dal Veracode agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Per assegnare gli utenti a Veracode, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Veracode **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-veracode-tutorial/IC802915.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-veracode-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).