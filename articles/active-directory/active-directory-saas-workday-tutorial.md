<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con lavorativo | Microsoft Azure" 
    description="Informazioni su come utilizzare lavorativo con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workday"></a>Esercitazione: Integrazione di Azure Active Directory con lavorativo
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e lavorativo. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant lavorativo
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni per giorno lavorativo
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Configurare il provisioning dell'utente

![Scenario:] (./media/active-directory-saas-workday-tutorial/IC782919.png "Scenario:")

##<a name="enabling-the-application-integration-for-workday"></a>Attivare l'integrazione di applicazioni per giorno lavorativo
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Salesforce.

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni per giorno lavorativo, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-workday-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazione**, fare clic su **Aggiungi un'App**e quindi fare clic su **Aggiungi un'applicazione per la propria organizzazione da utilizzare**.

    ![Operazioni da eseguire?] (./media/active-directory-saas-workday-tutorial/IC700995.png "Operazioni da eseguire?")

5.  Nella **casella Cerca**digitare **lavorativo**.

    ![Giorno lavorativo] (./media/active-directory-saas-workday-tutorial/IC701021.png "Giorno lavorativo")

6.  Nel riquadro dei risultati selezionare **lavorativo**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Giorno lavorativo] (./media/active-directory-saas-workday-tutorial/IC701022.png "Giorno lavorativo")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a lavorativo con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nella pagina integrazione applicazione **lavorativo** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-workday-tutorial/IC782920.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al giorno lavorativo** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-workday-tutorial/IC782921.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** , eseguire le operazioni seguenti e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-workday-tutorial/IC782957.png "Configurare URL di App")

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere a lavorativo utilizzando il modello seguente:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b.  Nella casella di testo **URL di risposta lavorativo** , digitare l'URL di risposta lavorativo utilizzando il modello seguente:`https://impl.workday.com/<tenant>/login-saml.htmld`

    >[AZURE.NOTE]L'URL di risposta deve avere un sottodominio (ad esempio: www, wd2, wd3, implementazione del wd3, wd5, implementazione di wd5). 
    >Utilizzo di un elemento come "*http://www.myworkday.com*" funziona, ma non "*http://myworkday.com*". 
 
4.  Nella pagina **Configura il single sign-on in lavorativo** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-workday-tutorial/IC782922.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere come amministratore in sito giorno lavorativo della società.

6.  Passare a **Menu \> area di lavoro**.

    ![Area di lavoro] (./media/active-directory-saas-workday-tutorial/IC782923.png "Area di lavoro")

7.  Passare a **amministrazione dell'Account**.

    ![Amministrazione dell'account] (./media/active-directory-saas-workday-tutorial/IC782924.png "Amministrazione dell'account")

8.  Passare a **Modifica Tenant Setup – sicurezza**.

    ![Modificare la protezione Tenant] (./media/active-directory-saas-workday-tutorial/IC782925.png "Modificare la protezione Tenant")

9.  Nella sezione **Reindirizzamento URL** , procedere come segue:

    ![Reindirizzamento URL] (./media/active-directory-saas-workday-tutorial/IC7829581.png "Reindirizzamento URL")

    un. Fare clic su **Aggiungi riga**.

    b. Nella casella di testo **URL di reindirizzamento di accesso** e la casella di testo **URL di reindirizzamento Mobile** , digitare l' **URL Tenant lavorativo** immesso nella pagina **Configura URL App** del portale di classica Azure.
    
    c. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in lavorativo** copiare l' **URL del servizio Sign-Out singolo**e quindi incollarla nella casella di testo **URL di reindirizzamento disconnessione** .

    d.  Nella casella di testo **ambiente** , digitare il nome dell'ambiente.  


    >[AZURE.NOTE] Il valore dell'attributo ambiente è associato al valore dell'URL tenant:
    >
    >-   Se il nome di dominio del tenant lavorativo URL inizia con l'implementazione del (ad esempio: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), l'attributo **ambiente** deve essere impostata su implementazione.
    >-   Se il nome di dominio inizia con un altro elemento, è necessario contattare il giorno lavorativo per ottenere il valore di **ambiente** corrispondente.

10. Nella sezione **Configurazione SAML** , procedere come segue:

    ![Configurazione SAML] (./media/active-directory-saas-workday-tutorial/IC782926.png "Configurazione SAML")

    un.  Selezionare **Abilita autenticazione SAML**.

    b.  Fare clic su **Aggiungi riga**.

11. Nella sezione provider di identità SAML, procedere come segue:

    ![Provider di identità SAML] (./media/active-directory-saas-workday-tutorial/IC7829271.png "Provider di identità SAML")

    un. Nella casella di testo Nome del Provider di identità, digitare un nome di provider (ad esempio: *SPInitiatedSSO*).

    b. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in lavorativo** copiare il valore di **ID Provider di identità** e quindi incollarla nella casella di testo **emittente** .

    c. Selezionare **Abilita lavorativo Initialted disconnessione**.

    d. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in lavorativo** copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **URL di richiesta di disconnessione** .


    e. Fare clic su **Certificato chiave pubblica di identità Provider**e quindi fare clic su **Crea**. 

    ![Creare] (./media/active-directory-saas-workday-tutorial/IC782928.png "Creare")

    f. Fare clic su **creare x509 chiave pubblica**. 
        
    ![Creare] (./media/active-directory-saas-workday-tutorial/IC782929.png "Creare")


1. Nella sezione **Visualizzazione x509 chiave pubblica** , procedere come segue: 

    ![Visualizzazione x509 chiave pubblica] (./media/active-directory-saas-workday-tutorial/IC782930.png "Visualizzazione x509 chiave pubblica") 

    un. Nella casella di testo **nome** digitare un nome per il certificato (ad esempio: *DPI\_SP*).
        
    b. Nella casella di testo **Valido da** digitare valido dal valore dell'attributo del certificato.
    
    c.  Nella casella di testo **Valido per** digitare valido al valore dell'attributo del certificato.
        
    >[AZURE.NOTE] È possibile ottenere la validità dalla data e la validità data di fine del certificato scaricata facendo doppio clic su esso. Le date sono elencate sotto la scheda **Dettagli** .

    d. Creare un file **con codifica Base 64** del certificato scaricata.  

    >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    e.  Aprire il certificato codificato base 64 nel blocco note e quindi copiare il contenuto di esso.
    
    f.  Nella casella di testo **certificato** , incollare il contenuto degli Appunti.
    
    g.  Fare clic su **OK**.

12.  Eseguire i passaggi seguenti: 

    ![Configurazione di Single Sign-on] (./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configurazione di Single Sign-on")

    un.  Abilitare la **x509 coppia di chiave privata**.

    b.  Nella casella di testo **ID Provider del servizio** , digitare **http://www.workday.com**.

    c.  Selezionare **Abilita avviata SP SAML autenticazione**.

    d.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in lavorativo** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL del servizio Single Sign-on IdP** .
     
    e. Selezionare **non decompressione richiesta di autenticazione avviata SP**.

    f. **Metodo di autenticazione richiesta di firma**, selezionare **SHA256**. 
        
    ![Richiesta di autenticazione metodo di firma] (./media/active-directory-saas-workday-tutorial/IC782932.png "Richiesta di autenticazione metodo di firma") 
 
    g. Fare clic su **OK**. 
        
    ![OK] (./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Nel portale di classica Azure, nella pagina **Configura il single sign-on in lavorativo** fare clic su **Avanti**. 

    ![Configurare il single sign-on] (./media/active-directory-saas-workday-tutorial/IC782934.png "Configurare il single sign-on")

13. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**. 

    ![Configurare il single sign-on] (./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurare il single sign-on")



##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per ottenere un utente di test viene completato il provisioning in lavorativo, è necessario contattare il team di supporto lavorativo.  
Il team di supporto lavorativo creerà l'utente dell'utente.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-workday-perform-the-following-steps"></a>Per assegnare gli utenti a lavorativo, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **lavorativo **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-workday-tutorial/IC782935.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-workday-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).