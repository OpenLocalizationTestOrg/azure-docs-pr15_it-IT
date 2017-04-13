<properties 
    pageTitle="Esercitazione: Azure Active Directory integrazione integrazione Druva | Microsoft Azure" 
    description="Ecco come utilizzare Druva con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Esercitazione: Azure Active Directory integrazione integrazione Druva

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Druva.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Druva single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Druva sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Druva (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Druva
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-druva-tutorial/IC795084.png "Scenario:")
##<a name="enabling-the-application-integration-for-druva"></a>Attivare l'integrazione delle applicazioni per Druva

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Druva.

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Druva, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-druva-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-druva-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-druva-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Druva**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-druva-tutorial/IC795085.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Druva**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Druva] (./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Druva con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

L'applicazione Druva prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** .  
Schermata seguente è illustrato un esempio per l'oggetto.

![Attributi Token SAML] (./media/active-directory-saas-druva-tutorial/IC795087.png "Attributi Token SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Druva** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795027.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Druva** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795088.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Druva su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione Druva (ad esempio: "*https://cloud.druva.com/home/*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-druva-tutorial/IC795089.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Druva** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795090.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Druva come amministratore.

6.  Passare a **gestire \> impostazioni**.

    ![Impostazioni] (./media/active-directory-saas-druva-tutorial/IC795091.png "Impostazioni")

7.  Nella finestra di dialogo Impostazioni servizio Single Sign-On, procedere come segue:

    ![Impostazioni porzione singola Sign-On] (./media/active-directory-saas-druva-tutorial/IC795092.png "Impostazioni porzione singola Sign-On")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Druva** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **ID Provider l'URL di accesso** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Druva** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **ID Provider disconnessione URL** .
    3.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **ID Provider certificato**
    5.  Per aprire la pagina **Impostazioni** , fare clic su **Salva**.

8.  Nella pagina **Impostazioni** fare clic su **Genera Token di Single Sign-on**.

    ![Impostazioni] (./media/active-directory-saas-druva-tutorial/IC795093.png "Impostazioni")

9.  Nella finestra di dialogo **Single Sign-on autenticazione Token** , procedere come segue:

    ![Token di Single Sign-on] (./media/active-directory-saas-druva-tutorial/IC795094.png "Token di Single Sign-on")

    1.  Fare clic su **Copia**.
    2.  Fare clic su **Chiudi**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795095.png "Configurare Single Sign-On")

11. Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** .

    ![Attributi] (./media/active-directory-saas-druva-tutorial/IC795096.png "Attributi")

12. Per aggiungere i mapping attributo obbligatorio, procedere come segue:

  	|Nome attributo|Valore dell'attributo|
  	|---|---|
  	|sincronizzata\_auth\_token|<*valore negli Appunti*>|

    1.  Per ogni riga di dati nella tabella precedente, fare clic su **Aggiungi attributo utente**.
    2.  Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.
    3.  Nella casella di testo **Valore dell'attributo** , digitare il valore dell'attributo visualizzato per tale riga.
    4.  Fare clic su **completa**.

13. Fare clic su **Applica modifiche**.
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Druva, è necessario essere effettuato il provisioning in Druva.  
In caso di Druva, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **Druva** come amministratore.

2.  Passare a **gestire \> utenti**.

    ![Gestire gli utenti] (./media/active-directory-saas-druva-tutorial/IC795097.png "Gestire gli utenti")

3.  Fare clic su **Crea nuova**.

    ![Gestire gli utenti] (./media/active-directory-saas-druva-tutorial/IC795098.png "Gestire gli utenti")

4.  Nella finestra di dialogo Crea nuovo utente, eseguire la procedura seguente:

    ![Creare NewUser] (./media/active-directory-saas-druva-tutorial/IC795099.png "Creare NewUser")

    1.  Digitare l'indirizzo di posta elettronica e il nome di un account utente di Azure Active Directory valido si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Crea utente**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Druva utente account strumenti di creazione o API fornite dal Druva agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>Per assegnare gli utenti a Druva, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Druva **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-druva-tutorial/IC795100.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-druva-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
