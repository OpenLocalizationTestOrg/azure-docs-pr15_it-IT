<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con SAP HANA Cloud piattaforma | Microsoft Azure" 
    description="Informazioni su come utilizzare piattaforma Cloud HANA SAP con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Esercitazione: Integrazione di Azure Active Directory con piattaforma Cloud HANA SAP
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e piattaforma Cloud HANA SAP.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un account di piattaforma Cloud HANA SAP
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a piattaforma Cloud HANA SAP sarà possibile accesso singolo nell'applicazione tramite l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]È necessario distribuire un'applicazione o la sottoscrizione a un'applicazione nel proprio account di piattaforma Cloud HANA SAP per testare servizio single sign in. In questa esercitazione, un'applicazione viene distribuita nella finestra account.
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per piattaforma Cloud HANA SAP
2.  Configurare il single sign-on
3.  Assegnazione di un ruolo a un utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario:")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Attivare l'integrazione delle applicazioni per piattaforma Cloud HANA SAP
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per piattaforma Cloud HANA SAP.

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per piattaforma Cloud HANA SAP, procedere come segue:

1.  Nel portale di gestione di Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Piattaforma Cloud HANA SAP**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Piattaforma Cloud HANA SAP**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Hana SAP] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "Hana SAP")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione SAP HANA Cloud piattaforma con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato codificato base 64 al tenant di piattaforma Cloud HANA SAP.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **SAP HANA Cloud piattaforma** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al SAP HANA Cloud piattaforma** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configurare Single Sign-On")

3.  In una finestra del browser web diverso, accedere al SAP HANA Cloud piattaforma Pannello di controllo in https://account. \<host orizzontale\>.ondemand.com/cockpit (ad esempio: *https://account.hanatrial.ondemand.com/cockpit*).

4.  Fare clic sulla scheda **protezione** .

    ![Attendibile] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Attendibile")

5.  Nella sezione Gestione protezione, procedere come segue:

    ![Ottenere i metadati] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Ottenere i metadati")

    1.  Fare clic sulla scheda **Provider di servizi locale** .
    2.  Per scaricare il file di metadati SAP HANA Cloud piattaforma, fare clic su **Guida metadati**.

6.  Nel portale di classica Azure Active, nella pagina **Configura URL App** , eseguire le operazioni seguenti e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configurare URL di App")

    1.  Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere a un'applicazione **Piattaforma Cloud HANA SAP** . Questo è l'URL di account specifici di una risorsa protetta nell'applicazione piattaforma Cloud HANA SAP. L'URL è basato sul modello seguente: https:// *\<NomeApplicazione\>\<nome account\>.\< host orizzontale\>.ondemand.com/\<percorso\_a\_protetta\_risorse\> * (ad esempio: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Questo è l'URL dell'applicazione di piattaforma Cloud HANA SAP che richiede all'utente di eseguire l'autenticazione.

    2.  Aprire il file di metadati SAP HANA Cloud piattaforma scaricato e quindi individuare il tag **ns3:AssertionConsumerService** .
    3.  Copiare il valore dell'attributo **posizione** e quindi incollarlo nella casella di testo **SAP HANA Cloud piattaforma risposta URL** .

7.  Nella pagina **Configura il single sign-on SAP HANA Cloud piattaforma** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configurare Single Sign-On")

8.  Nella sezione **Provider di servizi locale** SAP HANA Cloud piattaforma Pannello di controllo, eseguire i passaggi seguenti:

    ![Gestione di trust] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Gestione di trust")

    1.  Fare clic su **Modifica**.
    2.  Come **Tipo di configurazione**, selezionare **personalizzata**.
    3.  Come **Nome del Provider locale**, lasciare il valore predefinito.
    4.  Per creare una **Chiave di accesso** e una coppia di **Certificato di firma** , fare clic su **Genera coppia di chiavi**.
    5.  Come **Propagazione principale**, selezionare **disabilitato**.
    6.  Come **Forzare l'autenticazione**, selezionare **disabilitato**.
    7.  Fare clic su **Salva**.

9.  Fare clic sulla scheda **Provider di identità attendibili** e quindi fare clic su **Aggiungi Provider di identità attendibili**.

    ![Gestione di trust] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Gestione di trust")

    >[AZURE.NOTE]Per gestire l'elenco dei provider di identità attendibile, è necessario selezionato il tipo di configurazione personalizzata nella sezione Provider di servizi locale. Per tipo di configurazione predefinita, è necessario un trust non modificabile e implicito al servizio ID SAP. Per nessuno, non è necessario le impostazioni di protezione.

10. Fare clic sulla scheda **Generale** e quindi fare clic su **Sfoglia** per caricare il file di metadati scaricati.

    ![Gestione di trust] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Gestione di trust")

    >[AZURE.NOTE] Dopo il caricamento del file di metadati, i valori per **Single Sign-on URL**, **Solo URL di disconnessione** e **Certificato di firma** sono compilati automaticamente.

11. Fare clic sulla scheda **attributi** .

12. Nella scheda **attributi** , procedere come segue:

    ![Attributi] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributi")

    1.  Facendo clic su **Add Assertion-Based attributo**, aggiungere i seguenti attributi asserzione:

        |Attributo asserzione| Attributo di capitale|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/claims/givenName|   nome|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/claims/surname|        Cognome|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/claims/EmailAddress|Posta elettronica|

    >[AZURE.NOTE]Configurazione degli attributi dipende come le applicazioni in HCP vengono sviluppati, ovvero quali attributi si aspettano nella risposta SAML e in quale nome (capitale attributo) accedono a questo attributo nel codice.
    >  
    >un.  L' **Attributo predefinito** nella schermata è sufficiente esemplificativo. Non è necessario utilizzare lo scenario.  
    >
    >b.  I nomi e i valori per **Attributo capitale** illustrata nella schermata dipendono dalla modalità di sviluppo dell'applicazione. È possibile che l'applicazione richiede mapping diversi.

13. Nel portale di classica Azure, della pagina **Configura il single sign-on SAP HANA Cloud piattaforma** finestra di dialogo, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configurare Single Sign-On")
  
Facoltativamente, è possibile configurare gruppi basati su asserzione relative al Provider di identità Azure Active Directory

>[AZURE.NOTE]Utilizzo di gruppi in SAP HANA Cloud piattaforma consente di assegnare in modo dinamico uno o più utenti a uno o più ruoli nelle applicazioni SAP HANA Cloud piattaforma è determinate dai valori degli attributi asserzione SAML 2.0. Ad esempio, se l'asserzione contiene l'attributo "*contratto = temporaneo*", è consigliabile tutti i utenti da aggiungere al gruppo "*TEMPORANEO*". Il gruppo "*TEMPORANEO*" può contenere uno o più ruoli da uno o più applicazioni distribuite nell'account piattaforma Cloud HANA SAP.
>  
>Utilizzare i gruppi in base asserzione se si desidera assegnare di massa molti utenti ai ruoli di uno o più delle applicazioni nell'account piattaforma Cloud HANA SAP. Se si desidera assegnare un numero di utenti singolo o small a (a) ruoli specifici è consigliabile l'assegnazione direttamente nella scheda "**autorizzazioni**" il pannello di controllo piattaforma Cloud HANA SAP.

##<a name="assigning-a-role-to-a-user"></a>Assegnazione di un ruolo a un utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a piattaforma Cloud HANA SAP, è necessario assegnare i ruoli di piattaforma SAP HANA Cloud ad essi.

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Per assegnare un ruolo a un utente, procedere come segue:

1.  Accedere a pannello di controllo per la **Piattaforma Cloud HANA SAP** .

2.  Eseguire i passaggi seguenti:

    ![Autorizzazioni] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorizzazioni")

    1.  Fare clic su **autorizzazioni**.
    2.  Fare clic sulla scheda **utenti** .
    3.  Nella casella di testo **utente** digitare l'indirizzo di posta elettronica dell'utente.
    4.  Fare clic su **Assegna** per assegnare all'utente in un ruolo.
    5.  Fare clic su **Salva**.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Per assegnare gli utenti a piattaforma Cloud HANA SAP, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **SAP HANA Cloud piattaforma** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).